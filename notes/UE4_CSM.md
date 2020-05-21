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

- Used by FSceneRenderer::RenderShadowProjections()
