---
title: Vytvoření základní šablony Terraformu v Azure pomocí Yeomanu
description: Zjistěte, jak vytvořit základní šablonu Terraformu v Azure pomocí Yeomanu.
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuální počítač, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 1133e6b35d200ec7f855b41f3ae5d6f37949fc97
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568494"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Vytvoření základní šablony Terraformu v Azure pomocí Yeomanu

[Terraform](https://docs.microsoft.com/azure/terraform/
) poskytuje způsob, jak snadno vytvořit infrastrukturu v Azure. [Yeoman](http://yeoman.io/) výrazně usnadňuje úlohy vývojáře modulu při vytváření modulů Terraformu a současně poskytuje vynikající *rámec osvědčených postupů*.

V tomto článku se dozvíte, jak používat generátor modulů Yeoman k vytvoření základní šablony Terraform. Potom se dozvíte, jak otestovat nové šablony Terraformu pomocí dvěma způsoby:

- Spusťte modul Terraformu pomocí souboru Dockeru, který vytvoříte v tomto článku.
- Spusťte modul Terraformu nativně ve službě Azure Cloud Shell.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- **Visual Studio Code**: Použijeme [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) prozkoumat soubory vytvořené generátoru Yeoman. Můžete však použít libovolný editor kódu podle vašeho výběru.
- **Terraform**: Budete potřebovat instalaci [Terraformu](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) ke spuštění modulu vytvořená pomocí Yeomanu.
- **Docker**: Použijeme [Docker](https://www.docker.com/get-started) ke spuštění modulu vytvořené Yeoman generátoru. (Pokud tomu dáváte přednost, můžete použít Ruby místo Dockeru pro spuštění vzorového modulu.)
- **Přejděte programovací jazyk**: Budete potřebovat instalaci [Přejít](https://golang.org/) protože testovacích případů pomocí Yeomanu vygenerovat jsou napsané v Go.

>[!NOTE]
>Většina procedur v tomto tutoriálu zahrnuje položky příkazového řádku. Kroky popsané zde platí pro všechny operační systémy a nástroje příkazového řádku. V našem příkladu jsme zvolili používání prostředí PowerShell pro místní prostředí i prostředí Git Bash pro prostředí cloud shell.

## <a name="prepare-your-environment"></a>Příprava prostředí

### <a name="install-nodejs"></a>Instalovat Node.js

Abyste mohli Terraform používat ve službě Cloud Shell, musíte si [nainstalovat Node.js](https://nodejs.org/en/download/) ve verzi 6.0 nebo novější.

>[!NOTE]
>Pokud si chcete ověřit, že je Node.js správně nainstalovaný, otevřete okno terminálu a zadejte `node --version`.

### <a name="install-yeoman"></a>Nainstalovat Yeoman

V příkazovém řádku zadejte `npm install -g yo`

![Nainstalovat Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Nainstalujte šablony Yeoman pro modul Terraform

Na příkazovém řádku zadejte `npm install -g generator-az-terra-module`.

![Nainstalujte generátor – az-terra – modul](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>K ověření, že je nainstalovaný Yeoman, v okně terminálu zadejte `yo --version`.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Vytvořte prázdnou složku pro uložení modulu generujícího Yeoman

Šablona Yeoman generuje soubory do **aktuálního adresáře**. Z tohoto důvodu je potřeba vytvořit adresář.

>[!Note]
>Tento adresář prázdný je potřeba umístit pod $GOPATH/src. Najdete pokyny [zde](https://github.com/golang/go/wiki/SettingGOPATH), abyste provedli tuto akci.

Z příkazovém řádku zadejte:

1. Přejděte do nadřazeného adresáře, který má obsahovat nový, prázdný adresář, který se chystáte vytvořit.
1. Zadejte `mkdir <new-directory-name>`.

    >[!NOTE]
    >< new-directory-name > nahraďte názvem nového adresáře. V tomto příkladu jsme pojmenovali nový adresář`GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Přejděte do nového adresáře tak, že zadáte `cd <new directory's name>`a pak stisknete **enter**.

    ![Přejdete do nového adresáře](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >K ujištění, že je tento adresář je prázdný, zadejte `ls`. Neměly by existovat žádné soubory uvedené ve výsledném výstupu tohoto příkazu.

## <a name="create-a-base-module-template"></a>Vytvořte šablonu základního modulu

Z příkazovém řádku zadejte:

1. Zadejte `yo az-terra-module`.

1. Použijte pokyny na obrazovce pokynů zadejte následující informace:

    - *Název modulu projektu Terraform* 

        ![Název projektu](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >V tomto příkladu jsme zadali `doc-sample-module`.

    - *Chcete zahrnout soubor Docker image?*

        ![Zahrnout soubor Docker image?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Zadejte `y`. Pokud vyberete **n**, kód vygenerovaný modulu bude podporovat spuštěna pouze v nativním režimu.

3. Zadejte `ls` k zobrazení výsledných souborů, které byly vytvořeny.

    ![Seznam vytvořených souborů](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Projděte si vygenerovaný kód modulu

1. Spusťte Visual Studio Code

1. Z řádku nabídek vyberte **Soubor > Otevřít složku** (Soubor > Otevřít složku), přejděte na složku, kterou jste vytvořili, a vyberte ji.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Podívejte se na některé soubory, které byly vytvořeny podle generátorem modulu Yeoman.

>[!Note]
>V tomto článku použijeme main.tf, variables.tf a souborů outputs.tf, vytvořené generátorem modulu Yeoman. Ale při vytváření vlastních modulů můžete upravit tyto souborů tak, aby vyhovovaly funkci modulů Terraformu. Další podrobné informace o těchto souborech a jejich použití naleznete v tématu [Terratest u modulů Terraformu.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

### <a name="maintf"></a>main.TF

Definuje modul s názvem *náhodné random-shuffle*. Vstupem je *string_list*. Výstup je počtem permutací.

### <a name="variablestf"></a>variables.tf

Definuje vstupní a výstupní proměnné použité modulem.

### <a name="outputstf"></a>outputs.TF

Definuje, co modul vydává. Zde je to hodnota vrácená prostřednictvím **random_shuffle**, což je integrovaný modul Terraform.

### <a name="rakefile"></a>Rakefile

Definuje kroky sestavení. Tyto kroky zahrnují:

- **Sestavení**: Ověří, formátování main.tf souboru.
- **Jednotka**: Kostru generované modul neobsahuje kód pro testování částí. Pokud chcete specifikovat scénář testování jednotky, přidejte sem kód.
- **e2e**: Spouští začátku do konce testu modulu.

### <a name="test"></a>test

- Testovací případy jsou napsané v Go.
- Všechny kódy v testu jsou testy end-to-end.
- Testy end-to-end se pokouší použít Terraform ke zřízení všech položek definovaných v části  **příslušenství** a pak porovná výstup v **template_output.go** kódu pomocí předem definovaných očekávaných hodnot.
- **Gopkg.Lock** a **Gopkg.toml**: Definování závislostí. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testování nového modulu Terraformu pomocí souboru Docker

>[!NOTE]
>V našem příkladu jsme se modul spustili jako místní modul bez zásahu do Azure.

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

    ![Úspěšně integrováno](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Na příkazovém řádku zadejte `docker image ls`.

    V seznamu se zobrazí se nově vytvořený modul *terra-mod-example*.

    ![Úložiště výsledků](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >Název modulu *terra-mod-example*, byl zadaný v příkazu, který jste zadali v kroku 1 výše.

1. Zadejte `docker run -it terra-mod-example /bin/sh`.

    Nyní jsou spuštěné v Dockeru a výpis souboru můžete zobrazit tak, že zadáte `ls`.

    ![Soubor se seznamem Dockeru](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Sestavení modulu

1. Zadejte `bundle install`.

    Počkejte na zprávu **dokončení sady** a potom pokračujte dalším krokem.

1. Zadejte `rake build`.

    ![Integrace Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Spustit tak kompletní test

1. Zadejte `rake e2e`.

1. Po chvíli se zobrazí zpráva **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Zadejte `exit` dokončete tak kompletní test a zavřete prostředí Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Generátor použijte Yeoman k vytvoření a otestování modulu ve službě Cloud Shell

V předchozí části jste zjistili, jak otestovat Terraformu modulu pomocí souboru Docker. V této části použijete Yeoman generátor pro vytváření a testování modulu ve službě Cloud Shell.

Pomocí Cloud Shellu, namísto použití souboru Dockeru výrazně zjednodušuje proces. Použití Cloud Shell:

- Není potřeba instalovat Node.js
- Není potřeba instalovat Yeoman
- Není potřeba instalovat Terraformu

Všechny tyto položky jsou předem nainstalované ve službě Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Spustit relaci služby Cloud Shell

1. Spusťte relaci Azure Cloud Shell pomocí buď [webu Azure portal](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com), nebo [mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. **Vítá vás Azure Cloud Shell** otevře se stránka. Vyberte **Bash (Linux)**. (Power Shell není podporována.)

    ![Vítá vás Azure Cloud Shell.](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >V tomto příkladu jsme vybrali Bash (Linux).

1. Pokud ještě nemáte nastavený účet Azure Storage, zobrazí se následující obrazovka. Vyberte **Create storage** (Vytvořit úložiště).

    ![Nemáte připojené žádné úložiště.](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell se spustí v prostředí, které jste vybrali, a zobrazí informace o cloudové jednotce, kterou pro vás právě vytvořil.

    ![Cloudová jednotka je vytvořená.](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Připravit složku pro uložení modul Terraformu

1. V tomto okamžiku Cloud Shell se už nakonfigurovali GOPATH v proměnných prostředí za vás. Chcete-li zobrazit cestu, zadejte `go env`.

1. Pokud ještě neexistuje, vytvořte složku $GOPATH: Zadejte `mkdir ~/go`.

1. Vytvořte složku ve složce $GOPATH: Zadejte `mkdir ~/go/src`. Tato složka se použije k uložení a uspořádat složky jiného projektu, které mohou vytvořit, jako je například složka < your-module-name >, které vytvoříte v dalším kroku.

1. Vytvořte složku pro uložení Terraformu modul: Zadejte `mkdir ~/go/src/<your-module-name>`.

    >[!NOTE]
    >V tomto příkladu jsme zvolili `my-module-name` název složky.

1. Přejděte do složky vašeho modulu: Enter `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Vytvoření a otestování modul Terraformu

1. Zadejte `yo az-terra-module` a postupujte podle pokynů v průvodci.

    >[!NOTE]
    >Když se zobrazí výzva, pokud chcete vytvořit soubory Docker, můžete zadat `N`.

1. Zadejte `bundle install` instalace závislostí.

    Počkejte na zprávu **dokončení sady** a potom pokračujte dalším krokem.

1. Zadejte `rake build` k sestavení vašeho modulu.

    ![Integrace Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Zadejte `rake e2e` testu začátku do konce.

1. Po chvíli se zobrazí zpráva **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Instalace a použití rozšíření Visual Studio Code Terraformu pro Azure.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
