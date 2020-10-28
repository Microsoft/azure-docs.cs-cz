---
title: Aktualizace modelů Machine Learning pomocí Azure Data Factory
description: Popisuje postup vytváření prediktivních kanálů pomocí Azure Data Factory V1 a Azure Machine Learning Studio (Classic).
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: c456c7eb31e1e8e66aa3276a0cb5f6f8b39efa9a
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631746"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>Aktualizace modelů Azure Machine Learning Studio (Classic) pomocí aktivity aktualizovat prostředek

> [!div class="op_single_selector" title1="Aktivity transformace"]
> * [Aktivita v podregistru](data-factory-hive-activity.md) 
> * [Aktivita prasete](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Sparku](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Azure Machine Learning Studio (klasická verze)](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Azure Machine Learning Studio (klasická verze)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aktualizace modelů strojového učení v Data Factory](../update-machine-learning-models.md).

Tento článek doplňuje hlavní článek o integraci Azure Data Factory-Azure Machine Learning Studio (Classic): [vytváření prediktivních kanálů pomocí Azure Machine Learning Studio (Classic) a Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Pokud jste to ještě neudělali, přečtěte si hlavní článek před čtením tohoto článku. 

## <a name="overview"></a>Přehled
Prediktivní modely v Azure Machine Learning Studio (klasický) experimenty bodování se v průběhu času musí přenášet pomocí nových vstupních datových sad. Až budete s rekurzem hotovi, chcete aktualizovat webovou službu bodování pomocí předaného modelu ML. Typický postup, jak povolit přeškolení a aktualizace modelů studia (Classic) prostřednictvím webových služeb, jsou tyto:

1. Vytvořte experiment v [Azure Machine Learning Studio (Classic)](https://studio.azureml.net).
2. Pokud jste s modelem spokojeni, použijte Azure Machine Learning Studio (Classic) k publikování webových služeb pro **školicí experiment** i bodování/ **prediktivní experiment** .

Následující tabulka popisuje webové služby použité v tomto příkladu.  Podrobnosti najdete v tématu [přeučení modelů Azure Machine Learning Studio (Classic)](../../machine-learning/classic/retrain-machine-learning-model.md) .

- **Školení webové služby** – přijímá školicí data a vytváří školené modely. Výstupem rekurze je soubor. ilearner v úložišti objektů BLOB v Azure. **Výchozím koncovým bodem** se automaticky vytvoří při publikování experimentu školení jako webové služby. Můžete vytvořit další koncové body, ale v příkladu se používá pouze výchozí koncový bod.
- **Webová služba bodování** – přijímá příklady neoznačených dat a zpřístupňuje předpovědi. Výstup předpovědi může mít různé formuláře, jako je například soubor. csv nebo řádky v Azure SQL Database v závislosti na konfiguraci experimentu. Výchozí koncový bod je automaticky vytvořen při publikování prediktivního experimentu jako webové služby. 

Následující obrázek znázorňuje vztah mezi školicími a vyhodnocovacími koncovými body v Azure Machine Learning Studio (Classic).

![webové služby](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**Webovou službu školení** můžete vyvolat pomocí **aktivity spuštění dávky Azure Machine Learning Studio (Classic)** . Vyvolání webové služby školení je stejné jako vyvolání webové služby Azure Machine Learning Studio (Classic) (webová služba bodování) pro data bodování. Předchozí části obsahují podrobnosti o tom, jak v kanálu Azure Data Factory vyvolat webovou službu Azure Machine Learning Studio (Classic). 

**Webovou službu bodování** můžete vyvolat pomocí **aktivity aktualizace prostředku Azure Machine Learning Studio (Classic)** a aktualizovat webovou službu pomocí nově vyučeného modelu. V následujících příkladech jsou uvedeny definice propojených služeb: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Webová služba bodování je klasický Web Service.
Pokud webová služba bodování je **klasický web** , vytvořte druhý **nevýchozí a aktualizovatelný koncový bod** pomocí Azure Portal. Postup najdete v článku [Vytvoření koncových bodů](../../machine-learning/classic/create-endpoint.md) . Po vytvoření nevýchozího koncového bodu s možností aktualizace proveďte následující kroky:

* Kliknutím na **DÁVKOVÉ spuštění** Získejte hodnotu identifikátoru URI pro vlastnost **mlEndpoint** JSON.
* Kliknutím na odkaz **aktualizovat prostředek** získáte hodnotu identifikátoru URI pro vlastnost **updateResourceEndpoint** JSON. Klíč rozhraní API se nachází na samotné stránce koncového bodu (v pravém dolním rohu).

![aktualizovatelný koncový bod](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Následující příklad uvádí ukázku definice JSON pro propojenou službu AzureML. Propojená služba používá apiKey k ověřování.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Webová služba bodování je Azure Resource Manager webové služby. 
Pokud je webová služba novým typem webové služby, který zveřejňuje Azure Resource Manager koncový bod, nemusíte přidávat druhý **jiný než výchozí** koncový bod. **UpdateResourceEndpoint** v propojené službě má formát: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Můžete získat hodnoty pro držitele v adrese URL při dotazování webové služby na [portálu Web Services Azure Machine Learning Studio (Classic)](https://services.azureml.net/). Nový typ koncového bodu prostředku aktualizace vyžaduje token AAD (Azure Active Directory). V propojené službě Studio (Classic) zadejte **servicePrincipalId** a **servicePrincipalKey** . Přečtěte si téma [Vytvoření instančního objektu a přiřazení oprávnění ke správě prostředků Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Tady je ukázka definice propojené služby AzureML: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Další podrobnosti najdete v následujícím scénáři. Obsahuje příklad pro přeškolení a aktualizace modelů studia (Classic) z Azure Data Factoryho kanálu.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Scénář: přeškolení a aktualizace modelu studia (Classic)
V této části najdete ukázkový kanál, který používá **aktivitu spuštění dávkového zpracování Azure Machine Learning Studio (Classic)** k přeškolení modelu. Kanál také používá **aktivitu aktualizace prostředku Azure Machine Learning Studio (Classic)** k aktualizaci modelu ve webové službě bodování. Oddíl také poskytuje fragmenty JSON pro všechny propojené služby, datové sady a kanály v příkladu.

Tady je zobrazení diagramu ukázkového kanálu. Jak vidíte, aktivita spuštění dávky studia (Classic) vezme vstup do školení a vytvoří výstup školení (soubor iLearner). Aktivita aktualizace prostředku studia (Classic) převezme tento výstup školení a aktualizuje model v koncovém bodě webové služby bodování. Aktivita aktualizace prostředku nevytváří žádný výstup. PlaceholderBlob je pouze fiktivní výstupní datová sada, kterou služba Azure Data Factory vyžaduje ke spuštění kanálu.

![Diagram kanálu](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Propojená služba úložiště objektů BLOB v Azure:
Azure Storage obsahuje následující data:

* školicí data. Vstupní data pro výukovou webovou službu studia (Classic).  
* soubor iLearner Výstup z webové služby školení pro Studio (Classic). Tento soubor je také vstupem aktivity aktualizovat prostředek.  

Tady je ukázka definice JSON propojené služby:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Vstupní datová sada pro školení:
Následující datová sada reprezentuje vstupní školicí data pro webovou službu studia (Classic). Aktivita spuštění dávky studia (Classic) přebírá tuto datovou sadu jako vstup.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Školení výstupní datové sady:
Následující datová sada představuje výstupní soubor iLearner z webové služby inAzure Machine Learning Studio (Classic) Training. Aktivita spuštění dávky Azure Machine Learning Studio (Classic) vytváří tuto datovou sadu. Tato datová sada je také vstupem do aktivity aktualizace prostředku Azure Machine Learning Studio (Classic).

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Služba propojené služby pro Studio (Classic) – koncový bod školení
Následující fragment kódu JSON definuje propojenou službu studia (Classic), která odkazuje na výchozí koncový bod webové služby školení.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

V **Azure Machine Learning Studio (Classic)** proveďte následující kroky a získejte hodnoty pro **mlEndpoint** a **apiKey** :

1. V nabídce vlevo klikněte na položku **webové služby** .
2. Klikněte na **webovou službu školení** v seznamu webových služeb.
3. Klikněte na Kopírovat vedle textového pole **klíč rozhraní API** . Vložte klíč do schránky do editoru JSON Data Factory.
4. V **Azure Machine Learning Studio (Classic)** klikněte na odkaz **spuštění dávky** .
5. Zkopírujte **identifikátor URI žádosti** z oddílu **žádosti** a vložte ho do Data Factory Editor JSON.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Propojená služba pro Studio (Classic) – aktualizovatelný koncový bod:
Následující fragment kódu JSON definuje propojenou službu studia (Classic), která odkazuje na nevýchozí aktualizovatelný koncový bod webové služby bodování.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Zástupný znak výstupní datové sady:
Aktivita aktualizace prostředku studia (Classic) negeneruje žádný výstup. Azure Data Factory ale vyžaduje výstupní datovou sadu k naplánování plánu kanálu. Proto v tomto příkladu používáme datovou sadu fiktivní/zástupný znak.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Kanál
Kanál má dvě aktivity: **AzureMLBatchExecution** a **povinná** . Aktivita spuštění dávky Azure Machine Learning Studio (Classic) vezme data školení jako vstup a vytvoří soubor iLearner jako výstup. Tato aktivita vyvolá webovou službu školení (zkušební experiment vydaný jako webovou službu) se vstupními školicími daty a přijme soubor ilearner z webové služby. PlaceholderBlob je pouze fiktivní výstupní datová sada, kterou služba Azure Data Factory vyžaduje ke spuštění kanálu.

![Diagram kanálu](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```