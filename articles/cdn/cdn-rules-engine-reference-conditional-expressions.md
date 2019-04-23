---
title: Podmíněné výrazy stroje pravidel Azure CDN | Dokumentace Microsoftu
description: Referenční dokumentace pro Azure CDN pravidla podmínky shody stroje a funkce.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 73c41b754c0aca5ddb1a49fcd2794aa41b2fa705
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324188"
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Podmíněné výrazy stroje pravidel Azure CDN
Toto téma obsahuje podrobný popis podmíněné výrazy pro Azure Content Delivery Network (CDN) [stroj pravidel](cdn-rules-engine.md).

První část pravidla je podmíněný výraz.

Podmíněný výraz | Popis
-----------------------|-------------
IF | Pokud výraz je vždy součástí prvním příkazem v pravidle. Stejně jako všechny ostatní podmíněné výrazy tento příkaz IF musí být přidružen shoda. Pokud žádné další podmíněné výrazy jsou definovány, tuto shodu určuje kritéria, která musí být splněny, než sada funkcí se můžou vztahovat na požadavek.
A POKUD | Výraz a v případě se dá přidat jenom po následující typy podmíněné výrazy: IF, a v případě. To znamená, že existuje jiná podmínka, která musí být splněny pro počáteční příkaz IF.
ELSE IF| Výraz ELSE IF Určuje alternativní podmínku, která musí být splněny, než sadu funkcí, které jsou specifické pro tento příkaz ELSE IF. Přítomnost příkaz ELSE IF označuje konec předchozího příkazu. Pouze podmíněný výraz, který se může použít jiný příkaz ELSE IF po příkazu ELSE IF. To znamená, že příkaz ELSE IF pouze slouží k určení jeden další podmínku, která musí být splněny.

**Příklad**: ![Podmínka shody CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Akce zadané předchozí pravidlo může přepsat následující pravidlo. Příklad: Pravidlo pokrývající vše zabezpečuje všechny požadavky ověřování založeného na tokenech. Přímo pod ho tak, aby výjimku pro určité typy požadavků může vytvořit jiné pravidlo.

### <a name="next-steps"></a>Další postup
* [Azure CDN – přehled](cdn-overview.md)
* [Referenční informace ke stroji pravidel](cdn-rules-engine-reference.md)
* [Podmínky shody stroje pravidel](cdn-rules-engine-reference-match-conditions.md)
* [Funkce stroje pravidel](cdn-rules-engine-reference-features.md)
* [Přepisování výchozího chování HTTP pomocí stroje pravidel](cdn-rules-engine.md)
