---
title: Azure Event Grid úrovně Premium a Basic
description: Tento článek popisuje rozdíl mezi úrovněmi Azure Event Grid úrovně Premium a Basic a jejich použití.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79300715"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid úrovně Premium a Basic
Azure Event Grid má dvě úrovně: **Premium** a **Basic**. Úroveň Basic využívá spotřebu nebo ceny za průběžné platby. Poskytuje vám všechny základní nástroje pro publikování na základě událostí, které potřebujete k použití Event Grid pro programovací modely založené na událostech. Úroveň Premium má ještě jeden krok s funkcemi zabezpečení zaměřenými na podnik. Úroveň Premium je v rané **verzi Preview** a mnoho jeho funkcí je stále ve vývoji.

## <a name="overview"></a>Přehled
Všechna vlastní témata a domény v Event Grid patří do úrovně Basic nebo Premium. Počínaje verzí `2020-04-01-preview` rozhraní API můžete zvolit požadovanou úroveň v rámci vytváření tématu nebo domény. Výchozí hodnota je úroveň Basic. Témata a domény, které byly vytvořeny pomocí starší verze rozhraní API, jsou standardně nastavené na úroveň Basic. Pokud chcete změnit cenovou úroveň svých témat a domén, přečtěte si téma [Postup aktualizace úrovně pro témata a domény](update-tier.md).

## <a name="capabilities-and-features"></a>Možnosti a funkce

Následující tabulka popisuje rozdíly mezi vrstvami:

|       &nbsp;                                           | Základní           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Pravidla brány firewall protokolu IP pro příchozí přenosy                          | Preview  | Preview |
| Značky služeb pro odchozí přenosy                                | Preview  | Preview |
| Integrace virtuální sítě privátního koncového bodu při příchozím přenosu dat          | Není k dispozici.   | Preview |

## <a name="availability"></a>Dostupnost
Během počáteční verze Preview jsou témata a domény úrovně Premium a integrace s privátním koncovým bodem dostupné v těchto oblastech:

- USA – východ
- USA – západ 2
- USA – středojih

## <a name="pricing-and-quotas"></a>Ceny a kvóty
Podrobnosti o cenách používání úrovně Basic najdete v tématu [Event Grid ceny](https://azure.microsoft.com/pricing/details/event-grid/) . Ceny úrovně Premium ještě nejsou oznámené a jsou bezplatné, dokud nebudou dostupné ceny.

Stávající kvóty týkající se počtu témat a domén a propustnosti platí pro prostředky úrovně Premium i Basic, dokud se neúčtují na ceny úrovně Premium.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- Pokud chcete upgradovat z úrovně Basic na úroveň Premium, přečtěte si článek o [cenové úrovni aktualizace](update-tier.md) . 
- Můžete nakonfigurovat bránu firewall protokolu IP pro prostředek Event Grid, abyste omezili přístup přes veřejný Internet jenom z vybrané sady IP adres nebo rozsahů IP adres. Podrobné pokyny najdete v tématu [Konfigurace brány firewall](configure-firewall.md).
- Privátní koncové body můžete nakonfigurovat tak, aby omezily přístup jenom z vybraných virtuálních sítí. Podrobné pokyny najdete v tématu [Konfigurace privátních koncových bodů](configure-private-endpoints.md).