---
title: Kurz – vytvoření základní šablony Terraformu v Azure pomocí Yeoman
description: Naučte se vytvořit základní šablonu Terraformu v Azure pomocí Yeoman.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 2f8cbc495a4b46255e7eb31bc1ff8b04fffcad15
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969270"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Kurz: Vytvoření základní šablony Terraformu v Azure pomocí Yeoman

V tomto kurzu se naučíte používat kombinaci [terraformu](/azure/terraform/) a [Yeoman](https://yeoman.io/). Terraformu je nástroj pro vytváření infrastruktury v Azure. Yeoman umožňuje snadno vytvářet moduly Terraformu.

V tomto článku se dozvíte, jak provádět následující úlohy:
> [!div class="checklist"]
> * Vytvořte základní šablonu Terraformu pomocí generátoru modulu Yeoman.
> * Otestujte šablonu Terraformu pomocí dvou různých metod.
> * Spusťte modul Terraformu pomocí souboru Docker.
> * Spusťte v Azure Cloud Shell nativně modul Terraformu.

## <a name="prerequisites"></a>Předpoklady

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- **Visual Studio Code**: [Stáhněte si Visual Studio Code](https://code.visualstudio.com/download) pro vaši platformu.
- **Terraformu**: [nainstalujte terraformu](/azure/virtual-machines/linux/terraform-install-configure ) a spusťte modul vytvořený nástrojem Yeoman.
- **Docker**: [nainstalujte Docker](https://www.docker.com/get-started) a spusťte modul vytvořený generátorem Yeoman.
- **Jazyk programovacího jazyka**: [instalace přejít](https://golang.org/) jako Yeoman testové případy je kód pomocí jazyka přejít.

>[!NOTE]
>Většina postupů v tomto kurzu se týká rozhraní příkazového řádku. Popsané kroky se vztahují na všechny operační systémy a nástroje příkazového řádku. V příkladech byl vybrán PowerShell pro místní prostředí a Git bash pro prostředí cloud Shell.

## <a name="prepare-your-environment"></a>Příprava prostředí

### <a name="install-nodejs"></a>Instalovat Node.js

Abyste mohli Terraform používat ve službě Cloud Shell, musíte si [nainstalovat Node.js](https://nodejs.org/en/download/) ve verzi 6.0 nebo novější.

>[!NOTE]
>Pokud si chcete ověřit, že je Node.js správně nainstalovaný, otevřete okno terminálu a zadejte `node --version`.

### <a name="install-yeoman"></a>Nainstalovat Yeoman

Spusťte následující příkaz:

```bash
npm install -g yo
```

![Nainstalovat Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Nainstalujte šablony Yeoman pro modul Terraform

Spusťte následující příkaz:

```bash
npm install -g generator-az-terra-module
```

![Nainstalujte generátor – az-terra – modul](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Chcete-li ověřit, zda je Yeoman nainstalován, spusťte následující příkaz:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Vytvoření adresáře pro modul generovaný Yeoman

Šablona Yeoman generuje soubory v aktuálním adresáři. Z tohoto důvodu je potřeba vytvořit adresář.

Tento adresář prázdný je potřeba umístit pod $GOPATH/src. Další informace o této cestě naleznete v článku [Nastavení gopath tak](https://github.com/golang/go/wiki/SettingGOPATH).

1. Přejděte do nadřazeného adresáře, ze kterého chcete vytvořit nový adresář.

1. Spusťte následující příkaz, který nahradí zástupný text. V tomto příkladu se používá název adresáře `GeneratorDocSample`.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Přejděte do nového adresáře:

    ```bash
    cd <new-directory-name>
    ```

    ![Přejdete do nového adresáře](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Vytvořte šablonu základního modulu

1. Spusťte následující příkaz:

    ```bash
    yo az-terra-module
    ```

1. Použijte pokyny na obrazovce pokynů zadejte následující informace:

    - **Název projektu modulu terraformu** – pro příklad se používá hodnota `doc-sample-module`.

        ![Název projektu](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Chcete zahrnout soubor Docker image?** -Zadejte `y`. Pokud vyberete `n`, vygenerovaný kód modulu bude podporovat spouštění pouze v nativním režimu.

        ![Zahrnout soubor Docker image?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Výpis obsahu adresáře pro zobrazení výsledných souborů, které jsou vytvořeny:

    ```bash
    ls
    ```

    ![Seznam vytvořených souborů](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Projděte si vygenerovaný kód modulu

1. Spusťte Visual Studio Code

1. Z řádku nabídek vyberte **Soubor > Otevřít složku** (Soubor > Otevřít složku), přejděte na složku, kterou jste vytvořili, a vyberte ji.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Generátor modulu Yeoman vytvořil následující soubory. Další informace o těchto souborech a jejich použití najdete v tématu [Terratest v terraformuch modulech.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

- `main.tf` – definuje modul s názvem `random-shuffle`. Vstup je `string_list`. Výstup je počtem permutací.
- `variables.tf` – definuje vstupní a výstupní proměnné používané modulem.
- `outputs.tf` – definuje výstupy modulu. Tady je hodnota vrácená `random_shuffle`, což je vestavěný modul Terraformu.
- `Rakefile` – definuje kroky sestavení. Tyto kroky zahrnují:
    - `build` – ověří formátování souboru main.tf.
    - `unit` – vygenerovaná kostra modulu neobsahuje kód pro testování částí. Pokud chcete specifikovat scénář testování jednotky, přidejte sem kód.
    - `e2e` – spustí kompletní test modulu.
- `test`
    - Testovací případy jsou napsané v Go.
    - Všechny kódy v testu jsou testy end-to-end.
    - Ucelené testy se snaží zřídit všechny položky definované v části `fixture`. Výsledky v souboru `template_output.go` jsou porovnávány s předem definovanými očekávanými hodnotami.
    - `Gopkg.lock` a `Gopkg.toml`: definuje závislosti. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Otestování nového modulu Terraformu pomocí souboru Docker

V této části se dozvíte, jak otestovat modul Terraformu pomocí souboru Docker.

>[!NOTE]
>V tomto příkladu se modul spouští místně; ne v Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Potvrďte, že je Docker nainstalovaný a spuštěný

Na příkazovém řádku zadejte `docker version`.

![Verze Dockeru](media/terraform-vscode-module-generator/ymg-docker-version.png)

Výsledný výstup potvrzuje, že je Docker nainstalovaný.

Chcete-li ověřit, že Dockeru skutečně běží, zadejte `docker info`.

![Docker info](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Instalace Docker kontejner

1. Na příkazovém řádku zadejte

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Zpráva **, která je úspěšně integrovaná,** se zobrazí.

    ![Zpráva indikující úspěšné sestavení](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Na příkazovém řádku zadejte `docker image ls` pro zobrazení seznamu vytvořených `terra-mod-example`.

    ![Seznam obsahující nový modul](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Zadejte `docker run -it terra-mod-example /bin/sh`. Po spuštění příkazu `docker run` se nacházíte v prostředí Docker. V tomto okamžiku můžete soubor zjistit pomocí příkazu `ls`.

    ![Seznam souborů v Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Sestavit modul

1. Spusťte následující příkaz:

    ```bash
    bundle install
    ```

1. Spusťte následující příkaz:

    ```bash
    rake build
    ```

    ![Integrace Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Spuštění kompletního testu

1. Spusťte následující příkaz:

    ```bash
    rake e2e
    ```

1. Po chvíli se zobrazí zpráva **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Zadejte `exit` pro dokončení testu a ukončení prostředí Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Vytvoření a otestování modulu v Cloud Shell pomocí generátoru Yeoman

V této části se generátor Yeoman používá k vytvoření a otestování modulu v Cloud Shell. Použití Cloud Shell místo použití souboru Docker výrazně zjednodušuje proces. Pomocí Cloud Shell jsou všechny následující produkty předem nainstalovány:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Spustit relaci Cloud Shell

1. Spusťte Azure Cloud Shell relaci prostřednictvím [Azure Portal](https://portal.azure.com/), [Shell.Azure.com](https://shell.azure.com)nebo [mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Otevře se stránka **Vítá vás Azure Cloud Shell** . Vyberte **bash (Linux)** .

    ![Vítá vás Azure Cloud Shell.](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Pokud ještě nemáte nastavený účet Azure Storage, zobrazí se následující obrazovka. Vyberte **Create storage** (Vytvořit úložiště).

    ![Nemáte připojené žádné úložiště.](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell se spustí v prostředí, které jste vybrali, a zobrazí informace o cloudové jednotce, kterou pro vás právě vytvořil.

    ![Cloudová jednotka je vytvořená.](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Příprava adresáře pro uložení modulu Terraformu

1. V tuto chvíli se Cloud Shell už v proměnných prostředí nakonfigurovali GOPATH tak. Cestu zobrazíte zadáním `go env`.

1. Pokud ještě neexistuje, vytvořte adresář $GOPATH: zadejte `mkdir ~/go`.

1. Vytvořte adresář v adresáři $GOPATH. Tento adresář slouží k uložení různých adresářů projektu vytvořených v tomto příkladu. 

    ```bash
    mkdir ~/go/src
    ```

1. Vytvořte adresář, který bude obsahovat modul Terraformu, který nahradí zástupný text. V tomto příkladu se používá název adresáře `my-module-name`.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Přejděte do adresáře modulu: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Vytvoření a otestování modulu Terraformu

1. Spusťte následující příkaz a postupujte podle pokynů. Když se zobrazí dotaz, jestli chcete vytvořit soubory Docker, zadáte `N`.

    ```bash
    yo az-terra-module
    ```

1. Spuštěním následujícího příkazu nainstalujte závislosti:

    ```bash
    bundle install
    ```

1. Spuštěním následujícího příkazu Sestavte modul:

    ```bash
    rake build
    ```

    ![Integrace Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Spusťte následující příkaz, který spustí text:

    ```bash
    rake e2e
    ```

    ![Výsledky testu – průchod](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nainstalujte a použijte rozšíření Azure terraformu Visual Studio Code](/azure/terraform/terraform-vscode-extension).