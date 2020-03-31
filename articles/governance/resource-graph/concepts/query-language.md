---
title: Principy dotazovacího jazyka
description: Popisuje tabulky resource graph a dostupné typy dat Kusto, operátory a funkce použitelné pomocí Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927485"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy dotazovacího jazyka Azure Resource Graph

Dotazovací jazyk pro Azure Resource Graph podporuje řadu operátorů a funkcí. Každá práce a provoz na základě [Kusto dotazovacího jazyka (KQL)](/azure/kusto/query/index). Chcete-li se dozvědět o dotazovacím jazyce používaném resource graph, začněte [kurzem pro KQL](/azure/kusto/query/tutorial).

Tento článek popisuje jazykové součásti podporované resource graph:

- [Tabulky grafu zdrojů](#resource-graph-tables)
- [Podporované prvky jazyka KQL](#supported-kql-language-elements)
- [Řídicí znaky](#escape-characters)

## <a name="resource-graph-tables"></a>Tabulky grafu zdrojů

Resource Graph poskytuje několik tabulek pro data, která ukládá o typech prostředků Správce prostředků Resource Manager a jejich vlastnostech. Tyto tabulky lze `join` použít `union` s nebo operátory získat vlastnosti z typů souvisejících prostředků. Zde je seznam tabulek dostupných v grafu zdrojů:

|Tabulky grafu zdrojů |Popis |
|---|---|
|Prostředky |Výchozí tabulka, pokud není definována v dotazu. Většina typů prostředků a vlastností Správce prostředků jsou zde. |
|Kontejnery prostředků |Zahrnuje typy prostředků `Microsoft.Resources/subscriptions`a data předplatného`Microsoft.Resources/subscriptions/resourcegroups`(ve verzi -- ) a skupiny prostředků ( ). |
|AdvisorResources |Zahrnuje zdroje `Microsoft.Advisor`související _s_ programem . |
|AlertsManagementResources |Zahrnuje zdroje `Microsoft.AlertsManagement`související _s_ programem . |
|Zdroje údržby |Zahrnuje zdroje `Microsoft.Maintenance`související _s_ programem . |
|Bezpečnostní zdroje |Zahrnuje zdroje `Microsoft.Security`související _s_ programem . |

Úplný seznam včetně typů prostředků naleznete v [tématu Reference: Podporované tabulky a typy prostředků](../reference/supported-tables-resources.md).

> [!NOTE]
> _Zdroje_ jsou výchozí tabulka. Při dotazování tabulky _Prostředky_ není nutné zadat název tabulky, pokud `join` nebo `union` nejsou použity. Doporučeným postupem je však vždy zahrnout počáteční tabulku v dotazu.

Pomocí Průzkumníka grafů prostředků na portálu můžete zjistit, jaké typy prostředků jsou k dispozici v každé tabulce. Jako alternativu použijte dotaz, `<tableName> | distinct type` například získat seznam typů prostředků dané tabulky grafu prostředků podporuje, které existují ve vašem prostředí.

Následující dotaz ukazuje `join`jednoduchý . Výsledek dotazu prolne sloupce dohromady a všechny duplicitní názvy sloupců z připojené tabulky _ResourceContainers_ v tomto příkladu jsou připojeny s **1**. Jako _ResourceContainers_ tabulka obsahuje typy pro odběry a skupiny prostředků, může být použit buď typ pro připojení k prostředku z tabulky _prostředků._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Následující dotaz ukazuje složitější použití `join`aplikace . Dotaz omezuje připojenou tabulku na prostředky `project` odběrů a zahrnuje pouze původní _pole subscriptionId_ a pole _název_ přejmenované na _SubName_. Přejmenování pole zabrání `join` jeho přidání jako _názvu1,_ protože pole již v _části Zdroje_existuje . Původní tabulka je filtrována `where` `project` a následující obsahuje sloupce z obou tabulek. Výsledkem dotazu je jeden trezor klíčů zobrazující typ, název trezoru klíčů a název předplatného, ve které se nachází.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Při omezení `join` výsledků `project`s , `join` vlastnost používá ke propojení dvě tabulky, _subscriptionId_ ve `project`výše uvedeném příkladu, musí být zahrnuty v .

## <a name="supported-kql-language-elements"></a>Podporované prvky jazyka KQL

Resource Graph podporuje všechny [datové typy](/azure/kusto/query/scalar-data-types/)KQL , [skalární funkce](/azure/kusto/query/scalarfunctions), [skalární operátory](/azure/kusto/query/binoperators)a [agregační funkce](/azure/kusto/query/any-aggfunction). Konkrétní [tabulkové operátory](/azure/kusto/query/queries) jsou podporovány Resource Graph, z nichž některé mají různé chování.

### <a name="supported-tabulartop-level-operators"></a>Podporované operátory tabulkové/nejvyšší úrovně

Zde je seznam tabulkových operátorů KQL podporovaných resource graphem s konkrétními ukázkami:

|KQL |Ukázkový dotaz Grafu prostředků |Poznámky |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Počet trezorů klíčů](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Zobrazit odlišné hodnoty pro určitý alias](../samples/starter.md#distinct-alias-values) | |
|[Rozšířit](/azure/kusto/query/extendoperator) |[Počet virtuálních počítačů podle typu operačního systému](../samples/starter.md#count-os) | |
|[Připojit](/azure/kusto/query/joinoperator) |[Trezor klíčů s názvem předplatného](../samples/advanced.md#join) |Připojte se k podporovaným přípěmům: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limit 3 `join` v jednom dotazu. Vlastní strategie připojení, jako je například připojení k vysílání, nejsou povoleny. Lze použít v rámci jedné tabulky nebo mezi _prostředky_ a _ResourceContainers_ tabulky. |
|[Limit](/azure/kusto/query/limitoperator) |[Seznam všech veřejných IP adres](../samples/starter.md#list-publicip) |Synonymum`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Starší operátor, `mv-expand` použijte místo. _RowLimit_ max 400. Výchozí hodnota je 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Seznam Cosmos DB s konkrétními umístěními zápisu](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max 400. Výchozí hodnota je 128. |
|[Objednávky](/azure/kusto/query/orderoperator) |[Seznam zdrojů seřazených podle názvu](../samples/starter.md#list-resources) |Synonymum`sort` |
|[Projektu](/azure/kusto/query/projectoperator) |[Seznam zdrojů seřazených podle názvu](../samples/starter.md#list-resources) | |
|[projekt-pryč](/azure/kusto/query/projectawayoperator) |[Odebrání sloupců z výsledků](../samples/advanced.md#remove-column) | |
|[Řazení](/azure/kusto/query/sortoperator) |[Seznam zdrojů seřazených podle názvu](../samples/starter.md#list-resources) |Synonymum`order` |
|[Sumarizovat](/azure/kusto/query/summarizeoperator) |[Počet prostředků Azure](../samples/starter.md#count-resources) |Zjednodušená pouze první stránka |
|[vzít](/azure/kusto/query/takeoperator) |[Seznam všech veřejných IP adres](../samples/starter.md#list-publicip) |Synonymum`limit` |
|[Top](/azure/kusto/query/topoperator) |[Zobrazení prvních pěti virtuálních počítačů podle názvu a jejich typu operačního režimu](../samples/starter.md#show-sorted) | |
|[Unie](/azure/kusto/query/unionoperator) |[Sloučení výsledků ze dvou dotazů do jednoho výsledku](../samples/advanced.md#unionresults) |Jedna tabulka povolena: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _ColumnName_ \] _Table_. Limit 3 `union` nohy v jednom dotazu. Fuzzy rozlišení `union` stolků nohou není povoleno. Lze použít v rámci jedné tabulky nebo mezi _prostředky_ a _ResourceContainers_ tabulky. |
|[where](/azure/kusto/query/whereoperator) |[Zobrazit prostředky, které obsahují úložiště](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Řídicí znaky

Některé názvy vlastností, například ty, které obsahují `.` nebo `$`, musí být zabaleny nebo uvozeny v dotazu nebo název vlastnosti je interpretován nesprávně a neposkytuje očekávané výsledky.

- `.`- Zalomte název vlastnosti jako takový:`['propertyname.withaperiod']`
  
  Příklad dotazu, který zabalí vlastnost _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- Escape znak v názvu vlastnosti. Použitý řídicí znak závisí na prostředí Resource Graph je spuštěn z.

  - **Bash** - `\`

    Příklad dotazu, který unikne _ \$typu_ vlastnosti v bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Neunikejte `$` charakteru.

  - **Powershell** - ``` ` ```

    Příklad dotazu, který v PowerShellu unikne _ \$typu_ vlastnosti:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk, který se používá v [dotazech Starter](../samples/starter.md).
- Zobrazení pokročilých použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat zdroje](explore-resources.md).