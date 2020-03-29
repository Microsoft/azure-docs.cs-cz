---
title: Nejčastější dotazy azure media services
description: Tento článek poskytuje odpovědi na nejčastější dotazy týkající se Mediálních služeb Azure.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: bdb5a43ad6669bfcd6e93ef4e3bf1646314e4606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705866"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Media Services v2 nejčastější dotazy

Tento článek řeší nejčastější dotazy, které vyvolává komunita uživatelů Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Obecné nejčastější dotazy k AMS

Otázka: Jak streamujete do zařízení Apple iOS?

A: přidat "(format=m3u8-aapl)" cestu k části "/Manifest" adresy URL, abyste řekli streamovanému původnímu serveru, aby vrátil zpět obsah HLS pro spotřebu na nativních zařízeních Apple iOS (podrobnosti viz [doručování obsahu](media-services-deliver-content-overview.md)),

Otázka: Jak škálování indexování?

A: Rezervované jednotky jsou stejné pro kódování a indexování úlohy. Postupujte podle pokynů [jak změnit velikost rezervovaných jednotek kódování](media-services-scale-media-processing-overview.md). **Všimněte si,** že výkon indexeru není ovlivněn typem rezervované jednotky.

Otázka: Nahrál jsem, zakódoval a publikoval video. Jaký by byl důvod, proč se video nepřehrává, když se ho snažím streamovat?

A: Jedním z nejčastějších důvodů je nemáte koncový bod streamování, ze kterého se pokoušíte přehrát ve stavu **Spuštěno.**  

Otázka: Mohu provést skládání v živém přenosu?

A: Skládání na živé datové proudy není aktuálně nabízena ve službě Azure Media Services, takže budete muset pre-compose na vašem počítači.

Otázka: Můžu používat Azure CDN s živým streamováním?

A: Media Services podporuje integraci s Azure CDN (další informace naleznete v [tématu Správa koncových bodů streamování v účtu Mediální služby](media-services-portal-manage-streaming-endpoints.md)).  Živé streamování můžete používat s CDN. Azure Media Services poskytuje výstupy plynulého streamování, HLS a MPEG-DASH. Všechny tyto formáty používají http pro přenos dat a získat výhody ukládání do mezipaměti HTTP. V živém streamování jsou skutečná video/audio data rozdělena na fragmenty a tyto jednotlivé fragmenty se uváznou do mezipaměti v CDN. Data je třeba aktualizovat pouze data. CDN pravidelně aktualizuje data manifestu.

Otázka: Podporuje služby Azure Media ukládání bitových kopií?

A: Pokud se právě snažíte ukládat obrázky JPEG nebo PNG, měli byste je uchovávat ve službě Azure Blob Storage. Pokud nechcete, aby byly spojeny s videonebo zvukovými prostředky, není jejich vložení do účtu mediálních služeb nijak výhodné. Nebo pokud budete potřebovat použít obrázky jako překryvy v kodéru videa. Standard Media Encoder Standard podporuje překrytí obrázků nad videi, a to je to, co uvádí JPEG a PNG jako podporované vstupní formáty. Další informace naleznete v [tématu Vytváření překryvů](media-services-advanced-encoding-with-mes.md#overlay).

Otázka: Jak lze kopírovat datové zdroje z jednoho účtu Mediálních služeb do druhého?

A: Chcete-li kopírovat prostředky z jednoho účtu Media Services do jiného pomocí rozhraní .NET, použijte metodu rozšíření [IAsset.Copy,](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) která je dostupná v úložišti [rozšíření Azure Media Services .NET SDK.](https://github.com/Azure/azure-sdk-for-media-services-extensions/) Další informace naleznete v [tomto](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) vlákně fóra.

Otázka: Jaké jsou podporované znaky pro pojmenování souborů při práci s AMS?

A: Media Services používá hodnotu vlastnosti IAssetFile.Name při vytváření adres URL pro streamování obsahu (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování procentuální chod. Hodnota **Name** vlastnost nemůže mít žádné z následujících [procent-kódování vyhrazené znaky:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$,/?%#[]". Také může existovat pouze jeden '.' pro příponu názvu souboru.

Otázka: Jak se připojit pomocí REST?

A: Informace o tom, jak se připojit k rozhraní API AMS, najdete [v tématu přístup k rozhraní API Azure Media Services s ověřováním Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Otázka: Jak mohu otočit video během procesu kódování?

A: [Standard kodéru médií](media-services-dotnet-encode-with-media-encoder-standard.md) podporuje otáčení úhly 90/180/270. Výchozí chování je "Auto", kde se snaží zjistit metadata rotace v příchozím souboru MP4/MOV a kompenzovat ji. Do jednoho z [přednastavení](media-services-mes-presets-overview.md)json definovaných zde zahrňte následující prvek **Zdroje** :

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


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
