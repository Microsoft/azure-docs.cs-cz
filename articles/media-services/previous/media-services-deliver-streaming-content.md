---
title: Publikování obsahu Mediálních služeb Azure pomocí rozhraní .NET | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit lokátor, který se používá k vytvoření adresy URL streamování. Ukázky kódu jsou zapsány v c# a používají sady Media Services SDK pro rozhraní .NET.
author: juliako
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b1d0c070a9196eaa9a2706a607baa9a2926e2db4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67051748"
---
# <a name="publish-media-services-content-using-net"></a>Publikování obsahu mediálních služeb pomocí rozhraní .NET  
> [!div class="op_single_selector"]
> * [Odpočinku](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Přehled
Sadu MP4 s adaptivní přenosovou rychlostí můžete streamovat vytvořením lokátoru streamování OnDemand a vytvořením adresy URL streamování. Kódování [tématu datového zdroje](media-services-encode-asset.md) ukazuje, jak zakódovat do sady MP4 s adaptivní přenosovou rychlostí. 

> [!NOTE]
> Pokud je váš obsah zašifrován, před vytvořením lokátoru nakonfigurujte zásady doručování datových zdrojů (jak je popsáno v [tomto](media-services-dotnet-configure-asset-delivery-policy.md) tématu). 
> 
> 

Můžete také použít ondemand streaming lokátor k vytvoření adresy URL, které odkazují na SOUBORY MP4, které lze postupně stáhnout.  

Toto téma ukazuje, jak vytvořit lokátor streamování OnDemand pro publikování datového zdroje a vytvoření adres URL streamování Plynulý, MPEG DASH a HLS. To také ukazuje horké stavět progresivní stahování adres URL. 

## <a name="create-an-ondemand-streaming-locator"></a>Vytvoření lokátoru streamování OnDemand
Chcete-li vytvořit lokátor streamování OnDemand a získat adresy URL, musíte provést následující kroky:

1. Pokud je obsah zašifrován, definujte zásady přístupu.
2. Vytvořte lokátor streamování OnDemand.
3. Pokud máte v plánu streamovat, získejte soubor manifestu streamování (.ism) do datového zdroje. 
   
   Pokud plánujete postupné stahování, získejte názvy souborů MP4 v datovém zdroji.  
4. Vytvořte adresy URL do souboru manifestu nebo souborů MP4. 


>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Použijte stejné ID zásad, pokud používáte vždy stejné dny / přístupová oprávnění. Například zásady pro lokátory, které jsou určeny k zůstat na místě po dlouhou dobu (zásady bez nahrávání). Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) tématu.

### <a name="use-media-services-net-sdk"></a>Použití mediálních služeb .NET SDK
Vytváření adres URL datových proudů 

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

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Můžete také streamovat obsah přes připojení SSL. Chcete-li tento přístup provést, ujistěte se, že vaše streamované adresy URL začínají protokolem HTTPS. V současné době AMS nepodporuje SSL s vlastními doménami.
> 
> 

Vytváření progresivních adres URL stahování 

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

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Použití rozšíření sady Media Services .NET SDK
Následující kód volá metody rozšíření .NET SDK, které vytvářejí lokátor a generují adresy URL plynulého streamování, HLS a MPEG-DASH pro adaptivní streamování.
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
* [Stažení datových zdrojů](media-services-deliver-asset-download.md)
* [Konfigurace zásad pro doručování majetku](media-services-dotnet-configure-asset-delivery-policy.md)

