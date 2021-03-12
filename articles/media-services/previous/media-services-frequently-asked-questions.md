---
title: Azure Media Services nejčastějších dotazech
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 220aae64bd9ec493af8c8ee61901e27027dc9798
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013376"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Nejčastější dotazy k Media Services V2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Tento článek se zabývá nejčastějšími dotazy, které vyvolala komunita uživatelů Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Nejčastější dotazy k generalizaci AMS

Otázka: jak můžete streamovat do zařízení Apple iOS?

Odpověď: Přidejte cestu "(Format = M3U8-AAPL)" do části adresy URL "/manifest" a sdělte tak serveru zdroje streamování, aby vracel zpět HLS obsah pro spotřebu na nativních zařízeních Apple iOS (podrobnosti najdete v tématu [doručování obsahu](media-services-deliver-content-overview.md)).

Otázka: Jak škálovat indexování?

Odpověď: rezervované jednotky jsou pro úlohy kódování a indexování stejné. Postupujte podle pokynů v tématu [Jak škálovat rezervované jednotky kódování](media-services-scale-media-processing-overview.md). **Všimněte si** , že výkon indexeru není ovlivněný typem rezervované jednotky.

Otázka: nahráli jste, zakódovi a publikovali video. Jaký je důvod, proč se video nedaří přehrát při pokusu o streamování?

Odpověď: jedním z nejběžnějších důvodů je, že nemáte koncový bod streamování, ze kterého se snažíte přehrát ve stavu **spuštěno** .  

Otázka: můžu v živém streamu dělat skládání?

Odpověď: skládání na živé streamy není v Azure Media Services momentálně nabízená, takže byste v počítači museli předem vytvářet.

Otázka: je možné použít Azure CDN se živým streamování?

Odpověď: Media Services podporuje integraci s Azure CDN (Další informace najdete v tématu [Správa koncových bodů streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md)).  Živé streamování můžete používat s CDN. Azure Media Services poskytuje Smooth Streaming, HLS a výstupy MPEG-SPOJOVNÍKů. Všechny tyto formáty používají pro přenos dat protokol HTTP a získají výhody ukládání do mezipaměti HTTP. V živém streamování jsou skutečná videa a zvuková data rozdělená na fragmenty a tyto jednotlivé fragmenty se ukládají do mezipaměti v CDN. Pouze data, která je třeba aktualizovat, jsou data manifestu. CDN pravidelně aktualizuje data manifestu.

Otázka: Služba Azure Media Services podporuje ukládání imagí?

Odpověď: Pokud chcete jenom ukládat image JPEG nebo PNG, měli byste je ponechat v Azure Blob Storage. Pokud si nepřejete, aby byly spojené s videem nebo zvukovým vybavením, nemusíte je ve svém Media Services účtu využívat. Nebo v případě, že budete možná potřebovat obrázky použít jako překryvy v kodéru videa. Media Encoder Standard podporuje překrytí obrázků nad videi a to znamená, že jako podporované vstupní formáty vypisuje JPEG a PNG. Další informace najdete v tématu [vytváření překryvů](media-services-advanced-encoding-with-mes.md#overlay).

Otázka: Jak můžu Kopírovat prostředky z jednoho Media Services účtu do jiného?

Odpověď: Chcete-li kopírovat prostředky z jednoho Media Services účtu do jiného pomocí .NET, použijte metodu rozšíření [IAsset. Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) , která je k dispozici v úložišti [rozšíření Azure Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Další informace najdete v [tomto](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) vlákně fóra.

Otázka: Jaké jsou podporované znaky pro pojmenovávání souborů při práci s AMS?

Odpověď: Media Services používá hodnotu vlastnosti IAssetFile.Name při vytváření adres URL pro obsah streamování (například http://{AMSAccount}. Origin. MediaServices. Windows. NET/{GUID}/{IAssetFile. Name}/streamingParameters.) Z tohoto důvodu není povolena procentuální kódování. Hodnota vlastnosti **Name** nemůže obsahovat žádný z následujících [znaků rezervovaných v procentech](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Přípona názvu souboru může mít taky jenom jeden znak.

Otázka: jak se připojit pomocí REST?

Odpověď: informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Otázka: Jak můžu otočit video během procesu kódování?

Odpověď: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) podporuje rotaci pomocí úhlů 90/180/270. Výchozí chování je "auto", kde se pokusí rozpoznat metadata otočení ve vstupním souboru MP4/MOV a kompenzovat pro něj. Zahrňte následující **zdrojový** element do jednoho [ze zadaných přednastavení JSON:](media-services-mes-presets-overview.md)

```json
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
```


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
