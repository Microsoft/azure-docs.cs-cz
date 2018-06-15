---
title: Publikovat obsah Azure Media Services pomocí rozhraní .NET | Microsoft Docs
description: Naučte se vytvořit lokátor, který je použit k vytvoření adresy URL streamování. Ukázky kódu jsou napsané v jazyce C# a pomocí sady Media Services SDK pro .NET.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 224c9cf5ef9925645de1d94dc5bc03c15ba91432
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788512"
---
# <a name="publish-azure-media-services-content-using-net"></a>Publikovat obsah Azure Media Services pomocí rozhraní .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Azure Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Přehled
Můžete datového proudu s adaptivní přenosovou rychlostí sady souborů MP4 vytvořením Lokátor streamování OnDemand a vytvoření adresy URL streamování. [Kódování prostředek](media-services-encode-asset.md) téma ukazuje, jak ke kódování do sady souborů MP4 adaptivní přenosovou rychlostí. 

> [!NOTE]
> Pokud váš obsah je zašifrován, konfigurace zásad doručení assetu (jak je popsáno v [to](media-services-dotnet-configure-asset-delivery-policy.md) tématu) před vytvořením lokátoru. 
> 
> 

Lokátor streamování OnDemand. můžete také použít k vytvoření adresy URL, které odkazují na soubory MP4, které lze progresivně stáhnout.  

Toto téma ukazuje, jak vytvořit k publikování asset a vytvoření Smooth, MPEG DASH a adresy URL streamování HLS Lokátor streamování OnDemand. Také ukazuje aktivní pro vytvoření adres URL progresivního stahování. 

## <a name="create-an-ondemand-streaming-locator"></a>Vytvořit lokátor streamování OnDemand.
Pokud chcete vytvořit lokátor streamování OnDemand a získání adres URL, musíte udělat následující věci:

1. Pokud je obsah šifrovat, definujte zásady přístupu.
2. Vytvořte Lokátor streamování OnDemand.
3. Pokud máte v plánu k vysílání datového proudu, získáte streamování souboru manifestu (.ism) v prostředku. 
   
   Pokud budete chtít progresivně stahovat, získáte názvy soubory MP4 v prostředku.  
4. Sestavení adresy URL k souboru manifestu nebo soubory MP4. 


>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Stejné ID zásady použijte, pokud vždy používají stejné dny / přístupová oprávnění. Například zásady pro lokátory, které jsou určeny k zůstat na místě po dlouhou dobu (bez odeslání zásady). Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) tématu.

### <a name="use-media-services-net-sdk"></a>Používání Media Services .NET SDK
Vytvoření datových proudů adres URL 

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
> Můžete také Streamovat obsah pomocí připojení SSL. Chcete-li provést tento postup, ujistěte se, spustíte adresy URL streamování s protokolem HTTPS. V současné době nepodporuje AMS SSL s vlastní domény.
> 
> 

Vytvoření adres URL progresivního stahování 

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

### <a name="use-media-services-net-sdk-extensions"></a>Pomocí rozšíření Media Services .NET SDK
Následující kód volá metody rozšíření .NET SDK, které vytvořit Lokátor a generovat technologie Smooth Streaming, HLS a adres URL pro MPEG-DASH pro adaptivní streamování.
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

