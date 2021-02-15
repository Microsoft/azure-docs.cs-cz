---
title: Použití šablon Správce prostředků v Data Factory
description: Naučte se vytvářet a používat šablony Azure Resource Manager k vytváření entit Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c17ef939b8d27035e5b28f4a651b9be2db8f2279
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382935"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Vytváření Azure Data Factory entit pomocí šablon
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

## <a name="overview"></a>Přehled
Při použití Azure Data Factory pro vaše potřeby integrace dat můžete použít stejný vzor v různých prostředích nebo implementaci stejné opakovaně úlohy ve stejném řešení. Šablony usnadňují implementaci a správu těchto scénářů. Šablony v Azure Data Factory jsou ideální pro scénáře, které zahrnují možnosti opětovné použitelnosti a opakování.

Vezměte v úvahu situaci, kdy má organizace 10 výrobních závodů po celém světě. Protokoly z každé továrny se ukládají do samostatné databáze SQL Server. Společnost chce vytvořit jeden datový sklad v cloudu pro ad hoc analýzu. Také chce mít stejnou logiku, ale různé konfigurace pro vývojové, testovací a produkční prostředí.

V takovém případě se úkol musí opakovat ve stejném prostředí, ale s různými hodnotami napříč 10 datovými továrnami pro každou výrobní továrnu. V důsledku toho je **opakování** k dispozici. Šablonování umožňuje abstrakci tohoto obecného toku (to znamená, že kanály mají stejné aktivity v každém objektu pro vytváření dat), ale pro každou výrobní závod používá samostatný soubor parametrů.

Vzhledem k tomu, že organizace chce nasadit tyto 10 datových továrn několikrát v různých prostředích, mohou šablony použít tuto reužitečnost **tím, že** využívá samostatné soubory parametrů pro vývoj, testování a produkční prostředí.

## <a name="templating-with-azure-resource-manager"></a>Šablonování s Azure Resource Manager
[Šablony Azure Resource Manager](../../azure-resource-manager/templates/overview.md) představují skvělý způsob, jak dosáhnout šablonování v Azure Data Factory. Správce prostředků šablony definují infrastrukturu a konfiguraci řešení Azure prostřednictvím souboru JSON. Vzhledem k tomu, že šablony Azure Resource Manager fungují se všemi/nejvíc službami Azure, je možné je široce využít ke snadné správě všech prostředků vašich assetů Azure. Další informace o šablonách Správce prostředků obecně najdete v tématu [vytváření Azure Resource Manager šablon](../../azure-resource-manager/templates/template-syntax.md) .

## <a name="tutorials"></a>Kurzy
Podrobné pokyny k vytváření Data Factory entit pomocí šablon Správce prostředků najdete v následujících kurzech:

* [Kurz: vytvoření kanálu pro kopírování dat pomocí šablony Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Kurz: vytvoření kanálu pro zpracování dat pomocí šablony Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory šablon na GitHubu
Podívejte se na následující šablony Azure pro rychlý Start na GitHubu:

* [Vytvoření datové továrny pro kopírování dat z Azure Blob Storage do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Vytvoření datové továrny s aktivitou podregistru v clusteru Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Vytvoření datové továrny pro kopírování dat z Salesforce do objektů blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Vytvoření datové továrny, která řetězí aktivity: kopíruje data ze serveru FTP do objektů blob Azure, vyvolá skript podregistru v clusteru HDInsight na vyžádání pro transformaci dat a výsledky kopírování do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Své šablony Azure Data Factory můžete sdílet v [rychlém startu Azure](https://azure.microsoft.com/documentation/templates/). Přečtěte si [příručku k příspěvku](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) při vývoji šablon, které se dají sdílet přes toto úložiště.

Následující části obsahují podrobné informace o definování Data Factorych prostředků v šabloně Správce prostředků.

## <a name="defining-data-factory-resources-in-templates"></a>Definování prostředků Data Factory v šablonách
Šablona nejvyšší úrovně pro definování objektu pro vytváření dat je:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
Třída DataFactory je definována v "Variables" jako:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definování propojených služeb

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Podrobnosti o vlastnostech JSON pro konkrétní propojenou službu, kterou chcete nasadit, najdete v tématu [propojená služba úložiště](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [propojené služby COMPUTE](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Parametr "dependsOn" Určuje název odpovídající datové továrny. V následující definici JSON se zobrazuje příklad definování propojené služby pro Azure Storage:

### <a name="define-datasets"></a>Definování datových sad

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
Podrobnosti o vlastnostech JSON pro konkrétní typ datové sady, který chcete nasadit, najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Všimněte si, že parametr "dependsOn" Určuje název odpovídající datové továrny a propojené služby úložiště. Příklad definujícího typu DataSet úložiště objektů BLOB v Azure je uvedený v následující definici JSON:

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

Podrobnosti o vlastnostech JSON pro definování konkrétního kanálu a aktivit, které chcete nasadit, najdete v tématu [definice kanálů](data-factory-create-pipelines.md#pipeline-json) . Všimněte si, že parametr "dependsOn" Určuje název objektu pro vytváření dat a všechny odpovídající propojené služby nebo datové sady. Příklad kanálu, který kopíruje data z Azure Blob Storage do Azure SQL Database je zobrazený v následujícím fragmentu kódu JSON:

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
## <a name="parameterizing-data-factory-template"></a>Šablona Data Factory Parametrizace
Osvědčené postupy pro Parametrizace najdete v tématu [osvědčené postupy pro vytváření šablon Azure Resource Manager](../../azure-resource-manager/templates/template-best-practices.md). Obecně platí, že použití parametrů by mělo být minimalizováno, zejména pokud lze použít proměnné. Zadejte pouze parametry v následujících scénářích:

* Nastavení se liší podle prostředí (příklad: vývoj, testování a produkce).
* Tajné kódy (například hesla)

Pokud potřebujete načíst tajné kódy z [Azure Key Vault](../../key-vault/general/overview.md) při nasazování Azure Data Factory entit pomocí šablon, zadejte **Trezor klíčů** a **název tajného** kódu, jak je znázorněno v následujícím příkladu:

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
> Při exportování šablon pro existující datové továrny se v současnosti zatím nepodporují, ale funguje.
>
>