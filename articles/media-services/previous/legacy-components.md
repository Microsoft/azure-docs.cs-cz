---
title: Azure Media Services starší verze komponent | Microsoft Docs
description: Toto téma popisuje Azure Media Services starších komponent.
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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: 457f5b8eb0a740800f9cc0fa69bd554f0679ae9a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509168"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starší součásti

V průběhu času byla k dispozici stabilní vylepšení a vylepšení komponent Media Service. V důsledku toho byly některé starší komponenty vyřazeny. Pokyny, jak migrovat aplikaci ze starší verze na aktuální komponentu, najdete v následujících článcích.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plány vyřazení zastaralých součástí a pokyny k migraci

Oznamujeme vyřazení *Windows Azure Media Encoder* (WAME) a *Azure Media Encoderch* (ázev) mediálních procesorů. Tyto procesory se zařadí 1. března 2020.

* [Migrace z Windows Azure Media Encoder na Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrace z Azure Media Encoder na Media Encoder Standard](migrate-azure-media-encoder.md)

Také oznamujeme vyřazení následujících Media Analytics mediálních procesorů: 
 
|Název procesoru médií|Datum vyřazení|Další poznámky|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1\. října z 2020|Tento procesor médií bude nahrazen [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Další informace najdete v tématu [migrace z Azure Media Indexer 2 do Azure Media Services video indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1\. října z 2020|Tento procesor médií bude nahrazen [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Další informace najdete v tématu [migrace z Azure Media Indexer do Azure Media Services video indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Další kroky

[Pokyny k migraci pro přesun z Media Services V2 na V3](../latest/migrate-from-v2-to-v3.md)
