---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80272759"
---
| Datový typ | Funkce povolené ve výrazech lambda s `any` | Funkce povolené ve výrazech lambda s `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Vše kromě `search.ismatch` a `search.ismatchscoring` | Stejné |
| `Collection(Edm.String)` | Porovnání s `eq` nebo `search.in` <br/><br/> Kombinování dílčích výrazů s `or` | Porovnání s `ne` nebo `not search.in()` <br/><br/> Kombinování dílčích výrazů s `and` |
| `Collection(Edm.Boolean)` | Porovnání s `eq` nebo `ne` | Stejné |
| `Collection(Edm.GeographyPoint)` | Použití `geo.distance` s `lt` nebo `le` <br/><br/> `geo.intersects` <br/><br/> Kombinování dílčích výrazů s `or` | Použití `geo.distance` s `gt` nebo `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Kombinování dílčích výrazů s `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Porovnávání pomocí `eq` , `ne` , `lt` , `gt` , `le` nebo `ge` <br/><br/> Kombinování porovnání s ostatními podvýrazy pomocí `or` <br/><br/> Kombinování porovnání s výjimkou `ne` ostatních dílčích výrazů using `and` <br/><br/> Výrazy používající kombinace `and` a `or` v [normálním formátu Disjunctive (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Porovnávání pomocí `eq` , `ne` , `lt` , `gt` , `le` nebo `ge` <br/><br/> Kombinování porovnání s ostatními podvýrazy pomocí `and` <br/><br/> Kombinování porovnání s výjimkou `eq` ostatních dílčích výrazů using `or` <br/><br/> Výrazy používající kombinace `and` a `or` v [normálním formátu conjunctive (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
