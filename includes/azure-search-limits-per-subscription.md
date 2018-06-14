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
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30844064"
---
Můžete vytvořit více služeb v rámci předplatného, každé z nich zřízené v konkrétní úroveň, omezena pouze počet služeb v každé úrovni povoleno. Například můžete vytvořit až 12 services na úroveň Basic a jiné 12 services na vrstvě S1 v rámci stejného předplatného. Další informace o úrovních najdete v tématu [zvolte SKU nebo vrstvy pro službu Azure Search](../articles/search/search-sku-tier.md).

Omezení maximální služby může být vyvolána při požadavku. Pokud potřebujete další služby v rámci stejného předplatného, kontaktujte podporu Azure.

| Prostředek            | Volné&nbsp;<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maximální služby    |1     | 12    | 12  | 6  | 6  | 6     |
| Maximální měřítko v SU&nbsp;<sup>2</sup> |neuvedeno |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Free je založen na sdílené, není vyhrazené prostředky. Škálování není podporována na sdílených prostředků.

<sup>2</sup> jednotek služby vyhledávání (SU) jsou fakturace jednotek, které jsou přidělené jako buď *repliky* nebo *oddílu*. Potřebujete i prostředky pro úložiště, indexování a operace dotazů. Další informace o SU výpočtů, najdete v části [škálovat prostředek úrovně pro úlohy dotazovací a indexovací](../articles/search/search-capacity-planning.md). 