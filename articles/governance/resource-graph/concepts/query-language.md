---
title: Principy Azure Graph prostředků dotazovací jazyk
description: Popisuje, jak funguje dotazovací jazyk pro Azure Resource Graph.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3600fc47a0fb318a49c1b37722cb7fffa51ec6f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951946"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy Azure Graph prostředků dotazovací jazyk

Dotazovací jazyk pro graf prostředků Azure podporuje řadu operátorů a funkcí. Každá práce a pracovat podobně jako na dotaz jazyka Kusto (KQL). Ale je důležité si uvědomit, že prostředek grafu dotazovací jazyk je podobná [KQL](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators), nejsou stejné.

> [!NOTE]
> Odkazy na dokumentaci KQL může vyžadovat ověření.

Nejlepší způsob, jak Seznamte se s jazykem dotazů grafem prostředků je začít s dokumentací pro KQL vám pomůžou pochopit, o jak strukturovaná jazyk a jak různých podporovány operátory a funkce pracují společně.

## <a name="supported-tabular-operators"></a>Nepodporuje tabulkové operátory

Tady je seznam podporovaných tabulkové operátorů v grafu prostředků:

- [DISTINCT](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator)
- [Rozšíření](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)
- [Limit](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/limit-operator)
- [Řadit podle](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/order-operator)
- [Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)
- [tokeny pro projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-away-operator)
- [Ukázka](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-operator)
- [distinct – ukázka](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-distinct-operator)
- [Seřadit podle:](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
- [shrnutí](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)
- [Take](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
- [nahoru](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator)
- [vnořené nahoru](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-nested-operator)
- [TOP-hitters](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-hitters-operator)
- [kde](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

## <a name="supported-functions"></a>Podporované funkce

Tady je seznam podporovaných funkcí v grafu prostředků:

- [ago()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago%28%29)
- [buldschema()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/buildschema%28%29)
- [Count()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/count%28%29)
- [strcat()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/strcat%28%29)
- [isnotempty()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/isnotempty%28%29_-notempty%28%29)
- [ToString()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/tostring%28%29)

## <a name="next-steps"></a>Další postup

- Zobrazit jazyk v aplikaci [Starter dotazy](../samples/starter.md)
- Viz advanced používá v [upřesňujících dotazů](../samples/advanced.md)
- Zjistěte, jak [materiály](explore-resources.md)