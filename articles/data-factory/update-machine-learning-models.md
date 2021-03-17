---
title: Aktualizace modelů Azure Machine Learning Studio (Classic) pomocí Azure Data Factory
description: Popisuje postup vytvoření prediktivních kanálů pomocí Azure Data Factory a Azure Machine Learning Studio (Classic).
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: ef89ecef34a7c5afb94547181f449b0fc393e67c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377563"
---
# <a name="update-azure-machine-learning-studio-classic-models-by-using-update-resource-activity"></a>Aktualizace modelů Azure Machine Learning Studio (Classic) pomocí aktivity aktualizovat prostředek

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek doplňuje hlavní článek o integraci Azure Data Factory-Azure Machine Learning Studio (Classic): [vytváření prediktivních kanálů pomocí Azure Machine Learning Studio (Classic) a Azure Data Factory](transform-data-using-machine-learning.md). Pokud jste to ještě neudělali, přečtěte si hlavní článek před čtením tohoto článku.

## <a name="overview"></a>Přehled
V rámci procesu zprovozňování Azure Machine Learning Studio (klasického modelu) je váš model vyškolený a uložený. Pak ho použijete k vytvoření prediktivní webové služby. Webovou službu je pak možné spotřebovat na webech, řídicích panelech a mobilních aplikacích.

Modely, které vytvoříte pomocí Azure Machine Learning Studio (Classic), nejsou obvykle statické. Jakmile budou nová data k dispozici nebo když spotřebitel rozhraní API má svá vlastní data, model musí být znovu provlakované. 

Rekurze se může vyskytnout často. S aktivitou spuštění služby Batch a aktualizací aktivity prostředku můžete zprovoznění Azure Machine Learning Studio (klasický) model přeškolení a aktualizovat prediktivní webovou službu pomocí Data Factory.

Následující obrázek znázorňuje vztah mezi školicími a prediktivními webovými službami.

![webové služby](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Aktivita aktualizace prostředku Azure Machine Learning Studio (Classic)

Následující fragment kódu JSON definuje aktivitu spuštění dávky Azure Machine Learning Studio (Classic).

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Vlastnost                      | Popis                              | Vyžadováno |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Název aktivity v kanálu     | Yes      |
| description                   | Text popisující, co aktivita dělá.  | No       |
| typ                          | U Azure Machine Learning Studio (Classic) aktivita aktualizace prostředku je typ aktivity  **povinná**. | Yes      |
| linkedServiceName             | Propojená služba Azure Machine Learning Studio (Classic), která obsahuje vlastnost updateResourceEndpoint. | Yes      |
| trainedModelName              | Název modulu trained model v experimentu webové služby, který se má aktualizovat | Yes      |
| trainedModelLinkedServiceName | Název propojené služby Azure Storage, která uchovává soubor ilearner, který je nahraný operací Update | Yes      |
| trainedModelFilePath          | Relativní cesta k souboru v trainedModelLinkedService, která představuje soubor ilearner, který je nahraný operací Update | Yes      |

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup

Celý proces zprovozňováního přeškolení modelu a aktualizace prediktivních webových služeb zahrnuje následující kroky:

- Vyvolejte **webovou službu školení** pomocí **aktivity spuštění dávky**. Vyvolání webové služby školení je stejné jako vyvolání prediktivní webové služby popsané v tématu [vytváření prediktivních kanálů pomocí Azure Machine Learning Studioch (klasických) a Data Factory aktivit spuštění dávky](transform-data-using-machine-learning.md). Výstupem webové služby školení je soubor iLearner, který můžete použít k aktualizaci prediktivní webové služby.
- Vyvolejte **koncový bod prostředku aktualizace** **prediktivní webové služby** pomocí **aktivity aktualizovat prostředek** a aktualizujte webovou službu pomocí nově vyučeného modelu.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Propojená služba Azure Machine Learning Studio (Classic)

Pro výše uvedený kompletní pracovní postup je potřeba vytvořit dvě propojené služby Azure Machine Learning Studio (Classic):

1. Propojená služba Azure Machine Learning Studio (Classic) s webovou službou školení, Tato propojená služba je využívána aktivitami provádění dávky stejným způsobem jako v případě, že jsou uvedena v části [vytváření prediktivních kanálů pomocí Azure Machine Learning Studio (Classic) a Data Factory aktivity spuštění dávky](transform-data-using-machine-learning.md). Rozdíl je výstupem webové služby školení je soubor iLearner, který se pak používá k aktualizaci prediktivní webové služby pomocí aktivity aktualizovat prostředek.
2. Propojená služba Azure Machine Learning Studio (Classic) do koncového bodu prostředku aktualizace prediktivní webové služby. Tato propojená služba se používá při aktualizaci aktivity prostředku k aktualizaci prediktivní webové služby pomocí souboru iLearner vráceného z výše uvedeného kroku.

U druhé propojené služby Azure Machine Learning Studio (Classic) se konfigurace liší v případě, že vaše webová služba Azure Machine Learning Studio (Classic) je klasický webový servis nebo nová webová služba. Rozdíly jsou popsány samostatně v následujících oddílech.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webová služba je nová Azure Resource Manager webové služby.

Pokud je webová služba novým typem webové služby, který zveřejňuje Azure Resource Manager koncový bod, nemusíte přidávat druhý **jiný než výchozí** koncový bod. **UpdateResourceEndpoint** v propojené službě má formát:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Můžete získat hodnoty pro držitele v adrese URL při dotazování webové služby na [portálu Web Services Azure Machine Learning Studio (Classic)](https://services.azureml.net/).

Nový typ koncového bodu prostředku aktualizace vyžaduje ověření objektu služby. Pokud chcete použít ověřování instančního objektu, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí roli **Přispěvatel** nebo **vlastník** předplatného nebo skupiny prostředků, do které patří webová služba. Přečtěte si téma [Vytvoření instančního objektu a přiřazení oprávnění ke správě prostředků Azure](../active-directory/develop/howto-create-service-principal-portal.md). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

- ID aplikace
- Klíč aplikace
- ID tenanta

Tady je ukázka definice propojené služby:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Další podrobnosti najdete v následujícím scénáři. Obsahuje příklad pro přeškolení a aktualizace modelů Azure Machine Learning Studio (Classic) z kanálu Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-studio-classic-model"></a>Ukázka: přeškolení a aktualizace modelu Azure Machine Learning Studio (Classic)

V této části najdete ukázkový kanál, který používá **aktivitu spuštění dávkového zpracování Azure Machine Learning Studio (Classic)** k přeškolení modelu. Kanál také používá **aktivitu aktualizace prostředku Azure Machine Learning Studio (Classic)** k aktualizaci modelu ve webové službě bodování. Oddíl také poskytuje fragmenty JSON pro všechny propojené služby, datové sady a kanály v příkladu.

### <a name="azure-blob-storage-linked-service"></a>Propojená služba úložiště objektů BLOB v Azure:
Azure Storage obsahuje následující data:

* školicí data. Vstupní data pro výukovou webovou službu Azure Machine Learning Studio (Classic)
* soubor iLearner Výstup z webové služby Azure Machine Learning Studio (klasický) školení. Tento soubor je také vstupem aktivity aktualizovat prostředek.

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-training-endpoint"></a>Propojená služba pro Azure Machine Learning Studio (Classic) – koncový bod školení
Následující fragment kódu JSON definuje propojenou službu Azure Machine Learning Studio (Classic), která odkazuje na výchozí koncový bod webové služby školení.

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

V **Azure Machine Learning Studio (Classic)** proveďte následující kroky a získejte hodnoty pro **mlEndpoint** a **apiKey**:

1. V nabídce vlevo klikněte na položku **webové služby** .
2. Klikněte na **webovou službu školení** v seznamu webových služeb.
3. Klikněte na Kopírovat vedle textového pole **klíč rozhraní API** . Vložte klíč do schránky do editoru JSON Data Factory.
4. V **Azure Machine Learning Studio (Classic)** klikněte na odkaz **spuštění dávky** .
5. Zkopírujte **identifikátor URI žádosti** z oddílu **žádosti** a vložte ho do Data Factory Editor JSON.

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Propojená služba pro Azure Machine Learning Studio (Classic) aktualizovatelný koncový bod:
Následující fragment kódu JSON definuje propojenou službu Azure Machine Learning Studio (Classic), která odkazuje na aktualizovatelný koncový bod webové služby bodování.

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

### <a name="pipeline"></a>Kanál
Kanál má dvě aktivity: **AzureMLBatchExecution** a **povinná**. Aktivita spuštění dávky převezme data školení jako vstup a vytvoří soubor iLearner jako výstup. Aktivita aktualizovat prostředek pak převezme tento soubor iLearner a použije ho k aktualizaci prediktivní webové služby.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
