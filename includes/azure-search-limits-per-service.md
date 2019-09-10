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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "67175387"
---
Úložiště je omezené na místo na disku nebo na základě *maximálního počtu* indexů, dokumentů nebo jiných prostředků na nejvyšší úrovni, podle toho, co nastane dřív. V následující tabulce jsou uvedené limity úložiště. Maximální omezení indexů, dokumentů a dalších objektů najdete v tématu [omezení podle prostředků](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Zdarma | Základní<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Smlouva o úrovni služeb (SLA)<sup>3</sup>  |Ne |Ano |Ano |Ano |Ano |Ano |Ano |Ano |
| Úložiště na oddíl |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Oddíly na službu |Není k dispozici |1 |12 |12 |12 |3 |12 |12 |
| Velikost oddílu |Není k dispozici |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliky |Není k dispozici |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> základní má jeden pevný oddíl. V této vrstvě se k přidělení více replik pro zvýšené úlohy dotazů používají další jednotky hledání.

<sup>2</sup> S3 HD má pevně stanovený limit tří oddílů, což je nižší než limit počtu oddílů pro S3. Nižší omezení počtu oddílů je nastaveno z důvodu, že počet indexů pro S3 HD je podstatně vyšší. Vzhledem k tomu, že existují omezení jak výpočetních prostředků (úložiště a zpracování), tak obsahu (indexy a dokumenty), omezení obsahu bude dosaženo dříve.

<sup>3</sup> smlouvy o úrovni služeb jsou nabízeny pro Fakturovatelné služby na vyhrazených prostředcích. Bezplatné služby a funkce ve verzi Preview nemají smlouvu SLA. Pro Fakturovatelné služby se SLA projeví, když zřizujete dostatečnou redundanci pro vaši službu. Pro dotaz (čtení) SLA se vyžadují dvě nebo víc replik. Pro dotazování a indexování (pro čtení i zápis) se vyžadují tři repliky a SLA. Počet oddílů není zvážen smlouvou SLA. 