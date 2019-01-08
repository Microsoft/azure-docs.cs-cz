---
title: Instalace a použití rozšíření Azure Terraform editoru Visual Studio Code
description: Zjistěte, jak nainstalovat a používat rozšíření Azure Terraform editoru Visual Studio Code.
services: terraform
ms.service: terraform
keywords: terraform, azure, devops, visual studio code, rozšíření
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: 137a57638207a05f797692b25a5d5a31bd66ce3d
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074576"
---
# <a name="install-and-use-the-azure-terraform-visual-studio-code-extension"></a>Instalace a použití rozšíření Azure Terraform editoru Visual Studio Code

Rozšíření Microsoft Azure Terraform editoru Visual Studio Code je navržené ke zvýšení produktivity vývojářů při vytváření, testování a používání Terraformu s Azure. Toto rozšíření poskytuje podporu příkazů Terraformu, vizualizaci grafů prostředků a integraci služby Cloud Shell do editoru Visual Studio Code.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Použití Terraformu k automatizaci a zjednodušení zřizování služeb Azure
> * Instalace a použití rozšíření Microsoft Terraform editoru Visual Studio Code pro služby Azure
> * Použití editoru Visual Studio Code ke psaní, plánování a provádění plánů Terraform

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Terraform**: [Instalace a konfigurace Terraformu](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**: Nainstalujte verzi [Visual Studio Code](https://code.visualstudio.com/download) , která je vhodná pro vaše prostředí.

## <a name="prepare-your-dev-environment"></a>Příprava vývojového prostředí

### <a name="install-git"></a>Instalace Gitu

K dokončení cvičení v tomto článku si budete muset [nainstalovat Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Instalace Terraformu od společnosti HashiCorp

Postupujte podle pokynů na webové stránce společnosti HashiCorp pro [instalaci Terraformu](https://www.terraform.io/intro/getting-started/install.html), která obsahuje:

- Stažení Terraformu
- Instalaci Terraformu
- Ověření správné instalace Terraformu

>[!Tip]
>Je potřeba, abyste postupovali podle pokynů ohledně nastavení systémové proměnné PATH.

### <a name="install-nodejs"></a>Instalovat Node.js

Abyste mohli Terraform používat ve službě Cloud Shell, musíte si [nainstalovat Node.js](https://nodejs.org/) ve verzi 6.0 nebo novější.

>[!NOTE]
>Pokud si chcete ověřit, že je Node.js správně nainstalovaný, otevřete okno terminálu a zadejte `node -v`.

### <a name="install-graphviz"></a>Instalace služby GraphViz

Pokud chcete v Terraformu používat funkci vizualizace, budete si muset [nainstalovat GraphViz](http://graphviz.org/).

>[!NOTE]
>Pokud si chcete ověřit, že je služba GraphViz správně nainstalovaná, otevřete okno terminálu a zadejte `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Instalace rozšíření Azure Terraform editoru Visual Studio Code

1. Spusťte editor Visual Studio Code.

1. Vyberte **Extensions** (Rozšíření).

    ![Tlačítko rozšíření](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Pomocí textového pole **Search Extensions in Marketplace** (Vyhledat rozšíření na Marketplace) vyhledejte rozšíření Azure Terraform:

    ![Vyhledání rozšíření editoru Visual Studio Code na Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Vyberte **Install** (Nainstalovat).

    >[!NOTE]
    >Když u rozšíření Azure Terraform vyberete **Install**, Visual Studio Code rozšíření Azure Account nainstaluje automaticky. Azure Account je soubor závislostí pro rozšíření Azure Terraform, které ho používá k ověřování předplatného Azure a rozšíření kódů souvisejících s Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Ověření nainstalovaného rozšíření Terraform v editoru Visual Studio Code

1. Vyberte **Extensions** (Rozšíření).

1. Do textového pole pro vyhledávání zadejte `@installed`.

    ![Nainstalovaná rozšíření](media/terraform-vscode-extension/tf-installed-extensions.png)

Rozšíření Azure Terraform se objeví na seznamu nainstalovaných rozšíření.

![Nainstalovaná rozšíření Terraformu](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Všechny příkazy Terraformu, které podporuje prostředí Cloud Shell, teď můžete spouštět z editoru Visual Studio Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Cvičení 1: Návod, jak příkazy základní Terraformu

V tomto cvičení vytvoříte a spustíte základní konfigurační soubor Terraformu, který zřídí novou skupinu prostředků Azure.

### <a name="prepare-a-test-plan-file"></a>Příprava souboru testovacího plánu

1. Z řádku nabídek v editoru Visual Studio Code vyberte **File > New File** (Soubor > Nový soubor).

1. V prohlížeči přejděte na [stránku Terraformu pro azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) a zkopírujte kód v bloku kódu **Example Usage** (Příklady použití):

    ![Příklady použití](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Vložte zkopírovaný kód do nového souboru, který jste v editoru Visual Studio Code vytvořili.

    ![Vložení kódu Example Usage](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Hodnotu **name** (název) skupiny prostředků můžete změnit, ale musí zůstat ve vašem předplatném Azure jedinečná.

1. Z řádku nabídek vyberte **File > Save As** (Soubor > Uložit jako).

1. V dialogovém okně **Uložit jako** přejděte do požadovaného umístění a vyberte **Nová složka**. (Název nové složky změňte na něco popisnějšího než jen *Nová složka*.)

    >[!NOTE]
    >V tomto příkladu má složka název TERRAFORM-TEST-PLAN.

1. Ujistěte se, že je nová složka zvýrazněná (vybraná) a pak vyberte **Otevřít**.

1. V dialogovém okně **Uložit jako** změňte výchozí název souboru na *main.tf*.

    ![Uložení souboru main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Vyberte **Uložit**.
- Z řádku nabídek vyberte **File > Open Folder** (Soubor > Otevřít složku). Přejděte do adresáře s novou složkou, kterou jste vytvořili, a vyberte ji.

### <a name="run-terraform-init-command"></a>Spuštění příkazu terraform *init*

1. Spusťte editor Visual Studio Code.

1. Z řádku nabídek editoru Visual Studio Code vyberte **File > Open Folder...** (Soubor > Otevřít složku...) a vyhledejte a vyberte soubor *main.tf*.

    ![Soubor main.tf](media/terraform-vscode-extension/tf-main-tf.png)

1. V panelu nabídky vyberte **zobrazení > paleta příkazů... > Terraformu pro Azure: Init**.

1. Když se zobrazí potvrzení, vyberte **OK**.

    ![Chcete otevřít Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. Při prvním spuštění Cloud Shellu z nové složky se vám zobrazí výzva k nastavení webové aplikace. Vyberte **Open** (Otevřít).

    ![První spuštění Cloud Shellu](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. Otevře se stránka Welcome to Azure Cloud Shell (Vítá vás Azure Cloud Shell). Vyberte prostředí Bash nebo PowerShell.

    ![Vítá vás Azure Cloud Shell.](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >V tomto příkladu jsme vybrali Bash (Linux).

1. Pokud ještě nemáte nastavený účet Azure Storage, zobrazí se následující obrazovka. Vyberte **Create storage** (Vytvořit úložiště).

    ![Nemáte připojené žádné úložiště.](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell se spustí v prostředí, které jste vybrali, a zobrazí informace o cloudové jednotce, kterou pro vás právě vytvořil.

    ![Cloudová jednotka je vytvořená.](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Teď už můžete Cloud Shell opustit.

1. Z řádku nabídek vyberte **View** > **Command Palette** > **Azure Terraform: init** (Zobrazení > Paleta příkazů > Azure Terraform: Init).

    ![Terraform se úspěšně inicializoval.](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Vizualizace plánu

Dříve v tomto kurzu jste nainstalovali GraphViz. Terraform může GraphViz použít ke generování vizuálního znázornění konfigurace či plánu provádění. Rozšíření Azure Terraform editoru Visual Studio Code tuto funkci implementuje prostřednictvím příkazu *visualize*.

- V panelu nabídky vyberte **zobrazení > paletu příkazů > Terraformu pro Azure: Vizualizujte**.

    ![Vizualizace plánu](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Spuštění příkazu terraform *plan*

Příkaz terraform *plan* se používá k ověření, zda plán provádění pro sadu změn provede, co zamýšlíte.

>[!NOTE]
>Příkaz terraform *plan* u vašich skutečných prostředků Azure žádné změny neprovede. Pokud chcete změny obsažené v plánu skutečně provést, použijte k tomu příkaz terraform *apply*.

- Z řádku nabídek vyberte **View** > **Command Palette** > **Azure Terraform: plan** (Zobrazení > Paleta příkazů > Azure Terraform: plan).

    ![Příkaz terraform plan](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Spuštění příkazu terraform *apply*

Když budete s výsledky příkazu terraform *plan* spokojení, můžete spustit příkaz *apply*.

1. Z řádku nabídek vyberte **View** > **Command Palette** > **Azure Terraform: apply** (Zobrazení > Paleta příkazů > Azure Terraform: apply).

    ![Příkaz terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Zadejte `yes`.

    ![Potvrzení příkazu terraform apply](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Ověření provedení příkazu terraform plan

Pokud si chcete ověřit, že se nová skupina prostředků Azure úspěšně vytvořila:

1. Otevřete web Azure Portal.

1. Na levém navigačním panelu vyberte **Skupiny prostředků**.

    ![Ověření nové skupiny prostředků](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Nová skupina prostředků by měla být uvedená ve sloupci **NÁZEV**.

>[!NOTE]
>Okno webu Azure Portal můžete nechat otevřené, budeme ho potřebovat v dalším kroku.

### <a name="run-terraform-destroy-command"></a>Spuštění příkazu terraform *destroy*

1. Z řádku nabídek vyberte **View** > **Command Palette** > **Azure Terraform: destroy** (Zobrazení > Paleta příkazů > Azure Terraform: destroy).

    ![Příkaz terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Zadejte *ano*.

    ![Potvrzení příkazu terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Ověření odstranění skupiny prostředků

Pokud si chcete ověřit, že Terraform novou skupinu prostředků úspěšně odstranil:

1. Na stránce **Skupiny prostředků** webu Azure Portal vyberte **Aktualizovat**.

1. Vaše skupina prostředků zde už nebude uvedená.

    ![Ověření odstranění skupiny prostředků](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Cvičení 2: Terraform *compute* modulu

V tomto cvičení se dozvíte, jak načíst modul *compute* nástroje Terraform do prostředí editoru Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Klonování modulu terraform-azurerm-compute

1. Použitím [tohoto odkazu](https://github.com/Azure/terraform-azurerm-compute) získáte přístup k modulu Terraform Azure Rm Compute na GitHubu.

1. Vyberte **Clone or download** (Naklonovat nebo stáhnout).

    ![Naklonovat nebo stáhnout](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >V tomto příkladu jsme složku pojmenovali *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Otevření složky v editoru Visual Studio Code

1. Spusťte editor Visual Studio Code.

1. Z řádku nabídek vyberte **File > Open Folder** (Soubor > Otevřít složku), přejděte na složku, kterou jste vytvořili v předchozím kroku, a vyberte ji.

    ![Složka terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Inicializace Terraformu

Než budete moct začít používat příkazy nástroje Terraform z editoru Visual Studio Code, musíte si stáhnout moduly plug-in pro dva poskytovatele Azure: random a azurerm.

1. Do podokna terminálu v integrovaném vývojovém prostředí editoru Visual Studio Code zadejte `terraform init`.

    ![Příkaz terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Zadejte `az login`, stiskněte **<Enter** a postupujte podle pokynů na obrazovce.

### <a name="module-test-lint"></a>Test modulu: *lint*

1. V panelu nabídky vyberte **zobrazení > paletu příkazů > Terraformu pro Azure: Spustit Test**.

1. Ze seznamu možností typu testu vyberte **lint**.

    ![Vyberte typ testu](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Když se zobrazí potvrzení, vyberte **OK** a postupujte podle pokynů na obrazovce.

    ![Chcete otevřít Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Když spustíte test **lint** nebo **end to end**, Azure použije službu kontejneru ke zřízení testovacího počítače, který daný test provede. Z tohoto důvodu může obvykle trvat několik minut, než se výsledky testu vrátí.

Po chvíli uvidíte v podokně terminálu výpis, který bude vypadat nějak takto:

![Výsledky testu lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Test modulu: *end-to-end*

1. V panelu nabídky vyberte **zobrazení > paletu příkazů > Terraformu pro Azure: Spustit Test**.

1. Ze seznamu možností typu testu vyberte **end to end**.

    ![Vyberte typ testu](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Když se zobrazí potvrzení, vyberte **OK** a postupujte podle pokynů na obrazovce.

    ![Chcete otevřít Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Když spustíte test **lint** nebo **end to end**, Azure použije službu kontejneru ke zřízení testovacího počítače, který daný test provede. Z tohoto důvodu může obvykle trvat několik minut, než se výsledky testu vrátí.

Po chvíli uvidíte v podokně terminálu výpis, který bude vypadat nějak takto:

![Výsledky testu end-to-end](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Seznam modulů Terraformu dostupných pro Azure (a další podporované poskytovatele)](https://registry.terraform.io/)