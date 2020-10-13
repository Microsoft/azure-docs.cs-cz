---
title: CI/CD s Azure Pipelines a šablonami
description: Popisuje, jak nakonfigurovat průběžnou integraci v Azure Pipelines pomocí šablon Azure Resource Manager. Ukazuje, jak použít skript prostředí PowerShell nebo zkopírovat soubory do pracovního umístění a nasadit z něj.
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 6784df30340e4c54b8b1d6e82b45046666824315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653396"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrace šablon ARM se službou Azure Pipelines

Azure Resource Manager šablon (šablon ARM) můžete integrovat s Azure Pipelines pro kontinuální integraci a průběžné nasazování (CI/CD). Kurz [průběžné integrace šablon ARM pomocí Azure Pipelines](deployment-tutorial-pipeline.md) ukazuje, jak pomocí [úlohy nasazení šablony ARM](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) nasadit šablonu z úložiště GitHub. Tento přístup funguje, když chcete nasadit šablonu přímo z úložiště.

V tomto článku se seznámíte se dvěma způsoby nasazení šablon pomocí Azure Pipelines. V tomto článku se dozvíte, jak:

* **Přidejte úlohu, která spouští skript Azure PowerShell**. Tato možnost je výhodou zajištění konzistence během životního cyklu vývoje, protože můžete použít stejný skript, který jste použili při spouštění místních testů. Skript nasadí šablonu, ale může také provádět jiné operace, jako je například získání hodnot, které se mají použít jako parametry.

   Visual Studio poskytuje [projekt skupiny prostředků Azure](create-visual-studio-deployment-project.md) , který obsahuje skript prostředí PowerShell. Skript rozhlíží artefakty z vašeho projektu na účet úložiště, ke kterému Správce prostředků získat přístup. Artefakty jsou položky ve vašem projektu, například propojené šablony, skripty a binární soubory aplikace. Pokud chcete pokračovat v používání skriptu z projektu, použijte úlohu PowerShellového skriptu, která je uvedená v tomto článku.

* **Přidejte úkoly pro kopírování a nasazování úloh**. Tato možnost nabízí pohodlný alternativu pro skript projektu. V kanálu můžete nakonfigurovat dva úkoly. Jedna úloha postupně projedná artefakty do přístupného umístění. Druhá úloha nasadí šablonu z tohoto umístění.

## <a name="prepare-your-project"></a>Příprava projektu

Tento článek předpokládá, že vaše šablona ARM a organizace Azure DevOps jsou připravené k vytvoření kanálu. Následující kroky ukazují, jak se ujistit, že jste připraveni:

* Máte organizaci Azure DevOps. Pokud ho ještě nemáte, [Vytvořte si ho zdarma](/azure/devops/pipelines/get-started/pipelines-sign-up). Pokud má váš tým již organizaci Azure DevOps, ujistěte se, že jste správcem projektu Azure DevOps, který chcete použít.

* Nakonfigurovali jste [připojení služby](/azure/devops/pipelines/library/connect-to-azure) ke svému předplatnému Azure. Úlohy v kanálu se spouštějí pod identitou instančního objektu. Postup vytvoření připojení najdete v tématu [Vytvoření projektu DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Máte [šablonu ARM](quickstart-create-templates-use-visual-studio-code.md) , která definuje infrastrukturu pro váš projekt.

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

V této části se dozvíte, jak nakonfigurovat průběžné nasazování pomocí jedné úlohy, která spouští skript PowerShellu v projektu. Pokud potřebujete skript prostředí PowerShell, který nasadí šablonu, přečtěte si téma [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) nebo [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1).

Následující soubor YAML vytvoří [úlohu Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell):

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-Template.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

Když nastavíte úlohu na `AzurePowerShell@5` , kanál použije [modul AZ Module](/powershell/azure/new-azureps-module-az). Pokud ve svém skriptu používáte modul AzureRM, nastavte úlohu na `AzurePowerShell@3` .

```yaml
steps:
- task: AzurePowerShell@3
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

V nástroji `ScriptArguments` Zadejte všechny parametry, které váš skript potřebuje. Následující příklad ukazuje některé parametry skriptu, ale budete muset přizpůsobit parametry pro váš skript.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

Když vyberete **Save (Uložit**), kanál sestavení se automaticky spustí. Vraťte se ke shrnutí kanálu sestavení a sledujte stav.

![Zobrazení výsledků](./media/add-template-to-azure-pipelines/view-results.png)

Můžete vybrat aktuálně běžící kanál a zobrazit podrobnosti o těchto úlohách. Až se dokončí, zobrazí se výsledky pro každý krok.

## <a name="copy-and-deploy-tasks"></a>Kopírování a nasazení úloh

V této části se dozvíte, jak nakonfigurovat průběžné nasazování pomocí dvou úloh. První fáze úlohy artefakty do účtu úložiště a druhý úkol nasadí šablonu.

K kopírování souborů do účtu úložiště musí instanční objekt pro připojení služby mít přiřazenou roli Přispěvatel dat objektů BLOB úložiště nebo vlastníka dat objektu BLOB úložiště. Další informace najdete v tématu [Začínáme s AzCopy](../../storage/common/storage-use-azcopy-v10.md).

Následující YAML ukazuje [úlohu kopírování souborů Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy).

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

K revizi vašeho prostředí se používá několik částí této úlohy. `SourcePath`Určuje umístění artefaktů relativně k souboru kanálu.

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

Po vytvoření úlohy kopírovat soubor jste připraveni přidat úlohu pro nasazení připravené šablony.

Následující YAML ukazuje [úlohu nasazení šablony Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

K dispozici je několik částí této úlohy, které je potřeba zkontrolovat podrobněji.

- `deploymentScope`: Vyberte rozsah nasazení z možností: `Management Group` , `Subscription` a `Resource Group` . Další informace o oborech najdete v tématu [obory nasazení](deploy-rest.md#deployment-scope).

- `azureResourceManagerConnection`: Zadejte název připojení služby, které jste vytvořili.

- `subscriptionId`: Zadejte ID cílového předplatného. Tato vlastnost se vztahuje pouze na rozsah nasazení skupiny prostředků a obor nasazení předplatného.

- `resourceGroupName` a `location` : zadejte název a umístění skupiny prostředků, do které chcete nasadit. Tato úloha vytvoří skupinu prostředků, pokud neexistuje.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Zadejte odkaz na dvoufázové šablonu. Při nastavování hodnoty použijte proměnné vracené z úlohy kopírování souborů. Následující příklad odkazuje na šablonu s názvem mainTemplate.jsv. Složka s názvem **Templates** je obsažena, protože do umístění, do kterého úloha kopírování souborů zkopírovala soubor. V kanálu zadejte cestu k šabloně a název šablony.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

Váš kanál vypadá takto:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Když vyberete **Save (Uložit**), kanál sestavení se automaticky spustí. Vraťte se ke shrnutí kanálu sestavení a sledujte stav.

## <a name="next-steps"></a>Další kroky

Další informace o používání šablon ARM s akcemi na GitHubu najdete v tématu [nasazení Azure Resource Manager šablon pomocí akcí GitHubu](deploy-github-actions.md).
