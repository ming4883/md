# GPU Update
* class FLandscapeLayersRender_RenderThread

```cpp
FLandscapeLayersHeightmapPS
{
  void SetParameters(FRHICommandList& RHICmdList, const FLandscapeLayersHeightmapShaderParameters& InParams)
	{
		SetTextureParameter(RHICmdList, GetPixelShader(), ReadTexture1Param, ReadTexture1SamplerParam, TStaticSamplerState<SF_Point, AM_Clamp, AM_Clamp, AM_Clamp>::GetRHI(), InParams.ReadHeightmap1->Resource->TextureRHI);
		SetTextureParameter(RHICmdList, GetPixelShader(), ReadTexture2Param, ReadTexture2SamplerParam, TStaticSamplerState<SF_Point, AM_Clamp, AM_Clamp, AM_Clamp>::GetRHI(), InParams.ReadHeightmap2 != nullptr ? InParams.ReadHeightmap2->Resource->TextureRHI : GWhiteTexture->TextureRHI);

		FVector4 LayerInfo(InParams.LayerAlpha, InParams.LayerVisible ? 1.0f : 0.0f, InParams.LayerBlendMode == LSBM_AlphaBlend ? 1.0f : 0.f, 0.f);
		FVector4 OutputConfig(InParams.ApplyLayerModifiers ? 1.0f : 0.0f, 0.0f /*unused*/, InParams.ReadHeightmap2 ? 1.0f : 0.0f, InParams.GenerateNormals ? 1.0f : 0.0f);
		FVector2D TextureSize(InParams.HeightmapSize.X, InParams.HeightmapSize.Y);

		SetShaderValue(RHICmdList, GetPixelShader(), LayerInfoParam, LayerInfo);
		SetShaderValue(RHICmdList, GetPixelShader(), OutputConfigParam, OutputConfig);
		SetShaderValue(RHICmdList, GetPixelShader(), TextureSizeParam, TextureSize);
		SetShaderValue(RHICmdList, GetPixelShader(), LandscapeGridScaleParam, InParams.GridSize);
		SetShaderValue(RHICmdList, GetPixelShader(), ComponentVertexCountParam, (float)InParams.CurrentMipComponentVertexCount);
	}
}
```

```
// LandscapeLayersPs.usf @36
float3 SampleTextureYNormal(float2 InTextureCoordinates, float2 InTexelSize, bool InUp)
{
	float2 SamplingUV = InTextureCoordinates;
	SamplingUV.y = InUp ? SamplingUV.y - InTexelSize.y : SamplingUV.y + InTexelSize.y;
	float2 PosSample = ReadTexture1.SampleLevel(ReadTexture1Sampler, SamplingUV, 0).rg;
	float PosHeight = ExtractHeight(PosSample) - 32768.0;
	return float3(SamplingUV * TextureSize * LandscapeGridScale.xy, PosHeight * LandscapeGridScale.z * TERRAIN_ZSCALE);
}

float3 SampleTextureXNormal(float2 InTextureCoordinates, float2 InTexelSize, bool InLeft)
{
	float2 SamplingUV = InTextureCoordinates;
	SamplingUV.x = InLeft ? SamplingUV.x - InTexelSize.x : SamplingUV.x + InTexelSize.x;
	float2 PosSample = ReadTexture1.SampleLevel(ReadTexture1Sampler, SamplingUV, 0).rg;
	float PosHeight = ExtractHeight(PosSample) - 32768.0;
	return float3(SamplingUV * TextureSize * LandscapeGridScale.xy, PosHeight * LandscapeGridScale.z * TERRAIN_ZSCALE);
}

// LandscapeLayersPs.usf @199
{
	{
		float3 PosRight = SampleTextureXNormal(InTextureCoordinates, TexelSize, false);
		float3 PosLeft = SampleTextureXNormal(InTextureCoordinates, TexelSize, true);
		float3 PosUp = SampleTextureYNormal(InTextureCoordinates, TexelSize, true);
		float3 PosDown = SampleTextureYNormal(InTextureCoordinates, TexelSize, false);

		float3 VectorRightDown = cross(PosRight - CurrentPos, PosDown - CurrentPos);
		float3 VectorDownLeft = cross(PosDown - CurrentPos, PosLeft - CurrentPos);

		float3 VectorUpRight = cross(PosUp - CurrentPos, PosRight - CurrentPos);
		float3 VectorLeftUp = cross(PosLeft - CurrentPos, PosUp - CurrentPos);

		FinalNormal = normalize(VectorUpRight + VectorLeftUp + VectorRightDown + VectorDownLeft);
	}
	//...
	// Scale back to be 0-1 normal
	OutColor.b = (FinalNormal.x + 1.0) * 0.5;
	OutColor.a = (FinalNormal.y + 1.0) * 0.5;
}
```

```
// Engine\Source\Runtime\Landscape\Private\LandscapeEditInterface.cpp
if (InCalcNormals)
{
	// Calculate the normals for each of the two triangles per quad.
	// Note that the normals at the edges are not correct because they include normals
	// from triangles outside the current area. They are not updated
	VertexNormals = new FVector[NumVertsX*NumVertsY];
	FMemory::Memzero(VertexNormals, NumVertsX*NumVertsY * sizeof(FVector));

	// Need to consider XYOffset for XY displacemented map
	FVector2D* XYOffsets = new FVector2D[NumVertsX*NumVertsY];
	FMemory::Memzero(XYOffsets, NumVertsX*NumVertsY * sizeof(FVector2D));
	GetXYOffsetDataFast(X1, Y1, X2, Y2, XYOffsets, 0);

	for (int32 Y = 0; Y < NumVertsY - 1; Y++)
	{
		for (int32 X = 0; X < NumVertsX - 1; X++)
		{
			FVector Vert00 = FVector(XYOffsets[(X + 0) + NumVertsX*(Y + 0)].X, XYOffsets[(X + 0) + NumVertsX*(Y + 0)].Y, ((float)InData[(X + 0) + InStride*(Y + 0)] - 32768.0f) * LANDSCAPE_ZSCALE) * DrawScale;
			FVector Vert01 = FVector(XYOffsets[(X + 0) + NumVertsX*(Y + 0)].X, XYOffsets[(X + 0) + NumVertsX*(Y + 0)].Y + 1.0f, ((float)InData[(X + 0) + InStride*(Y + 1)] - 32768.0f) * LANDSCAPE_ZSCALE) * DrawScale;
			FVector Vert10 = FVector(XYOffsets[(X + 0) + NumVertsX*(Y + 0)].X + 1.0f, XYOffsets[(X + 0) + NumVertsX*(Y + 0)].Y, ((float)InData[(X + 1) + InStride*(Y + 0)] - 32768.0f) * LANDSCAPE_ZSCALE) * DrawScale;
			FVector Vert11 = FVector(XYOffsets[(X + 0) + NumVertsX*(Y + 0)].X + 1.0f, XYOffsets[(X + 0) + NumVertsX*(Y + 0)].Y + 1.0f, ((float)InData[(X + 1) + InStride*(Y + 1)] - 32768.0f) * LANDSCAPE_ZSCALE) * DrawScale;

			FVector FaceNormal1 = ((Vert00 - Vert10) ^ (Vert10 - Vert11)).GetSafeNormal();
			FVector FaceNormal2 = ((Vert11 - Vert01) ^ (Vert01 - Vert00)).GetSafeNormal();

			// contribute to the vertex normals.
			VertexNormals[(X + 1 + NumVertsX*(Y + 0))] += FaceNormal1;
			VertexNormals[(X + 0 + NumVertsX*(Y + 1))] += FaceNormal2;
			VertexNormals[(X + 0 + NumVertsX*(Y + 0))] += FaceNormal1 + FaceNormal2;
			VertexNormals[(X + 1 + NumVertsX*(Y + 1))] += FaceNormal1 + FaceNormal2;
		}
	}

	delete[] XYOffsets;
}
```
