---
title: zahrnout soubor
description: zahrnout soubor
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175387"
---
Storage je omezená místem na disku nebo vynuceným omezením na *maximální počet* indexy, dokumentu nebo jiným prostředkům vysoké úrovně, podle toho, co nastane dřív. Následující tabulka popisuje omezení úložiště. Maximální omezení na indexy, dokumenty a dalších objektů najdete v tématu [omezení prostředkem](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Free | Základní<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Smlouva o úrovni (SLA) služeb<sup>3</sup>  |Ne |Ano |Ano |Ano |Ano |Ano |Ano |Ano |
| Úložiště na oddíl |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Oddíly na službu |neuvedeno |1 |12 |12 |12 |3 |12 |12 |
| Velikost oddílu |Není k dispozici |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliky |neuvedeno |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> basic má pevnou jeden oddíl. Na této úrovni dalších vyhledávacích jednotek se používají pro přidělení více replik pro úlohy s vyšší dotazu.

<sup>2</sup> S3 HD má vynucené omezení tři oddíly, které je menší než omezení počtu oddílů pro S3. Nižší omezení počtu oddílů je nastaveno z důvodu, že počet indexů pro S3 HD je podstatně vyšší. Vzhledem k tomu, že existují omezení jak výpočetních prostředků (úložiště a zpracování), tak obsahu (indexy a dokumenty), omezení obsahu bude dosaženo dříve.

<sup>3</sup> smluv o úrovni služeb se nabízejí fakturovatelné služby na vyhrazených prostředcích. Bezplatné služby a ve verzi preview funkce mít žádnou smlouvu SLA. Fakturovatelné služby smlouvy SLA se projeví při zřizování dostatečná redundanci pro vaši službu. Dva nebo více replik jsou požadovány pro smlouvy SLA dotazování (čtení). Tři nebo více replik jsou požadovány pro dotazy a indexování smlouvy o úrovni služeb (čtení a zápis). Počet oddílů není k posouzení smlouvu SLA. 