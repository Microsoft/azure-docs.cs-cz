---
title: CI/CD pro jarní cloud Azure
description: CI/CD pro jarní cloud Azure
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 96795315fcb0dd9b90cd55e8baa46c6a394882ce
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038831"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD pro jarní cloud Azure

Nástroje pro průběžnou integraci a průběžné doručování umožňují vývojářům rychle nasadit aktualizace stávajících aplikací s minimálním úsilím a rizikem. Azure DevOps pomáhá organizovat a řídit tyto klíčové úlohy. V současné době neposkytuje Azure jarní Cloud konkrétní modul plug-in Azure DevOps.  Své jarní cloudové aplikace ale můžete integrovat s DevOps pomocí [úlohy CLI Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). V tomto článku se dozvíte, jak pomocí úlohy CLI Azure pomocí Azure jaře cloudu integrovat do Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Vytvoření připojení služby Azure Resource Manager

V [tomto článku](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) se dozvíte, jak vytvořit připojení služby Azure Resource Manager k vašemu projektu Azure DevOps. Nezapomeňte vybrat stejné předplatné, které používáte pro instanci vaší jarní cloudové služby Azure.

## <a name="azure-cli-task-templates"></a>Šablony úloh Azure CLI

### <a name="deploy-artifacts"></a>Nasadit artefakty

Můžete sestavit a nasadit své projekty pomocí řady `tasks`. Tento fragment kódu nejprve definuje úlohu Maven pro sestavení aplikace, za kterou následuje druhý úkol, který nasadí soubor JAR pomocí rozšíření Azure CLI cloudu Azure pro jaře.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Nasadit ze zdroje

Do Azure je možné nasadit přímo bez samostatného kroku sestavení.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
