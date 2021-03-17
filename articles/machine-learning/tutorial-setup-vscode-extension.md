---
title: 'Kurz: nastavení rozšíření Visual Studio Code (Preview)'
titleSuffix: Azure Machine Learning
description: Naučte se instalovat a spouštět skripty pro školení pomocí rozšíření Azure Machine Learning Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 11/16/2020
ms.openlocfilehash: f485c12bf82dde798a77175a3fab4d76488150c3
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701197"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension-preview"></a>Nastavení rozšíření Azure Machine Learning Visual Studio Code (Preview)

Naučte se instalovat a spouštět skripty pomocí rozšíření Azure Machine Learning Visual Studio Code.

V tomto kurzu se naučíte provádět následující úlohy:

> [!div class="checklist"]
> * Instalace rozšíření Azure Machine Learning Visual Studio Code
> * Přihlaste se k účtu Azure z Visual Studio Code
> * Použití rozšíření Azure Machine Learning ke spuštění ukázkového skriptu

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud ho ještě nemáte, zaregistrujte se a vyzkoušejte si [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Pokud ho nemáte, [nainstalujte ho](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalace rozšíření

1. Otevřete Visual Studio Code.
1. Kliknutím na ikonu **rozšíření** na **řádku aktivity** otevřete zobrazení rozšíření.
1. V zobrazení rozšíření vyhledejte "Azure Machine Learning".
1. Vyberte **Install** (Nainstalovat).

    > [!div class="mx-imgBorder"]
    > ![Nainstalovat rozšíření Azure Machine Learning VS Code](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

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
    > ![Spustit experiment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Po rozbalení palety příkazů postupujte podle zobrazených výzev.

    > [!NOTE]
    > Pokud již máte zřízeny stávající prostředky Azure Machine Learning, přečtěte si téma [Jak spustit experimenty v průvodci vs Code](./how-to-manage-resources-vscode.md#run-experiment).

    1. Vyberte své předplatné Azure.
    1. V seznamu prostředí vyberte **soubor conda závislosti**.
    1. Stiskněte klávesu **ENTER** pro procházení souboru závislostí conda. Tento soubor obsahuje závislosti potřebné ke spuštění vašeho skriptu. V tomto případě je soubor závislostí souborem v `env.yml` `mnist-vscode-docs-sample` adresáři.
    1. Pro procházení souboru školicího skriptu stiskněte klávesu **ENTER** . Jedná se o soubor, který obsahuje kód pro model strojového učení, který zařadí obrázky psaných číslic do kategorií. V tomto případě skript pro výuku modelu je `train.py` soubor v `mnist-vscode-docs-sample` adresáři.

1. V tomto okamžiku se v textovém editoru zobrazí konfigurační soubor podobný tomu níže. Tato konfigurace obsahuje informace potřebné ke spuštění školicí úlohy, jako je soubor, který obsahuje kód pro výuku modelu a jakékoli závislosti Pythonu zadané v předchozím kroku.

    ```json
    {
        "workspace": "WS06271500",
        "resourceGroup": "WS06271500-rg2",
        "location": "South Central US",
        "experiment": "WS06271500-exp1",
        "compute": {
            "name": "WS06271500-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS06271500-com1-rc1",
            "environment": {
                "name": "WS06271500-env1",
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

1. Až budete s konfigurací spokojeni, odešlete experiment otevřením palety příkazů a zadáním následujícího příkazu:

    ```text
    Azure ML: Submit Experiment
    ```

    Tím se `train.py` konfigurační soubor a odešle do pracovního prostoru Azure Machine Learning. Úkol školení se pak spustí na výpočetním prostředku v Azure.

### <a name="track-the-progress-of-the-training-script"></a>Sledování průběhu školicího skriptu

Spuštění skriptu může trvat několik minut. Postup sledování průběhu:

1. Na řádku aktivity vyberte ikonu **Azure** .
1. Rozbalte uzel předplatného.
1. Rozbalíte aktuálně běžící uzel experimentu. To se nachází uvnitř `{workspace}/Experiments/{experiment}` uzlu, ve kterém jsou hodnoty pro váš pracovní prostor a experiment stejné jako vlastnosti definované v konfiguračním souboru.
1. V seznamu jsou uvedena všechna spuštění experimentu a také jejich stav. Chcete-li získat nejnovější stav, klikněte na ikonu aktualizace v horní části zobrazení Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Sledovat pokrok experimentu](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Stažení proučeného modelu

Po dokončení experimentu je výstupem školený model. Chcete-li stáhnout výstupy místně:

1. Klikněte pravým tlačítkem na poslední běh a vyberte **Stáhnout výstupy**.

    > [!div class="mx-imgBorder"]
    > ![Stáhnout školený model](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Vyberte umístění, do kterého se mají ukládat výstupy.
1. Složka s názvem vašeho spuštění se stáhne místně. Přejděte na ni.
1. Soubory modelu jsou uvnitř `outputs/outputs/model` adresáře.

## <a name="next-steps"></a>Další kroky

* [Kurz: analýza a nasazení modelu TensorFlow pro klasifikaci imagí pomocí rozšíření Azure Machine Learning Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
