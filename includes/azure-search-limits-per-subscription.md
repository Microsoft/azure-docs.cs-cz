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
ms.openlocfilehash: a466ea29fa31c4c628724f3d5138a1612ef7a0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553386"
---
Můžete vytvořit víc služeb v rámci předplatného. Každý z nich je možné zřídit na konkrétní úroveň. Co vás omezuje pouze počet povolený v jednotlivých úrovních služby. Například může vytvořit až 12 služby na úrovni Basic a jiné 12 služby na úrovni S1 v rámci stejného předplatného. Další informace o úrovních najdete v tématu [zvolte skladovou Položku nebo úroveň pro službu Azure Search](../articles/search/search-sku-tier.md).

Maximální provozní limity lze navýšit na vyžádání. Pokud potřebujete další služby v rámci stejného předplatného, kontaktujte podporu Azure.

| Prostředek            | Bezplatné<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maximální služby    |1     | 12    | 12  | 6  | 6  | 6     |
| Maximální škálování jednotek vyhledávání (SU)<sup>2</sup> |neuvedeno |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup>Free je založen na sdílené, ne vyhrazené prostředky. Vertikální škálování nepodporuje sdílené prostředky.

<sup>2</sup>k fakturaci jednotek, které jsou přidělené jako jednotky vyhledávání *repliky* nebo *oddílu*. Budete potřebovat oba prostředky pro ukládání, indexování a operace dotazů. Další informace o výpočtech SU, naleznete v tématu [úrovně škálování prostředků pro dotazy a index](../articles/search/search-capacity-planning.md). 