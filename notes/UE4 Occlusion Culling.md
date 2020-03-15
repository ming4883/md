---
tags: [UE4]
title: UE4 Occlusion Culling
created: '2020-01-02T05:41:06.709Z'
modified: '2020-01-02T05:42:45.725Z'
---

# UE4 Occlusion Culling

## Software OC
// URendererSettings
uint32 bMobileAllowSoftwareOcclusionCulling:1; // r.Mobile.AllowSoftwareOcclusion
FAndroidTargetPlatform::SupportsSoftwareOcclusion()

ViewState->SceneSoftwareOcclusion

FStaticMeshOccluderData

```cpp
void FSceneViewState::ConditionallyAllocateSceneSoftwareOcclusion(ERHIFeatureLevel::Type InFeatureLevel)
{
	bool bMobileAllowSoftwareOcclusion = CVarMobileAllowSoftwareOcclusion.GetValueOnAnyThread() != 0;
	bool bShouldBeEnabled = InFeatureLevel <= ERHIFeatureLevel::ES3_1 && bMobileAllowSoftwareOcclusion;

	if (bShouldBeEnabled && !SceneSoftwareOcclusion)
	{
		SceneSoftwareOcclusion = MakeUnique<FSceneSoftwareOcclusion>();
	}
	else if (!bShouldBeEnabled && SceneSoftwareOcclusion)
	{
		SceneSoftwareOcclusion.Reset();
	}
}

```

## Hardware OC

void FDeferredShadingSceneRenderer::RenderOcclusion(FRHICommandListImmediate& RHICmdList)
void FSceneRenderer::BeginOcclusionTests(FRHICommandListImmediate& RHICmdList, bool bRenderQueries)OcclussionTest

void FDeferredShadingSceneRenderer::RenderOcclusion(FRHICommandListImmediate& RHICmdList)
void FSceneRenderer::BeginOcclusionTests(FRHICommandListImmediate& RHICmdList, bool bRenderQueries)
