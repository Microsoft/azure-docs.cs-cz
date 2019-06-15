---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079636"
---
| Typ dat | Funkce ve výrazech lambda s povoleny `any` | Funkce ve výrazech lambda s povoleny `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Vše kromě `search.ismatch` a `search.ismatchscoring` | Stejné |
| `Collection(Edm.String)` | Porovnání s `eq` nebo `search.in` <br/><br/> Kombinování dílčí výrazy s `or` | Porovnání s `ne` nebo `not search.in()` <br/><br/> Kombinování dílčí výrazy s `and` |
| `Collection(Edm.Boolean)` | Porovnání s `eq` nebo `ne` | Stejné |
| `Collection(Edm.GeographyPoint)` | Pomocí `geo.distance` s `lt` nebo `le` <br/><br/> `geo.intersects` <br/><br/> Kombinování dílčí výrazy s `or` | Pomocí `geo.distance` s `gt` nebo `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Kombinování dílčí výrazy s `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Porovnání pomocí `eq`, `ne`, `lt`, `gt`, `le`, nebo `ge` <br/><br/> Kombinování v porovnání s jinými dílčí výrazy pomocí `or` <br/><br/> Kombinování porovnání s výjimkou `ne` s jinými dílčí výrazy pomocí `and` <br/><br/> Výrazy pomocí kombinace `and` a `or` v [disjunktivní normální formuláře (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Porovnání pomocí `eq`, `ne`, `lt`, `gt`, `le`, nebo `ge` <br/><br/> Kombinování v porovnání s jinými dílčí výrazy pomocí `and` <br/><br/> Kombinování porovnání s výjimkou `eq` s jinými dílčí výrazy pomocí `or` <br/><br/> Výrazy pomocí kombinace `and` a `or` v [pojivové možností (CNF normální formuláře)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
