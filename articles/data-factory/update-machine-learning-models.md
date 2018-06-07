---
title: Aktualizace modelů machine learning pomocí Azure Data Factory | Microsoft Docs
description: Popisuje postup vytvoření vytvořit prediktivní kanály pomocí Azure Data Factory a strojového učení
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 931c6b2cc0230e4605089dfc1eb0764aa61ec7b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620458"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Aktualizovat modely Azure Machine Learning pomocí aktivita prostředku aktualizace
Tento článek doplňuje hlavní Azure Data Factory – článek integrace Azure Machine Learning: [vytvořit prediktivní kanály pomocí Azure Machine Learning a Azure Data Factory](transform-data-using-machine-learning.md). Pokud jste tak již neučinili, přečtěte si hlavní článek než si přečtete prostřednictvím tohoto článku. 

## <a name="overview"></a>Přehled
Jako součást procesu zprovozňování modely Azure Machine Learning je váš model vycvičena a uložit. Potom ji používáte k vytvoření predicative webové služby. Webové služby, pak mohou být využívány v weby, řídicí panely a mobilní aplikace.

Obvykle nejsou statické modely, které vytvoříte pomocí Machine Learning. Jakmile nová data k dispozici, nebo když příjemce rozhraní API má svá vlastní data musí být retrained modelu. Odkazovat na [Přeučování Model strojového učení](../machine-learning/machine-learning-retrain-machine-learning-model.md) podrobnosti o tom, jak můžete přeučování model v Azure Machine Learning. 

Retraining může dojít, často. Aktivita provedení dávky a aktivita prostředku aktualizace můžete zprovoznit model Azure Machine Learning retraining a aktualizaci prediktivní webové služby pomocí služby Data Factory. 

Následující obrázek znázorňuje vztah mezi školení a prediktivní webové služby. 

![Webové služby](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Aktivita prostředku aktualizace Azure Machine Learning 

Následující fragment kódu JSON definuje Azure Machine Learning dávkového spuštění aktivity.

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
| description                   | Popisuje, jakým způsobem aktivita naloží text.  | Ne       |
| type                          | Aktivity prostředků Azure Machine Learning aktualizace, je typ aktivity **AzureMLUpdateResource**. | Ano      |
| linkedServiceName             | Azure Machine Learning propojená služba, která obsahuje vlastnost updateResourceEndpoint. | Ano      |
| trainedModelName              | Název modulu Trained Model v experimentu webové služby, který má být aktualizován | Ano      |
| trainedModelLinkedServiceName | Název propojené služby Azure Storage podržíte soubor ilearner, který je odeslaný operace aktualizace | Ano      |
| trainedModelFilePath          | Relativní cesta k souboru v trainedModelLinkedService představující soubor ilearner, který je odeslaný operace aktualizace | Ano      |


## <a name="end-to-end-workflow"></a>Ucelený pracovní postup

Celý proces zprovozňování přeučení modelů a aktualizace prediktivní webové služby zahrnuje následující kroky: 

- Vyvolání **cvičení webové služby** pomocí **aktivita provedení dávky**. Vyvolání školení webové služby je stejný jako vyvolání webové služby prediktivní popsané v [vytvořit prediktivní kanály pomocí Azure Machine Learning a Data Factory dávkového spuštění aktivity](transform-data-using-machine-learning.md). Výstup školení webové služby je soubor iLearner, který můžete použít k aktualizaci prediktivní webové služby. 
- Vyvolání **aktualizovat koncový bod prostředků** z **prediktivní webové služby** pomocí **aktivita prostředku aktualizace** aktualizace webové služby s nově naučeného modelu. 

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning propojené služby

Pro výše uvedené pracovní postup začátku do konce postup musíte vytvořit dvě Azure Machine Learning propojené služby: 

1. Azure Machine Learning propojené služby k webové službě školení, tato propojená služba se používá aktivitou Batch Execution stejným způsobem jako co je uvedeno v [vytvořit prediktivní kanály pomocí Azure Machine Learning a dávky objekt pro vytváření dat Aktivita provedení](transform-data-using-machine-learning.md). Rozdílem je, že výstup webové služby školení je soubor iLearner, které se pak používá aktivitou aktualizovat prostředek k aktualizaci prediktivní webovou službu. 
2. Služby Azure Machine Learning propojené ke koncovému bodu aktualizace prostředků prediktivní webové služby. Tato propojená služba aktivitou aktualizovat prostředků slouží k aktualizaci prediktivní webovou službu pomocí souboru iLearner vrátil výše krok. 

Druhý Azure Machine Learning propojené služby se liší konfigurace, když se vaše webová služba Azure Machine Learning classic webové služby nebo novou webovou službu. Rozdíly jsou samostatně popsané v následujících částech. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webová služba je novou webovou službu správce prostředků Azure 

Pokud webová služba je nový typ webové služby, který zveřejňuje koncový bod Azure Resource Manager, není potřeba přidat druhý **jiné než výchozí** koncový bod. **UpdateResourceEndpoint** v propojené službě je ve formátu: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Můžete získat hodnoty pro zástupného v adrese URL při dotazování na webovou službu [portálu služby Azure Machine Learning webové](https://services.azureml.net/). 

Nový typ prostředku aktualizace koncového bodu vyžaduje ověření objektu služby. Použít objekt zabezpečení ověřování služby, registrace entitu aplikace v Azure Active Directory (Azure AD) a udělit mu **Přispěvatel** nebo **vlastníka** role předplatné nebo prostředek skupiny where Webová služba patří. Viz [postup vytvoření instančního objektu a přiřaďte oprávnění ke správě prostředků Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md). Poznamenejte si následující hodnoty, které můžete použít k definování propojené služby:

- ID aplikace
- Klíč aplikace 
- ID tenanta

Zde je ukázka propojené definice služby: 

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
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
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

V následujícím scénáři najdete další podrobnosti. Obsahuje příklad retraining a aktualizace Azure ML modely z kanál služby Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Ukázka: Retraining a aktualizace model Azure Machine Learning

Tato část obsahuje ukázkový kanál, který používá **aktivita provedení dávky Azure ML** k přeučování modelu. Kanál také používá **aktivita prostředku aktualizace Azure ML** k aktualizaci modelu v rámci vyhodnocování webové služby. Tato část taky poskytuje fragmenty kódu JSON pro všechny propojené služby, datové sady a kanál v příkladu.

### <a name="azure-blob-storage-linked-service"></a>Propojená služba Azure Blob storage:
Azure Storage obsahuje následující data:

* Cvičná data. Vstupní data pro webovou službu Azure ML školení.  
* reprezentuje soubor iLearner. Výstup z webové služby Azure ML školení. Tento soubor je také vstup aktivita prostředku aktualizace.  

Zde je ukázka definici JSON propojené služby:

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Propojené služby pro koncový bod Azure ML školení
Následující fragment kódu JSON definuje služby Azure Machine Learning propojené, který ukazuje na výchozí koncový bod webové služby školení.

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

V **Azure ML Studio**, proveďte následující kroky k získání hodnot pro **mlEndpoint** a **apiKey**:

1. Klikněte na tlačítko **webové služby** v levé nabídce.
2. Klikněte **cvičení webové služby** v seznamu webové služby.
3. Klikněte na možnost Kopírovat do **klíč rozhraní API** textové pole. Vložte klíč do schránky do editoru Data Factory JSON.
4. V **Azure ML studio**, klikněte na tlačítko **BATCH EXECUTION** odkaz.
5. Kopírování **URI požadavku** z **požadavku** části a vložte ho do editoru Data Factory JSON.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Propojená služba Azure ML aktualizovat vyhodnocování koncového bodu:
Následující fragment kódu JSON definuje služby Azure Machine Learning, která je propojená, který odkazuje na aktualizovat koncový bod vyhodnocování webové služby.  

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
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Aktivita provedení dávky trvá Cvičná data jako vstup a vytvoří soubor iLearner jako výstup. Aktivita prostředku aktualizace potom trvá tento soubor iLearner a použít ho k aktualizaci prediktivní webovou službu. 

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
## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Pig aktivity](transform-data-using-hadoop-pig.md)
* [Činnost MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streamované aktivitě](transform-data-using-hadoop-streaming.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
