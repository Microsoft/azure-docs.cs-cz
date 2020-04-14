---
title: 'Kurz: Nastavení rozšíření kódu sady Visual Studio'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak nastavit rozšíření Visual Studio Code Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: 731ab18346ac9f100862174312c2c9950026f1eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272902"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Nastavení rozšíření kódu Azure Machine Learning Visual Studio

Zjistěte, jak nainstalovat a spustit skripty pomocí rozšíření Azure Machine Learning Visual Studio Code.

V tomto kurzu se naučíte provádět následující úlohy:

> [!div class="checklist"]
> * Instalace rozšíření kódu Azure Machine Learning Visual Studio
> * Přihlášení k účtu Azure z kódu Visual Studia
> * Spuštění ukázkového skriptu pomocí rozšíření Azure Machine Learning

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, zaregistrujte se a vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Pokud ji nemáte, [nainstalujte ji](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalace rozšíření

1. Otevřete Visual Studio Code.
1. Vyberte **Ikonu Rozšíření** z **panelu aktivit** otevřete zobrazení Rozšíření.
1. V zobrazení rozšíření vyhledejte "Azure Machine Learning".
1. Vyberte **Install** (Nainstalovat).

    > [!div class="mx-imgBorder"]
    > ![Instalace rozšíření Azure Machine Learning vs code extension](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Případně můžete nainstalovat rozšíření Azure Machine Learning přes Visual Studio Marketplace [stažením instalačního programu přímo](https://aka.ms/vscodetoolsforai). 

Zbývající kroky v tomto kurzu byly testovány s **verzí 0.6.8** rozšíření.

## <a name="sign-in-to-your-azure-account"></a>Přihlášení ke svému účtu Azure

Aby bylo možné zřídit prostředky a spouštět úlohy v Azure, musíte se přihlásit pomocí přihlašovacích údajů účtu Azure. Azure Machine Learning automaticky nainstaluje rozšíření účtu Azure, aby vám pomohlo se správou účtů. Další informace [o rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)najdete na následujícím webu.

1. Otevřete paletu příkazů výběrem **možnosti Zobrazit > paletu příkazů** z řádku nabídek. 
1. Zadejte příkaz "Azure: Přihlásit se" do palety příkazů pro spuštění procesu přihlášení.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Spuštění školicího skriptu modelu strojového učení v Azure

Teď, když jste se přihlásili do Azure pomocí přihlašovacích údajů účtu, použijte postup v této části, kde se dozvíte, jak pomocí rozšíření trénovat model strojového učení.

1. Stáhněte si a rozbalte [nástroje kódu VS pro úložiště AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) kdekoli v počítači.
1. Otevřete `mnist-vscode-docs-sample` adresář v kódu sady Visual Studio.
1. Vyberte ikonu **Azure** na panelu aktivit.
1. V horní části zobrazení Azure Machine Learning View vyberte ikonu **Spustit experiment.**

    > [!div class="mx-imgBorder"]
    > ![Spustit experiment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Po rozšíření palety příkazů postupujte podle pokynů.

    1. Vyberte své předplatné Azure.
    1. V seznamu prostředí vyberte **soubor závislostí Conda**.
    1. Stisknutím **klávesy Enter** procházejte soubor závislostí Conda. Tento soubor obsahuje závislosti potřebné ke spuštění skriptu. V tomto případě je soubor `env.yml` závislostí `mnist-vscode-docs-sample` soubor uvnitř adresáře.
    1. Stisknutím **klávesy Enter** procházejte soubor školicího skriptu. Jedná se o soubor, který obsahuje kód pro model strojového učení, který kategorizuje obrázky ručně psaných číslic. V tomto případě skript trénovat model `train.py` je soubor `mnist-vscode-docs-sample` uvnitř adresáře.

1. V tomto okamžiku se v textovém editoru zobrazí konfigurační soubor podobný tomu níže. Konfigurace obsahuje informace potřebné ke spuštění úlohy školení, jako je soubor, který obsahuje kód pro trénování modelu a všechny závislosti Pythonu zadané v předchozím kroku.

    ```json
    {
        "workspace": "WS04131142",
        "resourceGroup": "WS04131142-rg1",
        "location": "South Central US",
        "experiment": "WS04131142-exp1",
        "compute": {
            "name": "WS04131142-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS04131142-com1-rc1",
            "environment": {
                "name": "WS04131142-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. Jakmile budete spokojeni s konfigurací, odešlete experiment otevřením palety příkazů a zadáním následujícího příkazu:

    ```text
    Azure ML: Submit Experiment
    ```

    Tím se `train.py` konfigurační soubor odešle do pracovního prostoru Azure Machine Learning. Trénovací úloha se pak spustí na výpočetní prostředek v Azure.

### <a name="track-the-progress-of-the-training-script"></a>Sledování průběhu školicího skriptu

Spuštění skriptu může trvat několik minut. Chcete-li sledovat jeho průběh:

1. Na panelu aktivit vyberte ikonu **Azure.**
1. Rozbalte uzel předplatného.
1. Rozbalte uzel aktuálně spuštěné experimentu. Nachází se uvnitř `{workspace}/Experiments/{experiment}` uzlu, kde jsou hodnoty pracovního prostoru a experimentu stejné jako vlastnosti definované v konfiguračním souboru.
1. Všechny běhy experimentu jsou uvedeny, stejně jako jejich stav. Pokud chcete získat nejnovější stav, klikněte na ikonu aktualizace v horní části zobrazení Azure Machine Learning View.

    > [!div class="mx-imgBorder"]
    > ![Sledovat průběh experimentu](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Stáhněte si trénovaný model

Po dokončení experimentu výstup je trénovaný model. Stažení výstupů místně:

1. Klepněte pravým tlačítkem myši na poslední spuštění a vyberte **možnost Stáhnout výstupy**.

    > [!div class="mx-imgBorder"]
    > ![Stáhnout trénovaný model](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Vyberte umístění, kam chcete výstupy uložit.
1. Složka s názvem spuštění se stáhne místně. Přejděte na ni.
1. Soubory modelu jsou `outputs/outputs/model` uvnitř adresáře.

## <a name="next-steps"></a>Další kroky

* [Kurz: Trénování a nasazování modelu TensorFlow klasifikace bitových obrázků pomocí rozšíření kódu Azure Machine Learning Visual Studio](tutorial-train-deploy-image-classification-model-vscode.md).
