---
title: 'Kurz: nastavení rozšíření Visual Studio Code'
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak nastavit rozšíření Visual Studio Code Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: 583071ee22e4fb9cffc741520b1583790002a5bf
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604867"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Nastavení rozšíření Azure Machine Learning Visual Studio Code

Naučte se instalovat a spouštět skripty pomocí rozšíření Azure Machine Learning Visual Studio Code.

V tomto kurzu se seznámíte s následujícími úlohami:

> [!div class="checklist"]
> * Instalace rozšíření Azure Machine Learning Visual Studio Code
> * Přihlaste se k účtu Azure z Visual Studio Code
> * Použití rozšíření Azure Machine Learning ke spuštění ukázkového skriptu

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho ještě nemáte, zaregistrujte se a vyzkoušejte si [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Pokud ho nemáte, [nainstalujte ho](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalace rozšíření

1. Otevřete Visual Studio Code.
1. Kliknutím na ikonu **rozšíření** na **řádku aktivity** otevřete zobrazení rozšíření.
1. V zobrazení rozšíření vyhledejte "Azure Machine Learning".
1. Vyberte **Install** (Nainstalovat).

    > [!div class="mx-imgBorder"]
    > ![nainstalovat rozšíření Azure Machine Learning VS Code](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Alternativně můžete nainstalovat Azure Machine Learning rozšíření přes Visual Studio Marketplace [stažením instalačního programu přímo](https://aka.ms/vscodetoolsforai). 

Zbývající kroky v tomto kurzu byly testovány pomocí **0.6.8 verze** rozšíření.

## <a name="sign-in-to-your-azure-account"></a>Přihlaste se ke svému účtu Azure.

Aby bylo možné zřídit prostředky a spouštět úlohy v Azure, musíte se přihlásit pomocí přihlašovacích údajů k účtu Azure. Pro pomoc se správou účtů Azure Machine Learning automaticky nainstaluje rozšíření účtu Azure. [Další informace o rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)najdete na následujícím webu.

1. Otevřete paletu příkazů tak, že na řádku nabídek vyberete **zobrazit > paleta příkazů** . 
1. Zadáním příkazu "Azure: Sign in" do palety příkazů spusťte proces přihlašování.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Spuštění skriptu školení modelu Machine Learning v Azure

Teď, když jste se k Azure přihlásili pomocí přihlašovacích údajů k účtu, použijte postup v této části, kde se dozvíte, jak používat rozšíření pro výuku modelu strojového učení.

1. Stažení a extrahování [vs Code nástrojů pro úložiště AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) kdekoli v počítači.
1. Otevřete `mnist-vscode-docs-sample` adresář v Visual Studio Code.
1. Na řádku aktivity vyberte ikonu **Azure** .
1. V horní části zobrazení Azure Machine Learning vyberte ikonu pro **Spuštění experimentu** .

    > [!div class="mx-imgBorder"]
    > ![spustit experiment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Po rozbalení palety příkazů postupujte podle zobrazených výzev.

    1. Vyberte své předplatné Azure.
    1. Vyberte **vytvořit nový pracovní prostor Azure ml** .
    1. Vyberte TensorFlow typ úlohy **školení s jedním uzlem** .
    1. Jako skript pro výuku zadejte `train.py`. Jedná se o soubor, který obsahuje kód pro model strojového učení, který zařadí obrázky psaných číslic do kategorií.
    1. Zadejte následující balíčky jako požadavky ke spuštění.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. V tomto okamžiku se v textovém editoru zobrazí konfigurační soubor podobný tomu níže. Tato konfigurace obsahuje informace potřebné ke spuštění školicí úlohy, jako je soubor, který obsahuje kód pro výuku modelu a jakékoli závislosti Pythonu zadané v předchozím kroku.

    ```json
    {
        "workspace": "WS01311608",
        "resourceGroup": "WS01311608-rg1",
        "location": "South Central US",
        "experiment": "WS01311608-exp1",
        "compute": {
            "name": "WS01311608-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS01311608-com1-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Až budete s konfigurací spokojeni, odešlete experiment otevřením palety příkazů a zadáním následujícího příkazu:

    ```text
    Azure ML: Submit Experiment
    ```

    Tím se `train.py` a konfigurační soubor odešle do pracovního prostoru Azure Machine Learning. Úkol školení se pak spustí na výpočetním prostředku v Azure.

### <a name="track-the-progress-of-the-training-script"></a>Sledování průběhu školicího skriptu

Spuštění skriptu může trvat několik minut. Postup sledování průběhu:

1. Na řádku aktivity vyberte ikonu **Azure** .
1. Rozbalte uzel předplatného.
1. Rozbalíte aktuálně běžící uzel experimentu. Nachází se v uzlu `{workspace}/Experiments/{experiment}`, ve kterém jsou hodnoty pro váš pracovní prostor a experiment stejné jako vlastnosti definované v konfiguračním souboru.
1. V seznamu jsou uvedena všechna spuštění experimentu a také jejich stav. Chcete-li získat nejnovější stav, klikněte na ikonu aktualizace v horní části zobrazení Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![sledovat průběh experimentu](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Stažení proučeného modelu

Po dokončení experimentu je výstupem školený model. Chcete-li stáhnout výstupy místně:

1. Klikněte pravým tlačítkem na poslední běh a vyberte **Stáhnout výstupy**.

    > [!div class="mx-imgBorder"]
    > ![stáhnout školený model](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Vyberte umístění, do kterého se mají ukládat výstupy.
1. Složka s názvem vašeho spuštění se stáhne místně. Přejděte na ni.
1. Soubory modelu jsou uvnitř `outputs/outputs/model`ho adresáře.

## <a name="next-steps"></a>Další kroky

* [Kurz: analýza a nasazení modelu TensorFlow pro klasifikaci imagí pomocí rozšíření Azure Machine Learning Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
