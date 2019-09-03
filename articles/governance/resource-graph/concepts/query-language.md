---
title: Porozumění dotazovacímu jazyku
description: Popisuje dostupné Kusto operátory a funkce použitelné pro Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231516"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy dotazovacího jazyka grafu prostředků Azure

Dotazovací jazyk pro graf prostředků Azure podporuje řadu operátorů a funkcí. Každá práce a provoz na základě [Azure Průzkumník dat](../../../data-explorer/data-explorer-overview.md).

Nejlepším způsobem, jak získat informace o dotazovacím jazyku, který používá graf prostředků, je začít s dokumentaci k [jazyku dotazů](/azure/kusto/query/index)pro Azure Průzkumník dat. Poskytuje informace o tom, jak je jazyk strukturovaný a jak různé podporované operátory a funkce spolupracují.

## <a name="supported-tabular-operators"></a>Podporované tabulkové operátory

Tady je seznam podporovaných tabulkových operátorů v grafu prostředků:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [zvětšení](/azure/kusto/query/extendoperator)
- [počtu](/azure/kusto/query/limitoperator)
- [Řadit podle](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [vzorku](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [Seřadit podle](/azure/kusto/query/sortoperator)
- [Souhrn](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [vrchol](/azure/kusto/query/topoperator)
- [horní – vnořený](/azure/kusto/query/topnestedoperator)
- [horní – hitters](/azure/kusto/query/tophittersoperator)
- [,](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Podporované funkce

Tady je seznam podporovaných funkcí v grafu prostředků:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Řídicí znaky

Některé názvy vlastností, jako jsou například ty, které `.` obsahují `$`nebo, musí být zabaleny nebo uvozeny v dotazu nebo musí být název vlastnosti interpretován nesprávně a neposkytují očekávané výsledky.

- `.`– Název vlastnosti zabalíte takto:`['propertyname.withaperiod']`
  
  Příklad dotazu, který zabalí vlastnost _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Řídí znak v názvu vlastnosti. Použitý řídicí znak závisí na grafu prostředků prostředí, ze kterého se spouští.

  - **bash** - `\`

    Příklad dotazu, který řídí  _\$typ_ vlastnosti v bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – neřídí `$` znak.

  - **PowerShell** - ``` ` ```

    Příklad dotazu, který řídí  _\$typ_ vlastnosti v PowerShellu:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Další postup

- Zobrazit jazyk používaný v počátečních [dotazech](../samples/starter.md)
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md)
- Naučte se [prozkoumat zdroje](explore-resources.md)