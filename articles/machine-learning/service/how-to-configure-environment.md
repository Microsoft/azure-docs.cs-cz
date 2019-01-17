---
title: Nastavení vývojového prostředí Pythonu
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak nakonfigurovat prostředí pro vývoj, při práci se službou Azure Machine Learning. V tomto článku se dozvíte, jak používat prostředí Conda, vytvořit konfigurační soubory a konfigurace poznámkové bloky Jupyter, poznámkových bloků Azure, Azure Databricks, Integrovaná vývojová prostředí, editory kódu a virtuální počítač pro datové vědy.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 01/14/2018
ms.custom: seodec18
ms.openlocfilehash: 4ef62157644e55ed291562f581389228b5776f51
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353222"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurace prostředí pro vývoj pro Azure Machine Learning

V tomto článku se dozvíte, jak nakonfigurovat vývojové prostředí pro práci se službou Azure Machine Learning. Služba Machine Learning je pro více platforem. 

Jediným požadavkem pro vaše vývojové prostředí je Python 3, Conda (pro izolované prostředí) a konfigurační soubor, který obsahuje informace o pracovním prostoru Azure Machine Learning.

Tento článek se zaměřuje na tyto prostředí a nástroje:

* [Azure Notebooks](#aznotebooks): Poznámkové bloky Jupyter služba, která je hostovaná v cloudu Azure. Je nejjednodušší způsob, jak začít, protože sada SDK Azure Machine Learning je už nainstalovaný.

* [Virtuální počítač pro datové vědy (DSVM)](#dsvm): Předem nakonfigurované vývoje služby experimentování ve službě prostředí nebo v cloudu Azure, která je navržená pro datové vědy práce a mohou být nasazeny na procesoru pouze instance virtuálních počítačů nebo instancí založený na grafickém procesoru. Python 3, Conda, poznámkové bloky Jupyter a sady SDK Azure Machine Learning jsou již nainstalovány. Virtuální počítač se dodává s oblíbenými strojové učení a obsáhlý learning architektur, nástrojů a editory pro vývoj řešení strojového učení. To je pravděpodobně nejkompletnější vývojové prostředí pro strojové učení na platformě Azure.

* [Poznámkový blok Jupyter](#jupyter): Pokud už používáte Poznámkový blok Jupyter, sady SDK má některé funkce, které byste měli nainstalovat.

* [Visual Studio Code](#vscode): Pokud používáte Visual Studio Code, má některá užitečná rozšíření, které můžete nainstalovat.

* [Azure Databricks](#aml-databricks): Oblíbené platformě pro analýzu dat, která je založená na Apache Sparku. Zjistěte, jak získat Azure Machine Learning SDK do vašeho clusteru tak, že můžete nasadit modely.

Pokud už máte prostředí Python 3, nebo chcete jenom základní kroky pro instalaci sady SDK, přečtěte si článek [místního počítače](#local) oddílu.

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, najdete v článku [Začínáme se službou Azure Machine Learning](quickstart-get-started.md).

- Buď [Continuum Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://conda.io/miniconda.html) Správce balíčků.

    > [!IMPORTANT]
    > Anaconda a Miniconda nejsou vyžadovány, při použití poznámkových bloků Azure.

- V systému Linux nebo macOS budete potřebovat prostředí bash.

    > [!TIP]
    > Pokud jste v systému Linux nebo macOS a využívat prostředí, než je bash (například zsh) může dojít k chybám při spuštění některé příkazy. Chcete-li tento problém obejít, použijte `bash` příkaz ke spuštění nového prostředí bash a spusťte příkazy existuje.

- Na Windows musíte příkazový řádek nebo Anaconda řádek (nainstalovaných Anaconda a Miniconda).

## <a id="anotebooks"></a>Poznámkových bloků Azure

[Azure Notebooks](https://notebooks.azure.com) (preview) je interaktivní vývojové prostředí v cloudu Azure. Je nejjednodušší způsob, jak začít s vývojem pro Azure Machine Learning.

* Sada SDK Azure Machine Learning je již nainstalována.
* Po vytvoříte pracovní prostor služby Azure Machine Learning na webu Azure Portal, můžete kliknout na tlačítko pro automatickou konfiguraci prostředí Azure poznámkového bloku pro práci s pracovním prostorem.

Vývoj s využitím poznámkových bloků Azure, najdete v článku [Začínáme se službou Azure Machine Learning](quickstart-get-started.md).

## <a id="dsvm"></a>Virtuální počítač pro datové vědy

Datové VĚDY je přizpůsobenou image virtuálního počítače (VM). Je navržen pro práci vědy data, která je předem nakonfigurovaným programem:

  - Balíčky, jako je TensorFlow, PyTorch, Scikit poučení, XGBoost a Azure Machine Learning SDK.
  - Vědy oblíbenými datovými nástrojů, jako je Spark samostatné a zotavení po havárii.
  - Nástroje Azure, jako je Azure CLI, AzCopy a Průzkumníka služby Storage.
  - Integrované vývojové prostředí (IDE) jako je Visual Studio Code, PyCharm a Rstudia.
  - Aplikace Jupyter Notebook Server. 

Azure Machine Learning SDK funguje na Ubuntu nebo Windows verzi datové VĚDY. Pokud chcete používat datové VĚDY jako vývojové prostředí, postupujte takto:

1. Vytvoření DSVM v některém z následujících prostředích:

    * Na webu Azure portal:

        * [Vytvoření virtuálního počítače se systémem Ubuntu datové vědy](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Vytvoření virtuálního počítače Windows datové vědy](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * Pokud používáte rozhraní příkazového řádku Azure, musíte nejprve se přihlásit ke svému předplatnému Azure pomocí `az login` příkazu.
        >
        > * Použijete-li příkazy v tomto kroku, je nutné zadat název skupiny prostředků, název virtuálního počítače, uživatelského jména a hesla.

        * Chcete-li vytvořit virtuální počítač Ubuntu datové vědy, použijte následující příkaz:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Chcete-li vytvořit virtuální počítač Windows datové vědy, použijte následující příkaz:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Sada SDK Azure Machine Learning je již nainstalována na datové VĚDY. Pokud chcete používat prostředí Conda, který obsahuje sadu SDK, použijte jednu z následujících příkazů:

    * Pro Ubuntu DSVM:

        ```shell
        conda activate py36
        ```

    * Pro Windows DSVM:

        ```shell
        conda activate AzureML
        ```

1. Pokud chcete ověřit, že můžete používat sadu SDK a zkontrolujte verzi, použijte následující kód Pythonu:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Konfigurace datové VĚDY pro použití vašeho pracovního prostoru služby Azure Machine Learning, najdete v článku [vytvořit konfigurační soubor pracovního prostoru](#workspace) oddílu.

Další informace najdete v tématu [virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Místní počítač

Při použití místního počítače (který může být také vzdálené virtuálního počítače), vytvořit prostředí Conda a nainstalujte ji následujícím způsobem:

1. Otevřete příkazový řádek nebo prostředí.

1. Vytvořte prostředí Conda pomocí následujících příkazů:

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    Může trvat několik minut pro vytvoření prostředí, pokud Python 3.6 a další součásti není třeba stahovat.

1. Instalace sady SDK Azure Machine Learning pomocí poznámkového bloku funkce a SDK přípravy dat pomocí následujícího příkazu:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Pokud se zobrazí zpráva, že PyYAML nelze odinstalovat, použijte následující příkaz:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Může trvat několik minut, než k instalaci sady SDK.

1. Instalace balíčků pro strojového učení experimentování. Použijte následující příkaz a nahraďte  *\<nový balíček >* názvem balíčku, kterou chcete nainstalovat:

    ```shell
    conda install <new package>
    ```

1. Pokud chcete ověřit, že sada SDK je nainstalovaná, použijte následující kód Pythonu:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Poznámkové bloky Jupyter

Poznámkové bloky Jupyter jsou součástí [Jupyter projektu](https://jupyter.org/). Poskytuje interaktivní prostředí pro psaní kódu ve kterém vytvoříte dokumenty, které kombinovat živého kódu s vyprávěného textu a grafiky. Poznámkové bloky Jupyter jsou také skvělý způsob, jak sdílet s ostatními, vaše výsledky, protože výstupní kód oddíly můžete uložit v dokumentu. Poznámkové bloky Jupyter můžete nainstalovat na různých platformách.

Podle postupu v [místního počítače](#local) části instalaci volitelné součásti pro aplikace Jupyter notebook. Pokud chcete povolit tyto komponenty ve vašem prostředí poznámkového bloku Jupyter, postupujte takto:

1. Otevřete příkazový řádek nebo prostředí.

1. K instalaci serveru s ohledem na Conda poznámkového bloku Jupyter, použijte následující příkaz:

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Otevřete Poznámkový blok Jupyter pomocí následujícího příkazu:

    ```shell
    jupyter notebook
    ```

1. Pokud chcete ověřit, že poznámkový blok Jupyter pomocí sady SDK, otevřete nový poznámkový blok, vyberte **myenv** jako jádra a pak spusťte následující příkaz v buňce Poznámkový blok:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Chcete-li konfigurovat Poznámkový blok Jupyter pro použití vašeho pracovního prostoru služby Azure Machine Learning, přejděte na [vytvořit konfigurační soubor pracovního prostoru](#workspace) části.

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code je editor kódu napříč platformami. Spoléhá na místní Python 3 a Conda instalace podpory Pythonu, ale poskytuje dodatečné nástroje pro práci s umělou Inteligencí. Také poskytuje podporu pro výběr prostředí Conda z editoru kódu.

Chcete-li použít Visual Studio Code pro vývoj, postupujte takto:

1. Zjistěte, jak používat Visual Studio Code pro vývoj v jazyce Python, najdete v článku [Začínáme s Pythonem v VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Pokud chcete vybrat prostředí Conda, otevřete VS Code a vyberte kombinaci kláves Ctrl + Shift + P (Linux a Windows) nebo příkaz + Shift + P (Mac).  
    __Příkaz palety__ otevře. 

1. Zadejte __Pythonu: Vyberte interpret__a pak vyberte prostředí Conda.

1. Pokud chcete ověřit, které můžete použít sady SDK, vytvořit a pak spusťte nový soubor Pythonu (.py), který obsahuje následující kód:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Instalaci rozšíření Azure Machine Learning pro Visual Studio Code najdete v tématu [Tools pro AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Další informace najdete v tématu [pomocí Azure Machine Learning pro Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Vlastní verzi sady Azure Machine Learning SDK pro Azure Databricks můžete použít pro začátku do konce vlastním strojovým učením. Nebo můžete trénování modelu v Databricks a nasadit ho pomocí [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code).

Příprava vašeho clusteru Databricks a ukázkové poznámkové bloky získat:

1. Vytvoření [Databricks pro cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) s následujícím nastavením:

    | Nastavení | Hodnota |
    |----|---|
    | Název clusteru | yourclustername |
    | Modul runtime Databricks | Any non ML runtime (non ML 4.x, 5.x) |
    | Verze Pythonu | 3 |
    | Pracovní procesy | 2 nebo vyšší |

    Použijte tato nastavení jenom v případě, že budete používat automatizované strojového učení v Databricks:
    
    |   Nastavení | Hodnota |
    |----|---|
    | Typy virtuálních počítačů uzlů pracovního procesu | Optimalizované pro paměť virtuálního počítače upřednostňované |
    | Povolení automatického škálování | Zrušte zaškrtnutí políčka |
    
    Počet pracovních uzlů v clusteru Databricks určuje maximální počet souběžných iterací v nastavení automatického ML.  

    Bude trvat několik minut pro vytvoření clusteru. Počkejte, než je cluster spuštěn, než budete pokračovat.

1. Nainstalujte a připojte balíček sady SDK Azure Machine Learning k vašemu clusteru.  

    * [Vytvoření knihovny](https://docs.databricks.com/user-guide/libraries.html#create-a-library) s jedním z těchto nastavení (_pouze jednu z těchto možností zvolte_):
    
        * Chcete-li nainstalovat sadu SDK Azure Machine Learning _bez_ automatizované funkce machine learning:
            | Nastavení | Hodnota |
            |----|---|
            |Zdroj | Nahrát Python Egg nebo PyPI
            |Název PyPi | azureml-sdk[databricks]
    
        * Chcete-li nainstalovat sadu SDK Azure Machine Learning _s_ automatizované strojového učení:
            | Nastavení | Hodnota |
            |----|---|
            |Zdroj | Nahrát Python Egg nebo PyPI
            |Název PyPi | azureml-sdk[automl_databricks]
    
    * Nesmí být zvolen **automaticky připojí k všechny clustery**

    * Vyberte **připojit** vedle vašeho názvu clusteru

    * Ujistěte se, dokud se stav změní na nejsou žádné chyby **připojené**. To může trvat několik minut.

    Pokud máte starší verzi sady SDK, zrušte zaškrtnutí možnosti z nainstalovaných knihoven clusteru a přesunout do koše. Nainstalujte novou verzi sady SDK a restartujte cluster. Pokud po této dochází k nějakému problému, odpojit a znovu ho připojte svůj cluster.

    Jakmile budete hotovi, knihovny je připojený, jak je znázorněno na následujících obrázcích. Mějte na paměti tyto [běžných potíží s Databricks](resource-known-issues.md#databricks).

    * Pokud jste nainstalovali sadu SDK Azure Machine Learning _bez_ automatizované strojového učení ![SDK bez automatizované strojového učení, které jsou nainstalované v Databricks ](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

    * Pokud jste nainstalovali sadu SDK Azure Machine Learning _s_ automatizované strojového učení ![SDK s automatizované strojového učení, které jsou nainstalované v Databricks ](./media/how-to-configure-environment/automlonadb.jpg)

   Pokud tento krok nezdaří, restartujte svůj cluster následujícím způsobem:

   a. V levém podokně vyberte **clustery**. 
   
   b. V tabulce vyberte název vašeho clusteru. 

   c. Na **knihovny** kartu, vyberte možnost **restartovat**.

1. Stáhněte si [soubor archivu poznámkových bloků Azure Databricks a Azure Machine Learning SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc).

   >[!Warning]
   > Mnoho ukázkové poznámkové bloky jsou k dispozici pro použití se službou Azure Machine Learning. Pouze [tyto ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fungují s Azure Databricks.

1.  [Importovat soubor archivu](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) do vaší Databricks clusteru a začít prozkoumávat, jak je popsáno na [poznámkových bloků Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) stránky.


## <a id="workspace"></a>Vytvořte konfigurační soubor pracovního prostoru

Pracovní prostor konfigurační soubor je soubor JSON s oznámením sady SDK ke komunikaci s pracovním prostorem služby Azure Machine Learning. Soubor *config.json*, a má následující formát:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Tento soubor JSON musí být do struktury adresářů, který obsahuje skripty Python nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, podadresáři s názvem *aml_config*, nebo v nadřazeném adresáři.

Chcete-li použít tento soubor z vašeho kódu, použijte `ws=Workspace.from_config()`. Tento kód načte informace ze souboru a připojí k vašemu pracovnímu prostoru.

Konfigurační soubor můžete vytvořit třemi způsoby:

* **Postupujte podle [rychlý start Azure Machine Learning](quickstart-get-started.md)**: A *config.json* vytvoří soubor v knihovně poznámkových bloků Azure. Soubor obsahuje informace o konfiguraci pro váš pracovní prostor. Stáhněte nebo zkopírujte *config.json* do jiných vývojových prostředích.

* **Vytvoření souboru ručně**: Pomocí této metody pomocí textového editoru. Můžete najít hodnoty, které pohledu vývojáře návštěvou pracovního prostoru v konfiguračním souboru [webu Azure portal](https://portal.azure.com). Zkopírujte název pracovního prostoru, skupinu prostředků a hodnoty ID předplatného a jejich použití v konfiguračním souboru.

     ![portál Azure](./media/how-to-configure-environment/configure.png)

* **Prostřednictvím kódu programu vytvořit soubor**: V následujícím fragmentu kódu připojíte se k pracovnímu prostoru zadáním ID předplatného, skupiny prostředků a název pracovního prostoru. Konfigurace pracovního prostoru pak uloží do souboru:

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

    Tento kód zapíše konfigurační soubor tak, *aml_config/config.json* souboru.

## <a name="next-steps"></a>Další postup

- [Trénování modelu](tutorial-train-models-with-aml.md) v Azure Machine Learning s datovou sadou mnist ručně]
- Zobrazení [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) odkaz
- Další informace o [sady SDK pro přípravu dat Azure Machine Learning](https://aka.ms/data-prep-sdk)
