---
title: Rozšíření Azure Terraformu VS Code | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak instalace a používání rozšíření Terraformu v aplikaci Visual Studio Code.
keywords: terraform, devops, azure, virtuálního počítače
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190064"
---
# <a name="azure-terraform-vs-code-extension"></a>Rozšíření VS Code Terraformu pro Azure

Rozšíření Microsoft Azure Terraformu Visual Studio Code (VS Code) je navržená pro zvýšení produktivity vývojářů při vytváření, testování a použití Terraformu v Azure. Toto rozšíření poskytuje podporu příkaz Terraformu, vizualizace grafu prostředků a cloud Shell integrace editoru VS Code.

## <a name="what-you-do"></a>Co můžete dělat

- Nainstalujte spustitelný soubor HashiCorp Terraform open source na svém počítači.
- Nainstalujte rozšíření VS Codu Terraformu pro Azure v místní instalaci VS Code.

## <a name="what-you-learn"></a>Co se naučíte

V tomto kurzu se dozvíte:

- Jak Terraformu můžete automatizovat a zjednodušit zřizování služeb Azure.
- Postup instalace a používání rozšíření VS Code Terraformu Microsoftu pro služby Azure.
- Postup použití VS Code pro zápis, plánování a provádění Terraformu plány.

## <a name="what-you-need"></a>Co potřebujete

- Počítač se systémem Windows 10, Linuxu nebo macOS 10.10 +.
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US).
- Aktivní předplatné Azure. [Aktivace bezplatné 30denní zkušební verze Microsoft Azure účtu](https://azure.microsoft.com/free/).
- K instalaci aplikace [Terraformu](https://www.terraform.io/) open source nástroj na místním počítači.
  
## <a name="prepare-your-dev-environment"></a>Příprava vývojového prostředí

### <a name="install-git"></a>Instalace Gitu

K dokončení cvičení v tomto článku, budete muset [nainstalujte Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Nainstalujte HashiCorp Terraform

Podle pokynů HashiCorp [nainstalovat Terraformu](https://www.terraform.io/intro/getting-started/install.html) webové stránky, které zahrnuje i službu:

- Stahování Terraformu
- Instalace Terraformu
- Ověření Terraform je správně nainstalovaný.

>[!Tip]
>Ujistěte se, postupujte podle pokynů ohledně nastavení systémové proměnné PATH.

### <a name="install-nodejs"></a>Instalovat Node.js

Použití Terraformu ve službě Cloud Shell, potřebujete [nainstalujte Node.js](https://nodejs.org/) 6.0 +.

>[!NOTE]
>Pokud chcete ověřit, jestli je Node.js nainstalovaný, otevřete okno terminálu a zadejte: `node -v`

### <a name="install-graphviz"></a>Nainstalujte GraphViz

K použití Terraformu vizualizace funkce, budete muset [nainstalovat GraphViz](http://graphviz.org/).

>[!NOTE]
>Pokud chcete ověřit, jestli je nainstalovaný GraphViz, otevřete okno terminálu a zadejte: `dot -V`

### <a name="install-the-azure-terraform-vs-code-extension"></a>Nainstalujte rozšíření VS Code Terraformu pro Azure:

1. Spusťte VS Code.
2. Klikněte na **rozšíření** ikonu.

    ![Tlačítko rozšíření](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. Použití **rozšíření vyhledávání na webu Marketplace** textové pole pro hledání rozšíření Terraformu pro Azure:

    ![Hledání rozšíření VS Code Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

4. Klikněte na **Nainstalovat**.

    >[!NOTE]
    >Po kliknutí na **nainstalovat** rozšíření Terraformu pro Azure, VS Code automaticky nainstaluje rozšíření Azure Account. Účet Azure je soubor závislostí pro rozšíření Terraformu pro Azure, kterou používá k provedení ověření předplatného Azure a souvisejících s Azure kódu rozšíření.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Ověřte, že je nainstalované rozšíření Terraformu v aplikaci Visual Studio Code

1. Klikněte na ikonu rozšíření.
2. Typ `@installed` do textového pole hledání.

    ![Nainstalovaná rozšíření](media/terraform-vscode-extension/tf-installed-extensions.png)

Rozšíření Terraformu pro Azure se zobrazí v seznamu nainstalovaných rozšíření.

![Nainstalovaná rozšíření Terraformu](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Nyní můžete spustit všechny podporované příkazy Terraformu v prostředí Cloud Shellu z editoru VS Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Cvičení 1: Návod, jak příkazy základní Terraformu

V tomto cvičení vytvoření a provedení základní Terraformu konfigurační soubor, který se zřídí nová skupina prostředků Azure.

### <a name="prepare-a-test-plan-file"></a>Příprava souboru testovacího plánu

1. V nástroji VS Code, vyberte **soubor > Nový soubor** z řádku nabídek.
2. Přejděte [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) a zkopírovat kód v **příklady použití** blok kódu:
3. Vložte zkopírovaný kód do nového souboru, který jste vytvořili v nástroji VS Code.

    ![Vložte kód příklady použití](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Můžete změnit **název** hodnota skupinu prostředků, ale musí být jedinečné pro vaše předplatné Azure.

4. V panelu nabídky vyberte **soubor > Uložit jako**.
5. V **uložit jako** dialogové okno, přejděte do umístění podle vaší volby a pak klikněte na tlačítko **novou složku**. (Změnit název nové složky na něco srozumitelnější než *novou složku*.)

    >[!NOTE]
    >V tomto příkladu je složka s názvem TERRAFORMU. testovací plán.

6. Ujistěte se, že je zvýrazněn novou složku (vybrané) a potom klikněte na tlačítko **otevřít**.
7. V **uložit jako** dialogového okna, změňte výchozí název souboru, který se *main.tf*.

    ![Uložit jako main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. Klikněte na **Uložit**.
- V panelu nabídek vyberte **soubor > Otevřít složku**. Přejděte do a vyberte novou složku, kterou jste vytvořili.

### <a name="run-terraform-init-command"></a>Spustit Terraformu *init* příkaz

1. Spusťte Visual Studio Code.
2. V řádku nabídek VS Code, vyberte **soubor > Otevřít složku...**  a vyhledejte a vyberte váš *main.tf* souboru.

    ![soubor Main.TF](media/terraform-vscode-extension/tf-main-tf.png)

3. V panelu nabídky vyberte **zobrazení > paleta příkazů... > Terraformu pro Azure: Init**.
4. Po chvíli když se zobrazí výzva *chcete otevřete Cloud Shell?* Klikněte na **OK**.

    ![Chcete otevřete Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. Při prvním spuštění Cloud Shellu z nové složky, zobrazí se výzva k nastavení webové aplikace. Klikněte na tlačítko **otevřít**.

    ![První spuštění služby Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. Vítá vás Azure Cloud Shell se otevře. Vyberte Bash nebo PowerShell.

    ![Vítá vás Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >V tomto příkladu byla vybrána Bash (Linux).

7. Pokud už jste nenastavili účet služby Azure storage, zobrazí se následující obrazovka. Klikněte na tlačítko **vytvořit úložiště**.

    ![Nemáte připojené žádné úložiště](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell se spustí v prostředí, které jste dříve vybrali a zobrazí informace o cloudovou jednotku, že právě pro vás vytvořili.

    ![Vytvořil cloudovou jednotku](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. Nyní se může ukončit službě Cloud Shell
10. V panelu nabídky vyberte **zobrazení** > **paletu příkazů** > **Terraformu pro Azure: init**.

    ![Terraform byl úspěšně inicializován.](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Vizualizujte plánu

Dříve v tomto kurzu jste nainstalovali GraphViz. Terraform můžete GraphViz generovat vizuální znázornění konfigurace nebo spuštění plánu. Implementuje tato funkce prostřednictvím rozšíření kódu VS Terraformu pro Azure *vizualizovat* příkazu.

- Z **nabídek**vyberte**zobrazení > paletu příkazů > Terraformu pro Azure: vizualizace**.

    ![Vizualizujte plánu](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Spustit Terraformu *plán* příkaz

Terraformu *plán* příkaz se používá ke kontrole, jestli provedete plán spuštění sady změn, co jste zamýšleli.

>[!NOTE]
>Terraform *plán* provedeny žádné změny k vašim prostředkům reálné Azure. Ve skutečnosti provádět změny obsažené v plánu, používáme Terraformu *použít* příkazu.

- V panelu nabídky vyberte **zobrazení** > **paletu příkazů** > **Terraformu pro Azure: plán**.

    ![Plán Terraformu](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Spustit Terraformu *použít* příkaz

Po splnění s výsledky Terraformu *plán*, můžete spustit *použít* příkazu.

1. V panelu nabídky vyberte **zobrazení** > **paletu příkazů** > **Terraformu pro Azure: použití**.

    ![Použití Terraformu](media/terraform-vscode-extension/tf-terraform-apply.png)

2. Typ *Ano*.

    ![Použití Terraformu Ano](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Ověřte, že se spustil plánu Terraformu

Pokud chcete zobrazit, pokud byl úspěšně vytvořen novou skupinu prostředků Azure:

1. Otevřete web Azure Portal.
2. Vyberte **skupiny prostředků** v levém navigačním podokně.

    ![Ověřte nový prostředek](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Nové skupiny prostředků by měly být uvedeny v **název** sloupce.

>[!NOTE]
>Můžete ponechat okna portálu Azure Portal otevřete prozatím; použijeme ho v dalším kroku.

### <a name="run-terraform-destroy-command"></a>Spustit Terraformu *zničit* příkaz

1. V panelu nabídky vyberte **zobrazení** > **paletu příkazů** > **Terraformu pro Azure: zničit**.

    ![Zničit Terraformu](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. Typ *Ano*.

    ![Terraform zničit Ano](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Ověřte, že vaše skupina prostředků se zničil.

Potvrďte, že Terraformu úspěšně zničen nové skupiny prostředků:

1. Klikněte na tlačítko **aktualizovat** na portálu Azure portal *skupiny prostředků* stránky.
2. Vaše skupina prostředků se už uvedené.

    ![Ověřte, že skupina prostředků se zničil.](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Cvičení 2: Terraformu *compute* modulu

V tomto cvičení se dozvíte, jak načíst Terraformu *compute* modulu do prostředí sady VS Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Klonovat terraformu azurerm – výpočetní modul

1. Použití [tento odkaz](https://github.com/Azure/terraform-azurerm-compute) pro přístup k modulu Terraformu pro Azure Rm Compute na Githubu.
2. Klikněte na tlačítko **klonovat nebo stáhnout**.

    ![Klonovat nebo stáhnout](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >V tomto příkladu byla naše složku s názvem *terraformu azurerm-compute*.

### <a name="open-the-folder-in-vs-code"></a>Otevřít složku v nástroji VS Code

1. Spusťte Visual Studio Code.
2. Z **nabídek**vyberte **soubor > Otevřít složku** a najděte a vyberte složku, kterou jste vytvořili v předchozím kroku.

    ![terraform-azurerm-compute složky](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Inicializovat Terraformu

Než začnete používat příkazy Terraformu v editoru VS Code, Stáhnout moduly plug-in pro dva poskytovatelé Azure: náhodné a azurerm.

1. V podokně terminálu rozhraní IDE VS Code, typ: `terraform init`

    ![příkaz init terraformu](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. Typ `az login` a postupujte podle pokynů na obrazovce pokyny.

### <a name="module-test-lint"></a>Modul testu: *lint*

1. Z **nabídek**vyberte **zobrazení > paletu příkazů > Terraformu pro Azure: spuštění testu**.
2. Ze seznamu možností typu testu, vyberte **lint**.

    ![Vyberte typ testu](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. Když se zobrazí výzva *chcete otevřete cloud Shell?* klikněte na tlačítko **OK** a postupujte podle pokynů na obrazovce pokyny.

    ![Chcete otevřete cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Při spuštění buď **lint** nebo **komplexní** testu, Azure využívá služby kontejneru pro zřízení testovacího počítače ke skutečné testu. Z tohoto důvodu si výsledky testu může obvykle trvat několik minut se mají vrátit.

Po chvíli se zobrazí výpis, v terminálu podokně podobný tomuto příkladu:

![Výsledky testu LINT](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Modul testu: *začátku do konce*

1. Z **nabídek**vyberte **zobrazení > paletu příkazů > Terraformu pro Azure: spuštění testu**.
2. Ze seznamu možností typu testu, vyberte **komplexní**.

    ![Vyberte typ testu](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. Pokud se zobrazí dotaz *chcete otevřete cloud Shell?* klikněte na tlačítko **OK** a postupujte podle pokynů na obrazovce pokyny.

    ![Chcete otevřete cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Při spuštění buď **lint** nebo **komplexní** testu, Azure využívá služby kontejneru pro zřízení testovacího počítače ke skutečné testu. Z tohoto důvodu si výsledky testu může obvykle trvat několik minut se mají vrátit.

Po chvíli se zobrazí výpis, v terminálu podokně podobný tomuto příkladu:

![Výsledky tak kompletní test](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Další postup

Jste viděli některé ze způsobů, jak zjednodušit Terraformu zřizování služeb Azure z editoru Visual Studio Code. Chcete nyní, podívejte se na některé z těchto prostředků:
- [Registr modulů Terraformu](https://registry.terraform.io/) uvádí všechny dostupné moduly Terraformu pro Azure a dalších podporovaných zprostředkovatelů.

Pro každý z těchto modulů je k dispozici následující informace:

- Popis modulu obecné funkce a jeho vlastnosti
- Příklad použití
- Konfigurace, které ukazují, jak sestavit, spustit, a testujte každého modulu na svém místním vývojovém počítači
- Soubor Dockerfile a umožňuje tak místně sestavovat a spouštět vývojové prostředí modulu.
