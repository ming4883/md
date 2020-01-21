#UE4 SkyAtmosphere



* Shader: E:\Sandbox\UnrealEngine\Engine\Shaders\Private\SkyAtmosphere.usf

```
float4 GetScreenWorldPos(float4 SVPos, float DeviceZ)
{
#if HAS_INVERTED_Z_BUFFER
	DeviceZ = max(0.000000000001, DeviceZ);	// TODO: investigate why SvPositionToWorld returns bad values when DeviceZ is far=0 when using inverted z
#endif
	return float4(SvPositionToWorld(float4(SVPos.xy, DeviceZ, 1.0)), 1.0);
}
```

