---
title: Průběžně doručovat funkce aktualizací kódu přes Azure DevOps
description: Zjistěte, jak vytvořit kanál Azure DevOps cílení na Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 9806a982982971b1b3ac9c28454e17813b2ad2a5
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479868"
---
# <a name="continuous-delivery-using-azure-devops"></a>Nepřetržité doručování pomocí Azure DevOps

Funkce můžete automaticky nasadit do aplikace funkce Azure Functions pomocí [kanály Azure](/azure/devops/pipelines/).
Pokud chcete definovat svůj kanál, můžete použít:

- Soubor YAML: Tento soubor popisuje kanálu, může mít oddíl kroků sestavení a vydání oddílu. Soubor YAML by měl být ve stejném úložišti jako aplikace.

- Šablony: Šablony jsou připravené provedených úloh, které vytvoření buildu nebo nasazení vaší aplikace.

## <a name="yaml-based-pipeline"></a>Na základě YAML kanálu

### <a name="build-your-app"></a>Sestavení aplikace

Sestavení aplikace v kanálech Azure závisí na programovacím jazyce podle vaší aplikace.
Jednotlivé jazyky mají konkrétní sestavení kroky k vytvoření nasazení artefakt, který slouží k nasazení vaší aplikace funkcí v Azure.

#### <a name="net"></a>.NET

Podle následující ukázky můžete použít k vytvoření souboru YAML k sestavení aplikace .NET.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Podle následující ukázky můžete použít k vytvoření souboru YAML k sestavení aplikace JavaScript:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

Podle následující ukázky můžete použít k vytvoření souboru YAML k sestavení aplikace v Pythonu, Python je podporována pouze pro Linux Azure Functions:

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

Podle následující ukázky můžete použít k vytvoření souboru YAML Powershellu aplikaci zabalit, se podporuje jenom prostředí PowerShell pro službu Windows Azure Functions:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Nasazení vaší aplikace

V závislosti na hostitelském operačním systému je potřeba zahrnout do souboru YAML podle následující ukázky YAML.

#### <a name="windows-function-app"></a>Funkce Windows App

Následující fragment kódu je možné nasadit do aplikace Windows – funkce

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux funkce aplikace

Následující fragment kódu je možné nasadit Linuxovou aplikaci – funkce

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Kanál založené na šablonách

Šablony v Azure DevOps, jsou předdefinované skupiny úloh, které vytvoření buildu nebo nasazení aplikace.

### <a name="build-your-app"></a>Sestavení aplikace

Sestavení aplikace v kanálech Azure závisí na programovacím jazyce podle vaší aplikace. Jednotlivé jazyky mají konkrétní sestavení kroky k vytvoření nasazení artefakt, který můžete použít k aktualizaci vaší aplikace funkcí v Azure.
Chcete-li použít šablony integrované sestavení při vytvoření nového kanálu sestavení, zvolte **použít klasický editor** k vytvoření kanálu pomocí návrháře šablony

![Azure classic editor kanály](media/functions-how-to-azure-devops/classic-editor.png)

Po dokončení konfigurace zdrojového kódu, vyhledávání šablon sestavení pro službu Azure Functions a vyberte šablonu, která odpovídá jazyku app.

![Služba Azure Functions šablony sestavení](media/functions-how-to-azure-devops/build-templates.png)

V některých případech se artefakty sestavení konkrétní složky struktura a je potřeba zkontrolovat **název kořenové složky Prepend cesty k archivu** možnost.

![Předřaďte kořenovou složku](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplikace jazyka JavaScript

Pokud vaše aplikace jazyka JavaScript jsou závislé na nativní moduly Windows, musíte aktualizovat:

- Fond agentů se verze **hostované VS2017**

  ![Změna agenta sestavení operačního systému](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Nasazení vaší aplikace

Když vytváříte nový kanál pro vydávání verzí, vyhledejte šablonu vydané verze Azure Functions.

![](media/functions-how-to-azure-devops/release-template.png)

Nasazení do slotu nasazení se nepodporuje v šabloně verze.

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Vytvoření kanálu služby Azure pomocí Azure CLI

Použití `az functionapp devops-pipeline create` [příkaz](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), budou vytvořeny kanálu služby Azure k sestavení a vydání změny kódu v úložišti. Příkaz vygenerujte nový soubor YAML, který definuje kanál sestavení a vydaných verzí, který se zapsat do úložiště. Nasazení do slotu nasazení se nepodporuje příkaz rozhraní příkazového řádku Azure.
Předpoklady pro tento příkaz v závislosti na umístění kódu:

- Pokud je váš kód v Githubu:

    - Musíte mít **zápisu** oprávnění k předplatnému.

    - Jste správcem projektu v Azure DevOps.

    - Máte oprávnění k vytváření osobního přístupového tokenu Githubu s dostatečnými oprávněními. [Požadavky na oprávnění token PAT Githubu.](https://aka.ms/azure-devops-source-repos)

    - Nemáte oprávnění k potvrzení změn do hlavní větve ve vašem úložišti GitHub pro potvrzení automaticky vygenerovaný soubor YAML.

- Pokud je váš kód v úložišti Azure:

    - Musíte mít **zápisu** oprávnění k předplatnému.

    - Jste správcem projektu v Azure DevOps.

## <a name="next-steps"></a>Další postup

+ [Přehled Azure Functions](functions-overview.md)
+ [Přehled služby Azure DevOps](/azure/devops/pipelines/)
