---
tags: [UE4]
title: UE4 Occlusion Culling
created: '2020-01-02T05:41:06.709Z'
modified: '2020-01-02T05:42:45.725Z'
---

# UE4 Occlusion Culling

## Software OC
ViewState->SceneSoftwareOcclusion

FStaticMeshOccluderData

## Hardware OC

void FDeferredShadingSceneRenderer::RenderOcclusion(FRHICommandListImmediate& RHICmdList)
void FSceneRenderer::BeginOcclusionTests(FRHICommandListImmediate& RHICmdList, bool bRenderQueries)OcclussionTest

void FDeferredShadingSceneRenderer::RenderOcclusion(FRHICommandListImmediate& RHICmdList)
void FSceneRenderer::BeginOcclusionTests(FRHICommandListImmediate& RHICmdList, bool bRenderQueries)
