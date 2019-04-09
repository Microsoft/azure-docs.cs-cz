---
title: Porovnání Video Indexer a Azure Media Services v3 předvolby | Dokumentace Microsoftu
description: Toto téma srovnává funkce Video Indexer a Azure Media Services v3 předvolby.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270332"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porovnání služeb Azure Media Services v3 přednastavení a Video Indexer 

Tento článek porovnává možnosti **rozhraní Video Indexer API** a **rozhraní API služby Media Services v3**. 

V současné době je k překrytí mezi součástmi, které nabízí [rozhraní Video Indexer API](https://api-portal.videoindexer.ai/) a [rozhraní API služby Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Následující tabulka nabízí aktuální obecných zásad pro pochopení rozdíly a podobnosti. 

## <a name="compare"></a>Porovnání

|Funkce|Video Indexer API |Analyzátor videa a zvuku analyzátor přednastavení<br/>v rozhraní API služby Media Services v3|
|---|---|---|
|Media Insights|[Rozšířené](video-indexer-output-json-v2.md) |[Základy](../latest/intelligence-concept.md)|
|Prostředí|Podívejte se na seznam podporovaných funkcí: <br/> [Přehled](video-indexer-overview.md)|Vrátí pouze poznatků z videí|
|Fakturace|[Ceny za Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Ceny za Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Dodržování předpisů|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)a [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certified. Nejnovější aktualizace najdete [aktuální stav certifikace modulu Video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Služba Media Services je kompatibilní s mnoha certifikace. Podívejte se na [Offerings.pdf dodržování předpisů Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) a vyhledejte položku "Media Services", chcete-li zobrazit, pokud je v souladu s certifikátem, které vás zajímají.|
|Bezplatná zkušební verze|USA – východ|Není k dispozici.|
|Dostupnost v oblastech|USA – východ 2, USA (střed) – Jih, USA – západ 2, Severní Evropa, západní Evropa, jihovýchodní Asie, jihovýchodní Asie a Austrálie – východ.  Nejnovější aktualizace najdete [produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) stránky.|Zobrazit [stav Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Další postup

[Video Indexer s přehledem](video-indexer-overview.md)

[Přehled služby Media Services v3](../latest/media-services-overview.md)
