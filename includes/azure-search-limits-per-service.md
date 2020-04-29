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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272780"
---
Úložiště je omezené na místo na disku nebo na základě *maximálního počtu* indexů, dokumentů nebo jiných prostředků na nejvyšší úrovni, podle toho, co nastane dřív. V následující tabulce jsou uvedené limity úložiště. Maximální omezení indexů, dokumentů a dalších objektů najdete v tématu [omezení podle prostředků](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Prostředek | Free | Základní<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | Paměť |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Smlouva o úrovni služeb (SLA)<sup>3</sup>  |Ne |Ano |Ano |Ano |Ano |Ano |Ano |Ano |
| Úložiště na oddíl |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Oddíly na službu |– |1 |12 |12 |12 |3 |12 |12 |
| Velikost oddílu |– |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliky |– |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> základní má jeden pevný oddíl. V této vrstvě se k přidělení více replik pro zvýšené úlohy dotazů používají další jednotky hledání.

<sup>2</sup> S3 HD má pevně stanovený limit tří oddílů, což je nižší než limit počtu oddílů pro S3. Nižší omezení počtu oddílů je nastaveno z důvodu, že počet indexů pro S3 HD je podstatně vyšší. Vzhledem k tomu, že existují omezení jak výpočetních prostředků (úložiště a zpracování), tak obsahu (indexy a dokumenty), omezení obsahu bude dosaženo dříve.

<sup>3</sup> smlouvy o úrovni služeb jsou nabízeny pro Fakturovatelné služby na vyhrazených prostředcích. Bezplatné služby a funkce ve verzi Preview nemají smlouvu SLA. Pro Fakturovatelné služby se SLA projeví, když zřizujete dostatečnou redundanci pro vaši službu. Pro dotaz (čtení) SLA se vyžadují dvě nebo víc replik. Pro dotazování a indexování (pro čtení i zápis) se vyžadují tři repliky a SLA. Počet oddílů není zvážen smlouvou SLA. 