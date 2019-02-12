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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: d1a7ae1e66caaaf17e3c4a38b09eaa2d900604b3
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004481"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tento článek se zabývá nejčastější dotazy vyvolané službou Azure Media Services (AMS) komunitu uživatelů.

## <a name="general-ams-faqs"></a>Nejčastější dotazy k obecné AMS

DOTAZ: Jak budete Streamovat do zařízení s Iosem Apple?

Odpověď: přidejte "(format = m3u8-aapl)" cesty "/ Manifest" část adresy URL říct streamování zdrojový server vrátit zpět obsahu HLS za spotřebu na Apple iOS nativní zařízení (podrobnosti najdete v tématu [doručování obsahu](media-services-deliver-content-overview.md)),

DOTAZ: Jak můžete škálovat, indexování?

Odpověď: Rezervované jednotky jsou stejné pro kódování a indexování úloh. Postupujte podle pokynů na [postupy škálování jednotek rezervovaných pro kódování](media-services-scale-media-processing-overview.md). **Poznámka:** , Indexer výkon není ovlivněn typu rezervované jednotky.

DOTAZ: Můžu nahrát, kódování a publikování videa. Co by se z důvodu video nehraje při Streamovat?

Odpověď: Jedním z nejběžnějších je můžete nemají koncový bod streamování, ze kterého se snažíte přehrávat v **systémem** stavu.  

DOTAZ: Můžete provést skládání v živém datovém proudu?

Odpověď: Skládání na živé streamy není teď nabízíme ve službě Azure Media Services, takže byste museli předem compose ve vašem počítači.

DOTAZ: Můžete použít Azure CDN s živým streamováním

Odpověď: Služba Media Services podporuje integraci s Azure CDN (Další informace najdete v tématu [jak spravovat koncové body streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md)).  Živé streamování s CDN můžete použít. Azure Media Services nabízí technologie Smooth Streaming, HLS a MPEG-DASH výstupy. Všechny tyto formáty používat protokol HTTP pro přenos dat a získat výhody ukládání do mezipaměti HTTP. V živém streamování skutečné video nebo zvuk data rozdělená na fragmenty a tento jednotlivé fragmenty získat uložili do mezipaměti v CDN. Pouze data je potřeba aktualizovat jsou manifestu data. CDN pravidelně aktualizuje manifestu data.

DOTAZ: Azure Media services podporuje ukládání imagí?

Odpověď: Pokud hledáte pouze ukládání obrázků JPEG nebo PNG, byste měli mít v Azure Blob Storage. Neexistuje žádná výhoda pro umístit je do účtu Media Services, pokud chcete zachovat související s Video nebo zvuk prostředky. Nebo pokud bude pravděpodobně potřeba použít jako překryvy v grafickou kodér bitové kopie. Media Encoder Standard podporuje zakreslovat bitové kopie na videa, tedy co uvádí JPEG a PNG podporuje vstupní formáty. Další informace najdete v tématu [vytváření překrytí](media-services-advanced-encoding-with-mes.md#overlay).

DOTAZ: Jak lze kopírovat prostředky z jednoho účtu Media Services do jiného?

Odpověď: Zkopírujte prostředky z jednoho účtu Media Services pomocí rozhraní .NET, použijte [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) metodu rozšíření k dispozici v [rozšíření Azure Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/) úložiště. Další informace najdete v tématu [to](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) vlákna ve fóru.

DOTAZ: Jaké jsou podporované znaky pro pojmenovávání souborů při práci s AMS?

Odpověď: Služba Media Services využívá hodnoty vlastnosti IAssetFile.Name při vytváření adres URL pro streamování obsahu (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování procent. Hodnota **název** vlastností nesmí obsahovat žádný z následujících [procent kódování – vyhrazené znaky](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Kromě toho může existovat pouze jeden "." pro příponu názvu souboru.

DOTAZ: Jak se připojit pomocí rozhraní REST?

Odpověď: Informace o tom, jak se připojit k rozhraní API pro AMS, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

DOTAZ: Jak můžete otočit videa během procesu kódování?

Odpověď: [Kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) podporuje rotaci úhly 90, 180 nebo 270. Výchozí chování je "Auto", kde se pokusí zjistit otočení metadat v souboru MP4/MOV příchozí a jako kompenzaci za to. Patří **zdroje** element na jedno z přednastavení json definované [tady](media-services-mes-presets-overview.md):

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
