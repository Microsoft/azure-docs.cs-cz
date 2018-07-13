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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755684"
---
Storage je omezená místem na disku nebo vynuceným omezením na *maximální počet* indexy, dokumentu nebo jiným prostředkům vysoké úrovně, podle toho, co nastane dřív. Následující tabulka popisuje omezení úložiště. Maximální omezení na indexy, dokumenty a dalších objektů najdete v tématu [omezení prostředkem](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Prostředek | Free | Základní&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Smlouva o úrovni (SLA) služeb <sup>3</sup>  |Ne |Ano |Ano |Ano |Ano |Ano |
| Úložiště na oddíl |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Oddíly na službu |neuvedeno |1 |12 |12 |12 |3 |
| Velikost oddílu |Není k dispozici |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliky |neuvedeno |3 |12 |12 |12 |12 |

<sup>1</sup> basic má pevnou jeden oddíl. Na této úrovni se používají další služby SUs pro přidělení více replik pro úlohy s vyšší dotazu.

<sup>2</sup> S3 HD má vynucené omezení na 3 oddíly, což je méně než omezení počtu oddílů pro S3. Nižší omezení počtu oddílů je nastaveno z důvodu, že počet indexů pro S3 HD je podstatně vyšší. Vzhledem k tomu, že existují omezení jak výpočetních prostředků (úložiště a zpracování), tak obsahu (indexy a dokumenty), omezení obsahu bude dosaženo dříve.

<sup>3</sup> smluv o úrovni služeb (SLA) jsou nabízeny služby fakturovatelné na vyhrazených prostředcích. Bezplatné služby a ve verzi preview funkce mít žádnou smlouvu SLA. Fakturovatelné služby smlouvy SLA se projeví při zřizování dostatečná redundanci pro vaši službu. Dva nebo více replik jsou požadovány pro smlouvu SLA dotazování (čtení). Tři nebo více replik jsou požadovány pro dotazy a indexování SLA (čtení a zápis). Počet oddílů není k posouzení smlouvu SLA. 