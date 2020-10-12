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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80272646"
---
V rámci předplatného můžete vytvořit několik služeb. Každé z nich je možné zřídit v konkrétní úrovni. Budete omezeni jenom počtem služeb povolených na jednotlivých úrovních. Můžete například vytvořit až 12 služeb na úrovni Basic a dalších 12 služeb na úrovni S1 v rámci stejného předplatného. Další informace o úrovních najdete v tématu [Výběr SKU nebo úrovně pro Azure kognitivní hledání](../articles/search/search-sku-tier.md).

Maximální omezení služby lze vyvolat na vyžádání. Pokud v rámci jednoho předplatného potřebujete další služby, obraťte se na podporu Azure.

| Prostředek            | Zdarma<sup>1</sup> | Základní | S1  | S2 | S3 | S3 &nbsp; HD | L1 | Paměť |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximální počet služeb    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximální škálování v jednotkách hledání (SU)<sup>2</sup> |Není k dispozici |3. SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> zdarma je založen na sdílených, nevyhrazených a prostředků. U sdílených prostředků se škálování nepodporuje.

<sup>2</sup> jednotky vyhledávání jsou fakturační jednotky, které jsou přiděleny buď jako *replika* , nebo jako *oddíl*. Budete potřebovat oba prostředky pro úložiště, indexování a operace dotazování. Další informace o výpočtech SU najdete v tématu [škálování úrovní prostředků pro úlohy dotazů a indexů](../articles/search/search-capacity-planning.md). 