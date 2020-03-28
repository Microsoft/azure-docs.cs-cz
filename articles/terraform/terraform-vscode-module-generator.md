---
title: Kurz – vytvoření základní šablony Terraform v Azure pomocí Yeoman
description: Zjistěte, jak vytvořit základní šablonu Terraform v Azure pomocí Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472141"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Kurz: Vytvoření základní šablony Terraform v Azure pomocí Yeoman

V tomto kurzu se dozvíte, jak používat kombinaci [Terraform](/azure/terraform/) a [Yeoman](https://yeoman.io/). Terraform je nástroj pro vytváření infrastruktury v Azure. Yeoman usnadňuje vytváření modulů Terraform.

V tomto článku se dozvíte, jak provést následující úkoly:
> [!div class="checklist"]
> * Vytvořte základní šablonu Terraform pomocí generátoru modulů Yeoman.
> * Otestujte šablonu Terraform dvěma různými způsoby.
> * Spusťte modul Terraform pomocí souboru Dockeru.
> * Spusťte modul Terraform nativně v prostředí Azure Cloud Shell.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- **Visual Studio Kód:** [Stáhněte si Visual Studio kód](https://code.visualstudio.com/download) pro vaši platformu.
- **Terraform**: [Instalace Terraform](terraform-install-configure.md) spustit modul vytvořený Yeoman.
- **Docker**: [Nainstalujte Docker](https://www.docker.com/get-started) pro spuštění modulu vytvořeného generátorem Yeoman.
- **Přejít programovací jazyk**: [Nainstalujte Go](https://golang.org/) jako Yeoman-generované testovací případy jsou kód pomocí jazyka Go.

>[!NOTE]
>Většina postupů v tomto kurzu zahrnují rozhraní příkazového řádku. Popsané kroky platí pro všechny operační systémy a nástroje příkazového řádku. Pro příklady powershellu byl vybrán pro místní prostředí a Git Bash pro prostředí cloudshellu.

## <a name="prepare-your-environment"></a>Příprava prostředí

### <a name="install-nodejs"></a>Instalace Node.js

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

Chcete-li ověřit, zda je nainstalován yeoman, spusťte následující příkaz:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Vytvoření adresáře pro modul generovaný yeomanem

Šablona Yeoman generuje soubory do aktuálního adresáře. Z tohoto důvodu je potřeba vytvořit adresář.

Tento adresář prázdný je potřeba umístit pod $GOPATH/src. Další informace o této cestě naleznete v článku [Nastavení cesty k cestě](https://github.com/golang/go/wiki/SettingGOPATH).

1. Přejděte do nadřazeného adresáře, ze kterého chcete vytvořit nový adresář.

1. Spusťte následující příkaz, který nahradí zástupný symbol. V tomto příkladu `GeneratorDocSample` se používá název adresáře.

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

    - **Terraform modul projekt Name** `doc-sample-module` - Hodnota se používá pro příklad.

        ![Název projektu](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Chcete zahrnout soubor Docker image?** - `y`Zadejte . Pokud vyberete `n`, bude kód generovaného modulu podporovat spuštění pouze v nativním režimu.

        ![Zahrnout soubor Docker image?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Seznam obsahu adresáře pro zobrazení výsledných souborů, které jsou vytvořeny:

    ```bash
    ls
    ```

    ![Seznam vytvořených souborů](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Projděte si vygenerovaný kód modulu

1. Spusťte Visual Studio Code

1. Z řádku nabídek vyberte **Soubor > Otevřít složku** (Soubor > Otevřít složku), přejděte na složku, kterou jste vytvořili, a vyberte ji.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Následující soubory byly vytvořeny generátorem modulů Yeoman. Další informace o těchto souborech a jejich použití naleznete [v tématu Terratest in Terraform Modules.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster).

- `main.tf`- Definuje modul `random-shuffle`s názvem . Vstup je `string_list`. Výstup je počtem permutací.
- `variables.tf`- Definuje vstupní a výstupní proměnné používané modulem.
- `outputs.tf`- Definuje, co modul výstupy. Zde je hodnota vrácená `random_shuffle`, což je vestavěný modul Terraform.
- `Rakefile`- Definuje kroky sestavení. Tyto kroky zahrnují:
    - `build`- Ověří formátování main.tf souboru.
    - `unit`- Kostra generovaného modulu neobsahuje kód pro jednotkový test. Pokud chcete specifikovat scénář testování jednotky, přidejte sem kód.
    - `e2e`- Spustí end-to-end test modulu.
- `test`
    - Testovací případy jsou napsané v Go.
    - Všechny kódy v testu jsou testy end-to-end.
    - End-to-end testy se pokusí zřídit `fixture`všechny položky definované v . Výsledky v `template_output.go` souboru jsou porovnány s předem definovanými očekávanými hodnotami.
    - `Gopkg.lock`a `Gopkg.toml`: Definuje závislosti. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testování nového modulu Terraform pomocí souboru Dockeru

Tato část ukazuje, jak otestovat modul Terraform pomocí souboru Dockeru.

>[!NOTE]
>Tento příklad spustí modul místně; není v Azure.

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

    ![Zpráva označující úspěšné sestavení](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Na příkazovém `docker image ls` řádku zadejte, `terra-mod-example` abyste viděli vytvořený modul v seznamu.

    ![Seznam obsahující nový modul](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Zadejte `docker run -it terra-mod-example /bin/sh`. Po spuštění `docker run` příkazu se umíte v prostředí Dockeru. V tomto okamžiku můžete soubor zjistit `ls` pomocí příkazu.

    ![Seznam souborů v Dockeru](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Sestavení modulu

1. Spusťte následující příkaz:

    ```bash
    bundle install
    ```

1. Spusťte následující příkaz:

    ```bash
    rake build
    ```

    ![Integrace Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Spuštění komplexního testu

1. Spusťte následující příkaz:

    ```bash
    rake e2e
    ```

1. Po chvíli se zobrazí zpráva **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Zadejte, `exit` chcete-li dokončit test a ukončit prostředí Dockeru.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Použití generátoru Yeoman k vytvoření a testování modulu v prostředí Cloud Shell

V této části generátor Yeoman se používá k vytvoření a testování modulu v prostředí Cloud Shell. Použití Cloud Shell namísto použití souboru Docker výrazně zjednodušuje proces. Pomocí prostředí Cloud Shell jsou všechny předinstalované následující produkty:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Spuštění relace cloudového prostředí

1. Spusťte relaci Azure Cloud Shellu buď prostřednictvím [portálu Azure](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com)nebo [mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Otevře se stránka **Vítá vás Azure Cloud Shell.** Vyberte **Bash (Linux)**.

    ![Vítá vás Azure Cloud Shell.](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Pokud ještě nemáte nastavený účet Azure Storage, zobrazí se následující obrazovka. Vyberte **Create storage** (Vytvořit úložiště).

    ![Nemáte připojené žádné úložiště.](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell se spustí v prostředí, které jste vybrali, a zobrazí informace o cloudové jednotce, kterou pro vás právě vytvořil.

    ![Cloudová jednotka je vytvořená.](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Příprava adresáře pro uložení modulu Terraform

1. V tomto okamžiku bude Cloud Shell již nakonfigurován GOPATH ve vašem prostředí proměnných pro vás. Chcete-li cestu `go env`zobrazit, zadejte .

1. Vytvořte $GOPATH adresář, pokud ještě neexistuje: `mkdir ~/go`Enter .

1. Vytvořte adresář v rámci $GOPATH adresáře. Tento adresář se používá k uložení různých adresářů projektu vytvořených v tomto příkladu. 

    ```bash
    mkdir ~/go/src
    ```

1. Vytvořte adresář, ve které bude modul Terraform nahrazovat zástupný symbol. V tomto příkladu `my-module-name` se používá název adresáře.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Přejděte do adresáře modulu: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Vytvoření a testování modulu Terraform

1. Spusťte následující příkaz a postupujte podle pokynů. Na dotaz, zda chcete vytvořit soubory `N`Dockeru, zadáte .

    ```bash
    yo az-terra-module
    ```

1. Chcete-li nainstalovat závislosti, spusťte následující příkaz:

    ```bash
    bundle install
    ```

1. Chcete-li vytvořit modul, spusťte následující příkaz:

    ```bash
    rake build
    ```

    ![Integrace Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Spusťte následující příkaz pro spuštění textu:

    ```bash
    rake e2e
    ```

    ![Výsledky zkušebního průchodu](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nainstalujte a použijte rozšíření Kódu Azure Terraform Visual Studio](/azure/terraform/terraform-vscode-extension).