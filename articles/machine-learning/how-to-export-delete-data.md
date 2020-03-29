---
title: Export nebo odstranění dat pracovního prostoru
titleSuffix: Azure Machine Learning
description: Zjistěte, jak exportovat nebo odstranit pracovní prostor pomocí azure machine learningstudio, CLI, SDK a ověřených rest api.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218292"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Export nebo odstranění dat pracovního prostoru služby Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V Azure Machine Learning můžete exportovat nebo odstranit data pracovního prostoru pomocí ověřeného rozhraní REST API. Tento článek vám řekne, jak na to.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Ovládání dat pracovního prostoru

Data v produktu uložená službou Azure Machine Learning jsou k dispozici pro export a odstranění prostřednictvím azure machine learningového studia, rozhraní CLI, sady SDK a ověřených rozhraní REST API. K telemetrickým datům se dá přistupovat prostřednictvím portálu ochrany osobních údajů Azure. 

V Azure Machine Learning se osobní data skládají z informací o uživateli v dokumentech historie spuštění a telemetrických záznamů některých interakcí uživatelů se službou.

## <a name="delete-workspace-data-with-the-rest-api"></a>Odstranění dat pracovního prostoru pomocí rozhraní REST API

Chcete-li odstranit data, lze pomocí slovesa HTTP DELETE provést následující volání rozhraní API. Ty jsou autorizovány tím, `Authorization: Bearer <arm-token>` že `<arm-token>` záhlaví v požadavku, kde `https://management.core.windows.net/` je přístupový token AAD pro koncový bod.  

Informace o tom, jak získat tento token a volat koncové body Azure, najdete [v tématu použití REST ke správě prostředků ML](how-to-manage-rest.md) a dokumentace rozhraní AZURE REST [API](https://docs.microsoft.com/rest/api/azure/).  

V následujících příkladech nahraďte text názvy {} instancí, které určují přidružený prostředek.

### <a name="delete-an-entire-workspace"></a>Odstranění celého pracovního prostoru

Toto volání slouží k odstranění celého pracovního prostoru.  
> [!WARNING]
> Všechny informace budou odstraněny a pracovní prostor již nebude použitelný.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Odstranění modelů

Pomocí tohoto hovoru můžete získat seznam modelů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Jednotlivé modely lze odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Odstranění datových zdrojů

Pomocí tohoto hovoru můžete získat seznam datových zdrojů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Jednotlivé datové zdroje lze odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Odstranění imagí

Pomocí tohoto hovoru můžete získat seznam obrázků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Jednotlivé obrázky lze odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Odstranit služby

Pomocí tohoto hovoru můžete získat seznam služeb a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Jednotlivé služby lze odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Export dat služby pomocí rozhraní REST API

Chcete-li exportovat data, lze pomocí slovesa HTTP GET provést následující volání rozhraní API. Ty jsou autorizovány tím, `Authorization: Bearer <arm-token>` že `<arm-token>` záhlaví v požadavku, kde je přístupový token AAD pro koncový bod`https://management.core.windows.net/`  

Informace o tom, jak získat tento token a volat koncové body Azure, najdete [v tématu použití REST ke správě prostředků ML](how-to-manage-rest.md) a dokumentace rozhraní AZURE REST [API](https://docs.microsoft.com/rest/api/azure/)..   

V následujících příkladech nahraďte text názvy {} instancí, které určují přidružený prostředek.

### <a name="export-workspace-information"></a>Export informací o pracovním prostoru

Pomocí tohoto volání můžete získat seznam všech pracovních prostorů:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Informace o jednotlivém pracovním prostoru lze získat pomocí:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Exportovat informace o výpočetních výkonech

Všechny výpočetní cíle připojené k pracovnímu prostoru lze získat pomocí:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Informace o jednom výpočetním cíli lze získat pomocí:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Exportovat data historie běhu

Pomocí tohoto hovoru získáte seznam všech experimentů a jejich informací:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Všechny běhy pro konkrétní experiment lze získat pomocí:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Položky historie spuštění lze získat pomocí:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Všechny metriky spuštění experimentu lze získat pomocí:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Metriku jednoho spuštění lze získat pomocí:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Export artefaktů

Pomocí tohoto volání získat seznam artefaktů a jejich cesty:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Oznámení o vývozu

Pomocí tohoto volání můžete získat seznam uložených úloh:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Oznámení pro jeden úkol lze získat pomocí:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Export úložišť dat

Toto volání slouží k získání seznamu úložišť dat:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Jednotlivá úložiště dat lze získat pomocí:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Export modelů

Pomocí tohoto hovoru můžete získat seznam modelů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Jednotlivé modely lze získat:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Exportovat majetek

Pomocí tohoto hovoru můžete získat seznam datových zdrojů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Jednotlivá aktiva lze získat:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Export obrazů

Pomocí tohoto hovoru můžete získat seznam obrázků a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Jednotlivé obrázky lze získat:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Exportní služby

Pomocí tohoto hovoru můžete získat seznam služeb a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Jednotlivé služby lze získat prostřednictvím:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Exportovat experimenty s potrubím

Jednotlivé experimenty lze získat:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Grafy exportu potrubí

Jednotlivé grafy lze získat na základě:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportovat potrubní moduly

Moduly lze získat:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportovat šablony kanálu

Šablony lze získat pomocí:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportovat zdroje dat kanálu

Zdroje dat lze získat pomocí:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Odstranění datových zdrojů v návrháři

V návrháři, ve kterém jste experiment vytvořili, odstraňte jednotlivé datové zdroje:

1. Přejít na návrháře

    ![Odstranění datových zdrojů](./media/how-to-export-delete-data/delete-experiment.png)

1. V seznamu vyberte jednotlivé pracovní verze kanálu, kterou chcete odstranit.

1. Vyberte **Odstranit**.

### <a name="delete-datasets-in-the-designer"></a>Odstranění datových sad v návrháři

Pokud chcete odstranit datové sady v návrháři, přejděte na připojené účty úložiště pomocí portálu Azure nebo Průzkumníka úložiště a odstraňte tam datové sady. Zrušení registrace datových sad v návrháři pouze odebere referenční bod v úložišti.

## <a name="export-data-in-the-designer"></a>Export dat v návrháři

V návrháři, ve kterém jste experiment vytvořili, exportujte data, která jste přidali:

1. Vlevo vyberte **Datové sady**.

1. V seznamu vyberte datovou sadu, kterou chcete exportovat.

    ![Stahování souborů](./media/how-to-export-delete-data/unregister-dataset.png)
