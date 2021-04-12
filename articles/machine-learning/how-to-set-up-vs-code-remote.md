---
title: Připojení k výpočetní instanci v Visual Studio Code (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se připojit k Azure Machine Learning výpočetní instanci v Visual Studio Code a spustit interaktivní Jupyter Notebook a vzdálené vývojové úlohy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028575"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Připojení k Azure Machine Learning výpočetní instance v Visual Studio Code (Preview)

V tomto článku se dozvíte, jak se připojit k instanci služby Azure Machine Learning COMPUTE pomocí Visual Studio Code.

[Instance služby Azure Machine Learning COMPUTE](concept-compute-instance.md) je plně spravovaná cloudová pracovní stanice pro odborníky přes data a poskytuje funkce pro správu a připravenost v podniku pro správce IT.

Existují dva způsoby, jak se můžete připojit k výpočetní instanci z Visual Studio Code:

* Vzdálená výpočetní instance. Tato možnost vám poskytne plnohodnotné vývojové prostředí pro vytváření projektů machine learningu.
* Vzdálený Jupyter Notebook Server. Tato možnost umožňuje nastavit výpočetní instanci jako server vzdálené Jupyter Notebook.

## <a name="configure-a-remote-compute-instance"></a>Konfigurace vzdálené výpočetní instance

Ke konfiguraci vzdálené výpočetní instance pro vývoj budete potřebovat několik požadavků.

* Rozšíření Azure Machine Learning Visual Studio Code. Další informace najdete v [Průvodci nastavením rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).
* Azure Machine Learning pracovní prostor. [Pomocí rozšíření Azure Machine Learning Visual Studio Code můžete vytvořit nový pracovní prostor](how-to-manage-resources-vscode.md#create-a-workspace) , pokud ho ještě nemáte.
* Azure Machine Learning výpočetní instance. [Použijte rozšíření Azure Machine Learning Visual Studio Code k vytvoření nové instance COMPUTE](how-to-manage-resources-vscode.md#create-compute-instance) , pokud ji ještě nemáte.

Připojení ke vzdálené výpočetní instanci:

# <a name="vs-code"></a>[VS Code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Rozšíření Azure Machine Learning

1. V VS Code spusťte rozšíření Azure Machine Learning.
1. Rozbalte uzel **výpočetní instance** v rozšíření.
1. Klikněte pravým tlačítkem na výpočetní instanci, ke které se chcete připojit, a vyberte **připojit k instanci COMPUTE**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Připojení k výpočetní instanci Visual Studio Code rozšíření Azure ML" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Paleta příkazů

1. V VS Code otevřete paletu příkazů výběrem možnosti **zobrazit > paleta příkazů**.
1. Zadejte do textového pole **Azure ml: připojit k výpočetní instanci**.
1. Vyberte své předplatné.
1. Vyberte svůj pracovní prostor.
1. Vyberte instanci služby COMPUTE nebo vytvořte novou.

# <a name="studio"></a>[Studio](#tab/studio)

Přejít na [ml.Azure.com](https://ml.azure.com)

> [!IMPORTANT]
> Abyste se mohli připojit ke vzdálené výpočetní instanci z Visual Studio Code, ujistěte se, že je účet, ke kterému jste přihlášení, v Azure Machine Learning Studiu stejný jako ten, který používáte v Visual Studio Code.

### <a name="compute"></a>Compute

1. Vyberte kartu **COMPUTE** .
1. Ve sloupci *identifikátor URI aplikace* vyberte **vs Code** pro výpočetní instanci, ke které se chcete připojit.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Připojení k výpočetní instanci VS Code Azure ML Studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Poznámkový blok

1. Vyberte kartu **Poznámkový blok** .
1. Na kartě *Poznámkový blok* vyberte soubor, který chcete upravit.
1. Vyberte **editory > upravit v vs Code (Preview)**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Připojení k výpočetní instanci VS Code poznámkovém bloku Azure ML" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

Otevře se nové okno pro vaši vzdálenou výpočetní instanci. Při pokusu o vytvoření připojení ke vzdálené výpočetní instanci dochází k následujícím úlohám:

1. Autorizace K ověření, že se uživatel pokouší vytvořit připojení, je nutné provést některé kontroly, aby bylo možné použít výpočetní instanci.
1. Na výpočetní instanci je nainstalovaný vzdálený server VS Code.
1. Připojení k protokolu WebSocket je vytvořeno pro interakci v reálném čase.

Jakmile je připojení navázáno, zůstane trvale. Token se vydá na začátku relace, která se automaticky aktualizuje, aby se zachovalo připojení k vaší výpočetní instanci.

Po připojení ke vzdálené výpočetní instanci použijte editor k těmto akcím:

* Vytvářejte [a spravujte soubory na vzdálené výpočetní instanci nebo sdílené složce souborů](https://code.visualstudio.com/docs/editor/codebasics).
* Použití [integrovaného terminálu vs Code](https://code.visualstudio.com/docs/editor/integrated-terminal) ke [spouštění příkazů a aplikací na vzdálené instanci služby COMPUTE](how-to-access-terminal.md).
* [Ladění skriptů a aplikací](https://code.visualstudio.com/Docs/editor/debugging)
* [Použití VS Code ke správě úložišť Git](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Konfigurace výpočetní instance jako vzdáleného poznámkového serveru

Abyste mohli nakonfigurovat výpočetní instanci jako server vzdálené Jupyter Notebook, budete potřebovat několik požadavků:

* Rozšíření Azure Machine Learning Visual Studio Code. Další informace najdete v [Průvodci nastavením rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).
* Azure Machine Learning pracovní prostor. [Pomocí rozšíření Azure Machine Learning Visual Studio Code můžete vytvořit nový pracovní prostor](how-to-manage-resources-vscode.md#create-a-workspace) , pokud ho ještě nemáte.

Připojení k výpočetní instanci:

1. Otevřete Jupyter Notebook v Visual Studio Code.
1. Po načtení integrovaného poznámkového bloku vyberte **Jupyter Server**.

    > [!div class="mx-imgBorder"]
    > ![Rozevírací seznam pro spuštění Azure Machine Learning vzdáleného Jupyter Notebook serveru](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Případně můžete také použít paletu příkazů:

    1. Otevřete paletu příkazů tak, že na řádku nabídek vyberete **zobrazit > paleta příkazů** .
    1. Do textového pole zadejte `Azure ML: Connect to Compute instance Jupyter server` .

1. Vyberte `Azure ML Compute Instances` ze seznamu možností serveru Jupyter.
1. Vyberte své předplatné ze seznamu předplatných. Pokud jste dříve nakonfigurovali výchozí pracovní prostor Azure Machine Learning, tento krok se přeskočí.
1. Vyberte svůj pracovní prostor.
1. Ze seznamu vyberte svou výpočetní instanci. Pokud ho nemáte, vyberte **vytvořit novou instanci služby Azure výpočetní prostředky služby ml** a vytvořte ji podle pokynů.
1. Změny se projeví až po opětovném načtení Visual Studio Code.
1. Otevřete Jupyter Notebook a spusťte buňku.

> [!IMPORTANT]
> Aby bylo možné navázat spojení, je **nutné** spustit buňku.

V tuto chvíli můžete pokračovat v práci s buňkami v Jupyter Notebook.

> [!TIP]
> Můžete také pracovat se soubory skriptu Pythonu (. py), které obsahují Jupyter buňky kódu. Další informace najdete v tématu [interaktivní dokumentace k Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili Visual Studio Code vzdálené, můžete použít výpočetní instanci jako vzdálenou výpočetní prostředky z Visual Studio Code k [interaktivnímu ladění kódu](how-to-debug-visual-studio-code.md).

[Kurz: analýza prvního modelu ml](tutorial-1st-experiment-sdk-train.md) ukazuje, jak používat výpočetní instanci s integrovaným poznámkovým blokem.
