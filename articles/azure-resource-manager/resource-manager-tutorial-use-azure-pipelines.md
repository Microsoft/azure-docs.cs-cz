---
title: Průběžná integrace se sadou Azure kanály | Dokumentace Microsoftu
description: Zjistěte, jak průběžné sestavování, testování a nasazování šablon Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 85dc0476da12bea64610b6910b0682fef00f4b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057789"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Kurz: Průběžná integrace šablon Azure Resource Manageru s kanály Azure

Zjistěte, jak pomocí Azure kanály průběžné sestavování a nasazování projekty ze šablony Azure Resource Manageru.

Azure DevOps poskytuje služby pro vývojáře pro podporu týmů na plánování práce, spolupráce na vývoj kódu a sestavování a nasazování aplikací. Vývojáři mohou pracovat v cloudu pomocí služby Azure DevOps. Azure DevOps poskytuje integrovanou sadu funkcí, ke kterým můžete přistupovat přes webový prohlížeč nebo klienta integrovaného vývojového prostředí. Azure kanálu je jedním z těchto funkcí. Kanály Azure je plně funkční kontinuální integrace (CI) a průběžné doručování (CD) služby. Funguje s poskytovatelem upřednostňované Git a můžete nasadit na většině hlavní cloudové služby. Pak můžete automatizovat sestavení, testování a nasazení kódu do Microsoft Azure, Google Cloud Platform nebo Amazon Web Services.

Tento kurz je určen pro vývojáře šablony Azure Resource Manageru, kteří jsou nové služby Azure DevOps a Azure kanály. Pokud jste již obeznámeni s GitHub a DevOps, můžete přeskočit na [vytvoření kanálu](#create-a-pipeline).

> [!NOTE]
> Vyberte název projektu. Při přechodu v průběhu kurzu nahraďte některý z **AzureRmPipeline** názvem vašeho projektu.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava úložiště GitHub
> * Vytvoření projektu Azure DevOps
> * Vytvoření kanálu Azure
> * Ověření nasazení kanálu
> * Aktualizace šablony a opětovné nasazení
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* **Účet GitHub**, kde ji použijete k vytvoření úložiště pro šablony. Pokud ho nemáte, můžete si [vytvořit zdarma](https://github.com). Další informace o používání úložiště GitHub, najdete v části [úložišť GitHub sestavení](/azure/devops/pipelines/repos/github).
* **Nainstalovat Git**. Tento kurz pokyn používá *Git Bash* nebo *Git Shell*. Pokyny najdete v tématu [nainstalujte Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Azure DevOps organizace**. Pokud ho nemáte, můžete jeden vytvořit zdarma. Zobrazit [vytvořit kolekci organizace nebo projektu]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[Visual Studio Code](https://code.visualstudio.com/) s rozšířením nástroje Správce prostředků**. Přečtěte si, [jak toto rozšíření nainstalovat](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>Příprava úložiště GitHub

GitHub slouží k uložení váš zdrojový kód projektu, včetně šablon Resource Manageru. Ostatní podporované úložiště najdete v části [úložiště podporuje Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Vytvořit úložiště GitHub

Pokud nemáte účet GitHub, najdete v článku [požadavky](#prerequisites).

1. Přihlaste se k [Githubu](https://github.com).
2. Vyberte image pro váš účet v pravém horním rohu a pak vyberte **úložištích**.

    ![Azure Resource Manageru Azure DevOps Azure kanály vytvořit úložiště GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Vyberte **nový**, zelené tlačítko.
1. V **název úložiště**, zadejte název úložiště.  Například **AzureRmPipeline úložiště**. Nezapomeňte nahradit některé z **AzureRmPipeline** názvem vašeho projektu. Můžete vybrat, zda **veřejné** nebo **privátní** pro procházení tohoto kurzu. A pak vyberte **vytvoření úložiště**.
1. Poznamenejte si adresu URL. Adresa URL úložiště má následující formát:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Toto úložiště se označuje jako *vzdáleného úložiště*. Každý z vývojářů z jednoho projektu můžete naklonovat jeho vlastní *místního úložiště*a sloučit změny do vzdáleného úložiště.

### <a name="clone-the-remote-repository"></a>Klonování vzdáleného úložiště

1. Otevřete prostředí Git nebo Git Bash.  Viz [Požadavky](#prerequisites).
1. Zkontrolujte aktuální složku **githubu**.
1. Spusťte následující příkaz:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Nahraďte **[YourAccountName]** s Githubem, váš název účtu a nahraďte **[YourGitHubRepositoryName]** názvem vašeho úložiště, kterou jste vytvořili v předchozím postupu.

    Na následujících snímcích obrazovky ukazuje příklad.

    ![Azure Resource Manageru Azure DevOps Azure kanály vytvořit prostředí bash Githubu](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

**CreateAzureStorage** složku pro uložení šablony je složka. **Pwd** příkaz zobrazí cestu ke složce. Je cesta kde uložit šablonu, kterou chcete v následujícím postupu.

### <a name="download-a-quickstart-template"></a>Stáhnout šablonu pro rychlý start

Místo vytvoření šablony, můžete si stáhnout [šablonu pro rychlý Start]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Tato šablona vytvoří účet služby Azure Storage.

1. Otevřít Visual Studio code. Viz [Požadavky](#prerequisites).
2. Otevřete šablonu s následující adresou URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Uložte soubor jako **azuredeploy.json** k **CreateAzureStorage** složky. Název složky a název souboru se používají, se nacházejí v kanálu.  Pokud změníte tyto názvy, je nutné aktualizovat názvů používaných v kanálu.

### <a name="push-the-template-to-the-remote-repository"></a>Vložit šablonu do vzdáleného úložiště

Azuredeploy.json se přidala do místního úložiště. V dalším kroku nahrajte šablonu do vzdáleného úložiště.

1. Otevřít *Git Shell* nebo *Git Bash*, pokud není otevřený.
1. Změňte adresář na složku CreateAzureStorage ve vašem místním úložišti.
1. Ověřte, **azuredeploy.json** je soubor ve složce.
1. Spusťte následující příkaz:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Může se zobrazit upozornění týkající se LF. Upozornění můžete ignorovat. **hlavní** hlavní větev.  Obvykle vytvoříte větev pro každou aktualizaci. Pro zjednodušení tohoto kurzu budete používat v hlavní větvi přímo.
1. V prohlížeči přejděte do vašeho úložiště GitHub.  Adresa URL je  **https://github.com/ [YourAccountName] / [YourGitHubRepository]** . Měly by se zobrazit **CreateAzureStorage** složky a **Azuredeploy.json** uvnitř složky.

Zatím jste vytvořili úložiště GitHub a nahrát šablonu do úložiště.

## <a name="create-a-devops-project"></a>Vytvořit projekt DevOps

DevOps organizace je potřeba, aby bylo možné přejít k dalšímu postupu.  Pokud ho nemáte, přečtěte si téma [požadavky](#prerequisites).

1. Přihlaste se k [Azure DevOps](https://dev.azure.com).
1. Vyberte organizaci DevOps z levé strany.

    ![Azure Resource Manageru Azure DevOps Azure kanály vytvořit projekt Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Vyberte **Vytvořit projekt**. Pokud nemáte k dispozici všechny projekty, se automaticky otevře stránka pro vytvoření projektu.
1. Zadejte následující hodnoty:

    * **Název projektu**: Zadejte název projektu. Název projektu, který jste vybrali na začátek tohoto kurzu můžete použít.
    * **Správa verzí**: Vyberte **Git**. Možná budete muset Rozbalit **Upřesnit** zobrazíte **verzí**.

    Pro ostatní vlastnosti použijte výchozí hodnotu.
1. Vyberte **Vytvořit projekt**.

Umožňuje vytvořte připojení služby, který slouží k nasazení projekty do Azure.

1. Vyberte **nastavení projektu** od nejnižší úrovně v levé nabídce.
1. Vyberte **služby připojení** pod **kanály**.
1. Vyberte **nová služba připojení**a pak vyberte **AzureResourceManager**.
1. Zadejte následující hodnoty:

    * **Název připojení**: Zadejte název připojení. Například **AzureRmPipeline conn**. Poznamenejte si tento název, třeba název při vytváření kanálu.
    * **Určit obor úrovně**: vyberte **předplatné**.
    * **Předplatné**: vyberte své předplatné.
    * **Skupina prostředků**: Ponechte prázdné.
    * **Povolit všechny kanály, abyste mohli používat toto připojení**. (vybráno)
1. Vyberte **OK**.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Až dosud jste dokončili následující úlohy.  Pokud přeskočíte v předchozích částech vzhledem k tomu, že máte zkušenosti s GitHub a DevOps, musíte dokončit úkoly, než budete pokračovat.

- Úložiště GitHub vytvořte a uložte [Tato šablona](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) k **CreateAzureStorage** složky v úložišti.
- Vytvořte projekt DevOps a vytvořte připojení služby Azure Resource Manageru.

K vytvoření kanálu s krokem k nasazení šablony:

1. Vyberte **kanály** v levé nabídce.
1. Vyberte **nový kanál**.
1. Z **připojit** kartu, vyberte možnost **Githubu**. Pokud se zobrazí dotaz, zadejte své přihlašovací údaje Githubu a postupujte podle pokynů. Pokud se zobrazí následující obrazovka, vyberte **vybrat pouze úložiště**a ověřte své úložiště je v seznamu dřív, než vyberete **schválit a instalace**.

    ![Azure Resource Manageru Azure DevOps Azure kanály pouze vyberte úložiště](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Z **vyberte** kartu, vyberte úložiště.  Výchozí název je **[YourAccountName] / [YourGitHubRepositoryName]** .
1. Z **konfigurovat** kartu, vyberte možnost **Starter kanálu**. Zobrazuje **azure pipelines.yml** soubor kanálu s dva kroky skriptu.
1. Nahradit **kroky** oddíl s následující kód YAML:

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    By měl vypadat:

    ![Azure yaml kanály Azure Resource Manageru Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Proveďte následující změny:

    * **azureSubscription**: zaktualizujte příslušnou hodnotu připojení služby vytvořené v předchozím postupu.
    * **Akce**: **vytvořit nebo aktualizovat skupinu prostředků** akce provede akce 2 - 1. Vytvořte skupinu prostředků, pokud je k dispozici nový název skupiny prostředků; 2. nasazení šablony zadán.
    * **Název skupiny prostředků**: Zadejte název nové skupiny prostředků. Například **AzureRmPipeline-rg**.
    * **umístění**: Zadejte umístění pro skupinu prostředků.
    * **templateLocation**: když **propojený artefakt** není zadána, úloha hledá soubor šablony přímo z připojené úložiště.
    * **csmFile** je cesta k souboru šablony. Není nutné zadat soubor parametrů šablony, protože všechny parametry definované v šabloně mají výchozí hodnoty.

    Další informace o úkolu, naleznete v tématu [úkol nasazení skupiny prostředků Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
1. Vyberte **Uložit a spustit**.
1. Vyberte **uložte a spusťte** znovu. Kopii souboru YAML se uloží do připojené úložiště. Zobrazíte soubor YAML přejděte do úložiště.
1. Ověřte, že kanál byl úspěšně proveden.

    ![Azure yaml kanály Azure Resource Manageru Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Otevřete skupinu prostředků. Název je zadán v souboru YAML kanálu.  Zobrazí se vytvoří jeden účet úložiště.  Název účtu úložiště začíná **ukládání**.
1. Vyberte název účtu úložiště ho otevřete.
1. Vyberte **vlastnosti**. Všimněte si, že **SKU** je **Standard_LRS**.

    ![Azure portal ověřovací kanály Azure Resource Manageru Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Aktualizace a opětovné nasazení

Při aktualizaci šablony a odešlete změny do vzdáleného úložiště, kanál automaticky aktualizuje prostředků, účet úložiště v tomto případě.

1. Otevřít **azuredeploy.json** ze svého místního úložiště ve Visual Studio Code.
1. Aktualizace **defaultValue** z **storageAccountType** k **Standard_GRS**. Viz následující snímek obrazovky:

    ![Azure Resource Manageru Azure DevOps Azure kanály aktualizovat yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Uložte změny.
1. Odešlete změny do vzdáleného úložiště spuštěním následujících příkazů z Gitu nebo prostředí Bash.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    První příkaz synchronizuje místní úložiště a vzdáleným úložištěm. Mějte na paměti, že soubor YAML kanálu byl přidán do vzdáleného úložiště.

    Hlavní větve vzdáleného úložiště bylo aktualizováno se znovu aktivuje kanál.

Pokud chcete ověřit změny, můžete zkontrolovat skladové položky účtu úložiště.  Zobrazit [ověření nasazení](#verify-the-deployment).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

Můžete také odstranit úložiště GitHub a projekt Azure DevOps.

## <a name="next-steps"></a>Další postup

V tomto kurzu vytvoříte kanál Azure DevOps k nasazení šablony Azure Resource Manageru. Informace o nasazování prostředků Azure napříč několika oblastmi a používání postupů bezpečného nasazení najdete tady:

> [!div class="nextstepaction"]
> [Použití Azure Deployment Manageru](./resource-manager-tutorial-deploy-vm-extensions.md)
