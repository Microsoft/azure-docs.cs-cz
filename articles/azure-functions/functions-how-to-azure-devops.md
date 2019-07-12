---
title: Průběžně doručovat funkce aktualizace kódu s použitím Azure DevOps – Azure Functions
description: Zjistěte, jak vytvořit kanál Azure DevOps, který cílí Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594459"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Průběžné doručování s využitím Azure DevOps

Funkce můžete automaticky nasadit aplikaci Azure Functions s použitím [kanály Azure](/azure/devops/pipelines/).

Máte dvě možnosti pro definování kanálu:

- **Soubor YAML**: Soubor YAML popisuje kanálu. Tento soubor může obsahovat oddíl kroků sestavení a vydání části. Soubor YAML musí být ve stejném úložišti jako aplikace.
- **Šablona**: Šablony jsou předem připravených úloh, které vytvoření buildu nebo nasazení vaší aplikace.

## <a name="yaml-based-pipeline"></a>Na základě YAML kanálu

K vytvoření kanálu na základě YAML, nejprve vytvořte svoji aplikaci a pak aplikaci nasaďte.

### <a name="build-your-app"></a>Sestavení aplikace

Jak vytvářet aplikace v kanálech Azure závisí na programovací jazyk vaší aplikace. Jednotlivé jazyky mají postup vytvoření artefaktů nasazení konkrétního sestavení. Artefakt nasazení se používá k nasazení vaší aplikace funkcí v Azure.

#### <a name="net"></a>.NET

Chcete-li vytvořit soubor YAML pro vytvoření aplikace .NET můžete použít následující ukázka:

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

Následující příklad můžete vytvořit soubor YAML pro sestavení aplikace v jazyce JavaScript:

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

Následující příklad můžete vytvořit soubor YAML pro sestavení aplikace v Pythonu. Pouze pro Linux Azure Functions je podporován Python.

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

Následující příklad můžete vytvořit soubor YAML pro zabalení aplikace prostředí PowerShell. Prostředí PowerShell se podporuje jenom pro Windows Azure Functions.

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

V souboru YAML, v závislosti na hostitelském operačním systému musí obsahovat jednu z následujících vzorků YAML.

#### <a name="windows-function-app"></a>Aplikace Windows – funkce

Následující fragment kódu můžete použít k nasazení aplikace funkcí Windows:

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

#### <a name="linux-function-app"></a>Aplikace function app pro Linux

Následující fragment kódu můžete použít k nasazení aplikace funkcí Linux:

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

Šablony v Azure DevOps jsou předdefinované skupiny úloh, které vytvoření buildu nebo nasazení aplikace.

### <a name="build-your-app"></a>Sestavení aplikace

Jak vytvářet aplikace v kanálech Azure závisí na programovací jazyk vaší aplikace. Jednotlivé jazyky mají postup vytvoření artefaktů nasazení konkrétního sestavení. Artefakt nasazení se používá k aktualizaci vaší aplikace funkcí v Azure.

Chcete-li použít šablony integrované sestavení, když vytvoříte nový kanál sestavení, vyberte **použít klasický editor** k vytvoření kanálu pomocí návrháře šablony.

![Vyberte editor kanály Azure classic](media/functions-how-to-azure-devops/classic-editor.png)

Po dokončení konfigurace zdrojového kódu, vyhledávání pro službu Azure Functions šablony sestavení. Vyberte šablonu, která odpovídá jazyku app.

![Vyberte šablonu služby Azure Functions sestavení](media/functions-how-to-azure-devops/build-templates.png)

V některých případech může mít artefakty sestavení strukturu konkrétní složky. Musíte vybrat **název kořenové složky Prepend cesty k archivu** zaškrtávací políčko.

![Možnost předřaďte název kořenové složky](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplikace jazyka JavaScript

Pokud vaše aplikace JavaScript závislý na nativní moduly Windows, je nutné aktualizovat verze fond agenta na **hostované VS2017**.

![Aktualizace fondu verze agenta](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Nasazení vaší aplikace

Když vytvoříte nový kanál pro vydávání verzí, vyhledejte požadovanou šablonu vydané verze Azure Functions.

![Vyhledejte šablona verze Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Nasazení do slotu nasazení se nepodporuje v šabloně verze.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Vytvoření kanálu sestavení pomocí příkazového řádku Azure

Chcete-li vytvořit kanál sestavení v Azure, použijte `az functionapp devops-pipeline create` [příkaz](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Kanál sestavení se vytvoří sestavení a vydání změny kódu, které jsou provedeny ve vašem úložišti. Příkaz vytvoří nový soubor YAML, který definuje kanálu sestavení a vydaných verzí a potvrdí ji do úložiště. Požadavky pro tento příkaz závisí na umístění vašeho kódu.

- Pokud je váš kód v Githubu:

    - Musíte mít **zápisu** oprávnění ke správě předplatného.

    - Musíte být správcem projektu v Azure DevOps.

    - Musíte mít oprávnění k vytvoření osobního přístupového tokenu Githubu (Jan), který má dostatečná oprávnění. Další informace najdete v tématu [token PAT Githubu požadavky na oprávnění.](https://aka.ms/azure-devops-source-repos)

    - Musíte mít oprávnění k potvrzení změn do hlavní větve ve vašem úložišti GitHub tak může potvrdit automaticky vygenerovaný soubor YAML.

- Pokud je váš kód v úložišti Azure:

    - Musíte mít **zápisu** oprávnění ke správě předplatného.

    - Musíte být správcem projektu v Azure DevOps.

## <a name="next-steps"></a>Další postup

- Zkontrolujte [přehled Azure Functions](functions-overview.md).
- Zkontrolujte [přehled Azure DevOps](/azure/devops/pipelines/).
