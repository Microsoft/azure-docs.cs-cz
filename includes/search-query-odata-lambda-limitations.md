---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272759"
---
| Datový typ | Funkce povolené ve výrazech lambda s`any` | Funkce povolené ve výrazech lambda s`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Všechno `search.ismatch` kromě a`search.ismatchscoring` | Stejné |
| `Collection(Edm.String)` | Srovnání s `eq` nebo`search.in` <br/><br/> Kombinace dílčích výrazů`or` | Srovnání s `ne` nebo`not search.in()` <br/><br/> Kombinace dílčích výrazů`and` |
| `Collection(Edm.Boolean)` | Srovnání s `eq` nebo`ne` | Stejné |
| `Collection(Edm.GeographyPoint)` | Použití `geo.distance` `lt` s nebo`le` <br/><br/> `geo.intersects` <br/><br/> Kombinace dílčích výrazů`or` | Použití `geo.distance` `gt` s nebo`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Kombinace dílčích výrazů`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Porovnání pomocí `eq` `ne`, `lt` `gt`, `le`, , nebo`ge` <br/><br/> Kombinace porovnání s jinými dílčími výrazy pomocí`or` <br/><br/> Kombinace porovnání s `ne` výjimkou jiných podvýrazů pomocí`and` <br/><br/> Výrazy používající `and` kombinace `or` a [disjunktivní normální formu (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Porovnání pomocí `eq` `ne`, `lt` `gt`, `le`, , nebo`ge` <br/><br/> Kombinace porovnání s jinými dílčími výrazy pomocí`and` <br/><br/> Kombinace porovnání s `eq` výjimkou jiných podvýrazů pomocí`or` <br/><br/> Výrazy používající `and` kombinace `or` a v [konjunktivní normální formě (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
