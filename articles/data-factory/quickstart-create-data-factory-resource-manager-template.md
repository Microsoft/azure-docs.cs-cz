---
title: Vytvoření továrny dat Azure pomocí šablony Správce prostředků
description: V tomto kurzu vytvoříte ukázkový kanál služby Azure Data Factory pomocí šablony Azure Resource Manageru.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/20/2019
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 5ea45346de5ea841867dd13dd4c9a0ed26647448
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419115"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Kurz: Vytvoření datové továrny Azure pomocí šablony Azure Resource Manageru

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Aktuální verze](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento rychlý start popisuje použití šablony Azure Resource Manageru k vytvoření datové továrny Azure. Kanál, který vytvoříte v této datové **továrně, zkopíruje** data z jedné složky do jiné složky v úložišti objektů blob Azure. Návod, jak **transformovat** data pomocí Azure Data Factory, najdete [v tématu Kurz: Transformace dat pomocí Spark](transform-data-using-spark.md).

> [!NOTE]
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Úvod do služby Azure Data Factory najdete v tématu [Úvod do Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nainstalujte nejnovější moduly Azure PowerShell podle pokynů v části [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Obecné informace o šablonách Azure Resource Manageru najdete v tématu [Vytváření šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md).

Následující oddíl poskytuje hotovou šablonu Resource Manageru pro definování entit služby Data Factory, abyste mohli rychle projít kurzem a otestovat šablonu. Pro lepší pochopení toho, jak jsou jednotlivé entity služby Data Factory definovány, přejděte k oddílu [Entity služby Data Factory v šabloně](#data-factory-entities-in-the-template).

Informace o syntaxi JSON a vlastnostech prostředků data factory v šabloně naleznete v [tématu Typy prostředků Microsoft.DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json"></a>JSON pro Data Factory

Vytvořte soubor JSON s názvem **ADFTutorialARM.json** ve složce **C:\ADFTutorial** (Vytvořte složku ADFTutorial, pokud ještě neexistuje) s následujícím obsahem:

```json
{  
    "$schema":"http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "type":"string",
            "metadata":"Data Factory Name"
        },
        "dataFactoryLocation":{  
            "type":"string",
            "defaultValue":"East US",
            "metadata":{  
                "description":"Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName":{  
            "type":"string",
            "metadata":{  
                "description":"Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey":{  
            "type":"securestring",
            "metadata":{  
                "description":"Key for the Azure storage account."
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
    "variables":{  
        "factoryId":"[concat('Microsoft.DataFactory/factories/', parameters('dataFactoryName'))]"
    },
    "resources":[  
        {  
            "name":"[parameters('dataFactoryName')]",
            "apiVersion":"2018-06-01",
            "type":"Microsoft.DataFactory/factories",
            "location":"[parameters('dataFactoryLocation')]",
            "identity":{  
                "type":"SystemAssigned"
            },
            "resources":[  
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
                    "type":"Microsoft.DataFactory/factories/linkedServices",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "type":"AzureBlobStorage",
                        "typeProperties":{  
                            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "fileName":"emp.txt",
                                "folderPath":"input",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "folderPath":"output",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
                    "type":"Microsoft.DataFactory/factories/pipelines",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "activities":[  
                            {  
                                "name":"MyCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  

                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  

                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"BinarySource",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageReadSettings",
                                            "recursive":true
                                        }
                                    },
                                    "sink":{  
                                        "type":"BinarySink",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageWriteSettings"
                                        }
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetIn",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetOut",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ]
                            }
                        ],
                        "annotations":[  

                        ]
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
                    "type":"Microsoft.DataFactory/factories/triggers",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "runtimeState":"Started",
                        "pipelines":[  
                            {  
                                "pipelineReference":{  
                                    "referenceName":"ArmtemplateSampleCopyPipeline",
                                    "type":"PipelineReference"
                                },
                                "parameters":{  

                                }
                            }
                        ],
                        "type":"ScheduleTrigger",
                        "typeProperties":{  
                            "recurrence":{  
                                "frequency":"Hour",
                                "interval":1,
                                "startTime":"[parameters('triggerStartTime')]",
                                "endTime":"[parameters('triggerEndTime')]",
                                "timeZone":"UTC"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
                    ]
                }
            ]
        }
    ]
}
```
## <a name="parameters-json"></a>Parametry JSON

Vytvořte soubor JSON s názvem **ADFTutorialARM-Parameters.json**, který obsahuje parametry pro šablonu Azure Resource Manageru.

> [!IMPORTANT]
> - Zadejte název a klíč svého účtu služby Azure Storage v parametrech **storageAccountName** a **storageAccountKey** v souboru parametrů. Vytvořili jste kontejner adftutorial a do vstupní složky v tomto úložišti objektů blob v Azure jste nahráli ukázkový soubor (emp.txt).
> - V parametru **dataFactoryName** zadejte globálně jedinečný název datové továrny. Například ARMTutorialFactoryJohnDoe11282017.
> - V parametru **triggerStartTime** zadejte aktuální den ve formátu `2019-09-08T00:00:00`.
> - V parametru **triggerEndTime** zadejte následující den ve formátu `2019-09-09T00:00:00`. Můžete také zkontrolovat aktuální čas UTC a jako čas ukončení zadat další hodinu nebo dvě. Pokud je například aktuální čas UTC 1:32, zadejte jako čas ukončení `2019-09-09:03:00:00`. V tomto případě trigger spustí kanál dvakrát (ve 2:00 a ve 3:00).

```json
{  
    "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "value":"<datafactoryname>"
        },
        "dataFactoryLocation":{  
            "value":"East US"
        },
        "storageAccountName":{  
            "value":"<yourstorageaccountname>"
        },
        "storageAccountKey":{  
            "value":"<yourstorageaccountkey>"
        },
        "triggerStartTime":{  
            "value":"2019-09-08T11:00:00"
        },
        "triggerEndTime":{  
            "value":"2019-09-08T14:00:00"
        }
    }
}
```

> [!IMPORTANT]
> Můžete mít samostatné soubory JSON s parametry pro vývojové, testovací a produkční prostředí, které lze použít se stejnou šablonou JSON služby Data Factory. Pomocí skriptu prostředí PowerShell můžete zautomatizovat nasazování entit služby Data Factory v těchto prostředích.

## <a name="deploy-data-factory-entities"></a>Nasazení entit služby Data Factory

V prostředí PowerShell spusťte následující příkaz k nasazení entit Data Factory ve vaší skupině prostředků (v tomto případě vezměte Jako příklad ADFTutorialResourceGroup) pomocí šablony Správce prostředků, kterou jste vytvořili dříve v tomto rychlém startu.

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Zobrazí se podobný výstup jako v následující ukázce:

```console
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 9/8/2019 10:52:29 AM
Mode                    : Incremental
TemplateLink            : 
Parameters              : 
                          Name                   Type                       Value     
                          =====================  =========================  ==========
                          dataFactoryName        String                     <data factory name>
                          dataFactoryLocation    String                     East US   
                          storageAccountName     String                     <storage account name>
                          storageAccountKey      SecureString                         
                          triggerStartTime       String                     9/8/2019 11:00:00 AM
                          triggerEndTime         String                     9/8/2019 2:00:00 PM
                          
Outputs                 : 
DeploymentDebugLogLevel : 
```

## <a name="start-the-trigger"></a>Spuštění triggeru

Šablona nasadí následující entity služby Data Factory:

- Propojená služba Azure Storage
- Binární datové sady (vstup a výstup)
- Kanál s aktivitou kopírování
- Trigger pro aktivaci kanálu

Nasazený trigger je v zastaveném stavu. Jedním ze způsobů spuštění aktivační události je použití rutiny **prostředí Start-AzDataFactoryV2Trigger** PowerShell. Podrobné kroky najdete v následujícím postupu:

1. V okně PowerShellu vytvořte proměnnou, která bude uchovávat název skupiny prostředků. Zkopírujte do okna PowerShellu následující příkaz a stiskněte ENTER. Pokud jste pro příkaz New-AzResourceGroupDeployment zadali jiný název skupiny prostředků, aktualizujte zde hodnotu.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Vytvořte proměnnou, která bude uchovávat název datové továrny. Zadejte stejný název, jaký jste zadali v souboru ADFTutorialARM-Parameters.json.

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Nastavte proměnnou pro název triggeru. Název triggeru je pevně zakódovaný v souboru šablony Resource Manageru (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Po zadání názvu datové továrny a triggeru získejte **stav triggeru** spuštěním následujícího příkazu PowerShellu:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Zde je ukázkový výstup:

    ```json

    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Všimněte si, že stav modulu runtime triggeru je **Zastaveno**.
5. **Spusťte aktivační událost**. Trigger spouští kanál definovaný v šabloně v celou hodinu. To znamená, že pokud jste tento příkaz spustili ve 14:25, trigger poprvé spustí kanál v 15:00. Potom spustí kanál každou hodinu až do koncového času, který jste zadali pro aktivační událost.

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Zde je ukázkový výstup:
    
    ```console
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Potvrďte, že aktivační událost byla spuštěna spuštěním příkazu Get-AzDataFactoryV2Trigger.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Zde je ukázkový výstup:
    
    ```console
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Po přihlášení k webu [Azure Portal](https://portal.azure.com/) klikněte na **Všechny služby**, vyhledejte klíčové slovo jako například **data fa** a vyberte **Datové továrny**.

2. Na stránce **Datové továrny** klikněte na datovou továrnu, kterou jste vytvořili. V případě potřeby můžete seznam filtrovat s použitím názvu vaší datové továrny.

3. Na stránce Data factory klikněte na **Author & Monitor** tile.

4. Na stránce **Začínáme** vyberte **kartu Monitor**.  ![Spustit kanál monitoru](media/doc-common-process/get-started-page-monitor-button.png)

    > [!IMPORTANT]
    > Zobrazí se pouze spuštění kanálu v celou hodinu (například ve 4:00, 5:00, 6:00 atd.). Po uplynutí další celé hodiny seznam aktualizujte kliknutím na **Aktualizovat** na panelu nástrojů.

5. Klikněte na odkaz **Zobrazit spuštění aktivity** ve sloupci **Akce.**

    ![Odkaz na akce kanálu](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)

6. Zobrazí se spuštění aktivit související s příslušným spuštěním kanálu. V tomto rychlém startu má kanál pouze jednu aktivitu typu Kopírování. Proto se zobrazí spuštění této aktivity.

    ![Spuštění aktivit](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
7. Klikněte na odkaz **Výstup** ve sloupci Akce. V okně **Výstup** se zobrazí výstup operace kopírování. Pokud chcete zobrazit celý výstup, klikněte na tlačítko pro maximalizaci. Maximalizované okno výstupu můžete minimalizovat nebo zavřít.

8. Jakmile se zobrazí úspěšné nebo chybné spuštění, zastavte trigger. Trigger spouští kanál jednou za hodinu. Kanál při každém spuštění zkopíruje stejný soubor ze vstupní složky do výstupní složky. Pokud chcete trigger zastavit, spusťte v okně PowerShellu následující příkaz.
    
    ```powershell
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="json-definitions-for-entities"></a><a name="data-factory-entities-in-the-template"></a>Definice JSON pro entity

V šabloně JSON jsou definovány následující entity služby Data Factory:

- [Propojená služba Azure Storage](#azure-storage-linked-service)
- [Binární vstupní datová sada](#binary-input-dataset)
- [Binární výstupní datová sada](#binary-output-dataset)
- [Data Pipeline s aktivitou kopírování](#data-pipeline)
- [Trigger](#trigger)

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage

Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. V rámci požadavků jste vytvořili kontejner a nahráli data do tohoto účtu úložiště. V tomto oddílu zadáte název a klíč svého účtu služby Azure Storage. Podrobnosti o vlastnostech JSON sloužících k definování propojené služby Azure Storage najdete v oddílu [Propojená služba Azure Storage](connector-azure-blob-storage.md#linked-service-properties).

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
    "type":"Microsoft.DataFactory/factories/linkedServices",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]"
    ]
}
```

Vlastnost connectionString používá parametry storageAccountName a storageAccountKey. Hodnoty těchto parametrů se předávají pomocí konfiguračního souboru. Definice také používá proměnné: azureStorageLinkedService a dataFactoryName definované v šabloně.

#### <a name="binary-input-dataset"></a>Binární vstupní datová sada

Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. V definici binární datové sady zadáte názvy kontejneru objektů blob, složky a souboru, který obsahuje vstupní data. Podrobnosti o vlastnostech JSON, které slouží k definování binární datové sady, naleznete [v tématu Vlastnosti binární datové sady.](format-binary.md#dataset-properties)

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="binary-output-dataset"></a>Binární výstupní datová sada

Zadáte název složky ve službě Azure Blob Storage, která uchovává zkopírovaná data ze vstupní složky. Podrobnosti o vlastnostech JSON, které slouží k definování binární datové sady, naleznete [v tématu Vlastnosti binární datové sady.](format-binary.md#dataset-properties)

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="data-pipeline"></a>Data Pipeline

Definujete kanál, který kopíruje data z jedné binární datové sady do jiné binární datové sady. Popisy elementů JSON sloužících k definování kanálu v tomto příkladu najdete v oddílu [Kód JSON kanálu](concepts-pipelines-activities.md#pipeline-json).

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "apiVersion":"2018-06-01",
    "properties":{  
        "activities":[  
            {  
                "name":"MyCopyActivity",
                "type":"Copy",
                "dependsOn":[  

                ],
                "policy":{  
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[  

                ],
                "typeProperties":{  
                    "source":{  
                        "type":"BinarySource",
                        "storeSettings":{  
                            "type":"AzureBlobStorageReadSettings",
                            "recursive":true
                        }
                    },
                    "sink":{  
                        "type":"BinarySink",
                        "storeSettings":{  
                            "type":"AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging":false
                },
                "inputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetIn",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ],
                "outputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetOut",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ]
            }
        ],
        "annotations":[  

        ]
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
    ]
}
```

#### <a name="trigger"></a>Trigger

Nadefinujete trigger, který spouští kanál jednou za hodinu. Nasazený trigger je v zastaveném stavu. Spusťte aktivační událost pomocí rutiny **Start-AzDataFactoryV2Trigger.** Další informace o aktivačních událostech naleznete v článku [Spuštění kanálu a aktivační události.](concepts-pipeline-execution-triggers.md#trigger-execution)

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
    "type":"Microsoft.DataFactory/factories/triggers",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "runtimeState":"Started",
        "pipelines":[  
            {  
                "pipelineReference":{  
                    "referenceName":"ArmtemplateSampleCopyPipeline",
                    "type":"PipelineReference"
                },
                "parameters":{  

                }
            }
        ],
        "type":"ScheduleTrigger",
        "typeProperties":{  
            "recurrence":{  
                "frequency":"Hour",
                "interval":1,
                "startTime":"[parameters('triggerStartTime')]",
                "endTime":"[parameters('triggerEndTime')]",
                "timeZone":"UTC"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
    ]
}
```

## <a name="reuse-the-template"></a>Znovupoužití šablony

V tomto kurzu jste vytvořili šablonu pro definování entit služby Data Factory a šablonu pro předávání hodnot parametrů. Chcete-li použít stejnou šablonu k nasazení entit služby Data Factory do různých prostředí, vytvořte pro každé prostředí soubor parametrů a použijte jej při nasazování příslušného prostředí.

Příklad:

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

Všimněte si, že první příkaz používá soubor parametrů pro vývojové prostředí, druhý příkaz používá soubor parametrů pro testovací prostředí a třetí příkaz používá soubor parametrů pro produkční prostředí.

Šablonu můžete také znovu použít k provádění opakujících se úloh. Můžete například vytvořit mnoho datových továren s jedním nebo více kanály, které implementují stejnou logiku, ale každá datová továrna používá jiný účet úložiště Azure. V tomto scénáři použijete k vytvoření datových továren stejnou šablonu ve stejném prostředí (vývojové, testovací nebo produkční) s různými soubory parametrů.

## <a name="next-steps"></a>Další kroky

Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. Projděte si [kurzy](tutorial-copy-data-dot-net.md), kde se dozvíte o použití služby Data Factory ve více scénářích.
