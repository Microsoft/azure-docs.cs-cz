---
title: Přístup k terminálu výpočetních instancí v pracovním prostoru
titleSuffix: Azure Machine Learning
description: Použijte terminál na výpočetní instanci pro operace Git, nainstalujte balíčky a přidejte jádra.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100101331"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Přístup k terminálu výpočetních instancí v pracovním prostoru

Přístup k terminálu výpočetní instance v pracovním prostoru:

* Použijte soubory ze souborů Git a verze. Tyto soubory jsou uložené v systému souborů pracovního prostoru a nejsou omezené na jednu instanci výpočetní instance.
* Nainstalujte balíčky na výpočetní instanci.
* Vytvořte další jádra na výpočetní instanci.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://aka.ms/AMLFree).
* Machine Learning pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Přístup k terminálu

Přístup k terminálu:

1. Otevřete pracovní prostor v [Azure Machine Learning Studiu](https://ml.azure.com).
1. Na levé straně vyberte **poznámkové bloky**.
1. Vyberte **otevřenou** bitovou kopii terminálu.

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Otevřít okno terminálu":::

1. Když je spuštěná výpočetní instance, zobrazí se okno terminálu pro tuto výpočetní instanci.
1. Pokud není spuštěná žádná výpočetní instance, spusťte nebo vytvořte výpočetní instanci pomocí části **COMPUTE** na pravé straně.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Spuštění nebo vytvoření výpočetní instance":::

Kromě výše uvedených kroků můžete k terminálu přistupovat taky z:

* RStudio: v levém horním rohu vyberte kartu **terminálu** .
* Jupyter Lab: v **druhém** záhlaví karty spouštěče vyberte dlaždici **terminálu** .
* Jupyter: v pravém horním rohu na kartě soubory vyberte **nový>terminálu** .
* SSH k počítači, pokud jste povolili přístup přes SSH při vytváření výpočetní instance.

## <a name="copy-and-paste-in-the-terminal"></a>Kopírování a vkládání v terminálu

> * Windows: `Ctrl-Insert` kopírování a používání `Ctrl-Shift-v` nebo `Shift-Insert` vložení.
> * Mac OS: `Cmd-c` pro kopírování a `Cmd-v` vložení.
> * Aplikace FireFox/IE nemusí správně podporovat oprávnění schránky.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Použít soubory ze souborů Git a verze

Přístup ke všem operacím Gitu z terminálu. Všechny soubory a složky Git budou uložené v systému souborů pracovního prostoru. Toto úložiště umožňuje používat tyto soubory z jakékoli výpočetní instance v pracovním prostoru.

> [!NOTE]
> Přidejte své soubory a složky kamkoli do složky **~/CloudFiles/Code/Users** , aby se zobrazily ve všech prostředích Jupyter.

Přečtěte si další informace o [klonování úložišť Git do systému souborů pracovního prostoru](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Nainstalovat balíčky

 Nainstaluje balíčky z okna terminálu. Nainstalujte balíčky Pythonu do prostředí **Python 3,6-AzureML** .  Nainstalujte balíčky R do prostředí jazyka **r** .

Balíčky můžete také nainstalovat přímo do Jupyter Notebook nebo RStudio:

* RStudio použijte kartu **balíčky** v pravém dolním rohu nebo kartu **Konzola** v levém horním rohu.  
* Python: přidejte instalační kód a proveďte v Jupyter Notebook buňce.

> [!NOTE]
> Pro správu balíčků v rámci poznámkového bloku použijte funkce **% PIP** nebo **% conda** Magic k automatické instalaci balíčků do **aktuálně běžícího jádra**, ne z **! PIP** nebo **! conda** , která odkazuje na všechny balíčky (včetně balíčků mimo aktuálně běžící jádro).

## <a name="add-new-kernels"></a>Přidat nové jádra

> [!WARNING]
>  Při přizpůsobování výpočetní instance se ujistěte, že neodstraňujete prostředí **azureml_py36** conda nebo **Python 3,6-AzureML** . To je potřeba pro funkce Jupyter/JupyterLab.

Přidání nového jádra Jupyter do výpočetní instance:

1. Pomocí okna terminálu vytvořte nové prostředí.  Například kód uvedený níže vytvoří `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Aktivujte prostředí.  Například po vytvoření `newenv` :

    ```shell
    conda activate newenv
    ```

1. Instalace balíčku PIP a ipykernel do nového prostředí a vytvoření jádra pro tento conda ENV

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Můžete nainstalovat kterýkoli z [dostupných jader Jupyter](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) .

## <a name="manage-terminal-sessions"></a>Správa relací terminálu

 Výběrem **Zobrazit aktivní relace** na panelu nástrojů terminálu zobrazíte seznam všech aktivních relací terminálu. Pokud neexistují žádné aktivní relace, tato karta se zakáže.

Pokud chcete zachovat prostředky výpočetní instance, zavřete všechny nevyužité relace.