---
title: Nejčastější dotazy k Azure Media Services | Microsoft Docs
description: Nejčastější dotazy (FAQ)
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5374f7f4-c189-43ef-8b7f-f2f4141e2748
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: juliako
ms.openlocfilehash: a47163d06e24814ca5724d1fabea84058f8764cf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788533"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tento článek řeší nejčastější dotazy aktivováno komunit uživatelů Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Nejčastější dotazy k AMS obecné

Otázka: jak můžete stream zařízení Apple iOS

Následující položky: přidejte "(format = m3u8-aapl)" cesta k "/ Manifest" část adresy URL streamování zdrojového serveru k vrácení zpět HLS obsah pro používání v Apple iOS nativní zařízení (podrobnosti viz (doručování obsahu)[media-services-deliver-content-overview.md]),

Otázka: jak můžete škálovat indexování?

Odpověď: vyhrazené jednotky jsou stejné pro úlohy kódování a indexování. Postupujte podle pokynů na [postup škálování jednotek rezervovaných pro kódování](media-services-scale-media-processing-overview.md). **Poznámka:** Indexer výkonu není ovlivněn vyhrazený typ jednotky.

Otázka: I nahrán, kódování a publikování videa. Co by být z důvodu video nehraje při pokusu Streamovat ho?

Odpověď: jeden z nejčastějších důvodů je koncový bod streamování, ze kterého se pokoušíte přehrávání v nemáte **systémem** stavu.  

Otázka: je možné dělat skládání na živý datový proud?

Odpověď: skládání na živé datové proudy není aktuálně nabízena v Azure Media Services, budete muset předem vytvořit ve vašem počítači.

Otázka: je možné použít Azure CDN s živým streamováním?

Odpověď: Media Services podporuje integraci se službou Azure CDN (Další informace najdete v tématu [jak spravovat koncové body streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md)).  Můžete použít živé streamování s CDN. Azure Media Services poskytuje technologie Smooth Streaming, MPEG-DASH v HLS výstupy. Všechny tyto formáty prostřednictvím protokolu HTTP pro přenos dat a získejte výhody ukládání do mezipaměti protokolu HTTP. V živé streamování skutečné video nebo zvuk dat je rozdělen na fragmenty a tento jednotlivé fragmenty získat uložené v mezipaměti v CDN. Pouze data je nutné aktualizovat jsou manifestu data. CDN se pravidelně aktualizuje manifestu data.

Otázka: nemá Azure Media services podporuje ukládání Image?

Odpověď: Pokud hledáte pouze ukládání obrázků JPEG nebo PNG, byste měli mít v Azure Blob Storage. Neexistuje žádné výhody je vložíte do vašeho účtu Media Services, pokud chcete zachovat je přidružený k Video nebo zvuk prostředky. Nebo pokud bude možná potřeba použít Image jako překryvy v kodér videa. Media Encoder Standard podporuje překrytí bitové kopie nad videa, a co obsahuje JPEG a PNG jako podporovaný vstupní formáty. Další informace najdete v tématu [vytváření překryvy](media-services-advanced-encoding-with-mes.md#overlay).

Otázka: zkopírování prostředků z jednoho účtu Media Services do jiného.

Odpověď: pro kopírování prostředků z jednoho účtu Media Services do jiného pomocí rozhraní .NET, použijte [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) rozšíření metoda dostupná ve [rozšíření Azure Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/) úložiště. Další informace najdete v tématu [to](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fórum pro přístup z více vláken.

Otázka: co jsou podporovanými znaky pro pojmenovávání souborů při práci s AMS?

Odpověď: Media Services použije hodnotu vlastnosti IAssetFile.Name při sestavování adresy URL pro streamování obsah (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování v procentech. Hodnota **název** vlastnost nemůže mít žádné z následujících [procent kódování vyhrazené znaky](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Navíc může existovat pouze jedna '. " pro příponu názvu souboru.

Otázka: jak se připojit pomocí REST?

Odpověď: informace o tom, jak připojit k rozhraní API pro AMS najdete v tématu [přístup k Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Otázka: jak můžete během procesu kódování otočit video.

Odpověď: na [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) podporuje oběh podle úhly 90, 180 nebo 270. Výchozí chování je "Auto", kde ji pokusí zjistit otočení metadata v souboru MP4 nebo MOV příchozí a kompenzovat ho. Patří **zdroje** element pro jedno z přednastavení json definované [sem](media-services-mes-presets-overview.md):

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
