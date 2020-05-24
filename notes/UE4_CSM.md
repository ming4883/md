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
      - BeginShadowRenderPass (lambda) // set up render target
      - ShadowDepthPass.DispatchDraw(nullptr, RHICmdList);
