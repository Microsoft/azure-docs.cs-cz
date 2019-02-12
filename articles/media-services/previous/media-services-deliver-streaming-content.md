---
title: Publikování obsahu Azure Media Services pomocí .NET | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit, který se používá k vytvoření adresy URL streamování. Ukázky kódu jsou napsané C# a používat Media Services SDK pro .NET.
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
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: 8e34d8cfbcd655dbb49279a0cefd63818963652a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999177"
---
# <a name="publish-media-services-content-using-net"></a>Publikovat obsah služby Media Services pomocí .NET  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Azure Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Přehled
Adaptivní přenosové rychlosti sady souborů MP4 vytvořením Lokátor streamování OnDemand a vytváření adresu URL streamování můžete Streamovat. [Kódování assetu](media-services-encode-asset.md) téma ukazuje, jak můžete zakódovat jako sada MP4 adaptivní přenosové rychlosti. 

> [!NOTE]
> Pokud váš obsah je zašifrován, nakonfigurujte zásady doručení assetu (jak je popsáno v [to](media-services-dotnet-configure-asset-delivery-policy.md) téma) před vytvořením lokátoru. 
> 
> 

Lokátor streamování OnDemand. můžete také použít pro vytvoření adres URL, které odkazují na soubory MP4, které je možné stáhnout postupně.  

Toto téma ukazuje, jak vytvořit publikovat asset a vytvořit protokol Smooth, MPEG DASH a adresy URL streamování HLS Lokátor streamování OnDemand. Také ukazuje horké pro vytvoření adres URL progresivního stahování. 

## <a name="create-an-ondemand-streaming-locator"></a>Vytvořit lokátor streamování OnDemand.
Pokud chcete vytvořit lokátor streamování OnDemand a získání adres URL, musíte udělat následující věci:

1. Pokud se obsah bude šifrovat, definujte zásady přístupu.
2. Vytvořte Lokátor streamování OnDemand.
3. Pokud máte v plánu stream, získáte soubor datového proudu manifest (.ism) v prostředku. 
   
   Pokud budete chtít progresivně stahovat, získáte názvy souborů MP4 v prostředku.  
4. Vytvoření adres URL k souboru manifestu nebo soubory MP4. 


>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Stejné ID zásad použijte, pokud vždy používáte stejné dny / přístupová oprávnění. Například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odesílání). Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) tématu.

### <a name="use-media-services-net-sdk"></a>Pomocí Media Services .NET SDK
Vytvoření adresy URL pro streamování 

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
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
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
> Také můžete Streamovat obsah pomocí připojení SSL. Chcete-li provést tento postup, ujistěte se, že spustíte adresy URL streamování pomocí protokolu HTTPS. V současné době nepodporuje AMS SSL s použitím vlastních domén.
> 
> 

Adresa URL progresivního stahování sestavení 

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

### <a name="use-media-services-net-sdk-extensions"></a>Použití rozšíření Media Services .NET SDK
Následující kód volá metody rozšíření sady .NET SDK vytvořit Lokátor a generovat technologie Smooth Streaming, HLS a MPEG-DASH adresy URL pro adaptivní streamování.
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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
* [Stáhnout prostředky](media-services-deliver-asset-download.md)
* [Konfigurace zásad doručení assetu](media-services-dotnet-configure-asset-delivery-policy.md)

