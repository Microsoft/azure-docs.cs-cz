---
title: zahrnout soubor
description: zahrnout soubor
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272646"
---
V rámci předplatného můžete vytvořit více služeb. Každý z nich může být zřízena na konkrétní úrovni. Jste omezeni pouze počtem služeb povolených na každé úrovni. Můžete například vytvořit až 12 služeb na úrovni Basic a dalších 12 služeb na úrovni S1 v rámci stejného předplatného. Další informace o úrovních najdete [v tématu Volba skladové položky nebo úrovně pro Azure Cognitive Search](../articles/search/search-sku-tier.md).

Maximální limity služeb lze na požádání zvýšit. Pokud potřebujete další služby v rámci stejného předplatného, obraťte se na podporu Azure.

| Prostředek            | Volný<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximální počet služeb    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximální měřítko ve vyhledávacích jednotkách (SU)<sup>2</sup> |Není dostupné. |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Zdarma je založena na sdílených, ne vyhrazených zdrojích. Škálování není podporováno u sdílených prostředků.

<sup>2</sup> Vyhledávací jednotky jsou fakturační jednotky přidělené jako *replika* nebo *oddíl*. Potřebujete prostředky pro operace úložiště, indexování a dotazů. Další informace o výpočtech SU najdete v [tématu Škálování úrovní prostředků pro úlohy dotazů a indexů](../articles/search/search-capacity-planning.md). 