---
title: Export nebo odstranit experimentování nebo model správy dat – Azure Machine Learning | Microsoft Docs
description: V Azure Machine Learning můžete exportovat nebo odstranit účet data související s experimentování nebo model správy pomocí portálu Azure, rozhraní příkazového řádku, sady SDK a ověřené rozhraní REST API. Tento článek ukazuje, jak.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 234c6c535e8a56d894f9e0f7a2f83602b613f5a5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654852"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Export nebo odstranit experimentování nebo model správy dat v Machine Learning

V Azure Machine Learning můžete exportovat nebo odstranit účet data související se správou experimentování nebo model s ověřené REST API. Tento článek vysvětluje, jak.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Řízení vaše data na účtu
V produktu data uložená pomocí Azure Machine Learning experimentování a modelu správy je k dispozici pro export a odstranění prostřednictvím portálu Azure, rozhraní příkazového řádku, sady SDK a ověřené rozhraní REST API. Telemetrická data jsou přístupné prostřednictvím portálu Azure o ochraně osobních údajů. 

V Azure Machine Learning osobní údaje se skládá z informace o uživateli v dokumentech historie spouštění a telemetrie záznamy některé interakce uživatele se službou.

## <a name="delete-account-data-with-the-rest-api"></a>Odstranění účtu data pomocí rozhraní REST API 

Chcete-li odstranit data, můžete provést následující volání rozhraní API s příkazem HTTP DELETE. Toto oprávnění tak, že `Authorization: Bearer <arm-token>` hlavičky v požadavku, kde `<arm-token>` je přístupový token AAD pro koncový bod `https://management.core.windows.net/` koncový bod.  

Zjistěte, jak to získat token a volání koncové body Azure, najdete v tématu [dokumentace k Azure REST API](https://docs.microsoft.com/rest/api/azure/).  

V následujících příkladech nahraďte text v {} s názvy instancí, které určují přidružených prostředků.

## <a name="delete-from-a-hosting-account"></a>Odstranění z hostitelských účtu

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>Odstranit ze služby pro správu modelu

### <a name="model-document"></a>Dokument modelu
Chcete-li získat seznam modelů a jejich ID použijte toto volání:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

S odstraněním jednotlivých modely:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Dokument s manifestem
Chcete-li získat seznam všech manifestů a jejich ID použijte toto volání:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

S odstraněním jednotlivých manifesty:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Dokumenty služeb
Chcete-li získat seznam všech služeb a jejich ID použijte toto volání:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Odstranit lze jednotlivé služby s:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Obrázek dokumentu
Chcete-li získat seznam všech obrázky a jejich ID použijte toto volání:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

S odstraněním jednotlivých bitové kopie:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Odstranit spuštění data historie artefaktů a oznámení
Spuštění historie artefaktů a oznámení úložiště pro projekt se odstraní po odstranění dokumentu odpovídající projektu:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Odstranit od zprostředkovatele prostředků účtu experimentování
Dokumenty projektu se odstraní pomocí:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

Pracovní prostor dokumentů se odstraní pomocí:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

Odstraní účet celý experimenty s:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>Exportovat data služby se rozhraní REST API
Aby bylo možné exportovat data, můžete provést následující volání rozhraní API s příkazem HTTP GET. Toto oprávnění tak, že `Authorization: Bearer <arm-token>` hlavičky v požadavku, kde `<arm-token>` je přístupový token AAD pro koncový bod `https://management.core.windows.net/`  

Zjistěte, jak to získat token a volání koncové body Azure, najdete v tématu [dokumentace k Azure REST API](https://docs.microsoft.com/rest/api/azure/).   

V následujících příkladech nahraďte text v {} s názvy instancí, které určují přidružených prostředků.

## <a name="export-hosting-account-data"></a>Export hostování data účtu

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Exportovat data služby model správy
### <a name="model-document"></a>Dokument modelu

Chcete-li získat seznam modelů a jejich ID použijte toto volání:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Je možné získat jednotlivé modely:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Manifesty
Chcete-li získat seznam všech manifestů a jejich ID použijte toto volání:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Je možné získat jednotlivé manifesty:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Služby
Chcete-li získat seznam všech služeb a jejich ID použijte toto volání:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Jednotlivé služby je možné získat: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Image
Chcete-li získat seznam všech obrázky a jejich ID použijte toto volání:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Jednotlivé služby je možné získat: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Export dat výpočetní
### <a name="compute-clusters"></a>Výpočetní clustery
Použijte toto volání se získat seznam všech výpočetní clustery a jejich názvy:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Jednotlivé clustery je možné získat:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Operationalization clustery
Použijte toto volání se získat seznam všech clustery a jejich názvy:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Jednotlivé clustery je možné získat:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Export spustit data historie
Chcete-li získat seznam všech spustí a jejich ID použijte toto volání:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Chcete-li získat seznam všech experimentů a jejich ID použijte toto volání:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Spusťte historii, které je možné získat položky:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Spusťte metriky, které je možné získat položky:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Je možné získat spuštění experimentů:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Spusťte artefakty historie:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

Spusťte historie artefakty identifikátory URI:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Export artefaktů
Použijte toto volání se získat seznam prostředků a jejich názvy:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Použijte toto volání se získat seznam artefaktů a jejich cesty:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Obsah artefaktů

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Dokumenty artefaktů

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Prostředky

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Export oznámení

1. Přejděte na [část uživatelé na portálu Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/)a potom vyberte uživatele z **název** sloupce. 
2. Poznámka: **ID objektu**a použít ho v následující volání:     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Exportovat informace o účtu experimentování
### <a name="experimentation-account-information"></a>Informace o účtu experimentování

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Informace o pracovním prostoru

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Informace o projektu

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
