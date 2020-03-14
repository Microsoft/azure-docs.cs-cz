---
title: Exportovat nebo odstraňovat data pracovního prostoru
titleSuffix: Azure Machine Learning
description: Naučte se exportovat nebo odstranit svůj pracovní prostor pomocí rozhraní API pro Azure Machine Learning Studio, CLI, SDK a ověřovaného rozhraní REST API.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 4abef0146b4bf0cfaa254d196b0ca68f0d8ac883
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218292"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportovat nebo odstraňovat data pracovní prostor služby Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ve službě Azure Machine Learning můžete exportovat nebo odstranit pracovní prostor dat s využitím ověřeného rozhraní REST API. Tento článek vysvětluje, jak.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Ovládejte svá data pracovního prostoru

Data v produktu uložená pomocí Azure Machine Learning jsou dostupná pro export a odstranění prostřednictvím Azure Machine Learning studia, CLI, sady SDK a ověřovaných rozhraní REST API. Telemetrická data, je přístupný prostřednictvím portálu Azure o ochraně osobních údajů. 

V Azure Machine Learning osobní údaje sestávají z informací o uživatelích v historii spouštění a záznamů telemetrie některých interakcí uživatelů se službou.

## <a name="delete-workspace-data-with-the-rest-api"></a>Odstranit pracovní prostor dat pomocí rozhraní REST API

Pokud chcete odstranit data, můžete provést následující volání rozhraní API s operací HTTP DELETE. Jedná se o autorizaci pomocí hlavičky `Authorization: Bearer <arm-token>` v žádosti, kde `<arm-token>` je přístupový token AAD pro `https://management.core.windows.net/` koncový bod.  

Informace o tom, jak tento token získat a zavolat koncovým bodům Azure, najdete v tématu [použití REST ke správě prostředků ml](how-to-manage-rest.md) a [dokumentace k Azure REST API](https://docs.microsoft.com/rest/api/azure/).  

V níže uvedených příkladech nahraďte text v {} názvy instancí, které určují přidružený prostředek.

### <a name="delete-an-entire-workspace"></a>Odstranit celý pracovní prostor

Pomocí tohoto volání můžete odstranit celý pracovní prostor.  
> [!WARNING]
> Odstraní všechny informace a pracovní prostor nebude možné použít.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Odstranit modely

Pomocí tohoto volání můžete získat seznam modelů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Jednotlivé modely můžete odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Odstranění prostředků

Pomocí tohoto volání můžete získat seznam prostředků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Jednotlivé prostředky můžete odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Odstranit Image

Pomocí tohoto volání můžete získat seznam obrázků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Jednotlivé Image můžete odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Odstranění služby

Pomocí tohoto volání můžete získat seznam služeb a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Jednotlivé služby můžete odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Export dat služby pomocí rozhraní REST API

Abyste mohli exportovat data, můžete provést následující volání rozhraní API s operací HTTP GET. Jedná se o autorizaci pomocí hlavičky `Authorization: Bearer <arm-token>` v žádosti, kde `<arm-token>` je přístupový token AAD pro koncový bod `https://management.core.windows.net/`  

Informace o tom, jak tento token získat a zavolat koncovým bodům Azure, najdete v tématu [použití REST ke správě prostředků ml](how-to-manage-rest.md) a [dokumentace k Azure REST API](https://docs.microsoft.com/rest/api/azure/)..   

V níže uvedených příkladech nahraďte text v {} názvy instancí, které určují přidružený prostředek.

### <a name="export-workspace-information"></a>Exportujte informace o pracovním prostoru

Pomocí tohoto volání můžete získat seznam všech pracovních prostorů:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Je možné získat informace o jednotlivých pracovního prostoru:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Informace o exportu výpočetní prostředky

Součástí všech výpočetních cíle připojený k pracovnímu prostoru je možné získat:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Je možné získat informace o cílové jeden výpočetní prostředí:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

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

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Je možné získat jednotlivé modely:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Export prostředků

Pomocí tohoto volání můžete získat seznam prostředků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Je možné získat jednotlivé prostředky:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Export obrázků

Pomocí tohoto volání můžete získat seznam obrázků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Je možné získat jednotlivé Image:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Export služby

Pomocí tohoto volání můžete získat seznam služeb a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Je možné získat jednotlivé služby:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

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

## <a name="delete-assets-in-the-designer"></a>Odstranit prostředky v Návrháři

V návrháři, kde jste vytvořili experiment, odstraňte jednotlivé prostředky:

1. Přejít k Návrháři

    ![Odstranění prostředků](./media/how-to-export-delete-data/delete-experiment.png)

1. V seznamu vyberte koncept jednotlivého kanálu, který chcete odstranit.

1. Vyberte **Odstranit**.

### <a name="delete-datasets-in-the-designer"></a>Odstranění datových sad v Návrháři

Pokud chcete v Návrháři odstranit datové sady, pomocí Azure Portal nebo Průzkumník služby Storage přejděte na propojené účty úložiště a odstraňte datové sady. Zrušení registrace datových sad v Návrháři odebere pouze referenční bod v úložišti.

## <a name="export-data-in-the-designer"></a>Exportovat data v Návrháři

V návrháři, kde jste vytvořili experiment, exportujte data, která jste přidali:

1. Na levé straně vyberte **datové sady**.

1. V seznamu vyberte datovou sadu, kterou chcete exportovat.

    ![Stáhnout data](./media/how-to-export-delete-data/unregister-dataset.png)
