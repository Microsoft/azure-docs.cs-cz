---
title: Konfigurace prostředí pro vývoj pro Azure Machine Learning | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat prostředí pro vývoj, při práci se službou Azure Machine Learning. V tomto dokumentu se dozvíte, jak používat prostředí Conda, vytvoření konfiguračních souborů a konfigurace poznámkové bloky Jupyter, poznámkových bloků Azure, Integrovaná vývojová prostředí, editory kódu a virtuální počítač pro datové vědy.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 11/6/2018
ms.openlocfilehash: b95b62ab2c51fec93870e3f5593d53a599f3dad2
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218946"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurace prostředí pro vývoj pro Azure Machine Learning

V tomto článku se naučíte, jak nakonfigurovat vývojové prostředí pro práci se službou Azure Machine Learning, včetně:

- Jak vytvořit konfigurační soubor, který se přidruží k pracovnímu prostoru Azure Machine Learning service vaše prostředí.
- Jak nakonfigurovat následující vývojových prostředích:
  - Poznámkové bloky Jupyter v počítači
  - Visual Studio Code
  - Editor vlastního kódu
- Jak nastavit [virtuální prostředí conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) a jeho použití pro službu Azure Machine Learning. Doporučujeme používat Continuum Anaconda izolovat vaše pracovní prostředí, aby se zabránilo konfliktům v závislostech mezi balíčky.

## <a name="prerequisites"></a>Požadavky

- Nastavte pracovní prostor služby Azure Machine Learning service. Postupujte podle kroků v [Začínáme se službou Azure Machine Learning](quickstart-get-started.md).
- Instalovat buď [Continuum Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://conda.io/miniconda.html) Správce balíčků.
- Pokud používáte Visual Studio Code, můžete získat [rozšíření Python](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> Můžete testovat příkazy prostředí, které jsou uvedené v tomto článku pomocí prostředí bash (v systému Linux a Mac OS) nebo na příkazovém řádku (ve Windows).

## <a name="create-a-workspace-configuration-file"></a>Vytvořte konfigurační soubor pracovního prostoru

Sady SDK Azure Machine Learning používá konfigurační soubor pracovního prostoru ke komunikaci s pracovním prostorem služby Azure Machine Learning.

- Pokud chcete vytvořit konfigurační soubor, proveďte [rychlý start Azure Machine Learning](quickstart-get-started.md).
  - Tento rychlý start proces vytvoří `config.json` souboru v poznámkových bloků Azure. Tento soubor obsahuje informace o konfiguraci pro váš pracovní prostor.
  - Stáhněte nebo zkopírujte `config.json` do stejného adresáře jako skripty nebo poznámkových bloků, které na něj odkazovat.

- Alternativně můžete vytvořit soubor ručně pomocí následujících kroků:

    1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com). Kopírovat __název pracovního prostoru__, __skupiny prostředků__, a __ID předplatného__. Tyto hodnoty slouží k vytvoření konfiguračního souboru.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

    1. Vytvoření souboru následujícím kódem Python a ujistěte se, že chcete kód spustit ve stejném adresáři jako skripty nebo poznámkových bloků, které odkazují na pracovním prostoru:

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        Kód se zapíše následující `aml_config/config.json` souboru:

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Můžete zkopírovat `aml_config` adresář nebo právě `config.json` souboru do jiného adresáře, který odkazuje na pracovním prostoru.

       > [!NOTE]
       > Jiné skripty a poznámkovými bloky ve stejném adresáři, nebo verzi uvedenou níže načíst pracovní prostor s `ws=Workspace.from_config()`.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure poznámkových bloků a virtuální počítače pro datové vědy

Azure poznámkových bloků a Azure virtuální počítače pro datové vědy (datové) jsou nakonfigurovaná tak, aby fungovaly se službou Azure Machine Learning. Tato prostředí patří požadované součásti, jako je Azure Machine Learning SDK.

### <a name="azure-notebooks"></a>Azure Notebooks

- Poznámkových bloků Azure je služba Poznámkový blok Jupyter v cloudu Azure.
- Stále budete potřebovat konfigurační soubor pracovního prostoru do těchto prostředí používat.

Příklad použití poznámkových bloků Azure se službou Azure Machine Learning najdete v tématu [Začínáme se službou Azure Machine Learning](quickstart-get-started.md).

### <a name="data-science-virtual-machines"></a>Virtuální počítače pro vědecké zpracování dat

- Virtuální počítač pro datové vědy je přizpůsobenou image virtuálního počítače (VM) navržené pro datové vědy práce. Zahrnuje:
  - Nástrojů pro oblíbené datové vědy
  - Integrované vývojové prostředí (IDE), jako je PyCharm a nástroje RStudio
  - Balíčků, jako jsou poznámkové bloky Jupyter a Tensorflow

Datové VĚDY se dodává s více prostředími Anaconda už nainstalovaná. Pokud chcete používat Azure Machine Learning Python SDK bez jakékoli instalace balíčku, otevřete okno příkazového řádku a aktivovat toto prostředí.

```shell
conda activate AzureML
```

Jednou v tomto prostředí můžete okamžitě importovat sady SDK Azure Machine Learning v sestavení nástroje podle vašeho výběru, bez instalace balíčku.

```python
import azureml.core
print(azureml.core.VERSION)
```

Další informace o virtuální počítače pro datové vědy, naleznete v tématu [virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="configure-jupyter-notebooks-on-your-computer"></a>Konfigurace poznámkové bloky Jupyter v počítači

1. Otevřete příkazový řádek nebo prostředí.

1. Vytvořte prostředí conda pomocí následujících příkazů:

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Může trvat několik minut pro vytvoření prostředí, pokud Python 3.6 a další součásti není třeba stahovat.

1. Instalace sady SDK Azure Machine Learning pomocí poznámkového bloku funkce a SDK přípravy dat pomocí následujícího příkazu:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   Můžete zobrazit v referenčních dokumentech Python pro třídy a metody v následujících sad SDK:
   + [Azure Machine Learning sady SDK pro Python](https://aka.ms/aml-sdk)
   + [Sada SDK pro přípravu dat Azure Machine Learning](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > Pokud se zobrazí zpráva `PyYAML` nelze odinstalovat, použijte následující příkaz:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Může trvat několik minut, než k instalaci sady SDK.

1. Instalace balíčků pro strojového učení experimentování. Použijte následující příkaz a nahraďte `<new package>` názvem balíčku, kterou chcete nainstalovat:

    ```shell
    conda install <new package>
    ```

1. Instalace serveru s ohledem na conda Poznámkový blok Jupyter a povolení experiment pomůcky (Chcete-li zobrazit informace o běhu). Pomocí následujících příkazů:

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. Otevřete Poznámkový blok Jupyter pomocí následujícího příkazu:

    ```shell
    jupyter notebook
    ```

1. Otevřete nový poznámkový blok, vyberte "myenv" jako vaše jádra a ověřte, že máte nainstalované Azure Machine Learning SDK. Spusťte následující příkaz v buňce Poznámkový blok:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Konfigurace sady Visual Studio Code

1. Otevřete příkazový řádek nebo prostředí.

1. Vytvořte prostředí conda pomocí následujících příkazů:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. Nainstalujte sadu SDK Azure Machine Learning a sady SDK přípravy dat pomocí následujícího příkazu:

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Instalace sady Visual Studio code Tools pro AI rozšíření. Zobrazit [Tools pro AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

1. Instalace balíčků pro strojového učení experimentování. Použijte následující příkaz a nahraďte `<new package>` názvem balíčku, kterou chcete nainstalovat:

    ```shell
    conda install <new package>
    ```

1. Otevřít Visual Studio Code a potom použít **CTRL-SHIFT-P** (ve Windows) nebo **příkazu-SHIFT-P** (v systému Mac OS) zobrazíte **paletu příkazů**. Zadejte _Python: Select Interpreter_ a vyberte prostředí conda, který jste vytvořili.

   > [!NOTE]
   > Visual Studio Code je automaticky znát prostředí conda ve vašem počítači. Další informace najdete v tématu [dokumentaci kódu sady Visual Studio](https://code.visualstudio.com/docs/python/environments#_conda-environments).

1. Ověření konfigurace pomocí Visual Studio Code k vytvoření nového souboru skriptu Pythonu s následujícím kódem a pak ho spusťte:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>Konfigurace vlastního kódu editoru

Pomocí sady SDK Azure Machine Learning můžete použít editor kódu podle vašeho výběru.

1. Vytvořit prostředí conda, jak je popsáno v kroku 2 [konfigurovat Visual Studio Code](#configure-visual-studio-code) výše.
1. Postupujte podle pokynů pro každý editor používat prostředí conda. Například můžete použít [PyCharm pokyny](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).

## <a name="next-steps"></a>Další postup

- [Trénování modelu v Azure Machine Learning s datovou sadou mnist ručně](tutorial-train-models-with-aml.md)
