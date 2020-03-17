Engine\Source\Developer\Android\AndroidTargetPlatform\Private\AndroidTargetPlatform.h

```cpp
virtual void GetTextureFormats(const UTexture* Texture, TArray< TArray<FName> >& OutFormats) const
	{
		check(Texture);

		// we remap some of the defaults (with PVRTC and ASTC formats)
		static FName FormatRemap[][2] =
		{
			// Default format:				ASTC format:
			{ { FName(TEXT("DXT1")) },{ FName(TEXT("ASTC_RGB")) } },
			{ { FName(TEXT("DXT5")) },{ FName(TEXT("ASTC_RGBA")) } },
			{ { FName(TEXT("DXT5n")) },{ FName(TEXT("ASTC_NormalAG")) } },
			{ { FName(TEXT("BC5")) },{ FName(TEXT("ASTC_NormalRG")) } },
			{ { FName(TEXT("BC6H")) },{ FName(TEXT("ASTC_RGB")) } },
			{ { FName(TEXT("BC7")) },{ FName(TEXT("ASTC_RGBAuto")) } },
			{ { FName(TEXT("AutoDXT")) },{ FName(TEXT("ASTC_RGBAuto")) } },
		};
    // Other code...
	}
```

D:\Sandbox\UnrealEngine\Engine\Source\Developer\TextureFormatASTC\Private\TextureFormatASTC.cpp
