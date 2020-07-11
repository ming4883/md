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
