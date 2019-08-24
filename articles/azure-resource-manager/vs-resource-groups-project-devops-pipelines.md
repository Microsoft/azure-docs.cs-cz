---
title: CI/CD s šablonami Azure Pipelines a Správce prostředků
description: Popisuje, jak nastavit průběžnou integraci v Azure Pipelines pomocí projektů nasazení skupiny prostředků Azure v sadě Visual Studio k nasazení Správce prostředků šablon.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: ae896fa0820fbd25ed3f2d29c89fbcd56e7fd6f5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982447"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrace šablon Správce prostředků s Azure Pipelines

Visual Studio poskytuje projekt skupiny prostředků Azure pro vytváření šablon a jejich nasazování do předplatného Azure. Tento projekt můžete integrovat s Azure Pipelines pro průběžnou integraci a průběžné nasazování (CI/CD).

Existují dva způsoby, jak nasadit šablony s Azure Pipelines:

* **Přidejte úlohu, která spouští skript Azure PowerShell**. Tato možnost je výhodou zajištění konzistence během životního cyklu vývoje, protože používáte stejný skript, který je součástí projektu aplikace Visual Studio (Deploy-AzureResourceGroup. ps1). Skript rozhlíží artefakty z vašeho projektu na účet úložiště, ke kterému Správce prostředků získat přístup. Artefakty jsou položky ve vašem projektu, například propojené šablony, skripty a binární soubory aplikace. Skript pak nasadí šablonu.

* **Přidejte úkoly pro kopírování a nasazování úloh**. Tato možnost nabízí pohodlný alternativu pro skript projektu. V kanálu můžete nakonfigurovat dva úkoly. Jedna fáze úlohy artefakty a druhá úloha nasadí šablonu.

Tento článek ukazuje oba přístupy.

## <a name="prepare-your-project"></a>Příprava projektu

Tento článek předpokládá, že váš projekt sady Visual Studio a organizace Azure DevOps jsou připravené k vytvoření kanálu. Následující kroky ukazují, jak se ujistit, že jste připraveni:

* Máte organizaci Azure DevOps. Pokud ho ještě nemáte, [Vytvořte si ho zdarma](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Pokud má váš tým již organizaci Azure DevOps, ujistěte se, že jste správcem projektu Azure DevOps, který chcete použít.

* Nakonfigurovali jste [připojení služby](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) ke svému předplatnému Azure. Úlohy v kanálu se spouštějí pod identitou instančního objektu. Postup vytvoření připojení najdete v tématu [Vytvoření projektu DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Máte projekt aplikace Visual Studio, který byl vytvořen ze šablony **skupiny prostředků Azure** Starter. Informace o vytváření tohoto typu projektu naleznete v tématu [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Projekt aplikace Visual Studio je [připojen k projektu Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Vytvoření kanálu

1. Pokud jste předtím nepřidali kanál, je nutné vytvořit nový kanál. Z vaší organizace Azure DevOps vyberte **kanály** a **Nový kanál**.

   ![Přidat nový kanál](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Určete, kde je váš kód uložený. Následující obrázek ukazuje výběr **Azure Repos Git**.

   ![Vybrat zdroj kódu](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Z tohoto zdroje vyberte úložiště, které obsahuje kód pro váš projekt.

   ![Vybrat úložiště](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Vyberte typ kanálu, který chcete vytvořit. Můžete vybrat **Počáteční kanál**.

   ![Vybrat kanál](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

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

Když nastavíte úlohu na `AzurePowerShell@3`, kanál použije k ověření připojení příkazy z modulu AzureRM. Ve výchozím nastavení používá skript prostředí PowerShell v projektu sady Visual Studio modul AzureRM. Pokud jste skript aktualizovali tak, aby používal [modul AZ Module](/powershell/azure/new-azureps-module-az), nastavte úlohu na `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

V poli zadejte název připojení služby ,kteréjstevytvořili.`azureSubscription`

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Pro `scriptPath`zadejte relativní cestu ze souboru kanálu ke skriptu. Můžete si prohlédnout své úložiště a zobrazit cestu.

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

   ![Uložení kanálu](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Zadejte zprávu pro potvrzení a proveďte zápis přímo do **Hlavní**větve.

1. Když vyberete **Save (Uložit**), kanál sestavení se automaticky spustí. Vraťte se ke shrnutí kanálu sestavení a sledujte stav.

   ![Zobrazení výsledků](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

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

K revizi vašeho prostředí se používá několik částí této úlohy. `SourcePath` Určuje umístění artefaktů relativně k souboru kanálu. V tomto příkladu soubory existují ve složce s názvem `AzureResourceGroup1` , což byl název projektu.

```yaml
SourcePath: '<path-to-artifacts>'
```

V poli zadejte název připojení služby ,kteréjstevytvořili.`azureSubscription`

```yaml
azureSubscription: '<your-connection-name>'
```

Jako název úložiště a kontejneru zadejte názvy účtu úložiště a kontejneru, které chcete použít pro ukládání artefaktů. Účet úložiště musí existovat.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Následující YAML ukazuje [úlohu nasazení skupiny prostředků Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

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

K revizi vašeho prostředí se používá několik částí této úlohy. V poli zadejte název připojení služby ,kteréjstevytvořili.`azureSubscription`

```yaml
azureSubscription: '<your-connection-name>'
```

V `resourceGroupName` případě `location`a zadejte název a umístění skupiny prostředků, do které chcete nasadit. Tato úloha vytvoří skupinu prostředků, pokud neexistuje.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

Úloha nasazení odkazuje na šablonu s názvem `WebSite.json` a soubor parametrů s názvem Web. Parameters. JSON. Použijte názvy šablon a souborů parametrů.

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
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Vyberte **Uložit**.

1. Zadejte zprávu pro potvrzení a proveďte zápis přímo do **Hlavní**větve.

1. Když vyberete **Save (Uložit**), kanál sestavení se automaticky spustí. Vraťte se ke shrnutí kanálu sestavení a sledujte stav.

   ![Zobrazení výsledků](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Můžete vybrat aktuálně běžící kanál a zobrazit podrobnosti o těchto úlohách. Až se dokončí, zobrazí se výsledky pro každý krok.

## <a name="next-steps"></a>Další postup

Podrobný postup při použití Azure Pipelines se šablonami správce prostředků najdete v tématu [kurz: Průběžná integrace šablon Azure Resource Manager s Azure Pipelines](resource-manager-tutorial-use-azure-pipelines.md)
