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

- FProjectedShadowInfo::RenderDepth()

- FMobileSceneRenderer::Render()
  - FMobileSceneRenderer::RenderShadowDepthMaps()
    - FMobileSceneRenderer::RenderShadowDepthMapAtlases()
