---
title: Průběžná integrace a Doručování s využitím šablon Resource Manageru a Azure kanály
description: Popisuje, jak nastavit průběžnou integraci v kanálech Azure s použitím projekty nasazení skupiny prostředků Azure v sadě Visual Studio k nasazení šablony Resource Manageru.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191457"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrace s Azure kanály šablon Resource Manageru

Visual Studio poskytuje v rámci projektu skupiny prostředků Azure pro vytváření šablon a jejich nasazení do vašeho předplatného Azure. Tento projekt lze integrovat s Azure kanály průběžné integrace a průběžného nasazování (CI/CD).

Existují dva způsoby, jak nasazení šablon pomocí Azure kanály:

* **Přidat úlohu, která spouští skript Azure Powershellu**. Tato možnost nabízí výhodu v podobě zajišťuje v rámci vývojového cyklu vzhledem k tomu, že používáte stejný skript, který je součástí projektu sady Visual Studio (Deploy-AzureResourceGroup.ps1). Skript fáze artefakty z projektu do účtu úložiště s přístupem k Resource Manageru. Artefakty jsou položky v projektu například propojenými šablonami, skripty a binární soubory aplikace. Skript potom nasadí šablony.

* **Přidat úlohy, kopírovat a nasazení úlohy**. Tato možnost je umístěna do skriptu projektu. Nakonfigurujte dvě úlohy v kanálu. Jeden úkol zpracování artefakty a druhá úloha nasazuje šablony.

Tento článek ukazuje oba přístupy.

## <a name="prepare-your-project"></a>Příprava projektu

Tento článek předpokládá, že projekt sady Visual Studio a Azure DevOps organizace jsou připraveny pro vytvoření kanálu. Následující kroky ukazují, jak zajistit, že jste připravení:

* Máte organizaci Azure DevOps. Pokud ho nemáte, [vytvořit zdarma](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Pokud váš tým už má organizace Azure DevOps, ujistěte se, že jste správce Azure DevOps projektu, který chcete použít.

* Jste nakonfigurovali [připojení služby](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) ke svému předplatnému Azure. Úlohy v kanálu provést s identitou instanční objekt. Postup vytvoření připojení najdete v tématu [vytvořit projekt DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Máte projekt aplikace Visual Studio, ze kterého byl vytvořen **skupiny prostředků Azure** úvodní šablona. Informace o vytvoření tohoto typu projektu naleznete v tématu [vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Váš projekt sady Visual Studio je [připojené Azure DevOps project](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Vytvoření kanálu

1. Pokud jste nepřidali kanálu dříve, musíte vytvořit nový kanál. Z vaší organizace Azure DevOps vyberte **kanály** a **nový kanál**.

   ![Přidat nový kanál](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Zadejte, kde je váš kód uložen. Následující obrázek ukazuje, že vyberete **úložiště Git v Azure**.

   ![Vyberte zdrojový kód](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Z tohoto zdroje vyberte úložiště, který má kód pro váš projekt.

   ![Vybrat úložiště](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Vyberte typ kanálu k vytvoření. Můžete vybrat **Starter kanálu**.

   ![Vyberte kanál](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Jste připraveni přidat úlohu prostředí Azure PowerShell nebo kopírovat soubor a nasazení úlohy.

## <a name="azure-powershell-task"></a>Úloha Azure Powershellu

Tato část ukazuje, jak nakonfigurovat průběžné nasazování pomocí jednu úlohu, která spouští skript prostředí PowerShell ve vašem projektu. Vytvoří následující soubor YAML [úloh prostředí Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

Pokud nastavíte úlohu na `AzurePowerShell@3`, kanál používá příkazy z modulu AzureRM k ověření připojení. Ve výchozím nastavení Powershellový skript v projektu sady Visual Studio používá modul AzureRM. Pokud jste aktualizovali váš skript, který chcete použít [Az modulu](/powershell/azure/new-azureps-module-az), nastavte úkol na `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Pro `azureSubscription`, zadejte název připojení služby, který jste vytvořili.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Pro `scriptPath`, zadejte relativní cestu ze souboru kanálu do skriptu. Můžete se podívat v úložišti zobrazíte cestu.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Pokud není nutné fázi artefakty, stačí pouze předejte název a umístění skupiny prostředků pro nasazení. Visual Studio skript vytvoří skupinu prostředků, pokud ještě neexistuje.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Pokud potřebujete fázi artefakty pro existující účet úložiště, použijte:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Nyní, víte, jak vytvořit úlohu, Podívejme se kroků a upravte kanál.

1. Otevřete svůj kanál a nahraďte jeho obsah vaší YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Vyberte **Uložit**.

   ![Uložení kanálu](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Zadejte zprávu potvrzení a potvrdit přímo **hlavní**.

1. Když vyberete **Uložit**, se automaticky spouští kanál sestavení. Vraťte se do souhrnu sestavení kanálu a podívejte se na stav.

   ![Zobrazení výsledků](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Můžete vybrat aktuálně spuštěné kanálu zobrazíte podrobnosti o úlohách. Po dokončení se zobrazí výsledky každého kroku.

## <a name="copy-and-deploy-tasks"></a>Zkopírujte a úlohy nasazování

Tato část ukazuje, jak nakonfigurovat průběžné nasazování pomocí dvou úloh připravit artefakty a šablony nasazení. 

Zobrazí se následující YAML [úlohu kopírování souborů Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Existuje několik částí této úlohy můžete upravit pro vaše prostředí. `SourcePath` Označuje umístění artefakty relativní k souboru kanálu. V tomto příkladu existují soubory ve složce s názvem `AzureResourceGroup1` která byla název projektu.

```yaml
SourcePath: '<path-to-artifacts>'
```

Pro `azureSubscription`, zadejte název připojení služby, který jste vytvořili.

```yaml
azureSubscription: '<your-connection-name>'
```

Název úložiště a kontejner zadejte názvy účtu úložiště a kontejner, který chcete použít pro ukládání artefaktů. Musí existovat účet úložiště.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Zobrazí se následující YAML [využít úkol nasazení skupiny prostředků Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Existuje několik částí této úlohy můžete upravit pro vaše prostředí. Pro `azureSubscription`, zadejte název připojení služby, který jste vytvořili.

```yaml
azureSubscription: '<your-connection-name>'
```

Pro `resourceGroupName` a `location`, zadejte název a umístění skupiny prostředků, kterou chcete nasadit. Úloha vytvoří skupinu prostředků, pokud neexistuje.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

Odkazy na úlohy nasazení do šablony s názvem `WebSite.json` a soubor parametrů s názvem WebSite.parameters.json. Použijte názvy souborů šablonu a parametry.

Nyní, že rozumíte vytvoření úlohy, Podívejme se kroků a upravte kanál.

1. Otevřete svůj kanál a nahraďte jeho obsah vaší YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Vyberte **Uložit**.

1. Zadejte zprávu potvrzení a potvrdit přímo **hlavní**.

1. Když vyberete **Uložit**, se automaticky spouští kanál sestavení. Vraťte se do souhrnu sestavení kanálu a podívejte se na stav.

   ![Zobrazení výsledků](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Můžete vybrat aktuálně spuštěné kanálu zobrazíte podrobnosti o úlohách. Po dokončení se zobrazí výsledky každého kroku.

## <a name="next-steps"></a>Další postup

Podrobný postup použití kanály Azure pomocí šablon Resource Manageru, najdete v části [kurzu: Průběžná integrace šablon Azure Resource Manageru s kanály Azure](resource-manager-tutorial-use-azure-pipelines.md).
