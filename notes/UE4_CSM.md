# Primitives Gathering
FSceneRenderer::InitDynamicShadows()
- FSceneRenderer::GatherShadowPrimitives()
  - FProjectedShadowInfo::ShouldDrawStaticMeshes()
    - ShadowDepthPassVisibleCommands.Add()
- FSceneRenderer::GatherShadowDynamicMeshElements()
  - FProjectedShadowInfo::GatherDynamicMeshElements()
    - GatherDynamicMeshElementsArray() // dynamics primitvies
    - SetupMeshDrawCommandsForShadowDepth() // static meshes
    
# Rendering

- FMobileSceneRenderer::Render()
  - FMobileSceneRenderer::RenderShadowDepthMaps()
    - FMobileSceneRenderer::RenderShadowDepthMapAtlases()
    - FProjectedShadowInfo::RenderDepthInner()
      - RenderTarget  
        // const FSortedShadowMapAtlas& ShadowMapAtlas = SortedShadowsForShadowDepthPass.ShadowMapAtlases[AtlasIndex];
		    //FSceneRenderTargetItem& RenderTarget = ShadowMapAtlas.RenderTargets.DepthTarget->GetRenderTargetItem();
      - BeginShadowRenderPass (lambda) // set up render target
      - ShadowDepthPass.DispatchDraw(nullptr, RHICmdList);

# Allocate

- FSceneRenderer::AllocateCSMDepthTargets()
```
FIntPoint WholeSceneAtlasSize(CurrentLayout.TextureLayout.GetSizeX(), CurrentLayout.TextureLayout.GetSizeY());
FPooledRenderTargetDesc WholeSceneShadowMapDesc2D(FPooledRenderTargetDesc::Create2DDesc(WholeSceneAtlasSize, PF_ShadowDepth, FClearValueBinding::DepthOne, TexCreate_None, TexCreate_DepthStencilTargetable, false));
WholeSceneShadowMapDesc2D.Flags |= GFastVRamConfig.ShadowCSM;
GRenderTargetPool.FindFreeElement(RHICmdList, WholeSceneShadowMapDesc2D, ShadowMapAtlas.RenderTargets.DepthTarget, GetCSMRenderTargetName(LayoutIndex));
```

# Projection

- FProjectedShadowInfo::SetupWholeSceneProjection()
  - FDirectionalLightSceneProxy::GetShadowSplitBounds()
    - FDirectionalLightSceneProxy::GetShadowSplitBoundsDepthRange()

# Setup
- FSceneRenderer::InitDynamicShadows(RHICmdList, DynamicIndexBuffer, DynamicVertexBuffer, DynamicReadBuffer);
