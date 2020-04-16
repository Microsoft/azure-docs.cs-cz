---
title: Aktualizace modelů strojového učení pomocí Azure Data Factory
description: Popisuje, jak vytvořit prediktivní kanály pomocí Azure Data Factory a strojového učení.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 4488c174ba5ff35ec2709d7c1b9f3093b4ee90a3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409074"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Aktualizace modelů Azure Machine Learning pomocí aktivity aktualizace prostředků

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek doplňuje hlavní Azure Data Factory – Azure Machine Learning integrace článek: [Vytvoření prediktivní kanály pomocí Azure Machine Learning a Azure Data Factory](transform-data-using-machine-learning.md). Pokud jste tak ještě neučinili, přečtěte si hlavní článek před přečtením tohoto článku.

## <a name="overview"></a>Přehled
Jako součást procesu zprovoznění modelů Azure Machine Learning je váš model trénovaný a uložený. Potom ji použijete k vytvoření prediktivní webové služby. Webovou službu pak lze spotřebovat na webových stránkách, řídicích panelech a mobilních aplikacích.

Modely, které vytvoříte pomocí strojového učení, obvykle nejsou statické. Jako nová data k dispozici nebo když příjemce rozhraní API má svá vlastní data model musí být retrained. Podrobnosti o tom, jak můžete přeškolit model modelu v Azure Machine Learning, najdete v části [Přeškolit model strojového](../machine-learning/machine-learning-retrain-machine-learning-model.md) učení.

K rekvalifikaci může docházet často. S aktivitou dávkového spuštění a aktivitou aktualizace prostředků můžete zprovoznit přeškolit model Azure Machine Learning a aktualizovat prediktivní webovou službu pomocí datové továrny.

Následující obrázek znázorňuje vztah mezi školením a prediktivními webovými službami.

![Webové služby](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Aktivita prostředků aktualizace Azure Machine Learning

Následující fragment JSON definuje aktivitu dávkového spouštění azure strojového učení.

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

| Vlastnost                      | Popis                              | Požaduje se |
| :---------------------------- | :--------------------------------------- | :------- |
| jméno                          | Název aktivity v kanálu     | Ano      |
| description                   | Text popisující, co aktivita dělá.  | Ne       |
| type                          | Pro aktivitu azure machine learning update zdroje, typ aktivity je **AzureMLUpdateResource**. | Ano      |
| linkedServiceName             | Propojená služba Azure Machine Learning, která obsahuje vlastnost updateResourceEndpoint. | Ano      |
| trainedModelName              | Název modulu Trénovaný model v experimentu webové služby, který má být aktualizován | Ano      |
| trainedModelLinkedServiceName | Název propojené služby Azure Storage, která obsahuje soubor ilearner, který je odeslán operací aktualizace | Ano      |
| trainedModelFilePath          | Relativní cesta k souboru v trainedModelLinkedService reprezentovat ilearner soubor, který je nahrán operace aktualizace | Ano      |

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup

Celý proces zprovoznění rekvalifikace modelu a aktualizace prediktivních webových služeb zahrnuje následující kroky:

- Vyvolat **školení webové služby** pomocí **aktivity dávkového spuštění**. Vyvolání trénovací webové služby je stejné jako vyvolání prediktivní webové služby popsané v [části Vytvořit prediktivní kanály pomocí Azure Machine Learning a aktivity batch execution datové továrny](transform-data-using-machine-learning.md). Výstupem školicí webové služby je soubor iLearner, který můžete použít k aktualizaci prediktivní webové služby.
- Vyvolání **koncového bodu prostředku aktualizace** **prediktivní webové služby** pomocí **aktivity Aktualizovat prostředky** k aktualizaci webové služby s nově trénovaným modelem.

## <a name="azure-machine-learning-linked-service"></a>Propojená služba Azure Machine Learning

Pro výše uvedený komplexní pracovní postup, který bude fungovat, musíte vytvořit dvě propojené služby Azure Machine Learning:

1. Azure Machine Learning propojené služby na školení webové služby, tato propojená služba se používá aktivity dávkového spuštění stejným způsobem, co je uvedeno v [vytvořit prediktivní kanály pomocí Azure Machine Learning a data Factory Batch Execution aktivity](transform-data-using-machine-learning.md). Rozdíl je výstup školení webové služby je soubor iLearner, který je pak používán aktualizovat aktivitu zdrojů k aktualizaci prediktivní webové služby.
2. Propojená služba Azure Machine Learning s koncovým bodem aktualizačního prostředku prediktivní webové služby. Tato propojená služba je používána aktivitou aktualizovat prostředky k aktualizaci prediktivní webové služby pomocí souboru iLearner vráceném z výše uvedeného kroku.

Pro druhou propojenou službu Azure Machine Learning se konfigurace liší, když je vaše webová služba Azure Machine Learning klasická webová služba nebo nová webová služba. Rozdíly jsou popsány samostatně v následujících částech.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webová služba je nová webová služba Azure Resource Manager

Pokud je webová služba nový typ webové služby, která zveřejňuje koncový bod Azure Resource Manager, není nutné přidat druhý **koncový bod, než výchozí.** **UpdateResourceEndpoint** v propojené službě je formátu:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Hodnoty pro držitele míst můžete získat v adrese URL při dotazování webové služby na [portálu Webových služeb Azure Machine Learning](https://services.azureml.net/).

Nový typ koncového bodu prostředku aktualizace vyžaduje ověření instančního objektu. Pokud chcete použít ověřování o objektu zabezpečení služby, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí roli **přispěvatele** nebo **vlastníka** předplatného nebo skupiny prostředků, do které webová služba patří. Podívejte [se, jak vytvořit instanční objekt a přiřadit oprávnění ke správě prostředků Azure](../active-directory/develop/howto-create-service-principal-portal.md). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:

- ID aplikace
- Klíč aplikace
- ID tenanta

Zde je ukázka definice propojené služby:

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

Následující scénář obsahuje další podrobnosti. Má příklad pro rekvalifikaci a aktualizaci modelů Azure Machine Learning studio z kanálu Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Ukázka: Rekvalifikace a aktualizace modelu Azure Machine Learning

Tato část obsahuje ukázkový kanál, který používá **aktivitu batch execution studia Azure Machine Learning** k přeškolení modelu. Kanál také používá **Azure Machine Learning studio Aktualizace prostředků aktivity** aktualizovat model ve webové službě vyhodnocování. Část také obsahuje fragmenty JSON pro všechny propojené služby, datové sady a kanál v příkladu.

### <a name="azure-blob-storage-linked-service"></a>Propojená služba úložiště objektů blob Azure:
Azure Storage obsahuje následující data:

* údaje o školení. Vstupní data pro webovou službu školení studio Azure Machine Learning.
* iLearner. Výstup z webové služby školení studia Azure Machine Learning. Tento soubor je také vstupem do aktivity aktualizovat zdroj.

Zde je ukázka json definice propojené služby:

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Propojený koncový bod školení studia Azure Machine Learning
Následující fragment JSON definuje propojenou službu Azure Machine Learning, která odkazuje na výchozí koncový bod školicí webové služby.

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

Ve **studiu Azure Machine Learning ,** postupujte takto, chcete-li získat hodnoty pro **mlEndpoint** a **apiKey**:

1. V levé nabídce klikněte na **WEBOVÉ SLUŽBY.**
2. Klikněte na **webovou službu školení** v seznamu webových služeb.
3. Klikněte na kopírovat vedle textového pole **klíče rozhraní API.** Vložte klíč do schránky do editoru JSON datové továrny.
4. Ve **studiu Azure Machine Learning klikněte**na odkaz **BATCH EXECUTION** .
5. Zkopírujte **identifikátor URI požadavku** z části **Požadavek** a vložte jej do editoru JSON datové továrny.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Propojený servis pro Azure Machine Learning studio aktualizovatelný bod hodnocení:
Následující fragment JSON definuje propojenou službu Azure Machine Learning, která odkazuje na aktualizovatelný koncový bod vyhodnocovací webové služby.

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
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Batch Spuštění aktivity trvá trénovací data jako vstup a vytvoří soubor iLearner jako výstup. Aktivita aktualizovat prostředky pak převezme tento soubor iLearner a použít jej k aktualizaci prediktivní webové služby.

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
Další články vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
