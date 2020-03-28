---
title: Průběžná integrace s využitím Azure Pipelines
description: Zjistěte, jak průběžně vytvářet, testovat a nasazovat šablony Azure Resource Manageru.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e7d6b23aa3f35c99cf03f855152b2b231a60a965
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75921622"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Kurz: Průběžná integrace šablon Azure Resource Manageru s Azure Pipelines

Zjistěte, jak používat Azure Pipelines k průběžnému vytváření a nasazování projektů šablon Azure Resource Manageru.

Azure DevOps poskytuje vývojářské služby pro podporu týmů pro plánování práce, spolupráci na vývoji kódu a vytváření a nasazování aplikací. Vývojáři můžou pracovat v cloudu pomocí služeb Azure DevOps Services. Azure DevOps poskytuje integrovanou sadu funkcí, ke kterým máte přístup prostřednictvím webového prohlížeče nebo klienta IDE. Azure Pipeline je jednou z těchto funkcí. Azure Pipelines je plně vybavená služba průběžné integrace (CI) a průběžného doručování (CD). Spolupracuje s vaším preferovaným poskytovatelem Gitu a může se nasazovat do většiny hlavních cloudových služeb. Potom můžete automatizovat sestavení, testování a nasazení kódu do Microsoft Azure, Google Cloud Platform nebo Amazon Web Services.

Tento kurz je určený pro vývojáře šablon Azure Resource Manager, kteří jsou nové služby Azure DevOps services a Azure Pipelines. Pokud jste již obeznámeni s GitHub a DevOps, můžete přeskočit na [vytvoření kanálu](#create-a-pipeline).

> [!NOTE]
> Vyberte název projektu. Když procházíte kurz, nahraďte některý z **AzureRmPipeline** s názvem projektu.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava úložiště GitHub
> * Vytvoření projektu Azure DevOps
> * Vytvoření kanálu Azure
> * Ověření nasazení kanálu
> * Aktualizace šablony a opětovné nasazení
> * Vyčištění prostředků

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* **Účet GitHub**, kde jej použijete k vytvoření úložiště pro šablony. Pokud účet nemáte, můžete si ho [zdarma vytvořit](https://github.com). Další informace o používání úložišť GitHub najdete v [tématu Vytváření úložišť GitHub](/azure/devops/pipelines/repos/github).
* **Nainstalujte Git**. Tento kurz instrukce používá *Git Bash* nebo *Git Shell*. Pokyny naleznete v [tématu Instalace Gitu]( https://www.atlassian.com/git/tutorials/install-git).
* **Organizace Azure DevOps**. Pokud ho nemáte, můžete si ho vytvořit zdarma. Viz [Vytvoření kolekce organizace nebo projektu]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio Kód s rozšířením Nástroje Správce prostředků. Viz [Použití kódu Visual Studia k vytvoření šablon Azure Resource Manageru](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Příprava úložiště GitHub

GitHub se používá k ukládání zdrojového kódu projektu včetně šablon Správce prostředků. Další podporovaná úložiště najdete v [tématu repozitáře podporované Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Vytvoření úložiště GitHubu

Pokud nemáte účet GitHub, [přečtěte si téma Požadavky](#prerequisites).

1. Přihlaste se ke [GitHubu](https://github.com).
2. V pravém horním rohu vyberte obrázek svého účtu a pak **vyberte Vaše repozitáře**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvářejí úložiště GitHub](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Vyberte **Nové**, zelené tlačítko.
1. Do **názvu úložiště**zadejte název úložiště.  Například **AzureRmPipeline-repo**. Nezapomeňte nahradit některý z **AzureRmPipeline** s názvem projektu. Můžete vybrat **veřejné** nebo **soukromé** pro procházení tohoto kurzu. A pak vyberte **Vytvořit úložiště**.
1. Poznamenejte si adresu URL. Adresa URL úložiště má následující formát:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Toto úložiště se označuje jako *vzdálené úložiště*. Každý z vývojářů stejného projektu může klonovat své vlastní *místní úložiště*a sloučit změny do vzdáleného úložiště.

### <a name="clone-the-remote-repository"></a>Klonování vzdáleného úložiště

1. Otevřete Git Shell nebo Git Bash.  Viz [Požadavky](#prerequisites).
1. Ověřte, zda je aktuální složka **github**.
1. Spusťte následující příkaz:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Nahraďte **[YourAccountName]** názvem účtu GitHub a nahraďte **[YourGitHubRepositoryName]** názvem úložiště, které jste vytvořili v předchozím postupu.

    Následující snímek obrazovky ukazuje příklad.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvářejí GitHub bash](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

Složka **CreateAzureStorage** je složka, ve které je šablona uložena. Příkaz **pwd** zobrazuje cestu ke složce. Cesta je místo, kde uložíte šablonu do následujícího postupu.

### <a name="download-a-quickstart-template"></a>Stažení šablony rychlého startu

Místo vytváření šablony si můžete stáhnout [šablonu rychlého startu]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Tato šablona vytvoří účet Azure Storage.

1. Otevřete kód sady Visual Studio. Viz [Požadavky](#prerequisites).
2. Otevřete šablonu s následující adresou URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Uložte soubor jako **azuredeploy.json** do složky **CreateAzureStorage.** Název složky i název souboru se používají tak, jak jsou v kanálu.  Pokud tyto názvy změníte, je nutné aktualizovat názvy použité v kanálu.

### <a name="push-the-template-to-the-remote-repository"></a>Zasunutí šablony do vzdáleného úložiště

Azuredeploy.json byl přidán do místního úložiště. Dále nahrajete šablonu do vzdáleného úložiště.

1. Pokud *není otevřena,* otevřete Git Shell nebo Git *Bash.*
1. Změňte adresář do složky CreateAzureStorage v místním úložišti.
1. Ověřte, zda je soubor **azuredeploy.json** ve složce.
1. Spusťte následující příkaz:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Můžete dostat varování o LF. Upozornění můžete ignorovat. **master** je hlavní větev.  Obvykle vytvoříte větev pro každou aktualizaci. Chcete-li kurz zjednodušit, použijte přímo hlavní větev.
1. Přejděte do úložiště GitHub z prohlížeče.  Adresa URL je ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. Zobrazí se složka **CreateAzureStorage** a **Azuredeploy.json** uvnitř složky.

Zatím jste vytvořili úložiště GitHub a nahráli šablonu do úložiště.

## <a name="create-a-devops-project"></a>Vytvoření projektu DevOps

Před pokračováním dalšího postupu je zapotřebí organizace DevOps.  Pokud ho nemáte, přečtěte si [téma Požadavky](#prerequisites).

1. Přihlaste se k [Azure DevOps](https://dev.azure.com).
1. Vyberte organizaci DevOps zleva.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvoří projekt Azure DevOps](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Vyberte **Vytvořit projekt**. Pokud nemáte žádné projekty, stránka vytvořit projekt se otevře automaticky.
1. Zadejte následující hodnoty:

    * **Název projektu**: zadejte název projektu. Název projektu, který jste vybrali na samém začátku kurzu.
    * **Správa verzí**: Vyberte **git**. Možná budete muset rozbalit **Upřesnit,** aby se **zobce mu pořídilo řízení verzí**.

    Použijte výchozí hodnotu pro ostatní vlastnosti.
1. Vyberte **Vytvořit projekt**.

Vytvořte připojení služby, které se používá k nasazení projektů do Azure.

1. V dolní části levé nabídky vyberte **Nastavení projektu.**
1. V části **Kanály**vyberte **Možnost Servisní připojení** .
1. Vyberte **Nové připojení služby**a pak vyberte **AzureResourceManager**.
1. Zadejte následující hodnoty:

    * **Název připojení**: zadejte název připojení. Například **AzureRmPipeline-conn**. Zapište si tento název, budete potřebovat název při vytváření kanálu.
    * **Úroveň oboru**: vyberte **Odběr**.
    * **Předplatné**: vyberte předplatné.
    * **Skupina zdrojů**: Ponechte ji prázdnou.
    * **Povolit všem kanálům používat toto připojení**. (vybráno)
1. Vyberte **OK**.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Dosud jste dokončili následující úkoly.  Pokud přeskočíte předchozí části, protože jste obeznámeni s GitHub a DevOps, musíte dokončit úkoly před pokračováním.

- Vytvořte úložiště GitHub a uložte [tuto šablonu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do složky **CreateAzureStorage** v úložišti.
- Vytvořte projekt DevOps a vytvořte připojení služby Azure Resource Manager.

Vytvoření kanálu s krokem nasazení šablony:

1. V levé nabídce vyberte **Možnost Potrubí.**
1. Vyberte **nový kanál**.
1. Na kartě **Connect** (Připojit) vyberte **GitHub**. Pokud budete dotázáni, zadejte svoje přihlašovací údaje githubu a postupujte podle pokynů. Pokud se zobrazí následující obrazovka, vyberte **Pouze vybrat úložiště**a před výběrem **možnosti Schválit & instalace**ověřte, zda je úložiště v seznamu.

    ![Azure Resource Manager Azure DevOps Azure Pipelines jenom vybrané úložiště](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na kartě **Vybrat** vyberte úložiště.  Výchozí název je **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Na kartě **Konfigurovat** vyberte **počáteční kanál**. Zobrazuje soubor kanálu **azure-pipelines.yml** se dvěma kroky skriptu.
1. Nahraďte oddíl **kroků** následujícím yaml:

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

    Musí to vypadat takto:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Proveďte následující změny:

    * **deploymentScope**: Vyberte rozsah nasazení `Management Group`z `Subscription` `Resource Group`možností: a . V tomto kurzu použijte **skupinu prostředků.** Další informace o oborech naleznete v tématu [Obory nasazení](deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: Zadejte název připojení služby, který jste vytvořili dříve.
    * **Název_předplatného**: Zadejte ID cílového předplatného.
    * **akce**: Akce **Vytvořit nebo aktualizovat skupinu prostředků** provádí 2 akce - 1. vytvořte skupinu prostředků, pokud je k dispozici nový název skupiny prostředků; 2. nasadit zadanou šablonu.
    * **resourceGroupName**: zadejte nový název skupiny prostředků. Například **AzureRmPipeline-rg**.
    * **umístění**: zadejte umístění skupiny prostředků.
    * **templateLocation**: když je zadán **propojený artefakt,** úloha hledá soubor šablony přímo z připojeného úložiště.
    * **csmFile** je cesta k souboru šablony. Není nutné zadávat soubor parametrů šablony, protože všechny parametry definované v šabloně mají výchozí hodnoty.

    Další informace o úkolu najdete v [tématech Úloha nasazení skupiny prostředků Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)a [úloha nasazení šablony Azure Resource Manageru.](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Vyberte **Uložit a spustit**.
1. Vyberte **Uložit a spustit** znovu. Kopie souboru YAML je uložena do připojeného úložiště. Soubor YAML můžete zobrazit podle procházení vašeho úložiště.
1. Ověřte, zda je kanál úspěšně proveden.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Otevřete skupinu prostředků. Název je to, co jste zadali v kanálu Soubor UAML.  Zobrazí se jeden účet úložiště vytvořen.  Název účtu úložiště začíná **store**.
1. Vyberte název účtu úložiště, který chcete otevřít.
1. Vyberte **vlastnosti**. Všimněte si, **že replikace** je **místně redundantní úložiště (LRS).**

    ![Ověření portálu Azure Resource Manager Azure DevOps Azure](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Aktualizovat a znovu nasadit

Když aktualizujete šablonu a odešlete změny do vzdáleného úložiště, kanál automaticky aktualizuje prostředky, v tomto případě účet úložiště.

1. Otevřete **azuredeploy.json** z místního úložiště v kódu Visual Studia.
1. Aktualizujte **výchozí hodnota** **storageAccountType** na **Standard_GRS**. Viz následující snímek obrazovky:

    ![Azure Resource Manager Azure DevOps Azure Pipelines aktualizuje yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Uložte změny.
1. Posunutím změn do vzdáleného úložiště spuštěním následujících příkazů z Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    První příkaz synchronizuje místní úložiště se vzdáleným úložištěm. Nezapomeňte, že do vzdáleného úložiště byl přidán soubor YAML kanálu.

    S hlavní větev vzdáleného úložiště aktualizována, kanál je aktivována znovu.

Chcete-li ověřit změny, můžete zkontrolovat replikace vlastnost účtu úložiště.  Viz [Ověření nasazení](#verify-the-deployment).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

Můžete také odstranit úložiště GitHub a projekt Azure DevOps.

## <a name="next-steps"></a>Další kroky

V tomto kurzu vytvoříte kanál Azure DevOps k nasazení šablony Azure Resource Manager. Informace o nasazování prostředků Azure napříč několika oblastmi a používání postupů bezpečného nasazení najdete tady:

> [!div class="nextstepaction"]
> [Použití postupů bezpečného nasazení](./deployment-manager-tutorial.md)
