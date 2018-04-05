---
title: zahrnout soubor
description: zahrnout soubor
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
Úložiště je omezené místa na disku nebo pevný limit na *maximální počet* indexy, dokumentu nebo jiným prostředkům vysoké úrovně, nastane dříve. Následující tabulka obsahuje informace o omezení úložiště. Maximální omezení pro indexy, dokumenty a další objekty, najdete v části [omezení prostředkem](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Prostředek | Free | Základní&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Smlouva o úrovni (SLA) <sup>3</sup>  |Ne |Ano |Ano |Ano |Ano |Ano |
| Úložiště na oddíl |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Oddíly na službu |neuvedeno |1 |12 |12 |12 |3 |
| Velikost oddílu |Není k dispozici |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliky |neuvedeno |3 |12 |12 |12 |12 |

<sup>1</sup> basic má pevnou jeden oddíl. Na této úrovni další služby SUs slouží pro přidělení více replik pro úlohy vyšší dotazu.

<sup>2</sup> S3 HD má vynucené omezení na 3 oddíly, což je méně než omezení počtu oddílů pro S3. Nižší omezení počtu oddílů je nastaveno z důvodu, že počet indexů pro S3 HD je podstatně vyšší. Vzhledem k tomu, že existují omezení jak výpočetních prostředků (úložiště a zpracování), tak obsahu (indexy a dokumenty), omezení obsahu bude dosaženo dříve.

<sup>3</sup> smlouvy o úrovni služeb (SLA) nabízí pro fakturovatelný služby na vyhrazených prostředcích. Bezplatné služby a preview funkce mít žádné SLA. Fakturovatelný služeb SLA projeví při zřizování dostatečná redundance pro vaši službu. Dvě nebo více replik jsou požadovány pro SLA dotazu (načíst). Tři nebo více replik jsou požadovány pro dotazy a indexování smlouvy o úrovni služeb (pro čtení a zápis). Počet oddílů není k posouzení SLA. 