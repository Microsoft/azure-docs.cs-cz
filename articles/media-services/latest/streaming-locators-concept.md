---
title: Streamování lokátory ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou lokátory streamování a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120354"
---
# <a name="streaming-locators"></a>Lokátory streamování

Pokud chcete, aby videa ve výstupním prostředku byla k dispozici klientům pro přehrávání, musíte vytvořit [lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) a adresy URL pro streamování. Ukázku v .NET najdete v tématu věnovaném [získání lokátoru streamování](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces vytváření **Lokátor streamování** nazývá publikování. Ve výchozím nastavení **Lokátor streamování** platnost okamžitě po provedení volání rozhraní API a trvá, dokud je odstraníme, pokud nenakonfigurujete volitelné počáteční a koncový čas. 

Při vytváření **Lokátor streamování**, je nutné zadat **Asset** název a **streamování zásad** název. Další informace naleznete v následujících tématech:

* [Prostředky](assets-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady symetrických klíčů](content-key-policy-concept.md)

> [!IMPORTANT]
> * Vlastnosti **lokátory streamování** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).

## <a name="associate-filters-with-streaming-locators"></a>Lokátory streamování přidružit filtry

Zobrazit [filtry: přidružili lokátory streamování](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtr, pořadí, stránka Lokátor streamování entity

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

[Kurz: Nahrávání, kódování a streamování videí pomocí .NET](stream-files-tutorial-with-api.md)
