---
title: Průběžná integrace s využitím Azure Pipelines
description: Zjistěte, jak průběžně vytvářet, testovat a nasazovat šablony Azure Resource Manageru.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 303edac9221b2a58986129388e33c84c3817d96e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369367"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Kurz: Průběžná integrace šablon Azure Resource Manageru s Azure Pipelines

V [předchozím kurzu](./deployment-tutorial-linked-template.md)nasadíte propojenou šablonu.  V tomto kurzu se dozvíte, jak používat Azure Pipelines k průběžnému vytváření a nasazování projektů šablon Azure Resource Manageru.

Azure DevOps poskytuje vývojářské služby pro podporu týmů pro plánování práce, spolupráci na vývoji kódu a vytváření a nasazování aplikací. Vývojáři můžou pracovat v cloudu pomocí služeb Azure DevOps Services. Azure DevOps poskytuje integrovanou sadu funkcí, ke kterým máte přístup prostřednictvím webového prohlížeče nebo klienta IDE. Azure Pipeline je jednou z těchto funkcí. Azure Pipelines je plně vybavená služba průběžné integrace (CI) a průběžného doručování (CD). Spolupracuje s vaším preferovaným poskytovatelem Gitu a může se nasazovat do většiny hlavních cloudových služeb. Potom můžete automatizovat sestavení, testování a nasazení kódu do Microsoft Azure, Google Cloud Platform nebo Amazon Web Services.

> [!NOTE]
> Vyberte název projektu. Když procházíte kurz, nahraďte některý z **AzureRmPipeline** s názvem projektu.
> Tento název projektu se používá ke generování názvů zdrojů.  Jedním z prostředků je účet úložiště. Názvy účtů úložiště musí mít délku 3 až 24 znaků a používat pouze čísla a malá písmena. Název musí být jedinečný. V šabloně je název účtu úložiště název projektu s připojeným "store" a název projektu musí být mezi 3 a 11 znaků. Název projektu tedy musí splňovat požadavky na název účtu úložiště a má méně než 11 znaků.

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

* **Účet GitHub**, kde jej použijete k vytvoření úložiště pro šablony. Pokud ho nemáte, můžete [si ho vytvořit zdarma](https://github.com). Další informace o používání úložišť GitHub najdete v [tématu Vytváření úložišť GitHub](/azure/devops/pipelines/repos/github).
* **Nainstalujte Git**. Tento kurz instrukce používá *Git Bash* nebo *Git Shell*. Pokyny naleznete v [tématu Instalace Gitu]( https://www.atlassian.com/git/tutorials/install-git).
* **Organizace Azure DevOps**. Pokud ho nemáte, můžete si ho vytvořit zdarma. Viz [Vytvoření kolekce organizace nebo projektu]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* (nepovinné) **Visual Studio Kód s rozšířením Nástroje Správce prostředků**. Viz [Použití kódu Visual Studia k vytvoření šablon Azure Resource Manageru](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Příprava úložiště GitHub

GitHub se používá k ukládání zdrojového kódu projektu včetně šablon Správce prostředků. Další podporovaná úložiště najdete v [tématu repozitáře podporované Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Vytvoření úložiště GitHubu

Pokud nemáte účet GitHub, [přečtěte si téma Požadavky](#prerequisites).

1. Přihlaste se ke [GitHubu](https://github.com).
1. V pravém horním rohu vyberte obrázek svého účtu a pak **vyberte Vaše repozitáře**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvářejí úložiště GitHub](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Vyberte **Nové**, zelené tlačítko.
1. Do **názvu úložiště**zadejte název úložiště.  Například **AzureRmPipeline-repo**. Nezapomeňte nahradit některý z **AzureRmPipeline** s názvem projektu. Můžete vybrat **veřejné** nebo **soukromé** pro procházení tohoto kurzu. A pak vyberte **Vytvořit úložiště**.
1. Poznamenejte si adresu URL. Adresa URL úložiště má následující formát:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Toto úložiště se označuje jako *vzdálené úložiště*. Každý z vývojářů stejného projektu může klonovat své vlastní *místní úložiště*a sloučit změny do vzdáleného úložiště.

### <a name="clone-the-remote-repository"></a>Klonování vzdáleného úložiště

1. Otevřete Git Shell nebo Git Bash.  Viz [Požadavky](#prerequisites).
1. Ověřte, zda je vaše aktuální složka **GitHub**.
1. Spusťte následující příkaz:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Nahraďte **[YourAccountName]** názvem účtu GitHub a nahraďte **[YourGitHubRepositoryName]** názvem úložiště, které jste vytvořili v předchozím postupu.

Složka **CreateWebApp** je složka, ve které je šablona uložena. Příkaz **pwd** zobrazuje cestu ke složce. Cesta je místo, kde uložíte šablonu do následujícího postupu.

### <a name="download-a-quickstart-template"></a>Stažení šablony rychlého startu

Namísto vytváření šablon si můžete šablony stáhnout a uložit je do složky **CreateWebApp.**

* Hlavní šablona:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* Propojená šablona:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Název složky i názvy souborů se používají tak, jak jsou v kanálu.  Pokud tyto názvy změníte, je nutné aktualizovat názvy použité v kanálu.

### <a name="push-the-template-to-the-remote-repository"></a>Zasunutí šablony do vzdáleného úložiště

Azuredeploy.json byl přidán do místního úložiště. Dále nahrajete šablonu do vzdáleného úložiště.

1. Pokud *není otevřena,* otevřete Git Shell nebo Git *Bash.*
1. Změňte adresář do složky CreateWebApp v místním úložišti.
1. Ověřte, zda je soubor **azuredeploy.json** ve složce.
1. Spusťte následující příkaz:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Můžete dostat varování o LF. Upozornění můžete ignorovat. **master** je hlavní větev.  Obvykle vytvoříte větev pro každou aktualizaci. Chcete-li kurz zjednodušit, použijte přímo hlavní větev.
1. Přejděte do úložiště GitHub z prohlížeče.  Adresa URL je ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. Zobrazí se složka **CreateWebApp** a tři soubory uvnitř složky.
1. Chcete-li šablonu otevřít, vyberte **linkedStorageAccount.json.**
1. Vyberte tlačítko **Nezpracovaná.** Adresa URL je spuštěna **raw.githubusercontent.com**.
1. Zkopírujte adresu URL.  Tuto hodnotu je třeba zadat při konfiguraci kanálu později v kurzu.

Zatím jste vytvořili úložiště GitHub a nahráli šablony do úložiště.

## <a name="create-a-devops-project"></a>Vytvoření projektu DevOps

Před pokračováním dalšího postupu je zapotřebí organizace DevOps.  Pokud ho nemáte, přečtěte si [téma Požadavky](#prerequisites).

1. Přihlaste se k [Azure DevOps](https://dev.azure.com).
1. Vyberte organizaci DevOps zleva.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvoří projekt Azure DevOps](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Vyberte **Nový projekt**. Pokud nemáte žádné projekty, stránka vytvořit projekt se otevře automaticky.
1. Zadejte následující hodnoty:

    * **Název projektu**: zadejte název projektu. Název projektu, který jste vybrali na samém začátku kurzu.
    * **Správa verzí**: Vyberte **git**. Možná budete muset rozbalit **Upřesnit,** aby se **zobce mu pořídilo řízení verzí**.

    Použijte výchozí hodnotu pro ostatní vlastnosti.
1. Vyberte **Vytvořit**.

Vytvořte připojení služby, které se používá k nasazení projektů do Azure.

1. V dolní části levé nabídky vyberte **Nastavení projektu.**
1. V části **Kanály**vyberte **Možnost Servisní připojení** .
1. Vyberte **Nové připojení služby**, vyberte **Správce prostředků Azure**a pak vyberte **Další**.
1. Vyberte **Instanční objekt**a pak vyberte **Další**.
1. Zadejte následující hodnoty:

    * **Úroveň oboru**: vyberte **Odběr**.
    * **Předplatné**: vyberte předplatné.
    * **Skupina zdrojů**: Ponechte ji prázdnou.
    * **Název připojení**: zadejte název připojení. Například **AzureRmPipeline-conn**. Zapište si tento název, budete potřebovat název při vytváření kanálu.
    * **Udělte oprávnění k přístupu všem kanálům**. (vybráno)
1. Vyberte **Uložit**.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Dosud jste dokončili následující úkoly.  Pokud přeskočíte předchozí části, protože jste obeznámeni s GitHub a DevOps, musíte dokončit úkoly před pokračováním.

* Vytvořte úložiště GitHub a uložte šablony do složky **CreateWebApp** v úložišti.
* Vytvořte projekt DevOps a vytvořte připojení služby Azure Resource Manager.

Vytvoření kanálu s krokem nasazení šablony:

1. V levé nabídce vyberte **Možnost Potrubí.**
1. Vyberte **nový kanál**.
1. Na kartě **Connect** (Připojit) vyberte **GitHub**. Pokud budete dotázáni, zadejte svoje přihlašovací údaje githubu a postupujte podle pokynů. Pokud se zobrazí následující obrazovka, vyberte **Pouze vybrat úložiště**a před výběrem **možnosti Schválit & instalace**ověřte, zda je úložiště v seznamu.

    ![Azure Resource Manager Azure DevOps Azure Pipelines jenom vybrané úložiště](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na kartě **Vybrat** vyberte úložiště.  Výchozí název je **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Na kartě **Konfigurovat** vyberte **počáteční kanál**. Zobrazuje soubor kanálu **azure-pipelines.yml** se dvěma kroky skriptu.
1. Odstraňte dva kroky skriptu ze souboru yml.
1. Přesuňte kurzor na řádek po **krocích:**.
1. Chcete-li otevřít podokno **Úkoly,** vyberte zobrazit **pomocníka** na pravé straně obrazovky.
1. Vyberte **nasazení šablony ARM**.
1. Zadejte následující hodnoty:

    * **deploymentScope**: Vyberte **skupinu prostředků**.. Další informace o oborech naleznete v tématu [Obory nasazení](deploy-rest.md#deployment-scope).
    * **Připojení Azure Resource Manager:** Vyberte název připojení služby, který jste vytvořili dříve.
    * **Předplatné**: Zadejte cílové ID předplatného.
    * **Akce**: Vyberte akci **Vytvořit nebo aktualizovat skupinu prostředků,** která provádí 2 akce - 1. vytvořte skupinu prostředků, pokud je k dispozici nový název skupiny prostředků; 2. nasadit zadanou šablonu.
    * **Skupina prostředků**: Zadejte nový název skupiny prostředků. Například **AzureRmPipeline-rg**.
    * **Umístění**: Vyberte umístění pro skupinu prostředků, například **Centrální USA**.
    * **Umístění šablony**: Vyberte **Propojený artefakt**, což znamená, že úloha hledá soubor šablony přímo z připojeného úložiště.
    * **Šablona**: Zadejte **CreateWebApp/azuredeploy.json**. Pokud jste změnili název složky a název souboru, je třeba změnit tuto hodnotu.
    * **Parametry šablony**: Toto pole ponechejte prázdné. Hodnoty parametrů zadáte v parametrech **Přepsat šablonu.
    * **overrideParameters**: Enter **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]**. Nahraďte název projektu a adresu URL propojené šablony. Adresa URL propojené šablony je to, co jste si zapsali na konci [úložiště Create a GitHub](#create-a-github-repository).
    * **Režim nasazení**: Vyberte **možnost Přírůstkové**.
    * **Název nasazení**: Zadejte **DeployPipelineTemplate**. Než uvidíte název **nasazení**, vyberte možnost **Upřesnit** .

    ![Krok Azure Resource Manager Azure DevOps Azure Pipelines](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Vyberte **Přidat**.

    Další informace o úkolu najdete v [tématech Úloha nasazení skupiny prostředků Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)a [úloha nasazení šablony Azure Resource Manageru.](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    Soubor yml musí být podobný:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Vyberte **Uložit a spustit**.
1. V podokně **Uložit a spustit** vyberte Uložit a **spustit** znovu. Kopie souboru YAML je uložena do připojeného úložiště. Soubor YAML můžete zobrazit podle procházení vašeho úložiště.
1. Ověřte, zda je kanál úspěšně proveden.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Otevřete skupinu prostředků. Název je to, co jste zadali v kanálu Soubor UAML.  Zobrazí se jeden účet úložiště vytvořen.  Název účtu úložiště začíná **store**.
1. Vyberte název účtu úložiště, který chcete otevřít.
1. Vyberte **vlastnosti**. Všimněte si, **že replikace** je **místně redundantní úložiště (LRS).**

## <a name="update-and-redeploy"></a>Aktualizovat a znovu nasadit

Když aktualizujete šablonu a odešlete změny do vzdáleného úložiště, kanál automaticky aktualizuje prostředky, v tomto případě účet úložiště.

1. Otevřete **linkedStorageAccount.json** z místního úložiště v kódu Visual Studia nebo v libovolném textovém editoru.
1. Aktualizujte **výchozí hodnota** **storageAccountType** na **Standard_GRS**. Viz následující snímek obrazovky:

    ![Azure Resource Manager Azure DevOps Azure Pipelines aktualizuje yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Uložte změny.
1. Posunutím změn do vzdáleného úložiště spuštěním následujících příkazů z Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    První příkaz (pull) synchronizuje místní úložiště se vzdáleným úložištěm. Do vzdáleného úložiště byl přidán pouze soubor YAML kanálu. Spuštěním příkazu pull stáhnete kopii souboru YAML do místní větve.

    Čtvrtý příkaz (push) nahraje revidovaný soubor linkedStorageAccount.json do vzdáleného úložiště. S hlavní větev vzdáleného úložiště aktualizována, kanál je aktivována znovu.

Chcete-li ověřit změny, můžete zkontrolovat replikace vlastnost účtu úložiště.  Viz [Ověření nasazení](#verify-the-deployment).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

Můžete také odstranit úložiště GitHub a projekt Azure DevOps.

## <a name="next-steps"></a>Další kroky

Blahopřejeme, dokončili jste tento kurz nasazení šablony Správce prostředků. Dejte nám vědět, pokud máte nějaké připomínky a návrhy v sekci zpětná vazba. Děkujeme!
Jste připraveni přejít na pokročilejší koncepty šablon. Další kurz jde do více podrobností o použití referenční dokumentace šablony na pomoc s definováním prostředků k nasazení.

> [!div class="nextstepaction"]
> [Využití referenčních informací k šablonám](./template-tutorial-use-template-reference.md)
