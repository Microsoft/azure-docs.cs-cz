---
title: Podmíněné výrazy pro modul pravidel Azure CDN – Verizon Premium
description: Referenční dokumentace pro Azure CDN od Verizon Premium pravidla motoru odpovídají podmínky a funkce.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082977"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Podmíněné výrazy modulu pravidel Azure CDN od společnosti Verizon Premium

Tento článek obsahuje podrobný popis modulu pravidel sítě [podmíněných](cdn-verizon-premium-rules-engine.md)výrazů pro Azure Content Delivery Network (CDN).

První část pravidla je podmíněný výraz.

Podmíněný výraz | Popis
-----------------------|-------------
IF | Výraz KDYŽ je vždy součástí prvního příkazu v pravidle. Stejně jako všechny ostatní podmíněné výrazy musí být tento příkaz KDYŽ přidružen ke shodě. Pokud nejsou definovány žádné další podmíněné výrazy, tato shoda určuje kritérium, které musí být splněno, aby mohla být sada funkcí použita pro požadavek.
A POKUD | Výraz A IF může být přidán pouze za následující typy podmíněných výrazů:IF, a IF. Označuje, že existuje další podmínka, která musí být splněna pro počáteční příkaz KDYŽ.
ELSE POKUD| Výraz ELSE IF určuje alternativní podmínku, která musí být splněna před tím, než dojde k sadě funkcí specifických pro tento příkaz ELSE IF. Přítomnost else IF prohlášení označuje konec předchozího příkazu. Jediný podmíněný výraz, který může být umístěn za příkazelse IF je jiný příkaz ELSE IF. To znamená, že příkaz ELSE IF lze použít pouze k určení jedné další podmínky, která musí být splněna.

**Příklad** ![: Podmínka shody CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Následné pravidlo může přepsat akce určené předchozím pravidlem.
   > Příklad: Pravidlo catch-all zabezpečuje všechny požadavky prostřednictvím ověřování na základě tokenu. Jiné pravidlo může být vytvořeno přímo pod ním, aby se výjimka pro určité typy požadavků.

## <a name="next-steps"></a>Další kroky

- [Přehled azure cdn](cdn-overview.md)
- [Odkaz na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Pravidla, které jsou v souladu s podmínkami motoru](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funkce modulu pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepsání výchozího chování PROTOKOLU HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)