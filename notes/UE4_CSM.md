FSceneRenderer::InitDynamicShadows()
- // store shadow geometies
  - FGatherShadowPrimitivesPacket::ViewDependentWholeSceneShadowSubjectPrimitives;
- // submit
  - FGatherShadowPrimitivesPacket::RenderThreadFinalize()
  - // invoke
    - FProjectedShadowInfo::AddSubjectPrimitive()
      - MobileCSMSubjectPrimitives.AddSubjectPrimitive() : FMobileCSMSubjectPrimitives
