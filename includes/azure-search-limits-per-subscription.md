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
ms.openlocfilehash: 0da7ad35f6efc031a52ef43caa514559c08c94fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464495"
---
Můžete vytvořit víc služeb v rámci předplatného. Každý z nich je možné zřídit na konkrétní úroveň. Co vás omezuje pouze počet povolený v jednotlivých úrovních služby. Například může vytvořit až 12 služby na úrovni Basic a jiné 12 služby na úrovni S1 v rámci stejného předplatného. Další informace o úrovních najdete v tématu [zvolte skladovou Položku nebo úroveň pro službu Azure Search](../articles/search/search-sku-tier.md).

Maximální provozní limity lze navýšit na vyžádání. Pokud potřebujete další služby v rámci stejného předplatného, kontaktujte podporu Azure.

| Prostředek            | Bezplatné<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximální služby    |1     | 12    | 12  | 6  | 6  | 6     | 6  | 6  |
| Maximální škálování jednotek vyhledávání (SU)<sup>2</sup> |neuvedeno |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Free je založen na sdílené, ne vyhrazené prostředky. Vertikální škálování nepodporuje sdílené prostředky.

<sup>2</sup> k fakturaci jednotek, které jsou přidělené jako jednotky vyhledávání *repliky* nebo *oddílu*. Budete potřebovat oba prostředky pro ukládání, indexování a operace dotazů. Další informace o výpočtech SU, naleznete v tématu [úrovně škálování prostředků pro dotazy a index](../articles/search/search-capacity-planning.md). 