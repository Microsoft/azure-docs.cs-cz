---
title: "Kurz: Vytvoření kanálu pomocí šablony Resource Manageru | Dokumentace Microsoftu"
description: "V tomto kurzu vytvoříte kanál služby Azure Data Factory pomocí šablony Azure Resource Manageru. Tento kanál kopíruje data ze služby Azure Blob Storage do služby Azure SQL Database."
services: data-factory
documentationcenter: 
author: linda33wj
manager: 
editor: 
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6d4006389fffe999ce83d3c2535505869ecc0519
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>Kurz: Použití šablony Azure Resource Manageru k vytvoření kanálu Data Factory pro kopírování dat 
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verzi 2 služby Data Factory, který je ve verzi Preview, přečtěte si [kurz aktivity kopírování v dokumentaci verze 2](../quickstart-create-data-factory-dot-net.md). 

V tomto kurzu je uvedeno, jak použít šablonu Azure Resource Manageru k vytvoření datové továrny Azure. Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Neprovádí transformaci vstupních dat, aby vytvořil výstupní data. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

V tomto kurzu vytvoříte kanál s jednou aktivitou: aktivita kopírování. Aktivita kopírování kopíruje data z podporovaného úložiště dat do podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktivita používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md).

Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace naleznete, když přejdete na [více aktivit v kanálu](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="prerequisites"></a>Požadavky
* Projděte si [Přehled a požadavky kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) a proveďte **nutné** kroky.
* Podle pokynů v článku [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) si na počítač nainstalujte nejnovější verzi prostředí Azure PowerShell. V tomto kurzu použijete prostředí PowerShell k nasazení entit služby Data Factory. 
* (volitelné) Informace o šablonách Azure Resource Manageru najdete v tématu [Vytváření šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="in-this-tutorial"></a>V tomto kurzu
V tomto kurzu vytvoříte datovou továrnu s následujícími entitami služby Data Factory:

| Entita | Popis |
| --- | --- |
| Propojená služba Azure Storage |Propojí účet služby Azure Storage s datovou továrnou. V tomto kurzu je služba Azure Storage zdrojové úložiště dat a služba Azure SQL Database je úložiště dat jímky pro aktivitu kopírování. Určuje účet úložiště, který obsahuje vstupní data pro aktivitu kopírování. |
| Propojená služba Azure SQL Database |Propojí službu Azure SQL Database s datovou továrnou. Určuje službu Azure SQL Database, která uchovává výstupní data pro aktivitu kopírování. |
| Vstupní datová sada Azure Blob |Odkazuje na propojenou službu Azure Storage. Propojená služba odkazuje na účet služby Azure Storage a datová sada Azure Blob určuje kontejner, složku a název souboru v úložišti, který obsahuje vstupní data. |
| Výstupní datová sada Azure SQL |Odkazuje na propojenou službu Azure SQL. Propojená služba Azure SQL odkazuje na server Azure SQL a datová sada Azure SQL určuje název tabulky, která obsahuje výstupní data. |
| Data Pipeline |Kanál má jednu aktivitu typu Kopírování, která přijímá datovou sadu Azure Blob jako vstup a datovou sadu Azure SQL jako výstup. Aktivita kopírování kopíruje data z Azure Blob do tabulky ve službě Azure SQL Database. |

Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Existují dva typy aktivit: [aktivity přesunu dat](data-factory-data-movement-activities.md) a [transformace dat](data-factory-data-transformation-activities.md). V tomto kurzu vytvoříte kanál s jednou aktivitou (aktivita kopírování).

![Kopírování Azure Blob do služby Azure SQL Database](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

Následující oddíl poskytuje hotovou šablonu Resource Manageru pro definování entit služby Data Factory, abyste mohli rychle projít kurzem a otestovat šablonu. Pro lepší pochopení toho, jak jsou jednotlivé entity služby Data Factory definovány, přejděte k oddílu [Entity služby Data Factory v šabloně](#data-factory-entities-in-the-template).

## <a name="data-factory-json-template"></a>Šablona JSON služby Data Factory
Šablona Resource Manageru nejvyšší úrovně pro definování datové továrny je: 

```json
{
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
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Ve složce **C:\ADFGetStarted** vytvořte soubor JSON s názvem **ADFCopyTutorialARM.json** s následujícím obsahem:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureStorage",
              "description": "Azure Storage linked service",
              "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
              }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
              }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureBlob",
              "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          {
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
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>Parametry JSON
Vytvořte soubor JSON s názvem **ADFCopyTutorialARM-Parameters.json**, který obsahuje parametry pro šablonu Azure Resource Manageru. 

> [!IMPORTANT]
> Zadejte název a klíč svého účtu služby Azure Storage v parametrech storageAccountName a storageAccountKey.  
> 
> Zadejte server SQL Azure, databázi, uživatele a heslo v parametrech sqlServerName, databaseName, sqlServerUserName, a sqlServerPassword.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the Azure SQL server>" },
        "databaseName": { "value": "<Name of the Azure SQL database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
    }
}
```

> [!IMPORTANT]
> Můžete mít samostatné soubory JSON s parametry pro vývojové, testovací a produkční prostředí, které lze použít se stejnou šablonou JSON služby Data Factory. Pomocí skriptu prostředí PowerShell můžete zautomatizovat nasazování entit služby Data Factory v těchto prostředích.  
> 
> 

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
1. Otevřete prostředí **Azure PowerShell** a spusťte následující příkaz:
   * Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal.
   
    ```PowerShell
    Login-AzureRmAccount    
    ```  
   * Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet.
   
    ```PowerShell
    Get-AzureRmSubscription
    ```   
   * Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat.
    
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```    
2. Spuštěním následujícího příkazu nasaďte entity služby Data Factory pomocí šablony Resource Manageru, kterou jste vytvořili v kroku 1.

    ```PowerShell   
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Monitorování kanálu

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.
2. Klikněte na **Datové továrny** v levé nabídce (nebo) klikněte na **Všechny služby** a v kategorii **INTELIGENTNÍ FUNKCE A ANALÝZY** klikněte na **Datové továrny**.
   
    ![Nabídka Datové továrny](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. Na stránce **Datové továrny** vyhledejte svou datovou továrnu (AzureBlobToAzureSQLDatabaseDF). 
   
    ![Vyhledávání datové továrny](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Klikněte na svou datovou továrnu Azure. Zobrazí se domovská stránka datové továrny.
   
    ![Domovská stránka datové továrny](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
6. Postupujte podle pokynů v tématu [Monitorování datových sad a kanálu](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) k monitorování kanálu a datových sad, které jste vytvořili v tomto kurzu. V současné době Visual Studio monitorování kanálů Data Factory nepodporuje.
7. Když je řez ve stavu **Připraveno**, ověřte zkopírování dat do tabulky **emp** ve službě Azure SQL Database.


Další informace o používání oken portálu Azure Portal k monitorování kanálu a datových sad, které jste vytvořili v tomto kurzu, najdete v článku [Monitorování datových sad a kanálu](data-factory-monitor-manage-pipelines.md).

Další informace o používání aplikace pro monitorování a správu k monitorování datových kanálů najdete v tématu [Monitorování a správa kanálů služby Azure Data Factory pomocí monitorovací aplikace](data-factory-monitor-manage-app.md).

## <a name="data-factory-entities-in-the-template"></a>Entity služby Data Factory v šabloně
### <a name="define-data-factory"></a>Definování datové továrny
Datovou továrnu definujete v šabloně Resource Manageru, jak je znázorněno v následující ukázce:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```

Hodnota dataFactoryName je definována takto: 

```json
"dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
```

Je to jedinečný řetězec vycházející z ID skupiny prostředků.  

### <a name="defining-data-factory-entities"></a>Definování entit služby Data Factory
V šabloně JSON jsou definovány následující entity služby Data Factory: 

1. [Propojená služba Azure Storage](#azure-storage-linked-service)
2. [Propojená služba Azure SQL](#azure-sql-database-linked-service)
3. [Datová sada Azure Blob](#azure-blob-dataset)
4. [Datová sada Azure SQL](#azure-sql-dataset)
5. [Data Pipeline s aktivitou kopírování](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. V rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) jste vytvořili kontejner a nahráli data do tohoto účtu úložiště. V tomto oddílu zadáte název a klíč svého účtu služby Azure Storage. Podrobnosti o vlastnostech JSON sloužících k definování propojené služby Azure Storage najdete v oddílu [Propojená služba Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service). 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```

Vlastnost connectionString používá parametry storageAccountName a storageAccountKey. Hodnoty těchto parametrů se předávají pomocí konfiguračního souboru. Definice také používá proměnné azureStorageLinkedService a dataFactoryName definované v šabloně. 

#### <a name="azure-sql-database-linked-service"></a>Propojená služba Azure SQL Database
Služba AzureSqlLinkedService propojí službu Azure SQL Database s datovou továrnou. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku emp. V tomto oddílu zadáte název serveru Azure SQL, název databáze, uživatelské jméno a heslo. Podrobnosti o vlastnostech JSON sloužících k definování propojené služby Azure SQL najdete v oddílu [Propojená služba Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlDatabase",
          "description": "Azure SQL linked service",
          "typeProperties": {
            "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
          }
    }
}
```

Vlastnost connectionString používá parametry sqlServerName, databaseName, sqlServerUserName a sqlServerPassword, jejichž hodnoty se předávají pomocí konfiguračního souboru. Definice také používá tyto proměnné z šablony: azureSqlLinkedServiceName a dataFactoryName.

#### <a name="azure-blob-dataset"></a>Datová sada Azure Blob
Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. V definici datové sady Azure Blob zadáte názvy kontejneru objektů blob, složky a souboru, který obsahuje vstupní data. Podrobnosti o vlastnostech JSON sloužících k definování datové sady Azure Blob najdete v oddílu [Vlastnosti datové sady Azure Blob](data-factory-azure-blob-connector.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]",
      "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Datová sada Azure SQL
Zadáte název tabulky ve službě Azure SQL Database, která uchovává zkopírovaná data ze služby Azure Blob Storage. Podrobnosti o vlastnostech JSON sloužících k definování datové sady Azure SQL najdete v oddílu [Vlastnosti datové sady Azure SQL](data-factory-azure-sql-connector.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Data Pipeline
Nadefinujete kanál, který kopíruje data z datové sady Azure Blob do datové sady Azure SQL. Popisy elementů JSON sloužících k definování kanálu v tomto příkladu najdete v oddílu [Kód JSON kanálu](data-factory-create-pipelines.md#pipeline-json). 

```json
{
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
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>Znovupoužití šablony
V tomto kurzu jste vytvořili šablonu pro definování entit služby Data Factory a šablonu pro předávání hodnot parametrů. Kanál kopíruje data z účtu služby Azure Storage do služby Azure SQL Database a tyto služby jsou určeny prostřednictvím parametrů. Chcete-li použít stejnou šablonu k nasazení entit služby Data Factory do různých prostředí, vytvořte pro každé prostředí soubor parametrů a použijte jej při nasazování příslušného prostředí.     

Příklad:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

Všimněte si, že první příkaz používá soubor parametrů pro vývojové prostředí, druhý příkaz používá soubor parametrů pro testovací prostředí a třetí příkaz používá soubor parametrů pro produkční prostředí.  

Šablonu můžete také znovu použít k provádění opakujících se úloh. Například: Potřebujete vytvořit mnoho datových továren s jedním nebo více kanály, které implementují stejnou logiku, ale každá datová továrna používá jiný účet služby Storage a SQL Database. V tomto scénáři použijete k vytvoření datových továren stejnou šablonu ve stejném prostředí (vývojové, testovací nebo produkční) s různými soubory parametrů.   

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste v operaci kopírování použili úložiště objektů blob jako zdrojové úložiště dat a databázi Azure SQL jako cílové úložiště dat. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje a cíle aktivitou kopírování: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Kliknutím na odkaz úložiště dat v tabulce získáte další informace o kopírování dat do nebo z úložiště dat.
