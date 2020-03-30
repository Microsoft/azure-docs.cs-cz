---
title: Porovnání předvoleb Video Indexer a Azure Media Services v3
description: Tento článek porovnává možnosti video indexeru a přednastavení Azure Media Services v3.
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
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602193"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porovnání předvoleb Azure Media Services v3 a video indexeru 

Tento článek porovnává možnosti **api indexeru videa** a **mediálních služeb v3 API**. 

V současné době dochází k překrývání funkcí nabízených [api video indexeru](https://api-portal.videoindexer.ai/) a [media services v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Následující tabulka nabízí aktuální pokyny pro pochopení rozdílů a podobností. 

## <a name="compare"></a>Porovnání

|Funkce|Api indexeru videa |Přednastavení analyzátoru videa a analyzátoru zvuku<br/>v mediálních službách v3 API|
|---|---|---|
|Přehledy médií|[Rozšířené](video-indexer-output-json-v2.md) |[Základy](../latest/intelligence-concept.md)|
|Zkušenosti|Podívejte se na úplný seznam podporovaných funkcí: <br/> [Přehled](video-indexer-overview.md)|Vrátí pouze přehledy videa.|
|Fakturace|[Ceny mediálních služeb](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Ceny mediálních služeb](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Dodržování předpisů|Nejnovější aktualizace dodržování předpisů najdete na webu [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) a vyhledejte text "Video Indexer", abyste zjistili, zda je v souladu s certifikátem zájmu.|Nejnovější aktualizace dodržování předpisů najdete na [webu Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) a vyhledejte "Mediální služby", abyste zjistili, zda jsou v souladu s certifikátem zájmu.|
|Bezplatná zkušební verze|USA – východ|Není k dispozici.|
|Dostupnost v oblastech|Viz [Dostupnost služeb Cognitive Services podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Viz [Dostupnost mediálních služeb podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)

[Mediální služby v3 – přehled](../latest/media-services-overview.md)
