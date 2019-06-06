---
title: Podmíněné výrazy stroje pravidel Azure CDN od Verizonu Premium | Dokumentace Microsoftu
description: Referenční dokumentace pro Azure CDN od Verizonu Premium pravidla podmínky shody stroje a funkce.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: c6e49e6fbc0c541ce9a8cd903eb313d61413257c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481531"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN od podmíněné výrazy stroje pravidel Verizon Premium

Tento článek obsahuje podrobný popis podmíněné výrazy pro Azure Content Delivery Network (CDN) [stroj pravidel](cdn-verizon-premium-rules-engine.md).

První část pravidla je podmíněný výraz.

Podmíněný výraz | Popis
-----------------------|-------------
IF | Pokud výraz je vždy součástí prvním příkazem v pravidle. Stejně jako všechny ostatní podmíněné výrazy tento příkaz IF musí být přidružen shoda. Pokud žádné další podmíněné výrazy jsou definovány, určuje tuto shodu kritérium, které musí být splněny, než sada funkcí se můžou vztahovat na požadavek.
A POKUD | Výraz a v případě se dá přidat jenom po následující typy podmíněné výrazy: IF, a v případě. To znamená, že existuje jiná podmínka, která musí být splněny pro počáteční příkaz IF.
ELSE IF| Výraz ELSE IF Určuje alternativní podmínku, která musí být splněny, než sadu funkcí, které jsou specifické pro tento příkaz ELSE IF. Přítomnost příkaz ELSE IF označuje konec předchozího příkazu. Pouze podmíněný výraz, který se může použít jiný příkaz ELSE IF po příkazu ELSE IF. To znamená, že příkaz ELSE IF pouze slouží k určení jeden další podmínku, která musí být splněny.

**Příklad**: ![Podmínka shody CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Akce zadané předchozí pravidlo může přepsat následující pravidlo.
   > Příklad: Pravidlo pokrývající vše zabezpečuje všechny požadavky ověřování založeného na tokenech. Přímo pod ho tak, aby výjimku pro určité typy požadavků může vytvořit jiné pravidlo.

## <a name="next-steps"></a>Další postup

- [Přehled služby Azure CDN](cdn-overview.md)
- [Referenční informace ke stroji pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepisování výchozího chování HTTP pomocí stroje pravidel](cdn-verizon-premium-rules-engine.md)