---
title: Exportovat nebo odstraňovat data pracovního prostoru - Azure Machine Learning | Dokumentace Microsoftu
description: Ve službě Azure Machine Learning můžete exportovat nebo odstranění pracovního prostoru pomocí webu Azure portal, rozhraní příkazového řádku, sady SDK a ověřené rozhraní REST API. V tomto článku se dozvíte, jak.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 09/24/2018
ms.openlocfilehash: 20c75b55e9b02f30e90ad40ea11383b91ae0a247
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48267291"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportovat nebo odstraňovat data pracovní prostor služby Machine Learning 

Ve službě Azure Machine Learning můžete exportovat nebo odstranit pracovní prostor dat s využitím ověřeného rozhraní REST API. Tento článek vysvětluje, jak.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Ovládejte svá data pracovního prostoru
V rámci produktu data uložená pomocí služby Azure Machine Learning je dostupná pro exportu a odstranění na webu Azure portal, rozhraní příkazového řádku, sady SDK a ověřil rozhraní REST API. Telemetrická data, je přístupný prostřednictvím portálu Azure o ochraně osobních údajů. 

Osobní údaje se skládá z informací o uživateli v historii spuštění dokumenty a telemetrie záznamy některé interakcí uživatelů ve službě ve službě Azure Machine Learning Services.

## <a name="delete-workspace-data-with-the-rest-api"></a>Odstranit pracovní prostor dat pomocí rozhraní REST API 

Pokud chcete odstranit data, můžete provést následující volání rozhraní API s operací HTTP DELETE. Tyto oprávnění tak, že `Authorization: Bearer <arm-token>` hlavičky v požadavku, ve kterém `<arm-token>` je přístupový token AAD pro `https://management.core.windows.net/` koncového bodu.  

Zjistěte, jak to získání tokenu a volání koncových bodů Azure, najdete v článku [dokumentace k rozhraní REST API služby Azure](https://docs.microsoft.com/rest/api/azure/).  

V následujících příkladech nahraďte text v {} pomocí názvů instancí, které určují přidružený prostředek.

### <a name="delete-an-entire-workspace"></a>Odstranit celý pracovní prostor

Pomocí tohoto volání můžete odstranit celý pracovní prostor.  
> [!WARNING]
> Odstraní všechny informace a pracovní prostor nebude možné použít.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Odstranit modely

Pomocí tohoto volání můžete získat seznam modelů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Jednotlivé modely můžete odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Odstranění prostředků

Pomocí tohoto volání můžete získat seznam prostředků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Jednotlivé prostředky můžete odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Odstranit Image

Pomocí tohoto volání můžete získat seznam obrázků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Jednotlivé Image můžete odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Odstranění služby

Pomocí tohoto volání můžete získat seznam služeb a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Jednotlivé služby můžete odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Export dat služby pomocí rozhraní REST API

Abyste mohli exportovat data, můžete provést následující volání rozhraní API s operací HTTP GET. Tyto oprávnění tak, že `Authorization: Bearer <arm-token>` hlavičky v požadavku, ve kterém `<arm-token>` je přístupový token AAD pro koncový bod `https://management.core.windows.net/`  

Zjistěte, jak to získání tokenu a volání koncových bodů Azure, najdete v článku [dokumentace k rozhraní REST API služby Azure](https://docs.microsoft.com/rest/api/azure/).   

V následujících příkladech nahraďte text v {} pomocí názvů instancí, které určují přidružený prostředek.

### <a name="export-workspace-information"></a>Exportujte informace o pracovním prostoru

Pomocí tohoto volání můžete získat seznam všech pracovních prostorů:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Je možné získat informace o jednotlivých pracovního prostoru:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Informace o exportu výpočetní prostředky

Součástí všech výpočetních cíle připojený k pracovnímu prostoru je možné získat:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Je možné získat informace o cílové jeden výpočetní prostředí:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Export dat historie spuštění
Pomocí tohoto volání můžete získat seznam všech experimentů a jejich informace:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Všechna spuštění pro konkrétní experimentu je možné získat:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Historii které položky můžete získat výčtem spuštění:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Všechny metriky spuštění experimentu je možné získat:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Je možné získat jednu metriku spuštění:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Export artefaktů

Pomocí tohoto volání můžete získat seznam artefaktů a jejich cesty:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Export oznámení

Pomocí tohoto volání můžete získat seznam uložených úlohy:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Je možné získat oznámení pro jednu úlohu:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Export úložiště dat

Pomocí tohoto volání můžete získat seznam úložišť dat:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Je možné získat jednotlivé datové úložiště:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportovat modely

Pomocí tohoto volání můžete získat seznam modelů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Je možné získat jednotlivé modely:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Export prostředků

Pomocí tohoto volání můžete získat seznam prostředků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Je možné získat jednotlivé prostředky:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Export obrázků

Pomocí tohoto volání můžete získat seznam obrázků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Je možné získat jednotlivé Image:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Export služby

Pomocí tohoto volání můžete získat seznam služeb a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Je možné získat jednotlivé služby:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Export kanálu experimentů

Je možné získat jednotlivé pokusy:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Export kanálu grafy

Je možné získat jednotlivé grafy:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Export modulů kanálu

Moduly je možné získat:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Export šablon kanálu

Šablony se dají získat pomocí:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Export kanálu zdroje dat

Je možné získat zdroje dat:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}
