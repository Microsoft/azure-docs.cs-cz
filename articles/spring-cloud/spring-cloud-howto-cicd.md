---
title: CI/CD pro jarní cloud Azure
description: CI/CD pro jarní cloud Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 40e674594c80a076fc9775fd4315aee938a43593
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888689"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD pro jarní cloud Azure

Nástroje pro průběžnou integraci a průběžné doručování vám umožní rychle nasadit aktualizace stávajících aplikací s minimálním úsilím a rizikem. Azure DevOps pomáhá organizovat a řídit tyto klíčové úlohy. V současné době neposkytuje Azure jarní Cloud konkrétní modul plug-in Azure DevOps.  Své jarní cloudové aplikace ale můžete integrovat s DevOps pomocí [úlohy CLI Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops&preserve-view=true).

V tomto článku se dozvíte, jak pomocí úlohy Azure CLI s Azure jarním cloudem integrovat s Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Vytvoření připojení služby Azure Resource Manager

V [tomto článku](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops&preserve-view=true) se dozvíte, jak vytvořit připojení služby Azure Resource Manager k vašemu projektu Azure DevOps. Nezapomeňte vybrat stejné předplatné, které používáte pro instanci vaší jarní cloudové služby Azure.

## <a name="azure-cli-task-templates"></a>Šablony úloh Azure CLI
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Nasadit artefakty

Můžete sestavit a nasadit své projekty pomocí řady `tasks` . Tento fragment kódu definuje proměnné, úlohu .NET Core pro sestavení aplikace a úlohu Azure CLI pro nasazení souboru *. zip* .

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --is-public true
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Nasadit artefakty

Můžete sestavit a nasadit své projekty pomocí řady `tasks` . Tento fragment kódu nejprve definuje úlohu Maven pro sestavení aplikace, za kterou následuje druhý úkol, který nasadí soubor JAR pomocí rozšíření Azure CLI cloudu Azure pro jaře.

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

### <a name="deploy-from-source"></a>Nasadit ze zdroje

Do Azure je možné nasadit přímo bez samostatného kroku sestavení.

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
::: zone-end

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: nasazení první aplikace pro cloudovou službu Azure jaře](spring-cloud-quickstart.md)
