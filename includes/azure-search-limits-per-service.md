---
title: zahrnout soubor
description: zahrnout soubor
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272780"
---
Úložiště je omezeno na disku nebo pevným limitem *maximálního počtu* indexů, dokumentů nebo jiných prostředků vysoké úrovně podle toho, co nastane dříve. V následující tabulce jsou dokumentována omezení úložiště. Maximální limity indexů, dokumentů a dalších objektů naleznete v [tématu Omezení podle prostředků](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Prostředek | Free | Základní<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Smlouva o úrovni služeb (SLA)<sup>3</sup>  |Ne |Ano |Ano |Ano |Ano |Ano |Ano |Ano |
| Úložiště na oddíl |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Oddíly na službu |Není dostupné. |1 |12 |12 |12 |3 |12 |12 |
| Velikost oddílu |Není dostupné. |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliky |Není dostupné. |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic má jeden pevný oddíl. Na této úrovni další vyhledávací jednotky se používají pro přidělování více replik pro zvýšené úlohy dotazů.

<sup>2</sup> S3 HD má pevný limit tří oddílů, což je nižší než limit oddílu pro S3. Nižší omezení počtu oddílů je nastaveno z důvodu, že počet indexů pro S3 HD je podstatně vyšší. Vzhledem k tomu, že existují omezení jak výpočetních prostředků (úložiště a zpracování), tak obsahu (indexy a dokumenty), omezení obsahu bude dosaženo dříve.

<sup>3</sup> Smlouvy o úrovni služeb jsou nabízeny pro fakturovatelné služby z vyhrazených zdrojů. Bezplatné služby a funkce náhledu nemají žádnou sla. U fakturovatelných služeb se sla projeví, když pro vaši službu zařídíte dostatečnou redundanci. Pro informace o sla dotazů (čtení) jsou vyžadovány dvě nebo více replik. Pro informace o sla dotazů a indexování (čtení a zápisu) jsou vyžadovány tři nebo více replik. Počet oddílů není sohledem na sla. 