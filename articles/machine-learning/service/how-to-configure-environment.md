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
ms.openlocfilehash: 2fd2d35bde95a3e268f46b398f2163f9d40ab1ee
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613949"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurace prostředí pro vývoj pro Azure Machine Learning

V tomto dokumentu zjistěte, jak nakonfigurovat vývojové prostředí pro práci se službou Azure Machine Learning. Služba Azure Machine Learning je pro více platforem. Jediným požadavkem pro vaše vývojové prostředí je __Python 3__, __Conda__ (pro izolované prostředí) a konfigurační soubor, který obsahuje informace o pracovním prostoru Azure Machine Learning.

Tento dokument se zaměřuje na tyto konkrétní prostředí a nástroje:

* [Azure Notebooks](#aznotebooks): poznámkové bloky Jupyter A služby hostované v cloudu Azure. Je __nejsnadněji__ způsob, jak začít, jak sada SDK Azure Machine Learning je již nainstalována.
* [Virtuální počítač pro datové vědy](#dsvm): virtuální počítač v cloudu Azure, který je __navržené pro datové vědy pracovní__. Python 3, Conda, poznámkové bloky Jupyter a sady SDK Azure Machine Learning jsou již nainstalovány. Virtuální počítač obsahuje oblíbených architektur, nástrojů a editory pro vývoj řešení pro ML ML. To je pravděpodobně __nejúplnější__ vývojové prostředí pro ML na platformě Azure.
* [Poznámkové bloky Jupyter](#jupyter): Pokud už používáte poznámkové bloky Jupyter, sady SDK má některé funkce, které byste měli nainstalovat.
* [Visual Studio Code](#vscode): Pokud používáte Visual Studio Code, jsou některá užitečná rozšíření, které můžete nainstalovat.

Pokud už máte prostředí Python 3, nebo chcete jenom základní kroky pro instalaci sady SDK, přečtěte si článek [místního počítače](#local) oddílu.

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Postupujte podle kroků v [Začínáme se službou Azure Machine Learning](quickstart-get-started.md) k jejímu vytvoření.

- Buď [Continuum Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://conda.io/miniconda.html) Správce balíčků.

    > [!IMPORTANT]
    > Anaconda a Miniconda nejsou vyžadovány, při použití poznámkových bloků Azure.

- V systému Linux nebo Mac OS je nutné prostředí bash.

    > [!TIP]
    > Pokud jste v systému Linux nebo Mac OS a využívat prostředí, než je bash (například zsh) při spuštění některých příkazů může vyskytnout chyby. Chcete-li tento problém obejít, použijte `bash` příkaz ke spuštění nového prostředí bash a spusťte příkazy existuje.

- Na Windows musíte příkazový řádek nebo Anaconda řádek (nainstalovaných Anaconda a Miniconda).

## <a id="anotebooks"></a>Poznámkových bloků Azure

[Azure Notebooks](https://notebooks.azure.com) (preview) je interaktivní vývojové prostředí v cloudu Azure. Je __nejsnadněji__ způsob, jak začít s vývojem pro Azure Machine Learning.

* Sada SDK Azure Machine Learning je __už nainstalovaná__.
* Po vytvoření pracovního prostoru služby Azure Machine Learning service na webu Azure Portal, můžete kliknout na tlačítko pro automatickou konfiguraci prostředí Azure poznámkového bloku pro práci s pracovním prostorem.

Abyste mohli začít vyvíjet s poznámkovými bloky Azure, postupujte [Začínáme se službou Azure Machine Learning](quickstart-get-started.md) dokumentu.

## <a id="dsvm"></a>Virtuální počítač pro datové vědy

Je přizpůsobenou image virtuálního počítače (VM) na Data virtuálního počítače VĚDY **navržené pro datové vědy pracovní**. Zahrnuje:

  - Nástrojů pro oblíbené datové vědy
  - Integrované vývojové prostředí (IDE), jako je PyCharm a nástroje RStudio
  - Balíčků, jako jsou poznámkové bloky Jupyter a Tensorflow

Azure Machine Learning SDK funguje na Ubuntu nebo Windows verzi DSVM. Použití DSVM jako vývojové prostředí, postupujte takto:

1. Pokud chcete vytvořit virtuální počítač pro datové vědy, postupujte podle pokynů v některém z následujících dokumentech:

    * [Vytvoření Ubuntu Data Science virtuálního počítače](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
    * [Vytvoření virtuálního počítače Windows datové vědy](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

1. Sada SDK Azure Machine Learning je **už nainstalovaná** na datové VĚDY. Pokud chcete používat prostředí Conda, který obsahuje sadu SDK, použijte jednu z následujících příkazů:

    * Na __Ubuntu__ DSVM, použijte tento příkaz:

        ```shell
        conda activate py36
        ```

    * Na __Windows__ DSVM, použijte tento příkaz:

        ```shell
        conda activate AzureML
        ```

1. Pokud chcete ověřit, že můžete používat sadu SDK a zkontrolujte verzi, použijte následující kód Pythonu:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Konfigurace datové VĚDY pro použití vašeho pracovního prostoru služby Azure Machine Learning, najdete v článku [konfigurovat pracovní prostor](#workspace) oddílu.

Další informace o virtuální počítače pro datové vědy, naleznete v tématu [virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Místní počítač

Při použití místního počítače (který může být také vzdálené virtuálního počítače), pomocí následujících kroků vytvořit prostředí conda a instalace sady SDK:

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

   > [!NOTE]
   > Pokud se zobrazí zpráva `PyYAML` nelze odinstalovat, použijte následující příkaz:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Může trvat několik minut, než k instalaci sady SDK.

1. Instalace balíčků pro strojového učení experimentování. Použijte následující příkaz a nahraďte `<new package>` názvem balíčku, kterou chcete nainstalovat:

    ```shell
    conda install <new package>
    ```

1. Chcete-li ověřit, že sada SDK je nainstalovaná, následující kód Pythonu:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Poznámkové bloky Jupyter

Poznámkové bloky Jupyter jsou součástí [Jupyter projektu](https://jupyter.org/). Poskytuje interaktivní prostředí pro psaní kódu ve kterém vytvoříte dokumenty, které kombinovat živého kódu s vyprávěného textu a grafiky. Poznámkové bloky Jupyter se také skvělý způsob, jak sdílet výsledky s ostatními, se po uložení výstupu oddíly kódu v dokumentu. Poznámkové bloky Jupyter můžete nainstalovat na různých platformách.

Kroky v [místního počítače](#local) části instalaci volitelné součásti pro aplikace Jupyter notebook. Pokud chcete povolit tyto komponenty ve vašem prostředí poznámkového bloku Jupyter, postupujte následovně:

1. Otevřete příkazový řádek nebo prostředí.

1. Instalace serveru s ohledem na conda Poznámkový blok Jupyter a povolení experiment widgetů, použijte následující příkazy:

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

1. Pokud chcete ověřit, že sadu SDK můžete použít Poznámkový blok Jupyter, otevřete nový poznámkový blok a vyberte "myenv" jako vaše jádra. Potom spusťte následující příkaz v buňce Poznámkový blok:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Konfigurace Poznámkový blok Jupyter pro použití vašeho pracovního prostoru služby Azure Machine Learning, najdete v článku [konfigurovat pracovní prostor](#workspace) oddílu.

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code je editor kódu napříč platformami. Spoléhá na místní Python 3 a Conda instalace podpory Pythonu, ale poskytuje dodatečné nástroje pro práci s umělou Inteligencí. Také poskytuje podporu pro výběr prostředí Conda z editoru kódu.

Pokud chcete použít Visual Studio Code pro vývoj, použijte následující kroky:

1. Zjistěte, jak používat Visual Studio Code pro vývoj v jazyce Python, najdete v článku [Začínáme s Pythonem v VSCode](https://code.visualstudio.com/docs/python/python-tutorial) dokumentu.

1. Chcete-li vybrat prostředí Conda, spusťte VS Code a pak použijte __Ctrl-Shift-P__ (Linux a Windows) nebo __příkazu-Shift-P__ (Mac), chcete-li získat __příkaz palety__. Zadejte __Python: Select Interpreter__ a pak vyberte prostředí conda.

1. Pokud chcete ověřit, které můžete použít sady SDK, vytvořte nový soubor Pythonu (.py), který obsahuje následující kód. Potom spusťte soubor:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pokud chcete nainstalovat Visual Studio code Tools pro AI rozšíření, naleznete v tématu [Tools pro AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) stránky.

    Další informace najdete v tématu [použití VS Code Tools pro AI pomocí služby Azure Machine Learning](how-to-vscode-tools.md) dokumentu.

## <a id="workspace"></a>Vytvořte konfigurační soubor pracovního prostoru

Konfigurační soubor pracovního prostoru je dokument JSON, která říká sady SDK ke komunikaci s pracovním prostorem služby Azure Machine Learning. Soubor `config.json` a má následující formát:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Tento soubor musí být do struktury adresářů, který obsahuje skripty Python nebo poznámkových bloků Jupyter. Může jí být buď ve stejném adresáři, podadresáři s názvem `aml_config`, nebo v nadřazeném adresáři.

Chcete-li použít tento soubor z vašeho kódu, použijte `ws=Workspace.from_config()`. Tento kód načte informace ze souboru a připojí k vašemu pracovnímu prostoru.

Existují tři způsoby, jak vytvořit konfigurační soubor:

* Pokud budete postupovat podle [rychlý start Azure Machine Learning](quickstart-get-started.md), `config.json` vytvoří soubor v knihovně poznámkových bloků Azure. Tento soubor obsahuje informace o konfiguraci pro váš pracovní prostor. Stáhněte nebo zkopírujte tento `config.json` do jiných vývojových prostředích.

* Je možné **soubor ručně vytvořit** pomocí textového editoru. Hodnoty, přejděte do konfiguračního souboru návštěvou pracovního prostoru v můžete najít [webu Azure portal](https://portal.azure.com). Kopírovat __název pracovního prostoru__, __skupiny prostředků__, a __ID předplatného__ hodnoty a jejich použití v konfiguračním souboru.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Je možné **prostřednictvím kódu programu vytvořit soubor**. Následující fragment kódu ukazuje, jak se připojit k pracovnímu prostoru zadáním ID předplatného, skupiny prostředků a název pracovního prostoru. Pak uloží konfigurace pracovního prostoru do souboru:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Tento kód zapíše konfigurační soubor tak, `aml_config/config.json` souboru.

## <a name="next-steps"></a>Další postup

- [Trénování modelu v Azure Machine Learning s datovou sadou mnist ručně](tutorial-train-models-with-aml.md)
- [Azure Machine Learning sady SDK pro Python](https://aka.ms/aml-sdk)
- [Sada SDK pro přípravu dat Azure Machine Learning](https://aka.ms/data-prep-sdk)