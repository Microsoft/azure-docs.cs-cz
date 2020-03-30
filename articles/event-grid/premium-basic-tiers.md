---
title: Úrovně Azure Event Grid Premium a Basic
description: Tento článek popisuje rozdíl mezi úrovněmi Azure Event Grid Premium a Basic a s tím, kdy se mají jednotlivé úrovně používat.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300715"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid premium a základní úrovně
Azure Event Grid má dvě úrovně: **Premium** a **Basic**. Základní úroveň používá spotřeba nebo průběžná platba. To vám dává všechny základní pub / sub nástroje, které potřebujete k použití Event Grid pro události-řízené programovací modely. Úroveň premium posouvá tento krok dále s bezpečnostními funkcemi zaměřenými na podnik. Prémiová úroveň je v raném **náhledu** s mnoha jeho funkcemi, které jsou stále ve vývoji.

## <a name="overview"></a>Přehled
Všechna vlastní témata a domény v Event Grid patří buď základní vrstvy nebo úrovně premium. Počínaje `2020-04-01-preview` verzí rozhraní API můžete zvolit požadovanou úroveň jako součást vytváření tématu nebo domény. Výchozí hodnota je základní vrstva. Témata a domény vytvořené pomocí starších verzí rozhraní API jsou ve výchozím nastavení na základní úroveň. Pokud chcete změnit cenovou úroveň pro vaše témata a domény, přečtěte si téma [Jak aktualizovat úroveň pro témata a domény](update-tier.md).

## <a name="capabilities-and-features"></a>Možnosti a funkce

Následující tabulka popisuje rozdíly mezi úrovněmi:

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Pravidla brány firewall IP pro příchozí přenos dat                          | Preview  | Preview |
| Značky služeb pro odchozí přenos dat                                | Preview  | Preview |
| Privátní integrace virtuální sítě koncového bodu při příchozím přenosu dat          | Není k dispozici.   | Preview |

## <a name="availability"></a>Dostupnost
Během počáteční verze Preview jsou témata a domény úrovně premium s privátní integrací koncového bodu k dispozici v následujících oblastech:

- USA – východ
- USA – západ 2
- USA – středojih

## <a name="pricing-and-quotas"></a>Ceny a kvóty
Podrobnosti o cenách použití základní úrovně najdete v [tématu Ceny event gridu.](https://azure.microsoft.com/pricing/details/event-grid/) Ceny na úrovni Premium ještě nejsou oznámeny a jsou zdarma, dokud ceny nejsou k dispozici.

Existující kvóty na téma a počet domén a propustnost platí pro prostředky úrovně Premium i základní úrovně, dokud není oznámeno stanovení cen úrovně premium.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- Pokud chcete upgradovat ze základní úrovně na úroveň Premium, přečtěte si článek [Aktualizovat cenovou úroveň.](update-tier.md) 
- Bránu firewall IP pro prostředek služby Event Grid můžete nakonfigurovat tak, aby omezovala přístup přes veřejný Internet pouze z vybrané sady adres IP nebo rozsahů IP adres. Podrobné pokyny naleznete v tématu [Konfigurace brány firewall](configure-firewall.md).
- Soukromé koncové body můžete nakonfigurovat tak, aby omezovaly přístup pouze z vybraných virtuálních sítí. Podrobné pokyny naleznete v tématu [Konfigurace soukromých koncových bodů](configure-private-endpoints.md).