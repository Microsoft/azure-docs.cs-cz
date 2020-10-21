---
title: Vysvětlení uplatňování slevy za rezervaci ve službě Azure Cache for Redis | Microsoft Docs
description: Přečtěte si, jak se uplatňuje sleva za rezervaci v instancích služby Azure Cache for Redis.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 4b5f77742d3b2611fd7baf5880b9f8fed047aa86
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147314"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>Jak se uplatňuje sleva za rezervaci ve službě Azure Cache for Redis

Když si koupíte rezervovanou kapacitu služby Azure Cache for Redis, na instance mezipaměti, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace pokrývá jenom náklady na výpočetní prostředky Azure Cache for Redis. Poplatky za úložiště a sítě se vám budou účtovat za použití normálních sazeb. Rezervovaná kapacita je dostupná jenom pro mezipaměti [úrovně Premium](//azure/azure-cache-for-redis/quickstart-create-redis).

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se ***nenahrazuje***. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Sleva uplatněná na Azure Cache for Redis

Sleva za rezervovanou kapacitu služby Azure Cache for Redis se pro vaše mezipaměti používá po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využití výpočetních prostředků spuštěnými mezipaměťmi. Pokud mezipaměť neběží celou hodinu, rezervace se automaticky využije pro ostatní mezipaměti odpovídající atributům rezervace. Sleva se použije může použít pro mezipaměti, které běží současně. Pokud nemáte mezipaměti, které běží celou hodinu a odpovídají atributům rezervace, pro danou hodinu nezískáte celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu Azure Cache for Redis uplatňuje v závislosti na počtu zakoupených mezipamětí a na tom, kdy jsou tyto mezipaměti spuštěné.

**Příklad 1:** Koupíte si rezervovanou kapacitu Azure Cache for Redis pro 6GB mezipaměť. Pokud používáte 13GB mezipaměť, která vyhovuje ostatním atributům rezervace, za využití výpočetních prostředků služby Azure Cache for Redis pro 7 GB se vám bude účtovat cena při průběžných platbách a na jednu hodinu využití výpočetních prostředků 6GB mezipaměti získáte slevu za rezervaci.

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu Azure Cache for Redis pro 26 GB mezipaměť a spuštěná mezipaměť vyhovují všem zbývajícím atributům rezervace.

* **Příklad 2:** Spustíte dvě 13GB mezipaměti na jednu hodinu. Na využití výpočetních prostředků obou těchto mezipamětí se bude vztahovat sleva za rezervaci 26 GB.

* **Příklad 3:** Od 13:00 do 13:30 spustíte jednu 26GB mezipaměť. Od 13:30 do 14:00 spustíte jinou 26GB mezipaměť. Na obě se bude vztahovat sleva za rezervaci.

* **Příklad 4:** Od 13:00 do 13:45 spustíte jednu 26GB mezipaměť. Od 13:30 do 14:00 spustíte jinou 26GB mezipaměť. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](./understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás
Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).