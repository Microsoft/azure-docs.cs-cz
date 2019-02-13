---
title: Použití šablon Resource Manageru ve službě Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a vytvořit entity služby Data Factory pomocí šablony Azure Resource Manageru.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 9fb63ae141665dbeb64ee7046427098d4482aa55
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111318"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Použijte šablony k vytvoření entity služby Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

## <a name="overview"></a>Přehled
Při používání služby Azure Data Factory pro vaše potřeby integrace dat, může být pro vás sami opakovaného použití stejného vzoru napříč různými prostředími nebo implementaci stejnou úlohu opakovaně ve stejném řešení. Šablony vám pomohou implementovat a spravovat tyto scénáře snadno způsobem. Šablony ve službě Azure Data Factory jsou ideální pro scénáře, které zahrnují možnost opakovaného využití a opakování.

Vezměte v úvahu situace, kdy organizace má 10 výrobních závodech proto po celém světě. Protokoly z každého zařízení jsou uloženy v databázi serveru SQL Server samostatný lokální. Společnost chce, aby se k vytvoření jednoho datového skladu v cloudu pro ad-hoc analytics. Také chce mít stejnou logiku, ale různé konfigurace pro vývoj, testování a produkční prostředí.

V tomto případě úkol musí mezi 10 datových továren u každého z výrobních závodů opakovat v rámci stejného prostředí, ale s různými hodnotami. V důsledku toho **opakování** je k dispozici. Šablonování umožňuje abstrakce tento obecný tok (to znamená, kanálů, které mají stejné aktivity v každé služby data factory), ale pro každou výrobních závodů, používá soubor parametrů samostatné.

Kromě toho, jak organizace potřebuje k nasazení těchto 10 datových továren vícekrát v různých prostředích, šablony může být využit **opětovné použití** s využitím samostatných souborů parametrů pro vývoj, testování, a produkční prostředí.

## <a name="templating-with-azure-resource-manager"></a>Šablon s Azure Resource Manageru
[Šablony Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md#template-deployment) jsou skvělý způsob, jak dosáhnout šablon ve službě Azure Data Factory. Šablony Resource Manageru definovat infrastrukturu a konfiguraci vašeho řešení Azure prostřednictvím souboru JSON. Protože šablon Azure Resource Manageru pracovat se službami Azure všechny nebo většina, je široce umožňuje snadno spravovat všechny prostředky z vašich prostředků Azure. Zobrazit [šablon pro vytváření Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md) Další informace o šablonách Resource Manageru obecně.

## <a name="tutorials"></a>Kurzy
Najdete v následujících kurzech najdete podrobné pokyny k vytvoření entity služby Data Factory pomocí šablony Resource Manageru:

* [Kurz: Vytvoření kanálu pro kopírování dat pomocí šablony Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Kurz: Vytvoření kanálu pro zpracování dat pomocí šablony Azure Resource Manageru](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory šablony na Githubu
Projděte si následující šablony rychlý start Azure na Githubu:

* [Vytvoření služby Data factory pro kopírování dat z Azure Blob Storage do služby Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Vytvoření datové továrny pomocí aktivity Hivu v clusteru Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Vytvoření datové továrny ke kopírování dat ze Salesforce do objektů BLOB Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Vytvoření datové továrny, který je zřetězený aktivity: kopíruje data ze serveru FTP do služeb Azure Blobs, spustí skript hive v clusteru HDInsight na vyžádání pro transformaci dat a zkopíruje výsledek do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Nebojte se sdílet své šablony služby Azure Data Factory na [Azure rychlý start](https://azure.microsoft.com/documentation/templates/). Odkazovat [průvodci pro přispěvatele](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) při vývoji šablon, které je možné sdílet prostřednictvím tohoto úložiště.

Následující části obsahují podrobné informace o definování prostředky Data Factory v šabloně Resource Manageru.

## <a name="defining-data-factory-resources-in-templates"></a>Definování prostředky Data Factory v šablonách
Nejvyšší úrovně šablonu pro definování datové továrny je:

```JSON
"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definování datové továrny
Datovou továrnu definujete v šabloně Resource Manageru, jak je znázorněno v následující ukázce:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
Hodnota dataFactoryName je definována v "proměnné" takto:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definování propojené služby

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Zobrazit [propojená služba Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [propojené výpočetní služby](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) podrobnosti o vlastnostech JSON pro konkrétní propojenou službu, kterou chcete nasadit. Parametr "dependsOn" Určuje název odpovídající datové továrny. Příklad definování propojené služby pro Azure Storage je uveden v následující definici JSON:

### <a name="define-datasets"></a>Definování datové sady

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Odkazovat na [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) podrobnosti o vlastnostech JSON pro konkrétní datové sady typu chcete nasadit. Všimněte si, že parametr "dependsOn" Určuje název odpovídající objekt pro vytváření dat a úložiště propojenou službu. Příklad definuje typ datové sady Azure blob Storage je uveden v následující definici JSON:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definovat kanály

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Odkazovat na [definování kanálů](data-factory-create-pipelines.md#pipeline-json) podrobnosti o vlastnostech JSON pro definování konkrétního kanálu a aktivity, které chcete nasadit. Poznámka: parametr "dependsOn" Určuje název objektu pro vytváření dat a odpovídající propojené služby a datové sady. V následujícím fragmentu kódu JSON je uveden příklad kanálu, který kopíruje data z Azure Blob Storage do služby Azure SQL Database:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Parametrizace šablony služby Data Factory
Osvědčené postupy na Parametrizace, naleznete v tématu [osvědčené postupy pro vytváření šablon Azure Resource Manageru](../../azure-resource-manager/resource-manager-template-best-practices.md). Obecně platí použití parametrů byste měli minimalizovat, zejména v případě, že proměnné lze použít místo toho. Pouze zadejte parametry v těchto scénářích:

* Nastavení se liší podle prostředí (Příklad: vývoj, testování a produkce)
* Tajné kódy (například hesla)

Pokud budete potřebovat k vyžádání tajné kódy z [Azure Key Vault](../../key-vault/key-vault-overview.md) při nasazování entit služby Azure Data Factory pomocí šablony, zadejte **služby key vault** a **název tajného kódu** jak je znázorněno Následující příklad:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Při exportu šablony pro existující datové továrny není aktuálně podporován, je v prací.
>
>
