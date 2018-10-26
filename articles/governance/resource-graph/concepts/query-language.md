---
title: Principy Azure Graph prostředků dotazovací jazyk
description: Popisuje, jak funguje dotazovací jazyk pro Azure Resource Graph.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 09bcedc5250755f06ba23b84a0ae90b4d43a23db
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086159"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy Azure Graph prostředků dotazovací jazyk

Dotazovací jazyk pro graf prostředků Azure podporuje řadu operátorů a funkcí. Každá práci a pracují na základě [Průzkumník dat Azure](../../../data-explorer/data-explorer-overview.md).

Nejlepší způsob, jak Seznamte se s jazykem dotazů grafem prostředků je začít s dokumentací pro Průzkumník dat Azure [dotazovací jazyk](/azure/kusto/query/index). Poskytuje představu o strukturování jazyk a jak různých podporovány operátory a funkce pracují společně.

## <a name="supported-tabular-operators"></a>Nepodporuje tabulkové operátory

Tady je seznam podporovaných tabulkové operátorů v grafu prostředků:

- [count](/azure/kusto/query/countoperator)
- [DISTINCT](/azure/kusto/query/distinctoperator)
- [Rozšíření](/azure/kusto/query/extendoperator)
- [Limit](/azure/kusto/query/limitoperator)
- [Řadit podle](/azure/kusto/query/orderoperator)
- [Projekt](/azure/kusto/query/projectoperator)
- [tokeny pro projekt](/azure/kusto/query/projectawayoperator)
- [Ukázka](/azure/kusto/query/sampleoperator)
- [distinct – ukázka](/azure/kusto/query/sampledistinctoperator)
- [Seřadit podle:](/azure/kusto/query/sortoperator)
- [shrnutí](/azure/kusto/query/summarizeoperator)
- [Take](/azure/kusto/query/takeoperator)
- [nahoru](/azure/kusto/query/topoperator)
- [vnořené nahoru](/azure/kusto/query/topnestedoperator)
- [TOP-hitters](/azure/kusto/query/tophittersoperator)
- [kde](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Podporované funkce

Tady je seznam podporovaných funkcí v grafu prostředků:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [ToString()](/azure/kusto/query/tostringfunction)

## <a name="next-steps"></a>Další postup

- Zobrazit jazyk v aplikaci [Starter dotazy](../samples/starter.md)
- Viz advanced používá v [upřesňujících dotazů](../samples/advanced.md)
- Naučte se [prozkoumat zdroje](explore-resources.md)
