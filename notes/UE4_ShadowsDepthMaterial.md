# Shadow Depths
FShadowDepthPassMeshProcessor::AddMeshBatch


FShadowDepthPassMeshProcessor::Process


TShadowDepthBasePS

TShadowDepthBaseVS

```cpp
const float MaxSlopeDepthBias = PassStruct.ShadowParams.z;
const float Slope = clamp(abs(NoL) > 0 ? sqrt(saturate(1 - NoL*NoL)) / NoL : MaxSlopeDepthBias, 0, MaxSlopeDepthBias);

const float SlopeDepthBias = PassStruct.ShadowParams.y;
const float SlopeBias = SlopeDepthBias * Slope;

const float ConstantDepthBias = PassStruct.ShadowParams.x;
const float DepthBias = SlopeBias + ConstantDepthBias;

#if PERSPECTIVE_CORRECT_DEPTH
  ShadowDepth = OutPosition.z;
  OutDepthBias = DepthBias;
#elif ONEPASS_POINTLIGHT_SHADOW
  ShadowDepth = 0;
  //OutPosition.z += DepthBias;
#else
  // Output linear, normalized depth
  const float InvMaxSubjectDepth = PassStruct.ShadowParams.w;
  ShadowDepth = OutPosition.z * InvMaxSubjectDepth + DepthBias;
  OutPosition.z = ShadowDepth * OutPosition.w;
#endif
```

FProjectedShadowInfo::ShadowDepthPassUniformBuffer.UpdateUniformBufferImmediate(ShadowDepthPassParameters);
SetupMeshDrawCommandsForShadowDepth(Renderer, PassUniformBuffer);

# Material Instance

UMaterialInstance::UpdatePermutationAllocations

# Base Pass Material

GetUniformMobileBasePassShaders<>()
