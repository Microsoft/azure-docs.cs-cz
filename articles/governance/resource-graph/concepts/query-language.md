---
title: Vysvětlení dotazovací jazyk
description: Popisuje dostupné Kusto operátory a funkce, které jsou použitelné s grafem prostředků Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: dcb21a6aedf16b034fad4f0822e22758dda03c33
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800514"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy Azure Graph prostředků dotazovací jazyk

Dotazovací jazyk pro graf prostředků Azure podporuje řadu operátorů a funkcí. Každá práci a pracují na základě [Průzkumník dat Azure](../../../data-explorer/data-explorer-overview.md).

Nejlepší způsob, jak Seznamte se s jazykem dotazů grafem prostředků je začít s dokumentací pro Průzkumník dat Azure [dotazovací jazyk](/azure/kusto/query/index). Poskytuje představu o strukturování jazyk a jak různých podporovány operátory a funkce pracují společně.

## <a name="supported-tabular-operators"></a>Nepodporuje tabulkové operátory

Tady je seznam podporovaných tabulkové operátorů v grafu prostředků:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [Rozšíření](/azure/kusto/query/extendoperator)
- [Limit](/azure/kusto/query/limitoperator)
- [Řadit podle](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [Ukázka](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
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
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Řídicí znaky

Některé názvy vlastností, jako jsou ty, které zahrnují `.` nebo `$`, musí být zabalené nebo uvozeny řídicími znaky v dotazu nebo vlastnost name je nesprávně interpretován a neposkytuje očekávané výsledky.

- `.` -Zabalte jako takový název vlastnosti: `['propertyname.withaperiod']`
  
  Příklad dotazu, který obtéká vlastnost _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -Řídicí znak v názvu vlastnosti. Řídicí znak, který slouží závisí na prostředí spuštění z prostředků grafu.

  - **Bash** - `\`

    Příklad dotazu, aby řídící vlastnost  _\$typ_ v prostředí bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -není escape `$` znak.

  - **PowerShell** - ``` ` ```

    Příklad dotazu, aby řídící vlastnost  _\$typ_ v prostředí PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Další postup

- Zobrazit jazyk v aplikaci [Starter dotazy](../samples/starter.md)
- Viz advanced používá v [upřesňujících dotazů](../samples/advanced.md)
- Naučte se [prozkoumat zdroje](explore-resources.md)