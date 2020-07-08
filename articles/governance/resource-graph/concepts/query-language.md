---
title: Principy dotazovacího jazyka
description: Popisuje tabulky grafů prostředků a dostupné Kusto datové typy, operátory a funkce použitelné pro Azure Resource Graph.
ms.date: 06/29/2020
ms.topic: conceptual
ms.openlocfilehash: 4c545a8a5113f800545660a3ea812b61711630c2
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970446"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy dotazovacího jazyka grafu prostředků Azure

Dotazovací jazyk pro graf prostředků Azure podporuje řadu operátorů a funkcí. Každá práce a provoz na základě [dotazovacího jazyka Kusto (KQL)](/azure/kusto/query/index). Pokud se chcete dozvědět o dotazovacím jazyku, který používá graf prostředků, začněte s [kurzem pro KQL](/azure/kusto/query/tutorial).

Tento článek se zabývá jazykovými součástmi, které podporuje graf prostředků:

- [Tabulky grafů prostředků](#resource-graph-tables)
- [Prvky vlastního jazyka grafu prostředků](#resource-graph-custom-language-elements)
- [Podporované prvky jazyka KQL](#supported-kql-language-elements)
- [Řídicí znaky](#escape-characters)

## <a name="resource-graph-tables"></a>Tabulky grafů prostředků

Graf prostředků poskytuje několik tabulek pro data, která uchovává o Azure Resource Manager typech prostředků a jejich vlastnostech. Tyto tabulky lze použít s `join` operátory nebo `union` k získání vlastností ze souvisejících typů prostředků. Tady je seznam tabulek dostupných v grafu prostředků:

|Tabulky grafů prostředků |Description |
|---|---|
|Prostředky |Výchozí tabulka, pokud není v dotazu definována. Většina Správce prostředkůch typů prostředků a vlastností je tady. |
|ResourceContainers |Zahrnuje předplatné (ve verzi Preview- `Microsoft.Resources/subscriptions` ) a `Microsoft.Resources/subscriptions/resourcegroups` typy prostředků a data skupiny prostředků (). |
|AdvisorResources |Zahrnuje prostředky _související_ s `Microsoft.Advisor` . |
|AlertsManagementResources |Zahrnuje prostředky _související_ s `Microsoft.AlertsManagement` . |
|HealthResources |Zahrnuje prostředky _související_ s `Microsoft.ResourceHealth` . |
|MaintenanceResources |Zahrnuje prostředky _související_ s `Microsoft.Maintenance` . |
|SecurityResources |Zahrnuje prostředky _související_ s `Microsoft.Security` . |

Úplný seznam včetně typů prostředků najdete v tématu [referenční informace: podporované tabulky a typy prostředků](../reference/supported-tables-resources.md).

> [!NOTE]
> _Prostředky_ jsou výchozí tabulkou. Při dotazování tabulky _Resources_ není nutné zadávat název tabulky, pokud `join` ani `union` nejsou použity. Doporučený postup je ale vždy zahrnout počáteční tabulku do dotazu.

Pomocí Průzkumníka grafů prostředků na portálu můžete zjistit, jaké typy prostředků jsou v každé tabulce k dispozici. Jako alternativu použijte dotaz, jako je například `<tableName> | distinct type` , abyste získali seznam typů prostředků, které podporuje daná tabulka grafu prostředků ve vašem prostředí.

Následující dotaz ukazuje jednoduché `join` . Výsledek dotazu smíchá sloupce dohromady a všechny duplicitní názvy sloupců z Spojené tabulky, _ResourceContainers_ v tomto příkladu, jsou připojeny s **1**. Jelikož tabulka _ResourceContainers_ má typy pro předplatné i skupiny prostředků, může být použit buď typ pro připojení k tabulce prostředků z tabulky _prostředků_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Následující dotaz ukazuje složitější použití `join` . Dotaz omezí propojenou tabulku na prostředky předplatného a s tím, že `project` zahrne pouze původní pole _SubscriptionId_ a pole _název_ bylo přejmenováno na _subname_. Přejmenování pole zabraňuje `join` jeho přidání jako _název1_ , protože pole již v _prostředcích_existuje. Původní tabulka je filtrována pomocí `where` a následující `project` obsahuje sloupce z obou tabulek. Výsledkem dotazu je jeden Trezor klíčů, který zobrazuje typ, název trezoru klíčů a název předplatného, ve kterém je.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Při omezení `join` výsledků pomocí `project` vlastnosti, kterou používá `join` pro relaci obou tabulek, je nutné v rámci výše uvedeného příkladu použít vlastnost _SubscriptionId_ `project` .

## <a name="resource-graph-custom-language-elements"></a>Prvky vlastního jazyka grafu prostředků

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Syntaxe sdíleného dotazu (Preview)

Ve verzi Preview se ke [sdílenému dotazu](../tutorials/create-share-query.md) dá získat přímý dotaz přímo v dotazu grafu prostředků. V tomto scénáři je možné vytvářet standardní dotazy jako sdílené dotazy a znovu je použít. Chcete-li volat sdílený dotaz v dotazu grafu prostředku, použijte `{{shared-query-uri}}` syntaxi. Identifikátor URI sdíleného dotazu je _ID prostředku_ pro sdílený dotaz na stránce **Nastavení** daného dotazu. V tomto příkladu je náš identifikátor URI sdíleného dotazu `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` .
Tento identifikátor URI odkazuje na předplatné, skupinu prostředků a celé jméno sdíleného dotazu, na který chcete odkazovat v jiném dotazu. Tento dotaz je stejný jako ten, který jste vytvořili v [kurzu: vytvoření a sdílení dotazu](../tutorials/create-share-query.md).

> [!NOTE]
> Dotaz, který odkazuje na sdílený dotaz, nelze uložit jako sdílený dotaz.

Příklad 1: použití pouze sdíleného dotazu

Výsledky dotazu tohoto grafu prostředku jsou stejné jako dotaz uložený ve sdíleném dotazu.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Příklad 2: zahrnutí sdíleného dotazu jako části většího dotazu

Tento dotaz nejprve používá sdílený dotaz a následně používá `limit` k dalšímu omezení výsledků.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Podporované prvky jazyka KQL

Graf prostředků podporuje všechny KQL [datové typy](/azure/kusto/query/scalar-data-types/), [skalární funkce](/azure/kusto/query/scalarfunctions), [skalární operátory](/azure/kusto/query/binoperators)a [agregační funkce](/azure/kusto/query/any-aggfunction). Graf prostředků podporuje konkrétní [tabulkové operátory](/azure/kusto/query/queries) , některé z nich mají různé chování.

### <a name="supported-tabulartop-level-operators"></a>Podporované operátory tabulkové/nejvyšší úrovně

Tady je seznam KQL tabulkových operátorů podporovaných grafem prostředků s konkrétními ukázkami:

|KQL |Ukázkový dotaz grafu prostředků |Poznámky |
|---|---|---|
|[výpočtu](/azure/kusto/query/countoperator) |[Počet trezorů klíčů](../samples/starter.md#count-keyvaults) | |
|[znak](/azure/kusto/query/distinctoperator) |[Zobrazit odlišné hodnoty pro konkrétní alias](../samples/starter.md#distinct-alias-values) | |
|[zvětšení](/azure/kusto/query/extendoperator) |[Počet virtuálních počítačů podle typu operačního systému](../samples/starter.md#count-os) | |
|[zúčastnit](/azure/kusto/query/joinoperator) |[Trezor klíčů s názvem předplatného](../samples/advanced.md#join) |Podporované charaktery spojení: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [LeftOuter](/azure/kusto/query/joinoperator#left-outer-join). Limit 3 `join` v jednom dotazu. Vlastní strategie spojení, jako je připojení všesměrového vysílání, nejsou povolené. Dá se použít v jedné tabulce nebo mezi tabulkami _Resources_ a _ResourceContainers_ . |
|[počtu](/azure/kusto/query/limitoperator) |[Seznam všech veřejných IP adres](../samples/starter.md#list-publicip) |Synonymum`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Místo toho použijte operátor starší verze `mv-expand` . _RowLimit_ max. 400. Výchozí hodnota je 128. |
|[MV – rozbalit](/azure/kusto/query/mvexpandoperator) |[Seznam Cosmos DB s konkrétními umístěními pro zápis](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max. 400. Výchozí hodnota je 128. |
|[za](/azure/kusto/query/orderoperator) |[Vypsat prostředky seřazené podle názvu](../samples/starter.md#list-resources) |Synonymum`sort` |
|[projektem](/azure/kusto/query/projectoperator) |[Vypsat prostředky seřazené podle názvu](../samples/starter.md#list-resources) | |
|[projekt – pryč](/azure/kusto/query/projectawayoperator) |[Odebrat sloupce z výsledků](../samples/advanced.md#remove-column) | |
|[druhu](/azure/kusto/query/sortoperator) |[Vypsat prostředky seřazené podle názvu](../samples/starter.md#list-resources) |Synonymum`order` |
|[Souhrn](/azure/kusto/query/summarizeoperator) |[Počet prostředků Azure](../samples/starter.md#count-resources) |Jenom zjednodušená první stránka |
|[nezbytná](/azure/kusto/query/takeoperator) |[Seznam všech veřejných IP adres](../samples/starter.md#list-publicip) |Synonymum`limit` |
|[vrchol](/azure/kusto/query/topoperator) |[Zobrazit prvních pět virtuálních počítačů podle názvu a jejich typu operačního systému](../samples/starter.md#show-sorted) | |
|[sjednocovací](/azure/kusto/query/unionoperator) |[Kombinování výsledků ze dvou dotazů do jednoho výsledku](../samples/advanced.md#unionresults) |Povolena jedna tabulka: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _ColumnName_ \] _Table_. Omezení 3 `union` ramen v jednom dotazu. Přibližné rozlišení `union` tabulek nohy není povoleno. Dá se použít v jedné tabulce nebo mezi tabulkami _Resources_ a _ResourceContainers_ . |
|[,](/azure/kusto/query/whereoperator) |[Zobrazit prostředky, které obsahují úložiště](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Řídicí znaky

Některé názvy vlastností, jako jsou například ty, které obsahují `.` nebo `$` , musí být zabaleny nebo uvozeny v dotazu nebo musí být název vlastnosti interpretován nesprávně a neposkytují očekávané výsledky.

- `.`– Název vlastnosti zabalíte takto:`['propertyname.withaperiod']`
  
  Příklad dotazu, který zabalí vlastnost _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Řídí znak v názvu vlastnosti. Použitý řídicí znak závisí na grafu prostředků prostředí, ze kterého se spouští.

  - **bash** - `\`

    Příklad dotazu, který řídí _ \$ typ_ vlastnosti v bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – neřídí `$` znak.

  - **Prostředí** - ``` ` ```

    Příklad dotazu, který řídí _ \$ typ_ vlastnosti v PowerShellu:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat prostředky](explore-resources.md).