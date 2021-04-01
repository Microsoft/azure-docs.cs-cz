---
title: Porovnání přednastavených Video Indexer a Azure Media Services V3
description: Tento článek porovnává Video Indexer možnosti a přednastavení Azure Media Services V3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 0961aa3e573e511a6941bc59ddc335f64799abb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047334"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porovnat Azure Media Services V3 předvolby a Video Indexer 

Tento článek porovnává možnosti rozhraní **video indexer API** a **rozhraní API pro Media Services V3**. 

V současné době dochází k překrytí mezi funkcemi nabízenými [rozhraními api video indexer](https://api-portal.videoindexer.ai/) a rozhraními [API Media Services V3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Následující tabulka nabízí aktuální pravidlo pro porozumění rozdílům a podobnostem. 

## <a name="compare"></a>Porovnání

|Funkce|Rozhraní API pro Video Indexer |Předvolby videa a analyzátoru zvuku<br/>v rozhraních API pro Media Services V3|
|---|---|---|
|Media Insights|[Rozšířené](video-indexer-output-json-v2.md) |[Základy](../latest/analyzing-video-audio-files-concept.md)|
|Možnosti|Úplný seznam podporovaných funkcí najdete v článku: <br/> [Přehled](video-indexer-overview.md)|Vrátí jenom video přehledy.|
|Fakturace|[Ceny Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Ceny Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Dodržování předpisů|Pro nejaktuálnější aktualizace dodržování předpisů navštivte [Offerings.pdfdodržování předpisů Azure ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) a vyhledejte "video indexer", abyste viděli, jestli vyhovuje certifikátu, který by měl zájem.|Pro nejaktuálnější aktualizace dodržování předpisů navštivte [Offerings.pdfdodržování předpisů Azure ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) a vyhledejte "Media Services", abyste viděli, jestli vyhovuje certifikátu, který by měl zájem.|
|Bezplatná zkušební verze|East US|Není k dispozici|
|Dostupnost v oblastech|Zobrazit [dostupnost Cognitive Services podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Viz [dostupnost Media Services v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)

[Přehled Media Services V3](../latest/media-services-overview.md)
