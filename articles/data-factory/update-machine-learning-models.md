---
title: Aktualizace modelů strojového učení pomocí Azure Data Factory | Dokumentace Microsoftu
description: Popisuje, jak vytvořit vytváření prediktivních kanálů pomocí služby Azure Data Factory a strojové učení
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
ms.openlocfilehash: caf3ecb64d0bdb5771b2fde705fdcbffdffccacb
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969297"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Aktualizace modelů Azure Machine Learning s využitím aktivity aktualizace prostředku
Tento článek doplňuje hlavní Azure Data Factory – článek integrace Azure Machine Learning: [Vytváření prediktivních kanálů pomocí Azure Machine Learning a Azure Data Factory](transform-data-using-machine-learning.md). Pokud jste tak již neučinili, přečtěte si v hlavním článku před přečtení tohoto článku. 

## <a name="overview"></a>Přehled
Jako součást procesu až po zprovoznění modelů Azure Machine Learning je váš model školení a uložen. Pak použijete ho k vytvoření prediktivní webové služby. Webová služba může být potom používán webové stránky, řídicí panely a mobilních aplikací.

Obvykle nejsou statické modely, které vytvoříte pomocí služby Machine Learning. K dispozici nová data nebo když příjemce rozhraní API má svá vlastní data musí být retrained modelu. Odkazovat na [Přeučování Model strojového učení](../machine-learning/machine-learning-retrain-machine-learning-model.md) podrobné informace o tom, jak programovém přeučení modelů ve službě Azure Machine Learning. 

Přeškolení dochází často. Aktivita provedení dávky a aktivita prostředku aktualizace které můžete model operacionalizovat za Azure Machine Learning přetrénování a aktualizaci prediktivní webové služby pomocí služby Data Factory. 

Následující obrázek znázorňuje vztah mezi trénovací a prediktivní webové služby. 

![Webové služby](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Aktivita prostředku aktualizace Azure Machine Learning 

Následující fragment kódu JSON definuje aktivita provedení dávky služby Machine Learning Azure.

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
| description                   | Text popisující, jakým způsobem aktivita naloží.  | Ne       |
| type                          | Azure Machine Learning aktualizace prostředku aktivity, typ aktivity je **AzureMLUpdateResource**. | Ano      |
| linkedServiceName             | Azure Machine Learning propojenou službu, která obsahuje vlastnost updateResourceEndpoint. | Ano      |
| Hodnota trainedModelName              | Název modulu Trained Model v experimentu webové služby, který má být aktualizován | Ano      |
| trainedModelLinkedServiceName | Název propojené služby Azure Storage drží soubor ilearner, který nahraje operace aktualizace | Ano      |
| trainedModelFilePath          | Relativní cesta k souboru v trainedModelLinkedService představující soubor ilearner, který nahraje operace aktualizace | Ano      |


## <a name="end-to-end-workflow"></a>Ucelený pracovní postup

Celý proces až po zprovoznění přetrénování modelu a aktualizace prediktivní webové služby zahrnuje následující kroky: 

- Vyvolat **školení webová služba** pomocí **aktivita provedení dávky služby**. Vyvolání školení webová služba je stejný jako vyvolání prediktivní webové služby je popsáno v [vytváření prediktivních kanálů pomocí Azure Machine Learning a provedení dávky služby Data Factory aktivity](transform-data-using-machine-learning.md). Výstup školení webová služba je soubor iLearner, který vám umožní aktualizovat prediktivní webové služby. 
- Vyvolat **aktualizovat koncový bod prostředku** z **prediktivní webová služba** pomocí **aktivita prostředku aktualizace** jak aktualizovat webovou službu s nově trénovaného modelu. 

## <a name="azure-machine-learning-linked-service"></a>Služba Azure Machine Learning propojený

Pro výše uvedené pracovní postup začátku do konce pro práci budete muset vytvořit dvě služby Azure Machine Learning propojený: 

1. Azure Machine Learning propojenou službu, která webová služba školení, tuto propojenou službu používá aktivita provedení dávky služby stejným způsobem jako co je uvedeno v [vytváření prediktivních kanálů pomocí služeb Azure Machine Learning a Data Factory Batch Aktivita provedení](transform-data-using-machine-learning.md). Rozdíl je, že výstupem školení webová služba je soubor iLearner, který se potom využijí aktivita prostředku aktualizace aktualizovat prediktivní webové služby. 
2. Služby Azure Machine Learning propojený na koncový bod aktualizace prostředku prediktivní webové služby. Tuto propojenou službu používá aktivita prostředku aktualizace aktualizovat prediktivní webové služby pomocí soubor iLearner, který vrátil uvedeného kroku. 

Pro službu Azure Machine Learning propojený druhý se liší konfigurace, pokud vaše webové služby Azure Machine Learning je klasických webových služeb nebo novou webovou službu. Rozdíly jsou popsány odděleně v následujících částech. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webová služba je nové webové služby Azure Resource Manageru 

Pokud webová služba je nový typ webová služba, která zpřístupňuje koncový bod Azure Resource Manageru, není potřeba přidat druhý **nevýchozí** koncového bodu. **UpdateResourceEndpoint** v propojené službě je ve formátu: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Můžete získat hodnoty místo zástupné znaky v adrese URL při dotazování na webovou službu [Azure Machine Learning Web Services portálu](https://services.azureml.net/). 

Nový typ koncový bod aktualizace prostředku vyžaduje ověřování instančních objektů. Použít ověřování instančních objektů, zaregistrujte aplikaci entity ve službě Azure Active Directory (Azure AD) a jí udělit **Přispěvatel** nebo **vlastníka** pro dané předplatné nebo prostředek skupiny where Webová služba patří. Viz [vytvoření instančního objektu a přiřazení oprávnění ke správě prostředků Azure](../active-directory/develop/howto-create-service-principal-portal.md). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

- ID aplikace
- Klíč aplikace 
- ID tenanta

Tady je ukázka propojené definice služby: 

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

V následujícím scénáři najdete další podrobnosti. Obsahuje příklad přetrénování a aktualizace modelů Azure ML z kanálu služby Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Ukázka: Přeškolení a aktualizuje model ve službě Azure Machine Learning

Tato část obsahuje ukázkový kanál, který používá **aktivita provedení dávky Azure ML** k přeučování modelu. Kanál také používá **aktivita prostředku aktualizace Azure ML** k aktualizaci modelu v hodnoticí webové služby. V části také poskytuje fragmentů JSON propojené služby, datové sady a kanál v tomto příkladu.

### <a name="azure-blob-storage-linked-service"></a>Propojená služba Azure Blob storage:
Azure Storage obsahuje následující data:

* Cvičná data. Vstupní data webové služby Azure ML školení.  
* reprezentuje soubor iLearner. Výstup z webové služby Azure ML školení. Tento soubor je také vstup do aktivity aktualizace prostředku.  

Tady je ukázková definice JSON propojené služby:

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Propojené služby pro koncový bod školení Azure ML
Následující fragment kódu JSON definuje služby Azure Machine Learning propojený, který odkazuje na výchozí koncový bod webové služby, školení.

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

V **Azure ML Studio**, proveďte kroky k získání hodnot pro **mlEndpoint** a **apiKey**:

1. Klikněte na tlačítko **webových služeb** v nabídce vlevo.
2. Klikněte na tlačítko **školení webová služba** v seznamu webových služeb.
3. Klikněte na tlačítko kopírování vedle **klíč rozhraní API** textového pole. Vložte klíč do schránky do editoru JSON služby Data Factory.
4. V **Azure ML studio**, klikněte na tlačítko **BATCH EXECUTION** odkaz.
5. Kopírovat **Request URI** z **žádosti** části a vložte ho do editoru JSON služby Data Factory.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Propojené služby pro Azure ML aktualizovat bodovací koncový bod:
Následující fragment kódu JSON definuje služby Azure Machine Learning propojený, odkazující na aktualizovat koncový bod hodnoticí webové služby.  

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
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Aktivita provedení dávky služby trvá trénovací data jako vstup a vytvoří soubor iLearner jako výstup. Aktivita aktualizace prostředku pak má tento soubor iLearner a pomocí něj aktualizovat prediktivní webové služby. 

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
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
