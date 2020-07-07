---
title: CI/CD s Azure Pipelines a šablonami
description: Popisuje, jak nastavit průběžnou integraci v Azure Pipelines pomocí projektů nasazení skupiny prostředků Azure v sadě Visual Studio k nasazení Správce prostředků šablon.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82084647"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrace šablon ARM pomocí Azure Pipelines

Visual Studio poskytuje projekt skupiny prostředků Azure pro vytváření šablon Azure Resource Manager (ARM) a jejich nasazování do předplatného Azure. Tento projekt můžete integrovat s Azure Pipelines pro průběžnou integraci a průběžné nasazování (CI/CD).

Existují dva způsoby, jak nasadit šablony s Azure Pipelines:

* **Přidejte úlohu, která spouští skript Azure PowerShell**. Tato možnost je výhodou zajištění konzistence během životního cyklu vývoje, protože používáte stejný skript, který je součástí projektu aplikace Visual Studio (Deploy-AzureResourceGroup.ps1). Skript rozhlíží artefakty z vašeho projektu na účet úložiště, ke kterému Správce prostředků získat přístup. Artefakty jsou položky ve vašem projektu, například propojené šablony, skripty a binární soubory aplikace. Skript pak nasadí šablonu.

* **Přidejte úkoly pro kopírování a nasazování úloh**. Tato možnost nabízí pohodlný alternativu pro skript projektu. V kanálu můžete nakonfigurovat dva úkoly. Jedna fáze úlohy artefakty a druhá úloha nasadí šablonu.

Tento článek ukazuje oba přístupy.

## <a name="prepare-your-project"></a>Příprava projektu

Tento článek předpokládá, že váš projekt sady Visual Studio a organizace Azure DevOps jsou připravené k vytvoření kanálu. Následující kroky ukazují, jak se ujistit, že jste připraveni:

* Máte organizaci Azure DevOps. Pokud ho ještě nemáte, [Vytvořte si ho zdarma](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Pokud má váš tým již organizaci Azure DevOps, ujistěte se, že jste správcem projektu Azure DevOps, který chcete použít.

* Nakonfigurovali jste [připojení služby](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) ke svému předplatnému Azure. Úlohy v kanálu se spouštějí pod identitou instančního objektu. Postup vytvoření připojení najdete v tématu [Vytvoření projektu DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Máte projekt aplikace Visual Studio, který byl vytvořen ze šablony **skupiny prostředků Azure** Starter. Informace o vytváření tohoto typu projektu naleznete v tématu [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](create-visual-studio-deployment-project.md).

* Projekt aplikace Visual Studio je [připojen k projektu Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Vytvoření kanálu

1. Pokud jste předtím nepřidali kanál, je nutné vytvořit nový kanál. Z vaší organizace Azure DevOps vyberte **kanály** a **Nový kanál**.

   ![Přidat nový kanál](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Určete, kde je váš kód uložený. Následující obrázek ukazuje výběr **Azure Repos Git**.

   ![Vybrat zdroj kódu](./media/add-template-to-azure-pipelines/select-source.png)

1. Z tohoto zdroje vyberte úložiště, které obsahuje kód pro váš projekt.

   ![Vybrat úložiště](./media/add-template-to-azure-pipelines/select-repo.png)

1. Vyberte typ kanálu, který chcete vytvořit. Můžete vybrat **Počáteční kanál**.

   ![Vybrat kanál](./media/add-template-to-azure-pipelines/select-pipeline.png)

Jste připraveni přidat úlohu Azure PowerShell nebo kopírovat soubor a nasazovat úlohy.

## <a name="azure-powershell-task"></a>Azure PowerShell úkol

V této části se dozvíte, jak nakonfigurovat průběžné nasazování pomocí jedné úlohy, která spouští skript PowerShellu v projektu. Následující soubor YAML vytvoří [úlohu Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

Když nastavíte úlohu na `AzurePowerShell@3` , kanál použije k ověření připojení příkazy z modulu AzureRM. Ve výchozím nastavení používá skript prostředí PowerShell v projektu sady Visual Studio modul AzureRM. Pokud jste skript aktualizovali tak, aby používal [modul AZ Module](/powershell/azure/new-azureps-module-az), nastavte úlohu na `AzurePowerShell@4` .

```yaml
steps:
- task: AzurePowerShell@4
```

V `azureSubscription` poli zadejte název připojení služby, které jste vytvořili.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Pro `scriptPath` zadejte relativní cestu ze souboru kanálu ke skriptu. Můžete si prohlédnout své úložiště a zobrazit cestu.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Pokud nepotřebujete připravit artefakty, stačí předat název a umístění skupiny prostředků, která se má použít pro nasazení. Skript sady Visual Studio vytvoří skupinu prostředků, pokud ještě neexistuje.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Pokud potřebujete připravit artefakty na existující účet úložiště, použijte:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Teď, když jste se seznámili s vytvářením úlohy, provedeme kroky pro úpravu kanálu.

1. Otevřete svůj kanál a nahraďte jeho obsah vaším YAML:

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

1. Zadejte zprávu pro potvrzení a proveďte zápis přímo do **Hlavní**větve.

1. Když vyberete **Save (Uložit**), kanál sestavení se automaticky spustí. Vraťte se ke shrnutí kanálu sestavení a sledujte stav.

   ![Zobrazení výsledků](./media/add-template-to-azure-pipelines/view-results.png)

Můžete vybrat aktuálně běžící kanál a zobrazit podrobnosti o těchto úlohách. Až se dokončí, zobrazí se výsledky pro každý krok.

## <a name="copy-and-deploy-tasks"></a>Kopírování a nasazení úloh

V této části se dozvíte, jak nakonfigurovat průběžné nasazování pomocí dvou úloh pro přípravu artefaktů a nasazení šablony.

Následující YAML ukazuje [úlohu kopírování souborů Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

K revizi vašeho prostředí se používá několik částí této úlohy. `SourcePath`Určuje umístění artefaktů relativně k souboru kanálu. V tomto příkladu soubory existují ve složce s názvem `AzureResourceGroup1` , což byl název projektu.

```yaml
SourcePath: '<path-to-artifacts>'
```

V `azureSubscription` poli zadejte název připojení služby, které jste vytvořili.

```yaml
azureSubscription: '<your-connection-name>'
```

Jako název úložiště a kontejneru zadejte názvy účtu úložiště a kontejneru, které chcete použít pro ukládání artefaktů. Účet úložiště musí existovat.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Následující YAML ukazuje [úlohu nasazení šablony Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

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

K revizi vašeho prostředí se používá několik částí této úlohy.

- `deploymentScope`: Vyberte rozsah nasazení z možností: `Management Group` `Subscription` a `Resource Group` . V tomto návodu použijte **skupinu prostředků** . Další informace o oborech najdete v tématu [obory nasazení](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Zadejte název připojení služby, které jste vytvořili.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Zadejte ID cílového předplatného. Tato vlastnost se vztahuje pouze na rozsah nasazení skupiny prostředků a obor nasazení předplatného.

- `resourceGroupName`a `location` : zadejte název a umístění skupiny prostředků, do které chcete nasadit. Tato úloha vytvoří skupinu prostředků, pokud neexistuje.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

Úloha nasazení odkazuje na šablonu s názvem `WebSite.json` a soubor parametrů s názvem WebSite.parameters.jsv. Použijte názvy šablon a souborů parametrů.

Teď, když jste se seznámili s vytvářením úloh, Projděte si postup pro úpravu kanálu.

1. Otevřete svůj kanál a nahraďte jeho obsah vaším YAML:

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

1. Zadejte zprávu pro potvrzení a proveďte zápis přímo do **Hlavní**větve.

1. Když vyberete **Save (Uložit**), kanál sestavení se automaticky spustí. Vraťte se ke shrnutí kanálu sestavení a sledujte stav.

   ![Zobrazení výsledků](./media/add-template-to-azure-pipelines/view-results.png)

Můžete vybrat aktuálně běžící kanál a zobrazit podrobnosti o těchto úlohách. Až se dokončí, zobrazí se výsledky pro každý krok.

## <a name="next-steps"></a>Další kroky

Podrobný postup použití Azure Pipelines se šablonami ARM najdete v tématu [kurz: průběžná integrace šablon Azure Resource Manager pomocí Azure Pipelines](deployment-tutorial-pipeline.md).
