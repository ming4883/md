# UE4 Shader Compile Code

```cpp
FMaterial::BeginCompileShaderMap()

class FHLSLMaterialTranslator
{
FString GetMaterialShaderCode()
}

1931 MaterialShared.cpp

const FString MaterialShaderCode = MaterialTranslator.GetMaterialShaderCode();

FMaterialShaderMap::Compile

GlobalBeginCompileShader

FMeshMaterialShaderMap::BeginCompile

FShaderCompilingManager::LaunchWorker()

FMeshMaterialShaderType::BeginCompileShader

FShaderCompileThreadRunnable::WriteNewTasks()
FShaderCompileUtilities::DoWriteTasks
```