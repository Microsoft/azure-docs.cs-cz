---
title: Začínáme s místně pokročilým vývojem Azure Percept
description: Začínáme s vývojem v rámci místního strojového učení pomocí VS Code a poznámkových bloků Jupyter na AzureML
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664660"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Začínáme s vývojem ve službě Machine Learning pomocí VS Code a poznámkových bloků Jupyter na AzureML

Tento článek vás provede procesem nastavení Azure Machine Learning pracovního prostoru, vytvořením výpočetní instance, nastavením vývojového prostředí Visual Studio Code na místním počítači a spuštěním buněk předkonfigurovaného ukázkového Jupyter poznámkového bloku v rámci VS Code.

[Poznámkový blok](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) provádí přenosové učení pomocí předem připraveného modelu TensorFlow (MobileNetSSDV2Lite) na AzureML v Pythonu s vlastní datovou sadou pro detekci bowls.

Poznámkový blok ukazuje, jak začít s [díky Coco datovou sadou](https://cocodataset.org/#home), vyfiltrovat ji do pouze třídy Interest (Bowls) a pak ji převést do příslušného formátu. Alternativně můžete použít nástroj pro označování open source [VoTT 2](https://github.com/microsoft/VoTT) k vytváření a popiskům ohraničovacích rámečků ve formátu Pascal VOC.

Po přeškolení modelu pro vlastní datovou sadu můžete model nasadit do Azure Percept DK pomocí metody s dvojitou metodou aktualizace modulu. Inferencing modelu můžete ověřit zobrazením živého datového proudu RTSP z Azure Percept Vision SoM. Přeškolení a nasazení modelu se provádí v rámci poznámkového bloku prostřednictvím vzdálené výpočetní instance.

## <a name="prerequisites"></a>Požadavky

- [Předplatné Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK s připojením služby Azure Percept Vision SoM](./overview-azure-percept-dk.md)
- [Prostředí Azure PERCEPT DK na zprovoznění](./quickstart-percept-dk-set-up.md) se dokončilo.

## <a name="download-azure-percept-github-repository"></a>Stáhnout úložiště GitHub Azure Percept

1. Přejít do [úložiště GitHub Azure PERCEPT DK](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).
1. Naklonujte úložiště nebo Stáhněte soubor ZIP. V horní části obrazovky klikněte na stáhnout **kód**  ->  **zip**.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="Obrazovka pro stažení GitHubu":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Vytvoření pracovního prostoru Azure Machine Learning a vzdálené instance COMPUTE

1. Přejít na [Azure Machine Learning portál](https://ml.azure.com).
1. V rozevíracích nabídkách vyberte svůj adresář, předplatné Azure a Machine Learning pracovní prostor a klikněte na **Začínáme**.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Obrazovka Začínáme s Azure ML.":::

    Pokud ještě nemáte pracovní prostor Azure Machine Learning, klikněte na **vytvořit nový pracovní prostor**. Na nové kartě prohlížeče udělejte toto:

    1. Vyberte své předplatné Azure.
    1. Vyberte skupinu prostředků.
    1. Zadejte název pracovního prostoru.
    1. Vyberte oblast.
    1. Vyberte edici vašeho pracovního prostoru.
    1. Klikněte na **Zkontrolovat a vytvořit**.
    1. Na další stránce zkontrolujte výběr a klikněte na **vytvořit**.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Obrazovka pro vytvoření pracovního prostoru v Azure ML":::

    Počkejte prosím několik minut na vytvoření pracovního prostoru. Po vytvoření pracovního prostoru se zobrazí zelená zaškrtnutí vedle vašich prostředků a **nasazení se dokončí** v horní části stránky s přehledem Machine Learning Services.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Potvrzení vytvoření pracovního prostoru" lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    Po vytvoření pracovního prostoru se vraťte na kartu portál Machine Learning a klikněte na **Začínáme**.

1. Na domovské stránce pracovního prostoru Machine Learning klikněte na **COMPUTE** v levém podokně.

1. Pokud neexistuje žádná existující výpočetní instance, vytvořte nový procesor nebo výpočetní prostředí GPU kliknutím na **Nový**. V okně **Nová instance COMPUTE** zadejte **výpočetní název**, zvolte **typ virtuálního počítače** a vyberte **Velikost virtuálního počítače**. Klikněte na **Vytvořit**.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Obrazovka pro vytvoření nové instance COMPUTE":::

    Po kliknutí na **vytvořit** bude trvat několik minut, než se vytvoří výpočetní instance. Po dokončení výpočetního prostředí se ve vašem stavu služby **COMPUTE** zobrazí zelený kroužek a **\<your compute name> spuštěný** . Tato výpočetní instance spouští server Jupyter a bude se využívat pro tento kurz.

## <a name="visual-studio-code-development-environment-setup"></a>Nastavení vývojového prostředí Visual Studio Code

1. Nainstalujte požadované nástroje.

    1. Možnost 1:

        Pomocí [instalačního programu sady nástrojů pro vývojáře](./dev-tools-installer.md) nainstalujte následující balíčky:

        - Visual Studio Code
        - Python 3,5, 3,6 nebo 3,7
        - Miniconda3
        - Intel OpenVino Toolkit 2020,2

        Poznámka: sada nástrojů Intel OpenVino Toolkit je v instalačním programu sady nástrojů pro vývojáře v seznamu volitelná, ale vyžaduje se pro práci s Azure Percept Vision SoM SoM pro vývojovou sadou Azure Percept DK.

    1. Možnost 2:

        Pokud nechcete používat instalační program sady nástrojů pro vývojáře, nainstalujte následující balíčky ručně kliknutím na odkazy níže a podle uvedených pokynů ke stažení a instalaci:

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3,5, 3,6 nebo 3,7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel OpenVino Toolkit 2020,2](https://docs.openvinotoolkit.org/)

    Poznámka: Pokud už máte nainstalovanou úplnou distribuci Anaconda, nemusíte instalovat Miniconda. Případně, pokud nechcete používat Anaconda nebo Miniconda, můžete vytvořit virtuální prostředí Python a nainstalovat balíčky potřebné ke spuštění vývoje modelu AI pomocí PIP.

1. Spusťte editor Visual Studio Code.
1. Nastavení prostředí pro datové vědy:

    - Možnost 1 – připojení ke stávající nebo nové vzdálené výpočetní instanci služby Machine Learning, která již obsahuje dodané balíčky ML **Tato možnost se používá v tomto kurzu**.

    - Možnost 2 – nastavení conda prostředí v místním počítači
        1. Otevřete příkazový řádek Anaconda nebo Miniconda a spuštěním následujícího příkazu vytvořte prostředí s názvem **MyENV** se zadanými balíčky:

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Další informace o vytváření a správě prostředí Anaconda najdete v [dokumentaci k Anaconda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

1. Vytvořit VS Code pracovní prostor:

    1. Vytvořte složku na vhodném místě, abyste mohli sloužit jako pracovní prostor Visual Studio Code. Pojmenujte ho **MyWorkspace**.
    1. Otevřete **MyWorkspace** v Visual Studio Code kliknutím na **soubor**  >  **Otevřít složku**  >  **Vybrat složku**.
    1. V Průzkumníku souborů přejděte na a vyberte [soubor Transferlearningusing_SSDLiteV2 model. ipynbb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) z místní kopie úložiště GitHub Azure Percept DK. Zkopírujte tento soubor poznámkového bloku do složky MyWorkspace.

## <a name="azure-integration-with-visual-studio-code"></a>Integrace Azure s Visual Studio Code

1. Pokud jste to ještě neudělali, zaregistrujte se do [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

1. Pro VS Code nainstalujte následující rozšíření Azure:
    - [Rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).
    - Rozšíření Python Insiders. V vs Code přejdete na **zobrazení**  ->  **paleta příkazů**. V paletě příkazů zadejte a vyberte **Python: přepnout na denní kanál Insider**.
    - [Rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Po zobrazení výzvy znovu načíst okno v VS Code.
    - [Rozšíření sady Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Přihlaste se ke svému účtu Azure v rámci Visual Studio Code, abyste mohli zřizovat prostředky a spouštět úlohy v Azure.
    1. Otevřete paletu příkazů v Visual Studio Code tím, že na řádku nabídek vyberete **zobrazení**  >  **paleta příkazů** .
    1. Zadejte **Azure: Přihlaste** se do palety příkazů a spusťte proces přihlášení.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Přihlašovací obrazovka Azure" lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Aktivujte rozšíření Python a účet Azure kliknutím na ikonu Azure na levé straně VS Code.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Ikona Azure v VS Code.":::

    1. Otevřete Transferlearningusing_SSDLiteV2 Poznámkový blok Model_VSCode. ipynb ze složky **MyWorkspace** .
    1. Otevřete paletu příkazů. Zadejte a vyberte **Python: Zadejte místní nebo vzdálený Jupyter Server pro připojení**.
    1. Měl by se zobrazit seznam možností připojení, ze kterých si můžete vybrat. Vyberte **instance služby Azure výpočetní prostředky služby ml**.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Možnosti Azure ML COMPUTE instance v VS Code.":::

    1. Podle pokynů v průvodci vyberte předplatné, pracovní prostor a instanci vzdálené výpočetní instance. Vyberte pracovní prostor a instanci vzdálené výpočetní instance vytvořené dříve v tomto kurzu. Máte také možnost vytvořit novou výpočetní instanci.
    1. Po výběru instance COMPUTE se zobrazí výzva k opětovnému načtení okna VS Code. Po opětovném načtení vyberte **Python 3,6-AzureML** kernel. Nyní můžete spustit libovolnou buňku v poznámkovém bloku. Když se spustí buňka poznámkového bloku, vytvoří se připojení mezi vaším poznámkovým blokem a vaší výpočetní instancí. Panel nástrojů Poznámkový blok se aktualizuje tak, aby odrážel výpočetní instanci, se kterou právě pracujete.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="Výběr jádra v VS Code." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Práce s poznámkovým blokem

Nyní jste připraveni spustit Poznámkový blok pro vlastní rozpoznávání Bowl v VS Code a nasadit ho do DevKit. Nezapomeňte spustit každou buňku poznámkového bloku jednotlivě, protože některé buňky vyžadují vstupní parametry před spuštěním skriptu. Buňky, které vyžadují vstupní parametry, mohou být upraveny přímo v poznámkovém bloku. Chcete-li spustit buňku, klikněte na ikonu Přehrát na levé straně buňky:

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Ikona buňky zvýraznění poznámkového bloku":::

## <a name="next-steps"></a>Další kroky

Další ukázkové poznámkové bloky pro Azure Machine Learning služby najdete na tomto [úložišti](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml).
