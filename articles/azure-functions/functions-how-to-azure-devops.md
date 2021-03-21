---
title: Průběžná aktualizace kódu aplikace Function App pomocí Azure DevOps
description: Přečtěte si, jak nastavit kanál Azure DevOps, který cílí na Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a3f423a144738fdaa4462606de6ad4a4e34d6775
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563411"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Průběžné doručování pomocí Azure DevOps

Funkci můžete automaticky nasadit do aplikace Azure Functions pomocí [Azure Pipelines](/azure/devops/pipelines/).

Máte dvě možnosti, jak svůj kanál definovat:

- **YAML**: soubor YAML popisuje kanál. Soubor může obsahovat oddíl kroků sestavení a oddíl Release. Soubor YAML musí být ve stejném úložišti jako aplikace.
- **Šablona**: šablony jsou připravené úkoly, které sestavují nebo nasazují vaši aplikaci.

## <a name="yaml-based-pipeline"></a>Kanál založený na YAML

Pokud chcete vytvořit kanál založený na YAML, nejdřív sestavte aplikaci a potom aplikaci nasaďte.

### <a name="build-your-app"></a>Vytvoření aplikace

Způsob sestavování aplikace v Azure Pipelines závisí na programovacím jazyku vaší aplikace. Každý jazyk má konkrétní kroky sestavení, které vytvářejí artefakt nasazení. Artefakt nasazení se používá k nasazení aplikace Function App v Azure.

# <a name="c"></a>[R\#](#tab/csharp)

Pomocí následující ukázky můžete vytvořit soubor YAML k vytvoření aplikace .NET:

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
    modifyOutputPath: false
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
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Pomocí následující ukázky můžete vytvořit soubor YAML pro sestavení aplikace JavaScriptu:

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
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Pomocí jedné z následujících ukázek můžete vytvořit soubor YAML k vytvoření aplikace pro konkrétní verzi Pythonu. Python se podporuje jenom pro aplikace Function App spuštěné v systému Linux.

**Verze 3,7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Verze 3,6**

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
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pomocí následující ukázky můžete vytvořit soubor YAML pro zabalení aplikace PowerShellu. PowerShell se podporuje jenom pro Windows Azure Functions.

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
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Nasazení aplikace

V závislosti na hostitelském operačním systému musíte do souboru YAML zahrnout jednu z následujících ukázek YAML.

#### <a name="windows-function-app"></a>Aplikace funkcí Windows

K nasazení aplikace funkcí systému Windows můžete použít následující fragment kódu:

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

#### <a name="linux-function-app"></a>Aplikace funkce Linux

K nasazení aplikace Functions pro Linux můžete použít následující fragment kódu:

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

## <a name="template-based-pipeline"></a>Kanál založený na šablonách

Šablony v Azure DevOps jsou předdefinované skupiny úloh, které sestavují nebo nasazují aplikaci.

### <a name="build-your-app"></a>Vytvoření aplikace

Způsob sestavování aplikace v Azure Pipelines závisí na programovacím jazyku vaší aplikace. Každý jazyk má konkrétní kroky sestavení, které vytvářejí artefakt nasazení. K aktualizaci aplikace Function App v Azure se používá artefakt nasazení.

Chcete-li použít předdefinované šablony sestavení, při vytváření nového kanálu sestavení vyberte **použít klasický Editor** k vytvoření kanálu pomocí šablon návrháře.

![Vybrat klasický Editor Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Po nakonfigurování zdroje kódu vyhledejte Azure Functions šablon sestavení. Vyberte šablonu, která odpovídá vašemu jazyku aplikace.

![Vybrat šablonu sestavení Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

V některých případech mají artefakty sestavení určitou strukturu složek. Je možné, že budete muset zaškrtnout políčko **název kořenové složky pro archivaci cest** .

![Možnost předřadit název kořenové složky](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScriptové aplikace

Pokud má vaše aplikace JavaScriptu závislost na nativních modulech Windows, musíte aktualizovat verzi fondu agentů na **hostovanou VS2017**.

![Aktualizace verze fondu agentů](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Nasazení aplikace

Když vytváříte nový kanál verze, vyhledejte šablonu verze Azure Functions.

![Vyhledat šablonu vydané verze Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Nasazení do slotu nasazení není v šabloně vydané verze podporováno.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Vytvoření kanálu sestavení pomocí Azure CLI

K vytvoření kanálu sestavení v Azure použijte `az functionapp devops-pipeline create` [příkaz](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Vytvoří se kanál sestavení pro sestavení a vydání všech změn kódu, které se provedou v úložišti. Příkaz vygeneruje nový soubor YAML, který definuje kanál sestavení a vydání a pak ho potvrdí do vašeho úložiště. Předpoklady pro tento příkaz závisí na umístění vašeho kódu.

- Pokud je váš kód na GitHubu:

    - Musíte mít oprávnění k **zápisu** do svého předplatného.

    - Musíte být správce projektu ve službě Azure DevOps.

    - Musíte mít oprávnění k vytvoření osobního přístupového tokenu GitHubu, který má dostatečná oprávnění. Další informace najdete v tématu [požadavky na oprávnění GitHubu Pat.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - Musíte mít oprávnění k potvrzení do hlavní větve ve vašem úložišti GitHub, abyste mohli potvrdit automaticky vygenerovaný soubor YAML.

- Pokud je váš kód v Azure Repos:

    - Musíte mít oprávnění k **zápisu** do svého předplatného.

    - Musíte být správce projektu ve službě Azure DevOps.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled Azure Functions](functions-overview.md).
- Přečtěte si [Přehled Azure DevOps](/azure/devops/pipelines/).