---
title: Porozumění dotazovacímu jazyku
description: Popisuje tabulky grafů prostředků a dostupné Kusto datové typy, operátory a funkce použitelné pro Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 80b33212afa7fed3f87b241d5cf69b43be66574d
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755920"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy dotazovacího jazyka grafu prostředků Azure

Dotazovací jazyk pro graf prostředků Azure podporuje řadu operátorů a funkcí. Každá práce a provoz na základě [dotazovacího jazyka Kusto (KQL)](/azure/kusto/query/index). Pokud se chcete dozvědět o dotazovacím jazyku, který používá graf prostředků, začněte s [kurzem pro KQL](/azure/kusto/query/tutorial).

Tento článek se zabývá jazykovými součástmi, které podporuje graf prostředků:

- [Tabulky grafů prostředků](#resource-graph-tables)
- [Podporované prvky jazyka KQL](#supported-kql-language-elements)
- [Řídicí znaky](#escape-characters)

## <a name="resource-graph-tables"></a>Tabulky grafů prostředků

Graf prostředků poskytuje několik tabulek pro data, která uchovává o Správce prostředků typech prostředků a jejich vlastnostech. Tyto tabulky lze použít s operátory `join` nebo `union` pro získání vlastností ze souvisejících typů prostředků. Tady je seznam tabulek dostupných v grafu prostředků:

|Tabulky grafů prostředků |Popis |
|---|---|
|Materiály |Výchozí tabulka, pokud není v dotazu definována. Většina Správce prostředkůch typů prostředků a vlastností je tady. |
|ResourceContainers |Zahrnuje předplatné (ve verzi Preview--`Microsoft.Resources/subscriptions`) a typy prostředků a data skupiny prostředků (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AlertsManagementResources |Zahrnuje prostředky _související_ s `Microsoft.AlertsManagement`. |
|SecurityResources |Zahrnuje prostředky _související_ s `Microsoft.Security`. |

> [!NOTE]
> _Prostředky_ jsou výchozí tabulkou. Při dotazování tabulky _Resources_ není nutné zadávat název tabulky, pokud se nepoužívají `join` nebo `union`. Doporučený postup je ale vždy zahrnout počáteční tabulku do dotazu.

Pomocí Průzkumníka grafů prostředků na portálu můžete zjistit, jaké typy prostředků jsou v každé tabulce k dispozici. Jako alternativu použijte dotaz, jako je například `<tableName> | distinct type`, abyste získali seznam typů prostředků, které tato tabulka grafu prostředků podporuje ve vašem prostředí.

Následující dotaz ukazuje jednoduchý `join`. Výsledek dotazu smíchá sloupce dohromady a všechny duplicitní názvy sloupců z Spojené tabulky, _ResourceContainers_ v tomto příkladu, jsou připojeny s **1**. Jelikož tabulka _ResourceContainers_ má typy pro předplatné i skupiny prostředků, může být použit buď typ pro připojení k tabulce prostředků z tabulky _prostředků_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Následující dotaz ukazuje složitější použití `join`. Dotaz omezí propojenou tabulku na předplatná a s `project`, aby zahrnovala pouze původní pole _SubscriptionId_ a pole _Name_ bylo přejmenováno na _jméno_. Při přejmenování pole se zabrání `join` přidání jako _název1_ , protože pole již v _prostředcích_existuje. Původní tabulka je filtrována pomocí `where` a následující `project` zahrnuje sloupce z obou tabulek. Výsledkem dotazu je jeden Trezor klíčů, který zobrazuje typ, název trezoru klíčů a název předplatného, ve kterém je.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Když omezíte `join` výsledků pomocí `project`, musí být v `project` zahrnuta vlastnost, kterou `join` používá k přidružení dvou tabulek, _SubscriptionId_ v předchozím příkladu.

## <a name="supported-kql-language-elements"></a>Podporované prvky jazyka KQL

Graf prostředků podporuje všechny KQL [datové typy](/azure/kusto/query/scalar-data-types/), [skalární funkce](/azure/kusto/query/scalarfunctions), [skalární operátory](/azure/kusto/query/binoperators)a [agregační funkce](/azure/kusto/query/any-aggfunction). Graf prostředků podporuje konkrétní [tabulkové operátory](/azure/kusto/query/queries) , některé z nich mají různé chování.

### <a name="supported-tabulartop-level-operators"></a>Podporované operátory tabulkové/nejvyšší úrovně

Tady je seznam KQL tabulkových operátorů podporovaných grafem prostředků s konkrétními ukázkami:

|KQL |Ukázkový dotaz grafu prostředků |Poznámky |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Počet trezorů klíčů](../samples/starter.md#count-keyvaults) | |
|[znak](/azure/kusto/query/distinctoperator) |[Zobrazit odlišné hodnoty pro konkrétní alias](../samples/starter.md#distinct-alias-values) | |
|[zvětšení](/azure/kusto/query/extendoperator) |[Počet virtuálních počítačů podle typu operačního systému](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Trezor klíčů s názvem předplatného](../samples/advanced.md#join) |Podporované charaktery spojení: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [LeftOuter](/azure/kusto/query/joinoperator#left-outer-join). Omezení 3 `join` v jednom dotazu. Vlastní strategie spojení, jako je připojení všesměrového vysílání, nejsou povolené. Dá se použít v jedné tabulce nebo mezi tabulkami _Resources_ a _ResourceContainers_ . |
|[počtu](/azure/kusto/query/limitoperator) |[Seznam všech veřejných IP adres](../samples/starter.md#list-publicip) |Synonymum `take` |
|[MV – rozbalit](/azure/kusto/query/mvexpandoperator) |[Seznam Cosmos DB s konkrétními umístěními pro zápis](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max. 400 |
|[za](/azure/kusto/query/orderoperator) |[Výpis prostředků seřazených podle názvu](../samples/starter.md#list-resources) |Synonymum `sort` |
|[projektem](/azure/kusto/query/projectoperator) |[Výpis prostředků seřazených podle názvu](../samples/starter.md#list-resources) | |
|[projekt – pryč](/azure/kusto/query/projectawayoperator) |[Odebrat sloupce z výsledků](../samples/advanced.md#remove-column) | |
|[druhu](/azure/kusto/query/sortoperator) |[Výpis prostředků seřazených podle názvu](../samples/starter.md#list-resources) |Synonymum `order` |
|[Souhrn](/azure/kusto/query/summarizeoperator) |[Počet prostředků Azure](../samples/starter.md#count-resources) |Jenom zjednodušená první stránka |
|[nezbytná](/azure/kusto/query/takeoperator) |[Seznam všech veřejných IP adres](../samples/starter.md#list-publicip) |Synonymum `limit` |
|[vrchol](/azure/kusto/query/topoperator) |[Zobrazení prvních pěti virtuálních počítačů podle názvu a jejich typu operačního systému](../samples/starter.md#show-sorted) | |
|[sjednocovací](/azure/kusto/query/unionoperator) |[Kombinování výsledků ze dvou dotazů do jednoho výsledku](../samples/advanced.md#unionresults) |Povolena jedna tabulka: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=`_ColumnName_ 1 _Table_. Omezení 3 `union` ramen v jednom dotazu. Nepovolené rozlišení `union` tabulek nožky nejsou povoleny. Dá se použít v jedné tabulce nebo mezi tabulkami _Resources_ a _ResourceContainers_ . |
|[,](/azure/kusto/query/whereoperator) |[Zobrazení prostředků, které obsahují úložiště](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Řídicí znaky

Některé názvy vlastností, například ty, které zahrnují `.` nebo `$`, musí být zabaleny nebo uvozeny v dotazu, nebo je název vlastnosti interpretován nesprávně a neposkytuje očekávané výsledky.

- `.` – název vlastnosti se zabalí jako: `['propertyname.withaperiod']`
  
  Příklad dotazu, který zabalí vlastnost _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-řídí znak v názvu vlastnosti. Použitý řídicí znak závisí na grafu prostředků prostředí, ze kterého se spouští.

  - **bash**  -  `\`

    Příklad dotazu, který řídí vlastnost _\$type_ v bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – neřídí znak `$`.

  - @No__t_2  -  **PowerShellu**

    Příklad dotazu, který řídí vlastnost _\$type_ v prostředí PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Další kroky

- Zobrazit jazyk používaný v [počátečních dotazech](../samples/starter.md)
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md)
- Naučte se [prozkoumat prostředky](explore-resources.md)