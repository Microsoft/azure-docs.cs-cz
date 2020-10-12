---
title: Podmíněné výrazy pro modul pravidel Azure CDN Verizon Premium
description: Referenční dokumentace pro Azure CDN z podmínek a funkcí Verizoní pravidel Premium Engine.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81253505"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN z podmíněných výrazů modulu Verizon Premium Rules

V tomto článku jsou uvedeny podrobné popisy podmíněných výrazů pro [modul pravidel](cdn-verizon-premium-rules-engine.md)služby Azure Content DELIVERY Network (CDN).

První část pravidla je podmíněný výraz.

Podmíněný výraz | Popis
-----------------------|-------------
IF | Výraz IF je vždy součástí prvního příkazu v pravidle. Stejně jako všechny ostatní podmíněné výrazy musí být tento příkaz IF přidružen k shodě. Pokud nejsou definovány žádné další podmíněné výrazy, tato shoda určuje kritérium, které je nutné splnit, než bude možné použít sadu funkcí pro požadavek.
A POKUD | Výraz AND IF lze přidat pouze za následující typy podmíněných výrazů: IF a IF. Označuje, že existuje další podmínka, která musí být splněna pro počáteční příkaz IF.
JINAK IF| Výraz ELSE IF Určuje alternativní podmínku, která musí být splněna před sadou funkcí specifických pro tento ELSE IF. Přítomnost jiného příkazu IF indikuje konec předchozího příkazu. Jediný podmíněný výraz, který může být umístěn po příkazu ELSE IF, je jiný příkaz if IF. To znamená, že příkaz ELSE IF lze použít pouze k zadání jedné další podmínky, která musí být splněna.

**Příklad**: ![ Podmínka párování CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Následné pravidlo může přepsat akce zadané předchozím pravidlem.
   > Příklad: pravidlo catch-All zabezpečuje všechny požadavky prostřednictvím Token-Based ověřování. Jiné pravidlo lze vytvořit přímo pod ním, aby bylo možné vytvořit výjimku pro určité typy požadavků.

## <a name="next-steps"></a>Další kroky

- [Přehled Azure CDN](cdn-overview.md)
- [Odkazy na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepsání výchozího chování HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)