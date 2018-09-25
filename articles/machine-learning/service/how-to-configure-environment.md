---
title: Konfigurace prostředí pro vývoj pro Azure Machine Learning | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat prostředí pro vývoj, při práci se službou Azure Machine Learning. V tomto dokumentu se dozvíte, jak používat prostředí Conda, vytvoření konfiguračních souborů a konfigurace poznámkové bloky Jupyter, poznámkových bloků Azure, Integrovaná vývojová prostředí, editory kódu a virtuální počítač pro datové vědy.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 7796accffb7041e567c5e18857d09e105b5268ce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961565"
---
# <a name="how-to-configure-a-development-environment-for-the-azure-machine-learning-service"></a>Jak nakonfigurovat prostředí pro vývoj pro službu Azure Machine Learning

Zjistěte, jak konfigurovat vaše vývojové prostředí pro práci se službou Azure Machine Learning. Se dozvíte, jak vytvořit konfigurační soubor, který se přidruží k pracovnímu prostoru Azure Machine Learning vašeho prostředí. Budete se také dozvíte, jak nakonfigurovat následující vývojových prostředích:

* Poznámkové bloky Jupyter ve vašem počítači
* Visual Studio Code
* Editor kódu podle vašeho výběru

Doporučuje se použít Continuum Anaconda [virtuální prostředí conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) izolovat vaše pracovní prostředí, aby se zabránilo konfliktům v závislostech mezi balíčky. Tento článek popisuje kroky pro nastavení prostředí conda a jeho použití pro službu Azure Machine Learning.


## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Pokud chcete jeden vytvořit, postupujte podle kroků v [Začínáme se službou Azure Machine Learning](quickstart-get-started.md) dokumentu.

* [Continuum Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://conda.io/miniconda.html) Správce balíčků.

 * Pro prostředí Visual Studio Code [rozšíření Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="create-workspace-configuration-file"></a>Vytvořte konfigurační soubor pracovního prostoru

Konfigurační soubor pracovního prostoru se sada SDK používá ke komunikaci s pracovním prostorem služby Azure Machine Learning.  Chcete-li získat tento soubor dvěma způsoby:

* Po dokončení [rychlý Start](quickstart-get-started.md), soubor `config.json` se vytvoří v Azure poznámkových bloků.  Tento soubor obsahuje informace o konfiguraci pro váš pracovní prostor.  Stáhněte si ji do stejného adresáře jako skripty nebo poznámkových bloků, které na něj odkazovat.

* Vytvořte konfigurační soubor sami pomocí následujících kroků:

    1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com). Kopírovat __název pracovního prostoru__, __skupiny prostředků__, a __ID předplatného__. Tyto hodnoty slouží k vytvoření konfiguračního souboru.

       Řídicí panel pracovního prostoru na portálu se podporuje jenom prohlížeče Edge, Chrome a Firefox.
    
        ![portál Azure](./media/how-to-configure-environment/configure.png) 
    
    3. V textovém editoru vytvořte soubor s názvem **config.json**.  Přidejte následující obsah do tohoto souboru, vkládání hodnoty z portálu:
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
    
        >[!NOTE] 
        >Později v kódu přečtěte si tento soubor:  `ws = Workspace.from_config()`
    
    4. Nezapomeňte uložit **config.json** do stejného adresáře jako skripty nebo poznámkových bloků, které na něj odkazovat.
    
## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Azure poznámkových bloků a virtuální počítač pro datové vědy

Azure poznámkových bloků a Azure Data virtuálních počítačů VĚDY jsou nakonfigurovaná tak, aby fungovaly se službou Azure Machine Learning. Požadované součásti, jako jsou sady SDK Azure Machine Learning jsou předem nainstalované v těchto prostředích.

Poznámkových bloků Azure je služba Poznámkový blok Jupyter v cloudu Azure. Virtuální počítač pro datové vědy je image virtuálního počítače, který je předkonfigurován pro datové vědy práce. Virtuální počítač obsahuje oblíbené nástroje, Integrovaná vývojová prostředí a balíčků, jako jsou poznámkové bloky Jupyter, PyCharm a Tensorflow.

Stále musíte použít těchto prostředí konfigurační soubor pracovního prostoru.

Další informace o virtuální počítače pro datové vědy, naleznete v tématu [virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) dokumentaci.

Příklad použití poznámkových bloků Azure ve službě Azure Machine Learning, najdete v článku [Začínáme se službou Azure Machine Learning](quickstart-get-started.md) dokumentu.

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>Konfigurace poznámkové bloky Jupyter ve vašem počítači

1. Otevřete příkazový řádek nebo prostředí.

2. Pokud chcete vytvořit prostředí conda, použijte následující příkazy:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    Může trvat několik minut pro vytvoření prostředí, jak Python 3.6 a další součásti možná muset stáhnout.

3. K instalaci sady SDK Azure Machine Learning pomocí poznámkového bloku funkce můžete použijte následující příkaz:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl,contrib]
    ```

    Může trvat několik minut, než k instalaci sady SDK.

4. K instalaci balíčků pro strojového učení experimentování, použijte následující příkaz a nahraďte `<new package>` názvem balíčku, kterou chcete nainstalovat:

    ```shell
    conda install <new package>
    ```

5. Instalace serveru s ohledem na conda Poznámkový blok Jupyter a povolení experiment pomůcky (Chcete-li zobrazit informace o běhu), použijte následující příkazy:

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Spusťte Poznámkový blok Jupyter, použijte následující příkaz:

    ```shell
    jupyter notebook
    ```

7. Otevřete nový poznámkový blok a vyberte "myenv" jako vaše jádra. Ověřte, že máte Machine Learning nainstalované sady SDK Azure následujícím příkazem spuštěné v buňce Poznámkový blok:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Konfigurace sady Visual Studio Code

1. Otevřete příkazový řádek nebo prostředí.

2. Pokud chcete vytvořit prostředí conda, použijte následující příkazy:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Pokud chcete nainstalovat sadu SDK Azure Machine Learning, použijte následující příkaz:
 
    ```shell
    pip install --upgrade azureml-sdk[automl,contrib]
    ```

4. Instalace sady Visual Studio code Tools pro AI, naleznete v příspěvku sady Visual Studio marketplace pro [Tools pro AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai). 

5. K instalaci balíčků pro strojového učení experimentování, použijte následující příkaz a nahraďte `<new package>` názvem balíčku, kterou chcete nainstalovat:

    ```shell
    conda install <new package>
    ```

6. Spusťte Visual Studio Code a pak použít __CTRL-SHIFT-P__ zobrazíte __paletu příkazů__. Zadejte *Python: Select Interpreter*a vyberte prostředí conda, který jste vytvořili.

    > [!NOTE]
    > Visual Studio Code je automaticky znát prostředí conda ve vašem počítači. Další informace najdete v tématu [dokumentaci kódu sady Visual Studio](https://code.visualstudio.com/docs/python/environments#_conda-environments).

7. Ověření konfigurace, použijte Visual Studio Code k vytvoření nového souboru skriptu Pythonu s následujícím kódem a pak ho spusťte:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>Konfigurace editoru kódu podle vašeho výběru

Použití editoru vlastního kódu s využitím Azure Machine Learning SDK, nejprve vytvořte prostředí conda jak je popsáno výše. Postupujte podle pokynů pro každý editor používat prostředí conda. Například se nachází na pokyny pro PyCharm [ https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html ](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).
 
## <a name="next-steps"></a>Další postup

* [Trénování modelu v Azure Machine Learning s datovou sadou mnist ručně](tutorial-train-models-with-aml.md)
