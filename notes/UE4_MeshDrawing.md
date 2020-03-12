DrawDynamicMeshPass()

class FHitProxyMeshProcessor : FMeshPassProcessor {}
-- BuildMeshDrawCommands();

TMeshProcessorShaders<>  PassShaders;

```
DrawDynamicMeshPass(View, RHICmdList,
  [Scene, &View, &DrawRenderState, &Mesh](FDynamicPassMeshDrawListContext* DynamicMeshPassContext)
{
  FHitProxyMeshProcessor PassMeshProcessor(
    Scene,
    &View,
    false,
    DrawRenderState,
    DynamicMeshPassContext);

  const uint64 DefaultBatchElementMask = ~0ull;
  PassMeshProcessor.AddMeshBatch(Mesh, DefaultBatchElementMask, nullptr);
});
```
