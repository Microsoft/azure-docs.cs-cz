---
title: Dotazování exportovaných dat z Azure Monitor pomocí Azure Průzkumník dat (Preview)
description: Použijte Azure Průzkumník dat k dotazování na data, která byla exportována z pracovního prostoru Log Analytics do účtu služby Azure Storage.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 5eff593075db118b23d74147e33b40eb4402193c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031153"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Dotazování exportovaných dat z Azure Monitor pomocí Azure Průzkumník dat (Preview)
Export dat z Azure Monitor do účtu úložiště Azure umožňuje snížit náklady a možnost znovu přidělit protokoly různým oblastem. Použijte Azure Průzkumník dat k dotazování na data, která byla exportována z vašich Log Analytics pracovních prostorů. Po nakonfigurování budou podporované tabulky, které se odesílají z vašich pracovních prostorů do účtu služby Azure Storage, k dispozici jako zdroj dat pro Azure Průzkumník dat.

Průběh procesu je následující: 

1.  Exportujte data z pracovního prostoru Log Analytics do účtu služby Azure Storage.
2.  Vytvořte v clusteru Azure Průzkumník dat externí tabulku a proveďte mapování datových typů.
3.  Dotazování dat z Azure Průzkumník dat.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Tok dotazování exportovaných dat v Azure Průzkumník dat.":::



## <a name="send-data-to-azure-storage"></a>Odesílání dat do služby Azure Storage
Protokoly Azure Monitor lze exportovat do Azure Storage účtu pomocí kterékoli z následujících možností.

- Pokud chcete exportovat všechna data z vašeho pracovního prostoru Log Analytics do účtu služby Azure Storage nebo centra událostí, použijte funkci exportu dat Log Analytics pracovního prostoru Azure Monitor protokolů. Viz [Log Analytics exportu dat v pracovním prostoru v Azure monitor (Preview)](./logs-data-export.md)
- Plánovaný export z dotazu protokolu pomocí aplikace logiky To se podobá funkci exportu dat, ale umožňuje odeslat filtrovaná nebo agregovaná data do služby Azure Storage. Tato metoda je sice v souladu s [omezeními dotazů protokolu](../service-limits.md#log-analytics-workspaces)  , viz [data archivu z pracovního prostoru Log Analytics do úložiště Azure pomocí aplikace logiky](./logs-export-logic-app.md).
- Jednorázové export pomocí aplikace logiky [Logic Apps a automatické automatizaci najdete v tématu konektory protokolů Azure monitor](./logicapp-flow-connector.md).
- Jednou při exportu do místního počítače pomocí skriptu PowerShellu. Viz [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> Můžete použít existující cluster Azure Průzkumník dat nebo vytvořit nový vyhrazený cluster s potřebnými konfiguracemi.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Vytvoření externí tabulky umístěné v úložišti objektů BLOB v Azure
Pomocí [externích tabulek](/azure/data-explorer/kusto/query/schema-entities/externaltables) můžete propojit službu Azure Průzkumník dat s účtem služby Azure Storage. Externí tabulka je entita schématu Kusto, která odkazuje na data uložená mimo databázi Kusto. Podobně jako u tabulek má externí tabulka dobře definované schéma. Na rozdíl od tabulek se data ukládají a spravují mimo cluster Kusto. Exportovaná data z předchozí části jsou uložena v řádcích JSON.

Chcete-li vytvořit odkaz, budete potřebovat schéma exportované tabulky. Použijte operátor [GetSchema](/azure/data-explorer/kusto/query/getschemaoperator) z Log Analytics k načtení těchto informací, které obsahují sloupce tabulky a jejich datové typy.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics schéma tabulky.":::

Pomocí výstupu teď můžete vytvořit dotaz Kusto pro vytvoření externí tabulky.
Podle pokynů v části [Vytvoření a změna externích tabulek v Azure Storage nebo Azure Data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)vytvořte externí tabulku ve formátu JSON a spusťte dotaz z databáze Azure Průzkumník dat.

>[!NOTE]
>Vytvoření externí tabulky je vytvořeno ze dvou procesů. První vytvoří externí tabulku, zatímco druhá vytváří mapování.

Následující skript prostředí PowerShell vytvoří příkazy [Create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) pro tabulku a mapování.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

Následující obrázek ukazuje příklad výstupu.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="Výstup příkazu extern Create":::

[![Příklad výstupu](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Zkopírujte, vložte a potom spusťte výstup skriptu v nástroji Azure Průzkumník dat Client a vytvořte tabulku a mapování.
>* Chcete-li použít všechna data uvnitř kontejneru, upravte skript a změňte adresu URL na hodnotu " https://your.blob.core.windows.net/containername ; SecKey'

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Dotazování exportovaných dat z Azure Průzkumník dat 

Po konfiguraci mapování můžete zadat dotaz na exportovaná data z Azure Průzkumník dat. Dotaz vyžaduje funkci [external_table](/azure/data-explorer/kusto/query/externaltablefunction) , jako v následujícím příkladu.

```kusto
external_table("HBTest","map") | take 10000
```

[![Dotazování Log Analytics exportovaných dat](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Naučte se [zapisovat dotazy do Azure Průzkumník dat](/azure/data-explorer/write-queries)