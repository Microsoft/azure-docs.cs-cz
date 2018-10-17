---
title: Generátor modulů Terraformu pro Azure vs. kódu
description: Zjistěte, jak pomocí generátoru Yeoman vytvoříme základní šablonu Terraformu.
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuální počítač, azure, yeoman
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 09/12/2018
ms.openlocfilehash: 513b123c44cf2cd37cf81a91e0d2da9599eb1fcd
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396250"
---
# <a name="create-a-terraform-base-template-using-yeoman"></a>Vytvoření základní šablony Terraformu pomocí Yeomanu

[Terraform](https://docs.microsoft.com/azure/terraform/
) poskytuje způsob, jak snadno vytvořit infrastrukturu v Azure. [Yeoman](http://yeoman.io/) výrazně usnadňuje úlohy vývojáře modulu při vytváření modulů Terraformu a současně poskytuje vynikající *rámec osvědčených postupů*.

V tomto článku se dozvíte, jak používat generátor modulů Yeoman k vytvoření základní šablony Terraform.

## <a name="prerequisites"></a>Požadavky

- Počítač se systémem Windows 10, Linux nebo macOS 10.10 +.
- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- **Visual Studio Code**: Použijeme [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) k prozkoumání souborů vytvořených generátorem Yeoman. Můžete však použít libovolný editor kódu podle vašeho výběru.
- **Terraform**: budete potřebovat instalaci [Terraformu](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) ke spuštění modulu vytvořenou pomocí Yeomanu.
- **Docker**: použijeme [Docker](https://www.docker.com/get-started) ke spuštění modulu vytvořené generátorem Yeoman. (Pokud tomu dáváte přednost, můžete použít Ruby místo Dockeru pro spuštění vzorového modulu.)
- **Programovací jazyk Go**: budete potřebovat instalaci [Go](https://golang.org/) protože testovací případy generované pomocí Yeomanu jsou napsané v Go.

>[!NOTE]
>Většina procedur v tomto tutoriálu zahrnuje položky příkazového řádku. Kroky popsané zde platí pro všechny operační systémy a nástroje příkazového řádku. V našich příkladech jsme se rozhodli použít PowerShell. Ale můžete použít některou z několika alternativy jako Git Bash, příkazové řádky Windows nebo příkazy příkazového řádku Linux nebo macOS.

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
        >Zadejte `y`. Pokud vyberete **n**, vygenerovaný kód modulu bude podporovat spuštění pouze v nativním režimu.

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

- **sestavení**: ověří formátování main.tf souboru.
- **jednotka**: Generovaná modulová kostra neobsahuje kód pro testování jednotky. Pokud chcete specifikovat scénář testování jednotky, přidejte sem kód.
- **e2e**: spouští test modulu end-to-end.

### <a name="test"></a>test

- Testovací případy jsou napsané v Go.
- Všechny kódy v testu jsou testy end-to-end.
- Testy end-to-end se pokouší použít Terraform ke zřízení všech položek definovaných v části  **příslušenství** a pak porovná výstup v **template_output.go** kódu pomocí předem definovaných očekávaných hodnot.
- **Gopkg.Lock** a **Gopkg.toml**: Definujte své závislostí. 

## <a name="test-the-module-using-docker"></a>Testujte modul pomocí Dockeru

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

1. Zadejte `bundle install`.

    Počkejte na zprávu **dokončení sady** a potom pokračujte dalším krokem.

1. Zadejte `rake build`.

    ![Integrace Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="perform-the-end-to-end-test"></a>Proveďte test end-to-end

1. Zadejte `rake e2e`.

1. Po chvíli se zobrazí zpráva **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Zadejte `exit` k dokončení end-to-end testu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Instalujte a použijte rozšíření Azure Terraform Visual Studio Code.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
