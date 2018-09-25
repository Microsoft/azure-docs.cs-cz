---
title: Export nebo odstranění služby experimentování ve službě nebo model správy dat – Azure Machine Learning | Dokumentace Microsoftu
description: Ve službě Azure Machine Learning můžete exportovat nebo odstranit účet data související se správou služby experimentování ve službě nebo model pomocí webu Azure portal, rozhraní příkazového řádku, sady SDK a ověřené rozhraní REST API. V tomto článku se dozvíte, jak.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 05/22/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7daa3bdf9fb51fee6b0e190625f07e0d14c3d1f4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995138"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Export nebo odstranění služby experimentování ve službě nebo data správy modelů ve službě Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Ve službě Azure Machine Learning můžete exportovat nebo odstranit účet data související se správou služby experimentování ve službě nebo model s ověřeným rozhraní REST API. Tento článek vysvětluje, jak.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Ovládejte svá data účtu
V rámci produktu data uložená pomocí služby experimentování ve službě a modelu správy Azure Machine Learning je dostupná pro exportu a odstranění prostřednictvím webu Azure portal, rozhraní příkazového řádku, sady SDK a ověřené rozhraní REST API. Telemetrická data, je přístupný prostřednictvím portálu Azure o ochraně osobních údajů. 

Ve službě Azure Machine Learning osobní údaje se skládá z informací o uživateli v historii spuštění dokumenty a telemetrie záznamy některé uživatelské interakce se službou.

## <a name="delete-account-data-with-the-rest-api"></a>Odstranit účet data pomocí rozhraní REST API 

Pokud chcete odstranit data, můžete provést následující volání rozhraní API s operací HTTP DELETE. Tyto oprávnění tak, že `Authorization: Bearer <arm-token>` hlavičky v požadavku, ve kterém `<arm-token>` je přístupový token AAD pro koncový bod `https://management.core.windows.net/` koncového bodu.  

Zjistěte, jak to získání tokenu a volání koncových bodů Azure, najdete v článku [dokumentace k rozhraní REST API služby Azure](https://docs.microsoft.com/rest/api/azure/).  

V následujících příkladech nahraďte text v {} pomocí názvů instancí, které určují přidružený prostředek.

## <a name="delete-from-a-hosting-account"></a>Odstranit z hostitelský účet

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>Odstranit z modelu služby správy

### <a name="model-document"></a>Model dokumentu
Pomocí tohoto volání můžete získat seznam modelů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Jednotlivé modely můžete odstranit pomocí:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Manifest dokumentu
Pomocí tohoto volání můžete získat seznam všech manifestů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Jednotlivé manifesty lze odstranit pomocí:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Služba dokumentů
Pomocí tohoto volání můžete získat seznam všech služeb a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Jednotlivé služby můžete odstranit pomocí:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Dokument s obrázkem
Pomocí tohoto volání můžete získat seznam všech imagí a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Jednotlivé Image můžete odstranit pomocí:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Odstranit spuštění data historie, artefaktů a oznámení
Spouštění historie, artefaktů a oznámení úložišť pro projekt se odstraní po odstranění odpovídající dokumentu projektu:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Odstranit od zprostředkovatele prostředků účtu služby experimentování ve službě
Dokumenty projektu jsou odstraněny pomocí:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

Pracovní prostor dokumenty jsou odstraněny pomocí:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

Celý experimentální účet se odstraňuje pomocí:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>Export dat služby pomocí rozhraní REST API
Abyste mohli exportovat data, můžete provést následující volání rozhraní API s operací HTTP GET. Tyto oprávnění tak, že `Authorization: Bearer <arm-token>` hlavičky v požadavku, ve kterém `<arm-token>` je přístupový token AAD pro koncový bod `https://management.core.windows.net/`  

Zjistěte, jak to získání tokenu a volání koncových bodů Azure, najdete v článku [dokumentace k rozhraní REST API služby Azure](https://docs.microsoft.com/rest/api/azure/).   

V následujících příkladech nahraďte text v {} pomocí názvů instancí, které určují přidružený prostředek.

## <a name="export-hosting-account-data"></a>Export, který je hostitelem dat účtu

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Exportovat data služby správy modelů
### <a name="model-document"></a>Model dokumentu

Pomocí tohoto volání můžete získat seznam modelů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Je možné získat jednotlivé modely:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Manifesty
Pomocí tohoto volání můžete získat seznam všech manifestů a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Je možné získat jednotlivé manifestů:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Služby
Pomocí tohoto volání můžete získat seznam všech služeb a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Je možné získat jednotlivé služby: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Image
Pomocí tohoto volání můžete získat seznam všech imagí a jejich ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Je možné získat jednotlivé služby: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Export dat výpočetní prostředky
### <a name="compute-clusters"></a>Výpočetních clusterů
Pomocí tohoto volání můžete získat seznam všech výpočetní clustery a jejich názvy:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Je možné získat jednotlivé clustery:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Clustery operacionalizace
Pomocí tohoto volání můžete získat seznam všech clusterů a jejich názvy:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Je možné získat jednotlivé clustery:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Export dat historie spuštění
Pomocí tohoto volání můžete získat seznam všech spuštění a jejich ID:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Pomocí tohoto volání můžete získat seznam všech experimentů a jejich ID:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Historii které položky můžete získat výčtem spuštění:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Spusťte metriky, které je možné získat položky:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Spuštění experimenty se dají získat pomocí:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Spouštění historie artefakty:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

Spusťte artefakty historie identifikátorů URI:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Export artefaktů
Pomocí tohoto volání můžete získat seznam prostředků a jejich názvy:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Pomocí tohoto volání můžete získat seznam artefaktů a jejich cesty:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Obsah artefaktu

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Dokumenty artefaktu

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Prostředky

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Export oznámení

1. Přejděte [části uživatelé na portálu Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/)a pak vyberte uživatele ze **název** sloupec. 
2. Poznámka: **ID objektu**a použít je v následující volání:     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Export údajů o účtu služby experimentování ve službě
### <a name="experimentation-account-information"></a>Informace o účtu služby experimentování ve službě

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Informace o pracovním prostoru

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Informace o projektu

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
