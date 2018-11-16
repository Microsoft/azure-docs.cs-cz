---
title: Nejčastější dotazy k Azure Media Services | Dokumentace Microsoftu
description: Nejčastější dotazy (FAQ)
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: bf5bc66ce55b0b9d6095cd395a11f68b40af1639
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685714"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tento článek se zabývá nejčastější dotazy vyvolané službou Azure Media Services (AMS) komunitu uživatelů.

## <a name="general-ams-faqs"></a>Nejčastější dotazy k obecné AMS

Otázka: jak budete Streamovat do zařízení s Iosem Apple

Odpověď: přidejte "(format = m3u8-aapl)" cesty "/ Manifest" část adresy URL říct streamování zdrojový server vrátit zpět obsahu HLS za spotřebu na Apple iOS nativní zařízení (podrobnosti najdete v tématu [doručování obsahu](media-services-deliver-content-overview.md)),

Otázka: jak můžete škálovat, indexování?

Odpověď: rezervovaných jednotek je stejný pro kódování a indexování úloh. Postupujte podle pokynů na [postupy škálování jednotek rezervovaných pro kódování](media-services-scale-media-processing-overview.md). **Poznámka:** , Indexer výkon není ovlivněn typu rezervované jednotky.

Otázka: mohu odeslat, kódování a publikování videa. Co by se z důvodu video nehraje při Streamovat?

Odpověď: jeden z nejběžnějších je koncový bod streamování, ze kterého se pokoušíte přehrávání v nemáte **systémem** stavu.  

Otázka: Mohu skládání v živém datovém proudu?

O: skládání na živé streamy není teď nabízíme ve službě Azure Media Services, budete muset předem compose ve vašem počítači.

Otázka: Mohu použít Azure CDN s živým streamováním?

Odpověď: Media Services podporuje integraci s Azure CDN (Další informace najdete v tématu [jak spravovat koncové body streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md)).  Živé streamování s CDN můžete použít. Azure Media Services nabízí technologie Smooth Streaming, HLS a MPEG-DASH výstupy. Všechny tyto formáty používat protokol HTTP pro přenos dat a získat výhody ukládání do mezipaměti HTTP. V živém streamování skutečné video nebo zvuk data rozdělená na fragmenty a tento jednotlivé fragmenty získat uložili do mezipaměti v CDN. Pouze data je potřeba aktualizovat jsou manifestu data. CDN pravidelně aktualizuje manifestu data.

Dotaz: neodpovídá Azure Media services podporuje ukládání imagí?

Odpověď: Pokud právě Pokud chcete ukládat obrázky JPEG nebo PNG, byste měli mít v Azure Blob Storage. Neexistuje žádná výhoda pro umístit je do účtu Media Services, pokud chcete zachovat související s Video nebo zvuk prostředky. Nebo pokud bude pravděpodobně potřeba použít jako překryvy v grafickou kodér bitové kopie. Media Encoder Standard podporuje zakreslovat bitové kopie na videa, tedy co uvádí JPEG a PNG podporuje vstupní formáty. Další informace najdete v tématu [vytváření překrytí](media-services-advanced-encoding-with-mes.md#overlay).

Otázka: jak lze kopírování prostředků z jednoho účtu Media Services do jiného.

Odpověď: účelem kopírování prostředků z jednoho účtu Media Services na jiný pomocí .NET, použijte [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) metodu rozšíření k dispozici v [rozšíření Azure Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/) úložiště. Další informace najdete v tématu [to](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) vlákna ve fóru.

Otázka: jaké jsou podporované znaky pro pojmenovávání souborů při práci s AMS?

Odpověď: Media Services využívá hodnoty vlastnosti IAssetFile.Name při vytváření adres URL pro streamování obsahu (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování procent. Hodnota **název** vlastností nesmí obsahovat žádný z následujících [procent kódování – vyhrazené znaky](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Kromě toho může existovat pouze jeden "." pro příponu názvu souboru.

Otázka: jak se připojit pomocí rozhraní REST?

Odpověď: informace o tom, jak se připojit k rozhraní API pro AMS, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Otázka: jak můžete otočit videa během procesu kódování.

Odpověď: tím [kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) podporuje rotaci úhly 90, 180 nebo 270. Výchozí chování je "Auto", kde se pokusí zjistit otočení metadat v souboru MP4/MOV příchozí a jako kompenzaci za to. Patří **zdroje** element na jedno z přednastavení json definované [tady](media-services-mes-presets-overview.md):

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
