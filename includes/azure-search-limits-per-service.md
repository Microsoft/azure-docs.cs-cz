---
title: zahrnout soubor
description: zahrnout soubor
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "83682614"
---
Vyhledávací služba je omezená na místo na disku nebo pevně nastavená na maximální počet indexů nebo indexerů, podle toho, co nastane dřív. V následující tabulce jsou uvedené limity úložiště. Maximální limity pro objekty najdete v tématu [omezení podle prostředků](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Prostředek | Free | Základní<sup>1</sup> | S1 | S2 | S3 | S3 &nbsp; HD | L1 | Paměť |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Smlouva o úrovni služeb (SLA)<sup>2</sup>  |No |Yes |Yes |Yes |Yes |Yes |Yes |Yes |
| Úložiště na oddíl |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Oddíly na službu |Nelze použít |1 |12 |12 |12 |3 |12 |12 |
| Velikost oddílu |– |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliky |– |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> základní má jeden pevný oddíl. Další jednotky vyhledávání se dají použít k přidání replik pro větší svazky dotazů.

<sup>2</sup> smlouvy o úrovni služeb jsou platné pro Fakturovatelné služby na vyhrazených prostředcích. Bezplatné služby a funkce ve verzi Preview nemají smlouvu SLA. Pro Fakturovatelné služby se SLA projeví, když zřizujete dostatečnou redundanci pro vaši službu. Pro dotaz (čtení) SLA se vyžadují dvě nebo víc replik. Pro dotazování a indexování (pro čtení i zápis) se vyžadují tři repliky a SLA. Počet oddílů není zvážen smlouvou SLA. 