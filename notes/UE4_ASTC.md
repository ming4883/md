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

D:\Sandbox\UnrealEngine\Engine\Source\Developer\TextureFormatIntelISPCTexComp\Private\TextureFormatIntelISPCTexComp.cpp

```
virtual bool CompressImage(
		const FImage& InImage,
		const struct FTextureBuildSettings& BuildSettings,
		bool bImageHasAlphaChannel,
		FCompressedImage2D& OutCompressedImage
		) const override
{
// 
FMultithreadedCompression<FASTCEncoderSettings>::Compress(MultithreadSettings, EncoderSettings, Image, OutCompressedImage, &IntelASTCCompressScans, bUseTasks);
}

static void IntelASTCCompressScans(FASTCEncoderSettings* pEncSettings, FImage* pInImage, FCompressedImage2D* pOutImage, int yStart, int yEnd, int SliceIndex)
{
}
```

Engine\Source\Developer\TextureFormatASTC\Private\TextureFormatASTC.cpp
```
class FTextureFormatASTC : public ITextureFormat
{
	virtual bool CompressImage(
			const FImage& InImage,
			const struct FTextureBuildSettings& BuildSettings,
			bool bImageHasAlphaChannel,
			FCompressedImage2D& OutCompressedImage
		) const override
	{
#if SUPPORTS_ISPC_ASTC
		// Route ASTC compression work to the ISPC module instead.
		return IntelISPCTexCompFormat.CompressImage(InImage, BuildSettings, bImageHasAlphaChannel, OutCompressedImage);
#endif
	}
};
```

Engine\Source\ThirdParty\IntelISPCTexComp\ispc_texcomp\ispc_texcomp_astc.cpp

```
// ~499
void CompressBlocksASTC(const rgba_surface* src, uint8_t* dst, astc_enc_settings* settings)
{
}
```


