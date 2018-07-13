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
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755660"
---
Můžete vytvořit víc služeb v předplatném, každý z nich zřízené na konkrétní úrovni pouze omezený počet povolený v jednotlivých úrovních služby. Například může vytvořit až 12 služby na úrovni Basic a jiné 12 služby na úrovni S1 v rámci stejného předplatného. Další informace o úrovních najdete v tématu [zvolte skladovou Položku nebo úroveň pro službu Azure Search](../articles/search/search-sku-tier.md).

Maximální provozní limity lze navýšit na vyžádání. Pokud potřebujete další služby v rámci stejného předplatného, kontaktujte podporu Azure.

| Prostředek            | Bezplatné&nbsp;<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD, HIGH DENSITY |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maximální služby    |1     | 12    | 12  | 6  | 6  | 6     |
| Maximální rozsah v SU&nbsp;<sup>2</sup> |neuvedeno |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Free je založen na sdílené, ne vyhrazené prostředky. Vertikální škálování nepodporuje sdílené prostředky.

<sup>2</sup> k fakturaci jednotek, které jsou přidělené jako jednotky vyhledávání (SU) *repliky* nebo *oddílu*. Budete potřebovat oba prostředky pro ukládání, indexování a operace dotazů. Další informace o výpočtech SU, naleznete v tématu [úrovně škálování prostředků pro dotazy a index](../articles/search/search-capacity-planning.md). 