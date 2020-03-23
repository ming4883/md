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
