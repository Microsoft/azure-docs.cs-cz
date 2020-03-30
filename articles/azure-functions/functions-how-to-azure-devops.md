---
title: Průběžná aktualizace kódu aplikace funkcí pomocí Azure DevOps
description: Zjistěte, jak nastavit kanál Azure DevOps, který cílí na funkce Azure.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255759"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Průběžné doručování pomocí Azure DevOps

Svou funkci můžete automaticky nasadit do aplikace Azure Functions pomocí [Azure Pipelines](/azure/devops/pipelines/).

Máte dvě možnosti pro definování kanálu:

- **YAML soubor**: Soubor YAML popisuje kanál. Soubor může mít oddíl kroky sestavení a část vydání. Soubor YAML musí být ve stejném repo jako aplikace.
- **Šablona**: Šablony jsou hotové úkoly, které vytvářejí nebo nasazují vaši aplikaci.

## <a name="yaml-based-pipeline"></a>Kanál založený na YAML

Pokud chcete vytvořit kanál založený na YAML, nejdřív sestavte aplikaci a pak ji nasaďte.

### <a name="build-your-app"></a>Vytvoření aplikace

Způsob sestavení aplikace v Azure Pipelines závisí na programovacím jazyce vaší aplikace. Každý jazyk má konkrétní kroky sestavení, které vytvářejí artefakt nasazení. Artefakt nasazení se používá k nasazení aplikace funkce v Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Následující ukázka můžete vytvořit soubor YAML k vytvoření aplikace .NET:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující ukázka můžete vytvořit soubor YAML k vytvoření aplikace JavaScript:

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

Můžete použít jeden z následujících ukázek k vytvoření souboru YAML k vytvoření aplikace pro konkrétní verzi Pythonu. Python je podporován pouze pro funkční aplikace běžící na Linuxu.

**Verze 3.7**

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

**Verze 3.6**

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

Následující ukázka můžete použít k vytvoření souboru YAML pro balíček aplikace PowerShell. Prostředí PowerShell je podporované jenom pro funkce Windows Azure.

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

V závislosti na hostitelském osu musíte do souboru YAML zahrnout jeden z následujících ukázek YAML.

#### <a name="windows-function-app"></a>Aplikace pro funkce windows

K nasazení aplikace pro funkce Windows můžete použít následující úryvek:

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

K nasazení aplikace pro funkce Linuxu můžete použít následující úryvek:

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

## <a name="template-based-pipeline"></a>Kanál založený na šabloně

Šablony v Azure DevOps jsou předdefinované skupiny úloh, které vytvářejí nebo nasazují aplikaci.

### <a name="build-your-app"></a>Vytvoření aplikace

Způsob sestavení aplikace v Azure Pipelines závisí na programovacím jazyce vaší aplikace. Každý jazyk má konkrétní kroky sestavení, které vytvářejí artefakt nasazení. Artefakt nasazení se používá k aktualizaci aplikace funkce v Azure.

Chcete-li použít předdefinované šablony sestavení, při vytváření nového kanálu sestavení vyberte **Použít klasický editor** k vytvoření kanálu pomocí šablon návrháře.

![Výběr klasického editoru Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Po konfiguraci zdroje kódu vyhledejte šablony sestavení Azure Functions. Vyberte šablonu, která odpovídá jazyku vaší aplikace.

![Výběr šablony sestavení Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

V některých případech mají artefakty sestavení určitou strukturu složek. Možná budete muset zaškrtnout políčko **Předeřadný název kořenové složky pro archivaci cest.**

![Možnost předřadit název kořenové složky](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplikace JavaScript

Pokud má vaše aplikace JavaScript závislost na nativních modulech Windows, musíte aktualizovat verzi fondu agentů na **Hosted VS2017**.

![Aktualizace verze fondu agenta](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Nasazení aplikace

Když vytvoříte nový kanál vydání, vyhledejte šablonu vydání Azure Functions.

![Hledání šablony vydání Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Nasazení do slotu nasazení není v šabloně vydání podporováno.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Vytvoření kanálu sestavení pomocí příkazového příkazového příkazu Azure

Chcete-li vytvořit kanál sestavení `az functionapp devops-pipeline create` v Azure, použijte [příkaz](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Kanál sestavení se vytvoří k sestavení a uvolnění všech změn kódu, které jsou provedeny v repo. Příkaz vygeneruje nový soubor YAML, který definuje kanál sestavení a vydání a pak jej potvrdí do vašeho repo. Předpoklady pro tento příkaz závisí na umístění kódu.

- Pokud je váš kód na GitHubu:

    - Musíte mít oprávnění k **zápisu** pro vaše předplatné.

    - Musíte být správceprojektu v Azure DevOps.

    - Musíte mít oprávnění k vytvoření githubu osobní přístupový token (PAT), který má dostatečná oprávnění. Další informace najdete v tématu [Požadavky na oprávnění GitHub PAT.](https://aka.ms/azure-devops-source-repos)

    - Musíte mít oprávnění k potvrzení hlavní větve v úložišti GitHub, abyste mohli potvrdit automaticky vygenerovaný soubor YAML.

- Pokud je váš kód v Azure Repos:

    - Musíte mít oprávnění k **zápisu** pro vaše předplatné.

    - Musíte být správceprojektu v Azure DevOps.

## <a name="next-steps"></a>Další kroky

- Projděte si [přehled funkcí Azure](functions-overview.md).
- Projděte si [přehled Azure DevOps](/azure/devops/pipelines/).
