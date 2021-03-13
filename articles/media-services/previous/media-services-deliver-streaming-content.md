---
title: Publikování obsahu Azure Media Services pomocí .NET | Microsoft Docs
description: Naučte se, jak vytvořit lokátor, který se používá k vytvoření adresy URL streamování. Ukázky kódu jsou napsané v jazyce C# a používají sadu Media Services SDK pro .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a1a06c9a5fdac1082a57bee71cccad271a6aae81
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015790"
---
# <a name="publish-media-services-content-using-net"></a>Publikování obsahu Media Services pomocí .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Azure Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Přehled
Můžete vytvořit datový proud sady MP4 s adaptivní přenosovou rychlostí vytvořením lokátoru pro streamování OnDemand a vytvořením adresy URL streamování. Téma [kódování prostředku](media-services-encode-asset.md) ukazuje, jak kódovat do sady MP4 s adaptivní přenosovou rychlostí. 

> [!NOTE]
> Pokud je váš obsah zašifrovaný, nakonfigurujte zásady doručení assetu (jak je popsáno v [tomto](media-services-dotnet-configure-asset-delivery-policy.md) tématu) ještě před vytvořením lokátoru. 
> 
> 

Lokátor streamování OnDemand můžete použít také k sestavení adres URL, které odkazují na soubory MP4, které se dají postupně stahovat.  

V tomto tématu se dozvíte, jak vytvořit Lokátor streamování s využitím OnDemand pro publikování assetu a jak vytvořit hladké adresy URL streamování a HLS streamování MPEG. Zobrazuje se také jako horká pro vytváření progresivních adres URL pro stahování. 

## <a name="create-an-ondemand-streaming-locator"></a>Vytvoření lokátoru pro streamování OnDemand
Chcete-li vytvořit Lokátor streamování OnDemand a získat adresy URL, je nutné provést následující akce:

1. Pokud je obsah zašifrovaný, definujte zásady přístupu.
2. Vytvořte Lokátor streamování OnDemand.
3. Pokud máte v úmyslu streamovat, Získejte v assetu soubor manifestu pro streamování (. ISM). 
   
   Pokud se chystáte se postupně stahovat, Získejte názvy souborů MP4 v assetu.  
4. Vytvoří adresy URL pro soubor manifestu nebo soubory MP4. 


>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždycky používáte stejné dny/přístupová oprávnění, použijte stejné ID zásad. Například zásady pro Lokátory, které mají zůstat v platnosti po dlouhou dobu (zásady bez odesílání). Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) tématu.

### <a name="use-media-services-net-sdk"></a>Použití sady Media Services .NET SDK
Vytváření adres URL streamování 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

Výstupy:

- Adresa URL manifestu pro streamování klientů pomocí Smooth Streamingho protokolu: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`
- Adresa URL manifestu pro streamování klientů pomocí protokolu HLS: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`
- Adresa URL manifestu pro streamování klientů pomocí protokolu MPEG disčárky: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

> [!NOTE]
> Svůj obsah můžete streamovat i přes připojení TLS. Pokud chcete tento přístup provést, ujistěte se, že vaše adresy URL streamování začínají na HTTPS. AMS v současné době nepodporuje TLS s vlastními doménami.
> 
> 

Sestavit progresivní adresy URL pro stahování 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
Výstupy:

- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4`

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Použití rozšíření Media Services .NET SDK
Následující kód volá metody rozšíření .NET SDK, které vytvářejí Lokátor a generují adresy URL Smooth Streaming, HLS a MPEG-SPOJOVNÍK pro adaptivní streamování.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
* [Stažení prostředků](media-services-deliver-asset-download.md)
* [Konfigurace zásad doručení assetu](media-services-dotnet-configure-asset-delivery-policy.md)

