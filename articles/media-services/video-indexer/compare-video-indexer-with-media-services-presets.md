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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 2e2abd4ffe5a6d0a6336d811599db687da146f1e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513180"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porovnat Azure Media Services V3 předvolby a Video Indexer 

Tento článek porovnává možnosti rozhraní **video indexer API** a **rozhraní API pro Media Services V3**. 

V současné době dochází k překrytí mezi funkcemi nabízenými [rozhraními api video indexer](https://api-portal.videoindexer.ai/) a rozhraními [API Media Services V3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Následující tabulka nabízí aktuální pravidlo pro porozumění rozdílům a podobnostem. 

## <a name="compare"></a>Porovnání

|Funkce|Rozhraní API pro Video Indexer |Předvolby videa a analyzátoru zvuku<br/>v rozhraních API pro Media Services V3|
|---|---|---|
|Media Insights|[Rozšíření](video-indexer-output-json-v2.md) |[Základy](../latest/intelligence-concept.md)|
|Možnosti|Úplný seznam podporovaných funkcí najdete v článku: <br/> [Přehled](video-indexer-overview.md)|Vrátí jenom video přehledy.|
|Vyúčtování|[Ceny Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Ceny Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Dodržování předpisů|[Iso 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1, 2, 3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)a [HiTRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certifikováno. Nejaktuálnější aktualizace najdete v [části aktuální stav certifikace video indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Media Services je kompatibilní s mnoha certifikacemi. Podívejte se na [nabídku dodržování předpisů Azure. PDF](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) a vyhledejte "Media Services", abyste zjistili, jestli jsou v souladu s certifikátem zájmu.|
|Bezplatná zkušební verze|USA – východ|Není k dispozici|
|Regionální dostupnost|Východní USA 2, Střed USA – jih, Západní USA 2, Severní Evropa, Západní Evropa, jihovýchodní Asie, Východní Asie a Austrálie – východ.  Nejaktuálnější aktualizace najdete na stránce [produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) .|Podívejte se na téma [stav Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)

[Přehled Media Services V3](../latest/media-services-overview.md)
