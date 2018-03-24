---
title: Pomocí Správce prostředků šablony v datové továrně | Microsoft Docs
description: Informace o vytváření a používání šablon Azure Resource Manageru k vytvoření entit služby Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 71aa5b6bdf23e4e072247432184683f9985586b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Pomocí šablony můžete vytvořit entit služby Azure Data Factory
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). 

## <a name="overview"></a>Přehled
Při použití Azure Data Factory pro integraci vašim datovým potřebám sami zjistíte opakovaného použití stejného vzoru v různých prostředích nebo implementace pro stejnou úlohu opakovaně v rámci stejného řešení. Šablony vám pomohou implementovat a spravovat tyto scénáře snadno způsobem. Šablony v Azure Data Factory jsou ideální pro scénáře, které zahrnují – opětovné použití a opakování.

Vezměte v úvahu situaci, kde může organizace má 10 výrobních závodech po celém světě. Protokoly z každého zařízení jsou uloženy v databázi systému SQL Server samostatný místní. Společnost chce sestavení jednoho datového skladu v cloudu pro ad-hoc analýzu. Také chce mít stejné logiky, která ale různé konfigurace pro vývoj, testování a provozním prostředí.

V takovém případě musí opakovat ve stejném prostředí, ale s různými hodnotami mezi 10 datové továrny pro každého z výrobních závodů úlohu. Ve skutečnosti **opakování** je k dispozici. Ukázka umožňuje abstrakce tento obecný tok (to znamená, že stejné aktivity v každé služby data factory kanály), ale používá soubor samostatného parametru pro každého z výrobních závodů.

Kromě toho, jak organizace chce nasadit tyto 10 datové továrny vícekrát různých prostředích, šablony můžete použít **– opětovné použití** s využitím soubory samostatného parametru pro vývoj, testování a provozním prostředí.

## <a name="templating-with-azure-resource-manager"></a>Ukázka s Azure Resource Manager
[Šablony Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md#template-deployment) jsou skvělý způsob, jak dosáhnout ukázka v Azure Data Factory. Šablony Resource Manageru definovat infrastrukturu a konfiguraci řešení Azure prostřednictvím soubor JSON. Protože šablon Azure Resource Manageru pracovat se službami Azure všechny nebo většinu, je široce umožňuje snadno spravovat všechny prostředky vaše prostředky Azure. V tématu [šablon pro tvorbu Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md) Další informace o šablonách Resource Manager obecně.

## <a name="tutorials"></a>Kurzy
Najdete v následujících kurzech podrobné pokyny k vytvoření entit služby Data Factory pomocí šablony Resource Manageru:

* [Kurz: Vytvoření kanálu ke zkopírování dat pomocí šablony Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Kurz: Vytvoření kanálu pro zpracování dat pomocí šablony Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory šablony na Githubu
Podívejte se na následující šablony Azure rychlý start na Githubu:

* [Vytvoření Data factory ke zkopírování dat z Azure Blob Storage do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Vytvoření služby Data factory s aktivitou Hive v clusteru Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Vytvoření Data factory ke zkopírování dat ze služby Salesforce do objektů BLOB služby Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Vytvoření objektu pro vytváření dat, který je zřetězený aktivity: kopíruje data ze serveru FTP do objektů BLOB služby Azure, vyvolá skriptu hive v clusteru HDInsight na vyžádání pro transformaci dat a zkopíruje výsledek do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Nebojte se sdílet své šablony Azure Data Factory v [Azure rychlý start](https://azure.microsoft.com/documentation/templates/). Odkazovat [příspěvku průvodce](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) při vývoji šablony, které je možné sdílet přes toto úložiště.

Následující části obsahují podrobnosti o definice prostředků pro vytváření dat v šabloně Resource Manager.

## <a name="defining-data-factory-resources-in-templates"></a>Definice prostředků pro vytváření dat v šablonách
Je nejvyšší úrovně šablonu pro definování objekt pro vytváření dat:

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
DataFactoryName je definováno v "proměnné" jako:

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

V tématu [propojená služba úložiště](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [propojené výpočetní služby](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) podrobnosti o vlastnostech JSON pro konkrétní propojené služby, které chcete nasadit. Parametr "dependsOn" Určuje název objektu pro vytváření dat odpovídající. V následující definici JSON je znázorněn příklad definování propojené služby pro Azure Storage:

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
Odkazovat na [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) podrobné informace o vlastnostech JSON pro typ konkrétní datové sady, které chcete nasadit. Všimněte si, že parametr "dependsOn" Určuje název odpovídající objekt pro vytváření dat a úložiště propojená služba. V následující definici JSON je uveden příklad definování typu datovou sadu Azure blob Storage:

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

Odkazovat na [definování kanály](data-factory-create-pipelines.md#pipeline-json) podrobné informace o vlastnostech JSON pro definování konkrétní kanálu a aktivity, které chcete nasadit. Poznámka: parametr "dependsOn" Určuje název objektu pro vytváření dat a všechny odpovídající propojené služby nebo datové sady. Příklad kanálu, který kopíruje data z Azure Blob Storage do Azure SQL Database je uveden v následujícím fragmentu kódu JSON:

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
Osvědčené postupy v Parametrizace, najdete v části [osvědčené postupy pro vytváření šablon Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md). Obecně platí, použití parametrů by měl být minimalizován, zejména v případě, že proměnné lze použít místo. Jenom zadejte parametry v následujících scénářích:

* Nastavení se liší podle prostředí (Příklad: vývoj, testování a provozním)
* Tajné klíče (jako jsou hesla)

Pokud potřebujete vyžádat tajné klíče z [Azure Key Vault](../../key-vault/key-vault-get-started.md) při nasazení entit služby Azure Data Factory pomocí šablony, zadejte **trezoru klíčů** a **tajný název** jak je znázorněno v následujícím příkladu:

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
> Při exportu šablony pro existující datové továrny není aktuálně ještě podporovaná, je ve službě funguje.
>
>
