Source\Runtime\Landscape\Private\LandscapeRenderMobile.cpp

```cpp
FLandscapeComponentSceneProxyMobile
```

Source\Runtime\Landscape\Private\LandscapeRender.cpp

```
void FLandscapeComponentSceneProxy::OnTransformChanged()
{
}

// InComponent : ULandscapeComponent
// FLandscapeComponentSceneProxyMobile::FLandscapeComponentSceneProxyMobile()
{
	WeightmapTextures = InComponent->MobileWeightmapTextures;
	NormalmapTexture = InComponent->MobileWeightmapTextures[0];
}
```


Shaders\Private\LandscapeVertexFactory.ush

```
FMaterialPixelParameters GetMaterialPixelParameters(FVertexFactoryInterpolantsVSToPS Interpolants, float4 SvPosition)
{
  Result.LayerWeights.xy = Texture2DSample(LandscapeParameters.NormalmapTexture, LandscapeParameters.NormalmapTextureSampler,         Interpolants.WeightMapTexCoord).rg;
	Result.LayerWeights.z = saturate(BlendableLayerMask.z - dot(Result.LayerWeights.xy, BlendableLayerMask.xy));
	Result.LayerWeights.w = 0; // Total weight need to be 1
}
```

UMaterialExpressionLandscapeLayerBlend

```
class FHLSLMaterialTranslator
{
virtual int32 StaticTerrainLayerWeight(FName ParameterName,int32 Default) override
{
	// ...
	int32 WeightmapCode;
	if (GetFeatureLevel() <= ERHIFeatureLevel::ES3_1 && NumActiveTerrainLayerWeightParameters <= 3 && bAtLeastOneWeightBasedBlend)
	{
		// Mobile can pack 3 layers into the normal map texture B and A channels, implying the 3rd using weight based blending
		// Layer texture is sampled into Parameters.LayerWeights in LandscapeVertexFactory.ush
		WeightmapCode = AddInlinedCodeChunk(MCT_Float4, TEXT("Parameters.LayerWeights"));
	}
	else
	{
		// Otherwise we sample normally
		const EMaterialSamplerType SamplerType = SAMPLERTYPE_Masks;
		FString WeightmapName = FString::Printf(TEXT("Weightmap%d"),WeightmapIndex);
		int32 TextureReferenceIndex = INDEX_NONE;
		int32 TextureCodeIndex = TextureParameter(FName(*WeightmapName), GEngine->WeightMapPlaceholderTexture, TextureReferenceIndex, SamplerType);
		WeightmapCode = TextureSample(TextureCodeIndex, TextureCoordinate(3, false, false), SamplerType);
	}

	FString LayerMaskName = FString::Printf(TEXT("LayerMask_%s"),*ParameterName.ToString());
	return Dot(WeightmapCode,VectorParameter(FName(*LayerMaskName), FLinearColor(1.f,0.f,0.f,0.f)));
}
};
```
