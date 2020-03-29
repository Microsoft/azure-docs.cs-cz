---
title: CI/CD pro Azure Spring Cloud
description: CI/CD pro Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278513"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD pro Azure Spring Cloud

Nástroje pro průběžnou integraci a průběžné doručování umožňují vývojářům rychle nasazovat aktualizace stávajících aplikací s minimálním úsilím a rizikem. Azure DevOps vám pomůže uspořádat a řídit tyto klíčové úlohy. Azure Spring Cloud v současné době nenabízí konkrétní modul plug-in Azure DevOps.  Můžete však integrovat jarní cloudové aplikace s DevOps pomocí [úlohy Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). Tento článek vám ukáže, jak používat úlohu Azure CLI s Azure Spring Cloud k integraci s Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Vytvoření připojení služby Azure Resource Manager

V [tomto článku](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) se dozvíte, jak vytvořit připojení služby Azure Resource Manager k projektu Azure DevOps. Nezapomeňte vybrat stejné předplatné, které používáte pro instanci služby Azure Spring Cloud.

## <a name="azure-cli-task-templates"></a>Šablony úloh Azure CLI

### <a name="deploy-artifacts"></a>Nasazení artefaktů

Můžete vytvářet a nasazovat `tasks`projekty pomocí řady . Tento fragment nejprve definuje úlohu Maven k sestavení aplikace, následovanou druhou úlohou, která nasazuje soubor JAR pomocí rozšíření Azure Azure CLI Azure Azure.

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
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Nasazení ze zdroje

Je možné nasadit přímo do Azure bez samostatného kroku sestavení.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
