---
title: Porozumění dotazovacímu jazyku
description: Popisuje dostupné Kusto operátory a funkce použitelné pro Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 54bb0b4f21752b91ceb9d4004c153ff4d95006aa
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976766"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy dotazovacího jazyka grafu prostředků Azure

Dotazovací jazyk pro graf prostředků Azure podporuje řadu operátorů a funkcí. Každá práce a provoz na základě [Azure Průzkumník dat](../../../data-explorer/data-explorer-overview.md).

Nejlepším způsobem, jak získat informace o dotazovacím jazyku, který používá graf prostředků, je začít s dokumentaci k [jazyku dotazů](/azure/kusto/query/index)pro Azure Průzkumník dat. Poskytuje informace o tom, jak je jazyk strukturovaný a jak různé podporované operátory a funkce spolupracují.

## <a name="supported-tabular-operators"></a>Podporované tabulkové operátory

Tady je seznam podporovaných tabulkových operátorů v grafu prostředků:

- [count](/azure/kusto/query/countoperator)
- [znak](/azure/kusto/query/distinctoperator)
- [zvětšení](/azure/kusto/query/extendoperator)
- [počtu](/azure/kusto/query/limitoperator)
- [Řadit podle](/azure/kusto/query/orderoperator)
- [projektem](/azure/kusto/query/projectoperator)
- [projekt – pryč](/azure/kusto/query/projectawayoperator)
- [vzorku](/azure/kusto/query/sampleoperator)
- [Ukázka – DISTINCT](/azure/kusto/query/sampledistinctoperator)
- [Seřadit podle](/azure/kusto/query/sortoperator)
- [Souhrn](/azure/kusto/query/summarizeoperator)
- [nezbytná](/azure/kusto/query/takeoperator)
- [vrchol](/azure/kusto/query/topoperator)
- [horní – vnořený](/azure/kusto/query/topnestedoperator)
- [horní – hitters](/azure/kusto/query/tophittersoperator)
- [,](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Podporované funkce

Tady je seznam podporovaných funkcí v grafu prostředků:

- [před ()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [ToString ()](/azure/kusto/query/tostringfunction)
- [zip ()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Řídicí znaky

Některé názvy vlastností, například ty, které zahrnují `.` nebo `$`, musí být zabaleny nebo uvozeny v dotazu, nebo je název vlastnosti interpretován nesprávně a neposkytuje očekávané výsledky.

- `.` – název vlastnosti se zabalí jako: `['propertyname.withaperiod']`.
  
  Příklad dotazu, který zabalí vlastnost _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-řídí znak v názvu vlastnosti. Použitý řídicí znak závisí na grafu prostředků prostředí, ze kterého se spouští.

  - **bash** -  @ no__t – 2

    Příklad dotazu, který řídí vlastnost _\$type_ v bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – neřídí znak `$`.

  - **PowerShell** -  @ no__t-2

    Příklad dotazu, který řídí vlastnost _\$type_ v prostředí PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Další kroky

- Zobrazit jazyk používaný v [počátečních dotazech](../samples/starter.md)
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md)
- Naučte se [prozkoumat prostředky](explore-resources.md)