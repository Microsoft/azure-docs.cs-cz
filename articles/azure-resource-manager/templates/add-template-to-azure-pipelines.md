---
title: CI/CD s Azure Pipelines a šablonami
description: Popisuje, jak nastavit průběžnou integraci v Azure Pipelines pomocí projektů nasazení Skupiny prostředků Azure ve Visual Studiu k nasazení šablon Správce prostředků.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 7617bf47595fce7baa533b0f7cc94a1803ddd349
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153450"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrace šablon ARM s Azure Pipelines

Visual Studio poskytuje projekt Skupiny prostředků Azure pro vytváření šablon Azure Resource Manager (ARM) a jejich nasazení do vašeho předplatného Azure. Tento projekt můžete integrovat s Azure Pipelines pro průběžnou integraci a průběžné nasazování (CI/CD).

Existují dva způsoby nasazení šablon pomocí Azure Pipelines:

* **Přidejte úlohu, ve které běží skript Azure PowerShellu**. Tato možnost má tu výhodu, že poskytuje konzistenci v průběhu celého životního cyklu vývoje, protože používáte stejný skript, který je součástí projektu Visual Studio (Deploy-AzureResourceGroup.ps1). Skript fáze artefakty z projektu na účet úložiště, který Resource Manager přístup. Artefakty jsou položky v projektu, jako jsou propojené šablony, skripty a binární soubory aplikací. Potom skript nasadí šablonu.

* **Přidejte úkoly ke kopírování a nasazování úloh**. Tato možnost nabízí vhodnou alternativu ke skriptu projektu. Nakonfigurujete dva úkoly v kanálu. Jeden úkol fáze artefakty a druhý úkol nasadí šablonu.

Tento článek ukazuje oba přístupy.

## <a name="prepare-your-project"></a>Příprava projektu

Tento článek předpokládá, že váš projekt Visual Studio a organizace Azure DevOps jsou připraveni k vytvoření kanálu. Následující kroky ukazují, jak se ujistit, že jste připraveni:

* Máte organizaci Azure DevOps. Pokud ho nemáte, [vytvořte si ho zdarma](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Pokud váš tým už má organizaci Azure DevOps, ujistěte se, že jste správcem projektu Azure DevOps, který chcete použít.

* Nakonfigurovali jste [připojení služby](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) k předplatnému Azure. Úlohy v kanálu spustit pod identitou instančního objektu. Postup vytvoření připojení naleznete v [tématu Vytvoření projektu DevOps](template-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Máte projekt Visual Studio, který byl vytvořen ze startovací šablony **skupiny prostředků Azure.** Informace o vytváření tohoto typu projektu najdete [v tématu vytváření a nasazování skupin prostředků Azure prostřednictvím sady Visual Studio](create-visual-studio-deployment-project.md).

* Váš projekt Visual Studio je [připojený k projektu Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Vytvoření kanálu

1. Pokud jste dříve nepřidali kanál, musíte vytvořit nový kanál. Ve své organizaci Azure DevOps vyberte **Kanály** a **Nový kanál**.

   ![Přidání nového kanálu](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Určete, kde je kód uložen. Následující obrázek znázorňuje výběr **Azure Repos Git**.

   ![Vybrat zdroj kódu](./media/add-template-to-azure-pipelines/select-source.png)

1. Z tohoto zdroje vyberte úložiště, které má kód pro váš projekt.

   ![Vybrat úložiště](./media/add-template-to-azure-pipelines/select-repo.png)

1. Vyberte typ kanálu, který chcete vytvořit. Můžete vybrat **počáteční kanál**.

   ![Vybrat kanál](./media/add-template-to-azure-pipelines/select-pipeline.png)

Jste připraveni přidat úlohu Azure PowerShellu nebo zkopírovat soubor a nasadit úlohy.

## <a name="azure-powershell-task"></a>Úloha Azure PowerShellu

Tato část ukazuje, jak nakonfigurovat průběžné nasazení pomocí jedné úlohy, která spouští skript prostředí PowerShell v projektu. Následující soubor YAML vytvoří [úlohu Prostředí Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

Když nastavíte `AzurePowerShell@3`úlohu , kanál používá příkazy z modulu AzureRM k ověření připojení. Ve výchozím nastavení používá skript Prostředí PowerShell v projektu Visual Studia modul AzureRM. Pokud jste aktualizovali skript tak, aby používal modul `AzurePowerShell@4` [Az](/powershell/azure/new-azureps-module-az), nastavte úkol na .

```yaml
steps:
- task: AzurePowerShell@4
```

Zadejte `azureSubscription`název připojení služby, které jste vytvořili.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

V `scriptPath`případě , zadejte relativní cestu ze souboru kanálu do skriptu. Cestu můžete vyhledat v úložišti.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Pokud nepotřebujete vytvořit artefakty fáze, stačí předat název a umístění skupiny prostředků, které chcete použít pro nasazení. Skript sady Visual Studio vytvoří skupinu prostředků, pokud ještě neexistuje.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Pokud potřebujete nastínit artefakty do existujícího účtu úložiště, použijte:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Nyní, když chápete, jak vytvořit úkol, projdeme si kroky k úpravě kanálu.

1. Otevřete kanál a nahraďte obsah yaml:

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

   ![Uložení kanálu](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Zadejte zprávu pro potvrzení a odevzdejte se přímo k **hlavnímu serveru**.

1. Když vyberete **Uložit**, kanál sestavení se automaticky spustí. Vraťte se do souhrnu kanálu sestavení a sledujte stav.

   ![Zobrazení výsledků](./media/add-template-to-azure-pipelines/view-results.png)

Můžete vybrat aktuálně spuštěný kanál a zobrazit podrobnosti o úkolech. Po dokončení se zobrazí výsledky pro každý krok.

## <a name="copy-and-deploy-tasks"></a>Kopírování a nasazování úloh

Tato část ukazuje, jak nakonfigurovat průběžné nasazení pomocí dvou úloh k vytvoření fáze artefaktů a nasazení šablony.

Následující YAML zobrazuje [úlohu kopírování souborů Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

Existuje několik částí tohoto úkolu revidovat pro vaše prostředí. Označuje `SourcePath` umístění artefaktů vzhledem k souboru kanálu. V tomto příkladu existují soubory `AzureResourceGroup1` ve složce s názvem, která byla název projektu.

```yaml
SourcePath: '<path-to-artifacts>'
```

Zadejte `azureSubscription`název připojení služby, které jste vytvořili.

```yaml
azureSubscription: '<your-connection-name>'
```

Pro úložiště a název kontejneru zadejte názvy účtu úložiště a kontejneru, který chcete použít pro ukládání artefaktů. Účet úložiště musí existovat.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Následující yaml ukazuje [úlohu nasazení šablony Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Existuje několik částí tohoto úkolu revidovat pro vaše prostředí.

- `deploymentScope`: Vyberte rozsah nasazení z `Management Group` `Subscription` možností: a `Resource Group`. V tomto návodu použijte **skupinu prostředků.** Další informace o oborech naleznete v tématu [Obory nasazení](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Zadejte název připojení služby, které jste vytvořili.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Zadejte cílové ID předplatného. Tato vlastnost platí pouze pro rozsah nasazení skupiny prostředků a obor nasazení předplatného.

- `resourceGroupName`a `location`: zadejte název a umístění skupiny prostředků, do které chcete nasadit. Úkol vytvoří skupinu prostředků, pokud neexistuje.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

Úloha nasazení odkazuje na `WebSite.json` šablonu s názvem a soubor parametrů s názvem WebSite.parameters.json. Použijte názvy souborů šablony a parametrů.

Nyní, když chápete, jak vytvořit úkoly, pojďme projít kroky k úpravě kanálu.

1. Otevřete kanál a nahraďte obsah yaml:

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
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Vyberte **Uložit**.

1. Zadejte zprávu pro potvrzení a odevzdejte se přímo k **hlavnímu serveru**.

1. Když vyberete **Uložit**, kanál sestavení se automaticky spustí. Vraťte se do souhrnu kanálu sestavení a sledujte stav.

   ![Zobrazení výsledků](./media/add-template-to-azure-pipelines/view-results.png)

Můžete vybrat aktuálně spuštěný kanál a zobrazit podrobnosti o úkolech. Po dokončení se zobrazí výsledky pro každý krok.

## <a name="next-steps"></a>Další kroky

Podrobný proces používání Azure Pipelines se šablonami ARM najdete [v tématu Kurz: Průběžná integrace šablon ARM s Azure Pipelines](template-tutorial-use-azure-pipelines.md).
