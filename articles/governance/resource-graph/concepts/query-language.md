---
title: Principy dotazovacího jazyka
description: Popisuje tabulky grafů prostředků a dostupné Kusto datové typy, operátory a funkce použitelné pro Azure Resource Graph.
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: f6cb13814fe725ff0253a0a5bf0098f0080fa407
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633797"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Principy dotazovacího jazyka grafu prostředků Azure

Dotazovací jazyk pro graf prostředků Azure podporuje řadu operátorů a funkcí. Každá práce a provoz na základě [dotazovacího jazyka Kusto (KQL)](/azure/kusto/query/index). Pokud se chcete dozvědět o dotazovacím jazyku, který používá graf prostředků, začněte s [kurzem pro KQL](/azure/kusto/query/tutorial).

Tento článek se zabývá jazykovými součástmi, které podporuje graf prostředků:

- [Tabulky grafů prostředků](#resource-graph-tables)
- [Prvky vlastního jazyka grafu prostředků](#resource-graph-custom-language-elements)
- [Podporované prvky jazyka KQL](#supported-kql-language-elements)
- [Rozsah dotazu](#query-scope)
- [Řídicí znaky](#escape-characters)

## <a name="resource-graph-tables"></a>Tabulky grafů prostředků

Graf prostředků poskytuje několik tabulek pro data, která uchovává o Azure Resource Manager typech prostředků a jejich vlastnostech. Některé tabulky lze použít s `join` operátory nebo `union` k získání vlastností ze souvisejících typů prostředků. Tady je seznam tabulek dostupných v grafu prostředků:

|Tabulka grafu prostředků |Může se jednat o `join` jiné tabulky? |Description |
|---|---|---|
|Zdroje informací |Yes |Výchozí tabulka, pokud není v dotazu definována. Většina Správce prostředkůch typů prostředků a vlastností je tady. |
|ResourceContainers |Yes |Zahrnuje předplatné (ve verzi Preview- `Microsoft.Resources/subscriptions` ) a `Microsoft.Resources/subscriptions/resourcegroups` typy prostředků a data skupiny prostředků (). |
|AdvisorResources |Ano (Preview) |Zahrnuje prostředky _související_ s `Microsoft.Advisor` . |
|AlertsManagementResources |Ano (Preview) |Zahrnuje prostředky _související_ s `Microsoft.AlertsManagement` . |
|ExtendedLocationResources |No |Zahrnuje prostředky _související_ s `Microsoft.ExtendedLocation` . |
|GuestConfigurationResources |No |Zahrnuje prostředky _související_ s `Microsoft.GuestConfiguration` . |
|KubernetesConfigurationResources |No |Zahrnuje prostředky _související_ s `Microsoft.KubernetesConfiguration` . |
|MaintenanceResources |Částečně, připojte _se pouze k_ . (Preview) |Zahrnuje prostředky _související_ s `Microsoft.Maintenance` . |
|PatchAssessmentResources|No |Zahrnuje prostředky _týkající_ se hodnocení oprav pro Azure Virtual Machines. |
|PatchInstallationResources|No |Zahrnuje prostředky _týkající_ se instalace služby Azure Virtual Machines patch. |
|PolicyResources |No |Zahrnuje prostředky _související_ s `Microsoft.PolicyInsights` . (**Preview**)|
|RecoveryServicesResources |Částečně, připojte _se pouze k_ . (Preview) |Zahrnuje prostředky _související_ s `Microsoft.DataProtection` a `Microsoft.RecoveryServices` . |
|SecurityResources |Částečně, připojte _se pouze k_ . (Preview) |Zahrnuje prostředky _související_ s `Microsoft.Security` . |
|ServiceHealthResources |No |Zahrnuje prostředky _související_ s `Microsoft.ResourceHealth` . |
|WorkloadMonitorResources |No |Zahrnuje prostředky _související_ s `Microsoft.WorkloadMonitor` . |

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

Následující dotaz ukazuje složitější použití `join` . Nejprve dotaz používá `project` k získání polí z _prostředků_ pro typ prostředku trezory Azure Key Vault. Další krok používá `join` ke sloučení výsledků s _ResourceContainers_ , kde je typ předplatné _u_ vlastnosti, která je v první tabulce `project` a v připojené tabulce `project` . Při přejmenování pole se vyhnete `join` jeho přidání jako _název1_ , protože vlastnost už je z _prostředků_ projekci. Výsledkem dotazu je jeden Trezor klíčů, který zobrazuje typ, název, umístění a skupinu prostředků trezoru klíčů, společně s názvem předplatného, které je v.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| project name, type, location, subscriptionId, resourceGroup
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, location, resourceGroup, SubName
| limit 1
```

> [!NOTE]
> Při omezení `join` výsledků pomocí `project` vlastnosti, kterou používá `join` pro relaci obou tabulek, je nutné v rámci výše uvedeného příkladu použít vlastnost _SubscriptionId_ `project` .

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Rozšířené vlastnosti (Preview)

Jako funkce ve _verzi Preview_ mají některé typy prostředků v grafu zdrojů k dispozici další vlastnosti související s typem, které jsou dostupné pro dotaz nad vlastnostmi poskytovanými Azure Resource Manager. Tato sada hodnot známá jako _Rozšířené vlastnosti_ existuje v podporovaném typu prostředku v `properties.extended` . Chcete-li zjistit, které typy prostředků mají _Rozšířené vlastnosti_, použijte následující dotaz:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Příklad: získání počtu virtuálních počítačů pomocí `instanceView.powerState.code` :

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

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

Graf prostředků podporuje podmnožinu [datových typů](/azure/kusto/query/scalar-data-types/)KQL, [skalárních funkcí](/azure/kusto/query/scalarfunctions), [skalárních operátorů](/azure/kusto/query/binoperators)a [agregačních funkcí](/azure/kusto/query/any-aggfunction). Graf prostředků podporuje konkrétní [tabulkové operátory](/azure/kusto/query/queries) , některé z nich mají různé chování.

### <a name="supported-tabulartop-level-operators"></a>Podporované operátory tabulkové/nejvyšší úrovně

Tady je seznam KQL tabulkových operátorů podporovaných grafem prostředků s konkrétními ukázkami:

|KQL |Ukázkový dotaz grafu prostředků |Poznámky |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Počet trezorů klíčů](../samples/starter.md#count-keyvaults) | |
|[znak](/azure/kusto/query/distinctoperator) |[Zobrazit prostředky, které obsahují úložiště](../samples/starter.md#show-storage) | |
|[zvětšení](/azure/kusto/query/extendoperator) |[Počet virtuálních počítačů podle typu operačního systému](../samples/starter.md#count-os) | |
|[zúčastnit](/azure/kusto/query/joinoperator) |[Trezor klíčů s názvem předplatného](../samples/advanced.md#join) |Podporované charaktery spojení: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [LeftOuter](/azure/kusto/query/joinoperator#left-outer-join). Limit 3 `join` v jednom dotazu, z něhož může být Křížová tabulka `join` . Pokud je veškeré použití mezi tabulkami `join` mezi _prostředky_ a _ResourceContainers_, je povolená 3 mezitabulka `join` . Vlastní strategie spojení, jako je připojení všesměrového vysílání, nejsou povolené. Které tabulky mohou používat `join` , naleznete v tématu [tabulky grafů prostředků](#resource-graph-tables). |
|[limit](/azure/kusto/query/limitoperator) |[Seznam všech veřejných IP adres](../samples/starter.md#list-publicip) |Synonymum `take` . Nefunguje s [přeskočením](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Místo toho použijte operátor starší verze `mv-expand` . _RowLimit_ max. 400. Výchozí hodnota je 128. |
|[MV – rozbalit](/azure/kusto/query/mvexpandoperator) |[Seznam Cosmos DB s konkrétními umístěními pro zápis](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max. 400. Výchozí hodnota je 128. Limit 3 `mv-expand` v jednom dotazu.|
|[order](/azure/kusto/query/orderoperator) |[Vypsat prostředky seřazené podle názvu](../samples/starter.md#list-resources) |Synonymum `sort` |
|[projektem](/azure/kusto/query/projectoperator) |[Vypsat prostředky seřazené podle názvu](../samples/starter.md#list-resources) | |
|[projekt – pryč](/azure/kusto/query/projectawayoperator) |[Odebrat sloupce z výsledků](../samples/advanced.md#remove-column) | |
|[druhu](/azure/kusto/query/sortoperator) |[Vypsat prostředky seřazené podle názvu](../samples/starter.md#list-resources) |Synonymum `order` |
|[Souhrn](/azure/kusto/query/summarizeoperator) |[Počet prostředků Azure](../samples/starter.md#count-resources) |Jenom zjednodušená první stránka |
|[nezbytná](/azure/kusto/query/takeoperator) |[Seznam všech veřejných IP adres](../samples/starter.md#list-publicip) |Synonymum `limit` . Nefunguje s [přeskočením](./work-with-data.md#skipping-records). |
|[vrchol](/azure/kusto/query/topoperator) |[Zobrazit prvních pět virtuálních počítačů podle názvu a jejich typu operačního systému](../samples/starter.md#show-sorted) | |
|[sjednocovací](/azure/kusto/query/unionoperator) |[Kombinování výsledků ze dvou dotazů do jednoho výsledku](../samples/advanced.md#unionresults) |Povolena jedna tabulka: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _ColumnName_ \] _Table_. Omezení 3 `union` ramen v jednom dotazu. Přibližné rozlišení `union` tabulek nohy není povoleno. Dá se použít v jedné tabulce nebo mezi tabulkami _Resources_ a _ResourceContainers_ . |
|[kde:](/azure/kusto/query/whereoperator) |[Zobrazit prostředky, které obsahují úložiště](../samples/starter.md#show-storage) | |

`join` `mv-expand` V dotazu sady SDK jednoho prostředku pro vytváření grafů je výchozí limit 3 a 3 operátorů. Můžete požádat o zvýšení těchto limitů pro vašeho tenanta prostřednictvím **pomoci a podpory**.

Aby bylo možné podporovat možnosti portálu Open Query, má Průzkumník Azure Resource Graph větší globální limit než sada Resource Graph SDK.

## <a name="query-scope"></a>Rozsah dotazu

Rozsah předplatných, ze kterých jsou vráceny prostředky, závisí na metodě přístupu ke grafu zdrojů. Azure CLI a Azure PowerShell naplní seznam předplatných, která se mají zahrnout do žádosti na základě kontextu oprávněného uživatele. Seznam předplatných lze pro každou z nich ručně definovat pomocí parametrů předplatné a **předplatného** **v uvedeném** pořadí.
V REST API a všech ostatních sadách SDK musí být seznam předplatných, které mají být zahrnuté prostředky, explicitně definován jako součást požadavku.

Ve verzi **Preview** verze REST API `2020-04-01-preview` přidá vlastnost pro určení oboru dotazu do [skupiny pro správu](../../management-groups/overview.md). Toto rozhraní API ve verzi Preview také umožňuje volitelnou vlastnost Subscription. Pokud není definována skupina pro správu nebo seznam předplatných, obor dotazu je všechny prostředky, včetně delegovaných prostředků [Azure Lighthouse](../../../lighthouse/concepts/azure-delegated-resource-management.md) , ke kterým má ověřený uživatel přístup. Nová `managementGroupId` vlastnost převezme ID skupiny pro správu, které se liší od názvu skupiny pro správu. `managementGroupId`Je-li parametr zadán, jsou zde zahrnuty prostředky z prvních 5000 předplatných v rámci nebo pod určenou hierarchií skupin pro správu. `managementGroupId` nelze použít ve stejnou dobu jako `subscriptions` .

Příklad: dotazování všech prostředků v rámci hierarchie skupiny pro správu s názvem moje skupina pro správu s ID ' myMG '.

- Identifikátor URI v REST API

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Text požadavku

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Řídicí znaky

Některé názvy vlastností, jako jsou například ty, které obsahují `.` nebo `$` , musí být zabaleny nebo uvozeny v dotazu nebo musí být název vlastnosti interpretován nesprávně a neposkytují očekávané výsledky.

- `.` – Název vlastnosti zabalíte takto: `['propertyname.withaperiod']`
  
  Příklad dotazu, který zabalí vlastnost _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -Řídí znak v názvu vlastnosti. Použitý řídicí znak závisí na grafu prostředků prostředí, ze kterého se spouští.

  - **bash** - `\`

    Příklad dotazu, který řídí _\$ typ_ vlastnosti v bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – neřídí `$` znak.

  - **Prostředí** - ``` ` ```

    Příklad dotazu, který řídí _\$ typ_ vlastnosti v PowerShellu:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat prostředky](explore-resources.md).
