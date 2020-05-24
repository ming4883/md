// Gather primitives
FSceneRenderer::InitDynamicShadows()
- // store shadow geometies
  - FGatherShadowPrimitivesPacket::ViewDependentWholeSceneShadowSubjectPrimitives;
- // submit
  - FGatherShadowPrimitivesPacket::RenderThreadFinalize()
  - // invoke
    - FProjectedShadowInfo::AddSubjectPrimitive()
      - MobileCSMSubjectPrimitives.AddSubjectPrimitive() : FMobileCSMSubjectPrimitives
      
- FMobileSceneRenderer::BuildCSMVisibilityState()
  - VisibleLightInfo.ShadowsToProject.Add(ProjectedShadowInfo);

// Rendering

- FMobileSceneRenderer::Render()
  - FMobileSceneRenderer::RenderShadowDepthMaps()
    - FMobileSceneRenderer::RenderShadowDepthMapAtlases()
    - FProjectedShadowInfo::RenderDepthInner()
      - RenderTarget  
        // const FSortedShadowMapAtlas& ShadowMapAtlas = SortedShadowsForShadowDepthPass.ShadowMapAtlases[AtlasIndex];
		    //FSceneRenderTargetItem& RenderTarget = ShadowMapAtlas.RenderTargets.DepthTarget->GetRenderTargetItem();
      - BeginShadowRenderPass (lambda) // set up render target
      - ShadowDepthPass.DispatchDraw(nullptr, RHICmdList);

// Allocate
FSceneRenderer::AllocateCSMDepthTargets()


			FIntPoint WholeSceneAtlasSize(CurrentLayout.TextureLayout.GetSizeX(), CurrentLayout.TextureLayout.GetSizeY());
			FPooledRenderTargetDesc WholeSceneShadowMapDesc2D(FPooledRenderTargetDesc::Create2DDesc(WholeSceneAtlasSize, PF_ShadowDepth, FClearValueBinding::DepthOne, TexCreate_None, TexCreate_DepthStencilTargetable, false));
			WholeSceneShadowMapDesc2D.Flags |= GFastVRamConfig.ShadowCSM;
			GRenderTargetPool.FindFreeElement(RHICmdList, WholeSceneShadowMapDesc2D, ShadowMapAtlas.RenderTargets.DepthTarget, GetCSMRenderTargetName(LayoutIndex));
