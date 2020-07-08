---
title: Exportovat nebo odstranit data pracovního prostoru
titleSuffix: Azure Machine Learning
description: Naučte se exportovat nebo odstranit svůj pracovní prostor pomocí rozhraní API pro Azure Machine Learning Studio, CLI, SDK a ověřovaného rozhraní REST API.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.custom: seodec18
ms.openlocfilehash: 78e8d02ecaaf57c457a0eaeed86d392cd6372fa9
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963695"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Export nebo odstranění dat pracovního prostoru služby Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V Azure Machine Learning můžete data pracovního prostoru exportovat nebo odstranit pomocí ověřeného REST API. V tomto článku se dozvíte, jak.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Řízení dat pracovního prostoru

Data v produktu uložená pomocí Azure Machine Learning jsou dostupná pro export a odstranění prostřednictvím Azure Machine Learning studia, CLI, sady SDK a ověřovaných rozhraní REST API. K datům telemetrie se dá dostat prostřednictvím portálu ochrany osobních údajů Azure. 

V Azure Machine Learning osobní údaje sestávají z informací o uživatelích v historii spouštění a záznamů telemetrie některých interakcí uživatelů se službou.

## <a name="delete-workspace-data-with-the-rest-api"></a>Odstranit data pracovního prostoru pomocí REST API

Aby bylo možné odstranit data, lze pomocí příkazu HTTP DELETE provést následující volání rozhraní API. Jedná se o autorizaci pomocí `Authorization: Bearer <arm-token>` hlavičky v žádosti, kde `<arm-token>` je přístupový token AAD pro `https://management.core.windows.net/` koncový bod.  

Informace o tom, jak tento token získat a zavolat koncovým bodům Azure, najdete v tématu [použití REST ke správě prostředků ml](how-to-manage-rest.md) a [dokumentace k Azure REST API](https://docs.microsoft.com/rest/api/azure/).  

V níže uvedených příkladech nahraďte text v {} názvech instancí, které určují přidružený prostředek.

### <a name="delete-an-entire-workspace"></a>Odstranit celý pracovní prostor

Pomocí tohoto volání můžete odstranit celý pracovní prostor.  
> [!WARNING]
> Všechny informace se odstraní a pracovní prostor už nebude možné použít.

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="delete-models"></a>Odstranit modely

Pomocí tohoto volání získáte seznam modelů a jejich ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

Jednotlivé modely lze odstranit pomocí:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="delete-assets"></a>Odstranit prostředky

Pomocí tohoto volání získat seznam assetů a jejich ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

Jednotlivé prostředky lze odstranit pomocí:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="delete-images"></a>Odstranění imagí

Pomocí tohoto volání získat seznam imagí a jejich ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

Jednotlivé Image je možné odstranit pomocí:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="delete-services"></a>Odstranit služby

Pomocí tohoto volání získáte seznam služeb a jejich ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

Jednotlivé služby je možné odstranit pomocí:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

## <a name="export-service-data-with-the-rest-api"></a>Export dat služby pomocí REST API

Aby bylo možné exportovat data, lze pomocí příkazu HTTP GET provést následující volání rozhraní API. Jedná se o autorizaci pomocí `Authorization: Bearer <arm-token>` hlavičky v žádosti, kde `<arm-token>` je přístupový token AAD pro koncový bod.`https://management.core.windows.net/`  

Informace o tom, jak tento token získat a zavolat koncovým bodům Azure, najdete v tématu [použití REST ke správě prostředků ml](how-to-manage-rest.md) a [dokumentace k Azure REST API](https://docs.microsoft.com/rest/api/azure/)..   

V níže uvedených příkladech nahraďte text v {} názvech instancí, které určují přidružený prostředek.

### <a name="export-workspace-information"></a>Exportovat informace o pracovním prostoru

Pomocí tohoto volání získáte seznam všech pracovních prostorů:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01`

Informace o jednotlivých pracovních prostorech lze získat pomocí:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="export-compute-information"></a>Exportovat výpočetní informace

Všechny výpočetní cíle připojené k pracovnímu prostoru lze získat:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01`

Informace o jednom cílovém výpočetním prostředí lze získat:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01`

### <a name="export-run-history-data"></a>Exportovat data historie spuštění

Pomocí tohoto volání získáte seznam všech experimentů a jejich informace:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments`

Všechna spuštění pro konkrétní experiment lze získat pomocí:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs`

Položky historie spuštění lze získat:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId}`

Všechny metriky spuštění pro experiment lze získat:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics`

Jednu metriku spuštění může získat:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}`

### <a name="export-artifacts"></a>Exportovat artefakty

Pomocí tohoto volání získáte seznam artefaktů a jejich cest:

`https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}`

### <a name="export-notifications"></a>Exportovat oznámení

Pomocí tohoto volání získáte seznam uložených úloh:

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks`

Oznámení pro jeden úkol může získat:

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}`

### <a name="export-data-stores"></a>Exportovat úložiště dat

Pomocí tohoto volání získáte seznam úložišť dat:

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores`

Jednotlivá úložiště dat lze získat:

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}`

### <a name="export-models"></a>Export modelů

Pomocí tohoto volání získáte seznam modelů a jejich ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

Jednotlivé modely lze získat pomocí:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="export-assets"></a>Exportovat prostředky

Pomocí tohoto volání získat seznam assetů a jejich ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

Jednotlivé prostředky lze získat:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="export-images"></a>Exportovat obrázky

Pomocí tohoto volání získat seznam imagí a jejich ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

Jednotlivé obrázky lze získat:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="export-services"></a>Exportovat služby

Pomocí tohoto volání získáte seznam služeb a jejich ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

Jednotlivé služby může získat:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

### <a name="export-pipeline-experiments"></a>Exportovat experimenty kanálu

Jednotlivé experimenty lze získat:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}`

### <a name="export-pipeline-graphs"></a>Exportovat grafy kanálu

Jednotlivé grafy lze získat pomocí:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}`

### <a name="export-pipeline-modules"></a>Exportovat moduly kanálu

Moduly lze získat pomocí:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}`

### <a name="export-pipeline-templates"></a>Export šablon kanálu

Šablony lze získat pomocí:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}`

### <a name="export-pipeline-data-sources"></a>Export datových zdrojů kanálu

Zdroje dat lze získat pomocí:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}`

## <a name="delete-assets-in-the-designer"></a>Odstranit prostředky v Návrháři

V návrháři, kde jste vytvořili experiment, odstraňte jednotlivé prostředky:

1. Přejít k Návrháři

    ![Odstranit prostředky](./media/how-to-export-delete-data/delete-experiment.png)

1. V seznamu vyberte koncept jednotlivého kanálu, který chcete odstranit.

1. Vyberte **Odstranit**.

### <a name="delete-datasets-in-the-designer"></a>Odstranění datových sad v Návrháři

Pokud chcete v Návrháři odstranit datové sady, pomocí Azure Portal nebo Průzkumník služby Storage přejděte na propojené účty úložiště a odstraňte datové sady. Zrušení registrace datových sad v Návrháři odebere pouze referenční bod v úložišti.

