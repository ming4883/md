#UE4 VT Ouputs



* Engine\Source\Runtime\Renderer\Private\VT\RuntimeVirtualTextureRender.cpp

  * ```cpp
    struct FRenderGraphSetup
    {
      /* ... */
      case ERuntimeVirtualTextureMaterialType::WorldHeight:
    }
    ```
    
  * ```cpp
  class FMaterialPolicy_WorldHeight
  	{
  	public:
  		static void ModifyCompilationEnvironment(FShaderCompilerEnvironment& OutEnvironment)
  		{
  			OutEnvironment.SetDefine(TEXT("OUT_WORLDHEIGHT"), 1);
  			OutEnvironment.SetRenderTargetOutputFormat(0, PF_R32_FLOAT);
  		}

  		static FRHIBlendState* GetBlendState()
  		{
  			return TStaticBlendState< CW_RED, BO_Max, BF_One, BF_One, BO_Add, BF_One, BF_One >::GetRHI();
  		}
  	};
  	```

  * ```cpp
  class FRuntimeVirtualTextureMeshProcessor : public FMeshPassProcessor
    {
        virtual void AddMeshBatch(const FMeshBatch& RESTRICT MeshBatch, uint64 BatchElementMask, const FPrimitiveSceneProxy* RESTRICT PrimitiveSceneProxy, int32 StaticMeshId = -1) override final
    	{
        }
    }
    ```

* Engine\Shaders\Private\VirtualTextureMaterial.usf

  * ```cpp
    void FPixelShaderInOut_MainPS(
    	in FVertexFactoryInterpolantsVSToPS Interpolants,
    	inout FPixelShaderIn In, 
    	inout FPixelShaderOut Out )
    {
    	ResolvedView = ResolveView();
    
    	FMaterialPixelParameters MaterialParameters = GetMaterialPixelParameters(Interpolants, In.SvPosition);
    
    	FPixelMaterialInputs PixelMaterialInputs;
    	CalcPixelMaterialInputs(MaterialParameters, PixelMaterialInputs);
    
    	float4 ScreenPosition = SvPositionToResolvedScreenPosition(In.SvPosition);
    	float3 TranslatedWorldPosition = SvPositionToResolvedTranslatedWorld(In.SvPosition);
    	CalcMaterialParametersEx(MaterialParameters, PixelMaterialInputs, In.SvPosition, ScreenPosition, In.bIsFrontFace, TranslatedWorldPosition, TranslatedWorldPosition);
    
    #if VIRTUAL_TEXTURE_OUTPUT
    	// Output is from a UMaterialExpressionRuntimeVirtualTextureOutput node
    	half3 BaseColor = GetVirtualTextureOutput0(MaterialParameters);
    	half Specular = GetVirtualTextureOutput1(MaterialParameters);
    	half Roughness = GetVirtualTextureOutput2(MaterialParameters);
    	half3 Normal = GetVirtualTextureOutput3(MaterialParameters);
    	half WorldHeight = GetVirtualTextureOutput4(MaterialParameters);
    	float Opacity = GetVirtualTextureOutput5(MaterialParameters);
    #else
    	// Output is from standard material output attribute node
    	half3 BaseColor = GetMaterialBaseColor(PixelMaterialInputs);
    	half Specular = GetMaterialSpecular(PixelMaterialInputs);
    	half Roughness = GetMaterialRoughness(PixelMaterialInputs);
    	half3 Normal = MaterialParameters.WorldNormal;
    	float WorldHeight = MaterialParameters.AbsoluteWorldPosition.z;
    	float Opacity = GetMaterialOpacity(PixelMaterialInputs);
    #endif
    
    	// Apply debug shading
    	BaseColor = lerp(BaseColor, half3(In.SvPosition.xy * ResolvedView.ViewSizeAndInvSize.zw, 0), ResolvedView.VirtualTextureParams.y * 0.25);
    
    #if defined(OUT_BASECOLOR)
    	Out.MRT[0] = float4(BaseColor, 1.f) * Opacity;
    #elif defined(OUT_BASECOLOR_NORMAL)
    	Out.MRT[0] = float4(BaseColor, 1.f) * Opacity;
    	Out.MRT[1] = float4(PackNormal(Normal).xy, 0.f, 1.f) * Opacity;
    #elif defined(OUT_BASECOLOR_NORMAL_SPECULAR)
    	float3 PackedNormal = PackNormal(Normal);
    	Out.MRT[0] = float4(BaseColor, 1.f) * Opacity;
    	Out.MRT[1] = float4(PackedNormal.xy, 0.f, 1.f) * Opacity;
    	Out.MRT[2] = float4(Specular, Roughness, PackedNormal.z, 1.f) * Opacity;
    #elif defined(OUT_WORLDHEIGHT)
    	Out.MRT[0] = float4(PackWorldHeight(WorldHeight), 0, 0, 1);
    #endif
    }
    ```
  
  * 