---
title: Průběžná integrace s využitím Azure Pipelines
description: Naučte se průběžně sestavovat, testovat a nasazovat šablony Azure Resource Manager.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 457d2a8868283eed2e211ff26ce77156eee68e01
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326414"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Kurz: průběžná integrace šablon Azure Resource Manager s Azure Pipelines

Naučte se používat Azure Pipelines k průběžnému sestavování a nasazování projektů šablon Azure Resource Manager.

Azure DevOps poskytuje Vývojářské služby pro podporu týmů pro plánování práce, spolupráci na vývoji kódu a sestavování a nasazování aplikací. Vývojáři můžou pracovat v cloudu pomocí Azure DevOps Services. Azure DevOps poskytuje integrovanou sadu funkcí, ke kterým můžete přistupovat prostřednictvím webového prohlížeče nebo klienta IDE. Kanál Azure je jednou z těchto funkcí. Azure Pipelines je plně funkční služba pro průběžnou integraci (CI) a průběžné doručování (CD). Funguje s vaším preferovaným poskytovatelem Git a může se nasadit do většiny hlavních cloudových služeb. Potom můžete automatizovat sestavení, testování a nasazení kódu pro Microsoft Azure, Google Cloud Platform nebo Amazon Web Services.

Tento kurz je určený pro Azure Resource Manager vývojáře šablon, kteří jsou novými Azure DevOps Services a Azure Pipelines. Pokud už jste obeznámeni s GitHubem a DevOps, můžete přeskočit na [vytvoření kanálu](#create-a-pipeline).

> [!NOTE]
> Vyberte název projektu. Když projdete kurz, nahraďte **AzureRmPipeline** názvem vašeho projektu.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava úložiště GitHub
> * Vytvoření projektu Azure DevOps
> * Vytvoření kanálu Azure
> * Ověření nasazení kanálu
> * Aktualizujte šablonu a znovu nasaďte
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* **Účet GitHubu**, kde ho použijete k vytvoření úložiště pro šablony. Pokud ho ještě nemáte, můžete si [ho vytvořit zdarma](https://github.com). Další informace o použití úložišť GitHub najdete v tématu [sestavení úložišť GitHubu](/azure/devops/pipelines/repos/github).
* **Nainstalovat Git** Tato instrukce kurzu používá *Git bash* nebo *git shell*. Pokyny najdete v tématu [instalace Gitu]( https://www.atlassian.com/git/tutorials/install-git).
* **Organizace Azure DevOps**. Pokud ho ještě nemáte, můžete si ho vytvořit zdarma. Viz [vytvoření organizace nebo kolekce projektů]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Pokud [chcete vytvořit Azure Resource Manager šablony](./resource-manager-tools-vs-code.md), přečtěte si téma použití Visual Studio Code.

## <a name="prepare-a-github-repository"></a>Příprava úložiště GitHub

GitHub slouží k uložení zdrojového kódu projektu, včetně šablon Správce prostředků. Další podporovaná úložiště najdete v tématu [úložiště podporovaná službou Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Vytvoření úložiště GitHub

Pokud nemáte účet GitHubu, přečtěte si téma [požadavky](#prerequisites).

1. Přihlaste se k [GitHubu](https://github.com).
2. V pravém horním rohu vyberte svůj obrázek účtu a pak vyberte **svoje úložiště**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvoření úložiště GitHubu](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Vyberte **nové**a zelené tlačítko.
1. Do **název úložiště**zadejte název úložiště.  Například **AzureRmPipeline-úložiště**. Nezapomeňte nahradit libovolný z **AzureRmPipeline** názvem vašeho projektu. V rámci tohoto kurzu můžete vybrat buď **veřejné** , nebo **soukromé** . A pak vyberte **vytvořit úložiště**.
1. Zapište adresu URL. Adresa URL úložiště má následující formát:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Toto úložiště se označuje jako *vzdálené úložiště*. Každý z vývojářů stejného projektu může klonovat své vlastní *místní úložiště*a sloučit změny do vzdáleného úložiště.

### <a name="clone-the-remote-repository"></a>Klonování vzdáleného úložiště

1. Otevřete prostředí Git nebo Git bash.  Viz [Požadavky](#prerequisites).
1. Ověřte, jestli je aktuální složka **GitHub**.
1. Spusťte následující příkaz:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Nahraďte **[YourAccountName]** názvem vašeho účtu GitHub a nahraďte **[YourGitHubRepositoryName]** názvem vašeho úložiště, který jste vytvořili v předchozím postupu.

    Příklad ukazuje následující snímek obrazovky.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvoření GitHubu bash](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

Složka **CreateAzureStorage** je složka, ve které je šablona uložena. Příkaz **PWD** zobrazuje cestu ke složce. Cesta je místo, kam šablonu uložíte, do následujícího postupu.

### <a name="download-a-quickstart-template"></a>Stažení šablony pro rychlé zprovoznění

Místo vytváření šablony si můžete stáhnout [šablonu pro rychlý Start]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Tato šablona vytvoří účet Azure Storage.

1. Otevřete Visual Studio Code. Viz [Požadavky](#prerequisites).
2. Otevřete šablonu s následující adresou URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Uložte soubor jako **azuredeploy. JSON** do složky **CreateAzureStorage** . Název složky i název souboru se používají tak, jak jsou v kanálu.  Pokud tyto názvy změníte, je nutné aktualizovat názvy používané v kanálu.

### <a name="push-the-template-to-the-remote-repository"></a>Vložení šablony do vzdáleného úložiště

Azuredeploy. JSON se přidal do místního úložiště. V dalším kroku nahrajete šablonu do vzdáleného úložiště.

1. Otevřete *prostředí Git* nebo *Git bash*, pokud není otevřené.
1. Změňte adresář na složku CreateAzureStorage v místním úložišti.
1. Ověřte, zda je soubor **azuredeploy. JSON** ve složce.
1. Spusťte následující příkaz:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Může se zobrazit upozornění na LF. Upozornění můžete ignorovat. **Hlavní** větev je hlavní.  Pro každou aktualizaci obvykle vytvoříte větev. Pro zjednodušení tohoto kurzu budete používat hlavní větev přímo.
1. Přejděte do úložiště GitHub z prohlížeče.  Adresa URL je **https://github.com/[YourAccountName]/[YourGitHubRepository]** . V rámci složky se zobrazí složka **CreateAzureStorage** a **Azuredeploy. JSON** .

Zatím jste vytvořili úložiště GitHub a nahráli šablonu do úložiště.

## <a name="create-a-devops-project"></a>Vytvoření projektu DevOps

Aby bylo možné přejít k dalšímu postupu, je nutné, aby byla DevOps organizace.  Pokud ho nemáte, přečtěte si téma [požadavky](#prerequisites).

1. Přihlaste se k [Azure DevOps](https://dev.azure.com).
1. Vyberte DevOps organizaci vlevo.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvoření projektu Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Vyberte **Vytvořit projekt**. Pokud nemáte žádné projekty, otevře se stránka vytvořit projekt automaticky.
1. Zadejte následující hodnoty:

    * **Název projektu**: zadejte název projektu. Můžete použít název projektu, který jste vybrali na začátku kurzu.
    * **Správa verzí**: vyberte **Git**. Pro zobrazení **správy verzí**možná budete muset rozbalit možnost **Upřesnit** .

    Pro ostatní vlastnosti použijte výchozí hodnotu.
1. Vyberte **Vytvořit projekt**.

Vytvořte připojení služby, které se používá k nasazení projektů do Azure.

1. V dolní části nabídky vlevo vyberte **nastavení projektu** .
1. V části **kanály**vyberte **připojení služby** .
1. Vyberte **nové připojení služby**a pak vyberte **AzureResourceManager**.
1. Zadejte následující hodnoty:

    * **Název připojení**: zadejte název připojení. Například **AzureRmPipeline-připojeno**. Poznamenejte si tento název. při vytváření kanálu budete potřebovat název.
    * **Úroveň oboru**: vyberte **předplatné**.
    * **Předplatné**: vyberte své předplatné.
    * **Skupina prostředků**: ponechte pole prázdné.
    * **Povolí všem kanálům používat toto připojení**. Vyberte
1. Vyberte **OK**.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Až do této chvíle jste dokončili následující úlohy.  Pokud přeskočíte předchozí oddíly, protože jste obeznámeni s GitHubem a DevOps, musíte před pokračováním dokončit úkoly.

- Vytvořte úložiště GitHub a uložte [tuto šablonu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do složky **CreateAzureStorage** v úložišti.
- Vytvořte projekt DevOps a vytvořte připojení služby Azure Resource Manager.

Vytvoření kanálu s krokem pro nasazení šablony:

1. V nabídce vlevo vyberte **kanály** .
1. Vyberte **Nový kanál**.
1. Na kartě **připojit** vyberte **GitHub**. Pokud se zobrazí výzva, zadejte svoje přihlašovací údaje k GitHubu a pak postupujte podle pokynů. Pokud se zobrazí následující obrazovka, vyberte **jenom možnost vybrat úložiště**a ověřte, jestli je vaše úložiště v seznamu, než vyberete **schválit & nainstalovat**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vybrat jenom úložiště](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na kartě **Vybrat** vyberte své úložiště.  Výchozí název je **[YourAccountName]/[YourGitHubRepositoryName]** .
1. Na kartě **Konfigurace** vyberte **Počáteční kanál**. Zobrazuje soubor kanálu **Azure-Pipelines. yml** se dvěma kroky skriptu.
1. Nahraďte oddíl **kroků** následujícím YAML:

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Bude vypadat takto:

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Proveďte následující změny:

    * **deloymentScope**: vyberte rozsah nasazení z možností: `Management Group`, `Subscription` a `Resource Group`. Použijte **skupinu prostředků** v tomto kurzu. Další informace o oborech najdete v tématu [obory nasazení](./resource-group-template-deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: zadejte název připojení služby, který jste vytvořili dříve.
    * **Subscription**: Zadejte ID cílového předplatného.
    * **Akce**: akce **vytvořit nebo aktualizovat skupinu prostředků** provede 2 akce-1. Pokud je zadaný název nové skupiny prostředků, vytvořte skupinu prostředků. odst. Nasaďte zadanou šablonu.
    * **resourceGroupName**: zadejte nový název skupiny prostředků. Například **AzureRmPipeline-RG**.
    * **umístění**: zadejte umístění pro skupinu prostředků.
    * **templateLocation**: když je zadán **propojený artefakt** , úloha vyhledá soubor šablony přímo z připojeného úložiště.
    * **csmFile** je cesta k souboru šablony. Nemusíte zadávat soubor parametrů šablony, protože všechny parametry definované v šabloně mají výchozí hodnoty.

    Další informace o úloze najdete v tématu [Úloha nasazení skupiny prostředků Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)a [úloha nasazení šablony Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) .
1. Vyberte **Uložit a spustit**.
1. Vyberte **Uložit a spustit** znovu. Kopie souboru YAML se uloží do připojeného úložiště. Soubor YAML můžete zobrazit tak, že přejdete do úložiště.
1. Ověřte, že se kanál úspěšně spustil.

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. Otevřete skupinu prostředků. Název je to, co jste zadali v souboru YAML kanálu.  Uvidíte, že se vytvořil jeden účet úložiště.  Název účtu úložiště začíná na **Storu**.
1. Vyberte název účtu úložiště, který chcete otevřít.
1. Vyberte **Vlastnosti**. Všimněte si, že **replikace** je **místně redundantní úložiště (LRS)** .

    ![Azure Resource Manager ověřování portálu Azure Pipelines Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Aktualizace a opětovné nasazení

Když aktualizujete šablonu a nahrajete změny do vzdáleného úložiště, kanál v tomto případě automaticky aktualizuje prostředky a účet úložiště.

1. Otevřete **azuredeploy. JSON** z místního úložiště v Visual Studio Code.
1. Aktualizujte hodnotu DefaultValue **storageAccountType** na **Standard_GRS**. Viz následující snímek obrazovky:

    ![Azure Resource Manager Azure DevOps Azure Pipelines Update YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Uložte změny.
1. Dodejte změny do vzdáleného úložiště spuštěním následujících příkazů z Gitu bash/shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    První příkaz synchronizuje místní úložiště se vzdáleným úložištěm. Zapamatujte si, že se soubor YAML kanálu přidal do vzdáleného úložiště.

    Když se hlavní větev vzdáleného úložiště aktualizuje, kanál se znovu aktivuje.

Chcete-li ověřit změny, můžete zkontrolovat vlastnost replikace účtu úložiště.  Viz [Ověření nasazení](#verify-the-deployment).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

Možná budete chtít odstranit i úložiště GitHub a projekt Azure DevOps.

## <a name="next-steps"></a>Další kroky

V tomto kurzu vytvoříte kanál Azure DevOps, který nasadí šablonu Azure Resource Manager. Informace o nasazování prostředků Azure napříč několika oblastmi a používání postupů bezpečného nasazení najdete tady:

> [!div class="nextstepaction"]
> [Použití postupů bezpečného nasazení](./deployment-manager-tutorial.md)
