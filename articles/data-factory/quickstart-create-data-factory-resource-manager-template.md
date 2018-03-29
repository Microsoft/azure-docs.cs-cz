---
title: Vytvoření datové továrny Azure pomocí šablony Resource Manageru | Dokumentace Microsoftu
description: V tomto kurzu vytvoříte ukázkový kanál služby Azure Data Factory pomocí šablony Azure Resource Manageru.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 7df0c8153cc5b2e3a785d79e23ecb7d56285c6f4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Kurz: Vytvoření datové továrny Azure pomocí šablony Azure Resource Manageru
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Verze 2 – Preview](quickstart-create-data-factory-resource-manager-template.md) 

Tento rychlý start popisuje použití šablony Azure Resource Manageru k vytvoření datové továrny Azure. Kanál, který vytvoříte v této datové továrně, **kopíruje** data z jedné složky do jiné složky v úložišti objektů blob Azure. Kurz předvádějící způsoby **transformace** dat pomocí Azure Data Factory najdete v tématu [Kurz: Transformace dat pomocí Sparku](transform-data-using-spark.md). 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, která je všeobecně dostupná (GA), prostudujte si [sestavení první datové továrny pomocí služby Data Factory verze 1](v1/data-factory-build-your-first-pipeline-using-arm.md).
>
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Úvod do služby Azure Data Factory najdete v tématu [Úvod do Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

## <a name="resource-manager-templates"></a>Šablony Resource Manageru
Obecné informace o šablonách Azure Resource Manageru najdete v tématu [Vytváření šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md). 

Následující oddíl poskytuje hotovou šablonu Resource Manageru pro definování entit služby Data Factory, abyste mohli rychle projít kurzem a otestovat šablonu. Pro lepší pochopení toho, jak jsou jednotlivé entity služby Data Factory definovány, přejděte k oddílu [Entity služby Data Factory v šabloně](#data-factory-entities-in-the-template).

## <a name="data-factory-json"></a>JSON pro Data Factory 
Ve složce **C:\ADFTutorial** vytvořte soubor JSON s názvem **ADFTutorialARM.json** s následujícím obsahem:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "dataFactoryName": {
            "type": "string",
            "metadata": {
                "description": "Name of the data factory. Must be globally unique."
            }
        },
        "dataFactoryLocation": {
            "type": "string",
            "allowedValues": [
                "East US",
                "East US 2",
                "West Europe"
            ],
            "defaultValue": "East US",
            "metadata": {
                "description": "Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey": {
            "type": "securestring",
            "metadata": {
                "description": "Key for the Azure storage account."
            }
        },
        "blobContainer": {
            "type": "string",
            "metadata": {
                "description": "Name of the blob container in the Azure Storage account."
            }
        },
        "inputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that has the input file."
            }
        },
        "inputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the input file/blob."
            }
        },
        "outputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that will hold the transformed data."
            }
        },
        "outputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the output file/blob."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },
    "variables": {
        "azureStorageLinkedServiceName": "ArmtemplateStorageLinkedService",
        "inputDatasetName": "ArmtemplateTestDatasetIn",
        "outputDatasetName": "ArmtemplateTestDatasetOut",
        "pipelineName": "ArmtemplateSampleCopyPipeline",
        "triggerName": "ArmTemplateTestTrigger"
    },
    "resources": [{
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "2017-09-01-preview",
        "type": "Microsoft.DataFactory/factories",
        "location": "[parameters('dataFactoryLocation')]",
        "properties": {
            "loggingStorageAccountName": "[parameters('storageAccountName')]",
            "loggingStorageAccountKey": "[parameters('storageAccountKey')]"
        },
        "resources": [{
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": {
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                            "type": "SecureString"
                        }
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('inputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
                        "fileName": "[parameters('inputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('outputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
                        "fileName": "[parameters('outputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "pipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "activities": [{
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "BlobSource"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "name": "MyCopyActivity",
                        "inputs": [{
                            "referenceName": "[variables('inputDatasetName')]",
                            "type": "DatasetReference"
                        }],
                        "outputs": [{
                            "referenceName": "[variables('outputDatasetName')]",
                            "type": "DatasetReference"
                        }]
                    }]
                }
            },
            {
                "type": "triggers",
                "name": "[variables('triggerName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]",
                    "[variables('pipelineName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "ScheduleTrigger",
                    "typeProperties": {
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1,
                            "startTime": "[parameters('triggerStartTime')]",
                            "endTime": "[parameters('triggerEndTime')]",
                            "timeZone": "UTC"
                        }
                    },
                    "pipelines": [{
                        "pipelineReference": {
                            "type": "PipelineReference",
                            "referenceName": "ArmtemplateSampleCopyPipeline"
                        },
                        "parameters": {}
                    }]
                }
            }
        ]
    }]
}
```

## <a name="parameters-json"></a>Parametry JSON
Vytvořte soubor JSON s názvem **ADFTutorialARM-Parameters.json**, který obsahuje parametry pro šablonu Azure Resource Manageru.  

> [!IMPORTANT]
> - Zadejte název a klíč svého účtu služby Azure Storage v parametrech **storageAccountName** a **storageAccountKey** v souboru parametrů. Vytvořili jste kontejner adftutorial a do vstupní složky v tomto úložišti objektů blob v Azure jste nahráli ukázkový soubor (emp.txt). 
> - V parametru **dataFactoryName** zadejte globálně jedinečný název datové továrny. Například ARMTutorialFactoryJohnDoe11282017. 
> - V parametru **triggerStartTime** zadejte aktuální den ve formátu `2017-11-28T00:00:00`.
> - V parametru **triggerEndTime** zadejte následující den ve formátu `2017-11-29T00:00:00`. Můžete také zkontrolovat aktuální čas UTC a jako čas ukončení zadat další hodinu nebo dvě. Pokud je například aktuální čas UTC 1:32, zadejte jako čas ukončení `2017-11-29:03:00:00`. V tomto případě trigger spustí kanál dvakrát (ve 2:00 a ve 3:00).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataFactoryName": {
      "value": "<datafactoryname>"
    },    
    "dataFactoryLocation": {
      "value": "East US"
    },
    "storageAccountName": {
      "value": "<yourstroageaccountname>"
    },
    "storageAccountKey": {
      "value": "<yourstorageaccountkey>"
    },
    "blobContainer": {
      "value": "adftutorial"
    },
    "inputBlobFolder": {
      "value": "input"
    },
    "inputBlobName": {
      "value": "emp.txt"
    },
    "outputBlobFolder": {
      "value": "output"
    },
    "outputBlobName": {
      "value": "emp.txt"
    },
    "triggerStartTime": {
        "value": "2017-11-28T00:00:00. Set to today"
    },
    "triggerEndTime": {
        "value": "2017-11-29T00:00:00. Set to tomorrow"
    }
  }
}
```

> [!IMPORTANT]
> Můžete mít samostatné soubory JSON s parametry pro vývojové, testovací a produkční prostředí, které lze použít se stejnou šablonou JSON služby Data Factory. Pomocí skriptu prostředí PowerShell můžete zautomatizovat nasazování entit služby Data Factory v těchto prostředích. 

## <a name="deploy-data-factory-entities"></a>Nasazení entit služby Data Factory 
Spuštěním následujícího příkazu v PowerShellu nasaďte entity služby Data Factory pomocí šablony Resource Manageru, kterou jste vytvořili dříve v tomto rychlém startu. 

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Zobrazí se podobný výstup jako v následující ukázce: 

```
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 11/29/2017 3:11:13 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                 Type            Value
                          ===============      ============    ==========
                          dataFactoryName      String          <data factory name>
                          dataFactoryLocation  String          East US
                          storageAccountName   String          <storage account name>
                          storageAccountKey    SecureString
                          blobContainer        String          adftutorial
                          inputBlobFolder      String          input
                          inputBlobName        String          emp.txt
                          outputBlobFolder     String          output
                          outputBlobName       String          emp.txt
                          triggerStartTime     String          11/29/2017 12:00:00 AM
                          triggerEndTime       String          11/29/2017 4:00:00 AM

Outputs                 :
DeploymentDebugLogLevel :
```

## <a name="start-the-trigger"></a>Spuštění triggeru

Šablona nasadí následující entity služby Data Factory: 

- Propojená služba Azure Storage
- Datové sady Azure Blob (vstup a výstup)
- Kanál s aktivitou kopírování
- Trigger pro aktivaci kanálu

Nasazený trigger je v zastaveném stavu. Jedním ze způsobů, jak trigger spustit, je použít rutinu PowerShellu **Start-AzureRmDataFactoryV2Trigger**. Podrobné kroky najdete v následujícím postupu: 

1. V okně PowerShellu vytvořte proměnnou, která bude uchovávat název skupiny prostředků. Zkopírujte do okna PowerShellu následující příkaz a stiskněte ENTER. Pokud jste pro příkaz New-AzureRmResourceGroupDeployment zadali jiný název skupiny prostředků, aktualizujte tuto hodnotu. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ``` 
1. Vytvořte proměnnou, která bude uchovávat název datové továrny. Zadejte stejný název, jaký jste zadali v souboru ADFTutorialARM-Parameters.json.   

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Nastavte proměnnou pro název triggeru. Název triggeru je pevně zakódovaný v souboru šablony Resource Manageru (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Po zadání názvu datové továrny a triggeru získejte **stav triggeru** spuštěním následujícího příkazu PowerShellu:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Zde je ukázkový výstup: 

    ```json
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Všimněte si, že stav modulu runtime triggeru je **Zastaveno**. 
5. **Spusťte trigger**. Trigger spouští kanál definovaný v šabloně v celou hodinu. To znamená, že pokud jste tento příkaz spustili ve 14:25, trigger poprvé spustí kanál v 15:00. Potom bude kanál spouštět každou hodinu až do času ukončení, který jste pro trigger zadali. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Zde je ukázkový výstup: 
    
    ```
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Znovu spusťte příkaz Get-AzureRmDataFactoryV2Trigger a potvrďte spuštění triggeru.  

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Zde je ukázkový výstup:
    
    ```
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. Po přihlášení k webu [Azure Portal](https://portal.azure.com/) klikněte na **Všechny služby**, vyhledejte klíčové slovo jako například **data fa** a vyberte **Datové továrny**.

    ![Procházení nabídky Datové továrny](media/quickstart-create-data-factory-resource-manager-template/browse-data-factories-menu.png)
2. Na stránce **Datové továrny** klikněte na datovou továrnu, kterou jste vytvořili. V případě potřeby můžete seznam filtrovat s použitím názvu vaší datové továrny.  

    ![Výběr datové továrny](media/quickstart-create-data-factory-resource-manager-template/select-data-factory.png)
3. Na stránce Datové továrny klikněte na dlaždici **Monitorování a správa**. 

    ![Dlaždice Monitorování a správa](media/quickstart-create-data-factory-resource-manager-template/monitor-manage-tile.png)
4. Ve webovém prohlížeči by se na samostatné kartě měla otevřít **aplikace pro integraci dat**. Pokud karta Monitorování není aktivní, přepněte na **kartu Monitorování**. Všimněte si, že spuštění kanálu aktivoval **trigger plánovače**. 

    ![Monitorování spuštění kanálu](media/quickstart-create-data-factory-resource-manager-template/monitor-pipeline-run.png)    

    > [!IMPORTANT]
    > Zobrazí se pouze spuštění kanálu v celou hodinu (například ve 4:00, 5:00, 6:00 atd.). Po uplynutí další celé hodiny seznam aktualizujte kliknutím na **Aktualizovat** na panelu nástrojů. 
5. Klikněte na odkaz ve sloupci **Akce**. 

    ![Odkaz na akce kanálu](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)
6. Zobrazí se spuštění aktivit související s příslušným spuštěním kanálu. V tomto rychlém startu má kanál pouze jednu aktivitu typu Kopírování. Proto se zobrazí spuštění této aktivity. 

    ![Spuštění aktivit](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
1. Klikněte na odkaz ve sloupci **Výstup**. V okně **Výstup** se zobrazí výstup operace kopírování. Pokud chcete zobrazit celý výstup, klikněte na tlačítko pro maximalizaci. Maximalizované okno výstupu můžete minimalizovat nebo zavřít. 

    ![Okno Výstup](media/quickstart-create-data-factory-resource-manager-template/output-window.png)
7. Jakmile se zobrazí úspěšné nebo chybné spuštění, zastavte trigger. Trigger spouští kanál jednou za hodinu. Kanál při každém spuštění zkopíruje stejný soubor ze vstupní složky do výstupní složky. Pokud chcete trigger zastavit, spusťte v okně PowerShellu následující příkaz. 
    
    ```powershell
    Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="json-definitions-for-entities"></a>Definice JSON pro entity
V šabloně JSON jsou definovány následující entity služby Data Factory: 

- [Propojená služba Azure Storage](#azure-storage-linked-service)
- [Vstupní datová sada Azure Blob](#azure-blob-input-dataset)
- [Výstupní datová sada Azure Blob](#azure-blob-output-dataset)
- [Data Pipeline s aktivitou kopírování](#data-pipeline)
- [Trigger](#trigger)

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. V rámci požadavků jste vytvořili kontejner a nahráli data do tohoto účtu úložiště. V tomto oddílu zadáte název a klíč svého účtu služby Azure Storage. Podrobnosti o vlastnostech JSON sloužících k definování propojené služby Azure Storage najdete v oddílu [Propojená služba Azure Storage](connector-azure-blob-storage.md#linked-service-properties). 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": {
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                "type": "SecureString"
            }
        }
    }
}
```

Vlastnost connectionString používá parametry storageAccountName a storageAccountKey. Hodnoty těchto parametrů se předávají pomocí konfiguračního souboru. Definice také používá proměnné azureStorageLinkedService a dataFactoryName definované v šabloně. 

#### <a name="azure-blob-input-dataset"></a>Vstupní datová sada Azure Blob
Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. V definici datové sady Azure Blob zadáte názvy kontejneru objektů blob, složky a souboru, který obsahuje vstupní data. Podrobnosti o vlastnostech JSON sloužících k definování datové sady Azure Blob najdete v oddílu [Vlastnosti datové sady Azure Blob](connector-azure-blob-storage.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('inputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
        "fileName": "[parameters('inputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
},

```

#### <a name="azure-blob-output-dataset"></a>Výstupní datová sada Azure Blob
Zadáte název složky ve službě Azure Blob Storage, která uchovává zkopírovaná data ze vstupní složky. Podrobnosti o vlastnostech JSON sloužících k definování datové sady Azure Blob najdete v oddílu [Vlastnosti datové sady Azure Blob](connector-azure-blob-storage.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('outputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
        "fileName": "[parameters('outputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
}
```

#### <a name="data-pipeline"></a>Data Pipeline
Nadefinujete kanál, který kopíruje data z jedné datové sady Azure Blob do jiné datové sady Azure Blob. Popisy elementů JSON sloužících k definování kanálu v tomto příkladu najdete v oddílu [Kód JSON kanálu](concepts-pipelines-activities.md#pipeline-json). 

```json
{
    "type": "pipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('inputDatasetName')]",
    "[variables('outputDatasetName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "activities": [
        {
        "type": "Copy",
        "typeProperties": {
            "source": {
            "type": "BlobSource"
            },
            "sink": {
            "type": "BlobSink"
            }
        },
        "name": "MyCopyActivity",
        "inputs": [
            {
            "referenceName": "[variables('inputDatasetName')]",
            "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
            "referenceName": "[variables('outputDatasetName')]",
            "type": "DatasetReference"
            }
        ]
        }
    ]
    }
}
```

#### <a name="trigger"></a>Trigger
Nadefinujete trigger, který spouští kanál jednou za hodinu. Nasazený trigger je v zastaveném stavu. Spusťte trigger pomocí rutiny **Start-AzureRmDataFactoryV2Trigger**. Další informace o triggerech najdete v článku [Spouštění kanálů a triggery](concepts-pipeline-execution-triggers.md#triggers). 

```json
{
    "type": "triggers",
    "name": "[variables('triggerName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('inputDatasetName')]",
        "[variables('outputDatasetName')]",
        "[variables('pipelineName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-28T00:00:00",
                "endTime": "2017-11-29T00:00:00",
                "timeZone": "UTC"               
            }
        },
        "pipelines": [{
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "ArmtemplateSampleCopyPipeline"
            },
            "parameters": {}
        }]
    }
}
```

## <a name="reuse-the-template"></a>Znovupoužití šablony
V tomto kurzu jste vytvořili šablonu pro definování entit služby Data Factory a šablonu pro předávání hodnot parametrů. Chcete-li použít stejnou šablonu k nasazení entit služby Data Factory do různých prostředí, vytvořte pro každé prostředí soubor parametrů a použijte jej při nasazování příslušného prostředí.     

Příklad:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Všimněte si, že první příkaz používá soubor parametrů pro vývojové prostředí, druhý příkaz používá soubor parametrů pro testovací prostředí a třetí příkaz používá soubor parametrů pro produkční prostředí.  

Šablonu můžete také znovu použít k provádění opakujících se úloh. Můžete například vytvořit mnoho datových továren s jedním nebo více kanály, které implementují stejnou logiku, ale každá datová továrna používá jiný účet úložiště Azure. V tomto scénáři použijete k vytvoření datových továren stejnou šablonu ve stejném prostředí (vývojové, testovací nebo produkční) s různými soubory parametrů. 


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Projděte si [kurzy](tutorial-copy-data-dot-net.md), kde se dozvíte o použití služby Data Factory ve více scénářích. 
