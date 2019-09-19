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
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "67175386"
---
V rámci předplatného můžete vytvořit několik služeb. Každé z nich je možné zřídit v konkrétní úrovni. Budete omezeni jenom počtem služeb povolených na jednotlivých úrovních. Můžete například vytvořit až 12 služeb na úrovni Basic a dalších 12 služeb na úrovni S1 v rámci stejného předplatného. Další informace o úrovních najdete v tématu [Výběr SKU nebo úrovně pro Azure Search](../articles/search/search-sku-tier.md).

Maximální omezení služby lze vyvolat na vyžádání. Pokud v rámci jednoho předplatného potřebujete další služby, obraťte se na podporu Azure.

| Resource            | Zdarma<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximální počet služeb    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximální škálování v jednotkách hledání (SU)<sup>2</sup> |Není k dispozici |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> zdarma je založen na sdílených, nevyhrazených a prostředků. U sdílených prostředků se škálování nepodporuje.

<sup>2</sup> jednotky vyhledávání jsou fakturační jednotky, které jsou přiděleny buď jako replika, nebo jako *oddíl*. Budete potřebovat oba prostředky pro úložiště, indexování a operace dotazování. Další informace o výpočtech SU najdete v tématu [škálování úrovní prostředků pro úlohy dotazů a indexů](../articles/search/search-capacity-planning.md). 