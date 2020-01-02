---
tags: [UE4]
title: UE4 LOD Screen Size
created: '2020-01-02T05:35:33.508Z'
modified: '2020-01-02T05:37:58.238Z'
---

# UE4 LOD Screen Size

## Data
```
class FStaticMeshRenderData
{
    FPerPlatformFloat ScreenSize
};

ComputeBoundsScreenSize(Origin, SphereRadius, View.ViewMatrices.GetViewOrigin(), View.ViewMatrices.GetProjectionMatrix());


FStaticMeshSceneProxy::FLODInfo::FLODInfo
class FStaticMeshRenderData
{
    FPerPlatformFloat ScreenSize
};

ComputeBoundsScreenSize(Origin, SphereRadius, View.ViewMatrices.GetViewOrigin(), View.ViewMatrices.GetProjectionMatrix());

```

## Compute
```
FLODMask FStaticMeshSceneProxy::GetLODMask(const FSceneView* View) constLODMask FStaticMeshSceneProxy::GetLODMask(const FSceneView* View) const;

Result.SetLOD(ComputeStaticMeshLOD(RenderData, ProxyBounds.Origin, ProxyBounds.SphereRadius, *View, ClampedMinLOD, InvScreenSizeScale));Result.SetLOD(ComputeStaticMeshLOD(RenderData, ProxyBounds.Origin, ProxyBounds.SphereRadius, *View, ClampedMinLOD, InvScreenSizeScale));
```
