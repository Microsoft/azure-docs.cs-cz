---
title: Nastavení vývojového prostředí Pythonu
titleSuffix: Azure Machine Learning
description: Naučte se nakonfigurovat vývojové prostředí pro Azure Machine Learning. Používejte prostředí Conda, vytvářejte konfigurační soubory a nakonfigurujte si vlastní cloudový notebook, poznámkové bloky Jupyter, Azure Databricks, IDE, editory kódu a virtuální počítač pro datové vědy.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: a5f46f5af723e1245afbc6bca90d25ae9036d646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472422"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurace vývojového prostředí pro Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak nakonfigurovat vývojové prostředí pro práci s Azure Machine Learning. Azure Machine Learning je platforma agnostik. Jediným pevným požadavkem pro vývojové prostředí je Python 3. Doporučuje se také izolované prostředí jako Anaconda nebo Virtualenv.

V následující tabulce jsou uvedeny všechny vývojové prostředí uvedené v tomto článku, spolu s klady a zápory.

| Prostředí | Výhody | Nevýhody |
| --- | --- | --- |
| [Cloudová výpočetní instance Azure Machine Learning (preview)](#compute-instance) | Nejjednodušší způsob, jak začít. Celá sada SDK je již nainstalovaná ve virtuálním počítači pracovního prostoru a kurzy poznámkového bloku jsou předem klonované a připravené ke spuštění. | Nedostatečná kontrola nad vývojovým prostředím a závislostmi. Další náklady vzniklé pro virtuální počítač s Linuxem (virtuální počítač lze zastavit, když se nepoužívá, aby se zabránilo poplatkům). Viz [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Místní prostředí](#local) | Úplná kontrola vývojového prostředí a závislostí. Spusťte s libovolným nástrojem sestavení, prostředím nebo ide podle vašeho výběru. | Začínáme déle. Nezbytné balíčky sady SDK musí být nainstalovány a prostředí musí být také nainstalováno, pokud je ještě nemáte. |
| [Azure Databricks](#aml-databricks) | Ideální pro spouštění rozsáhlých náročných pracovních postupů strojového učení na škálovatelné platformě Apache Spark. | Přehnané pro experimentální strojové učení nebo experimenty a pracovní postupy v menším měřítku. Další náklady vynaložené na Azure Databricks. Viz [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/databricks/). |
| [Virtuální počítač pro datové vědy (DSVM)](#dsvm) | Podobně jako cloudová výpočetní instance (Python a SDK jsou předinstalované), ale s dalšími předinstalovanými nástroji pro datové vědy a strojové učení. Snadné škálování a kombinování s dalšími vlastními nástroji a pracovními postupy. | Pomalejší prostředí začínáme ve srovnání s cloudovou výpočetní instancí. |

Tento článek také obsahuje další tipy k použití následujících nástrojů:

* [Jupyter Notebooks](#jupyter): Pokud již používáte Jupyter Notebook, SDK má některé doplňky, které byste měli nainstalovat.

* [Visual Studio Code:](#vscode)Pokud používáte Visual Studio Code, [rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) obsahuje rozsáhlou jazykovou podporu pro Python a také funkce, aby práce s Azure Machine Learning mnohem pohodlnější a produktivnější.

## <a name="prerequisites"></a>Požadavky

Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte [si tématu Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md). Pracovní prostor je vše, co potřebujete, abyste mohli začít pracovat s vlastním [cloudovým notebookem](#compute-instance), [DSVM](#dsvm)nebo [Azure Databricks](#aml-databricks).

Chcete-li nainstalovat prostředí sady SDK pro [místní počítač](#local), [server Jupyter Notebook nebo](#jupyter) kód sady Visual [Studio,](#vscode) potřebujete také:

- Správce balíčků [Anaconda](https://www.anaconda.com/download/) nebo [Miniconda.](https://conda.io/miniconda.html)

- Na Linuxu nebo macOS potřebujete bash shell.

    > [!TIP]
    > Pokud jste na Linuxu nebo macOS a používáte jiný shell než bash (například zsh), můžete při spuštění některých příkazů obdržet chyby. Chcete-li tento problém `bash` vyřešit, použijte příkaz ke spuštění nového prostředí bash a spusťte příkazy.

- V systému Windows potřebujete příkazový řádek nebo řádek Anaconda (nainstalovaný společnostmi Anaconda a Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Vlastní cloudová výpočetní instance

Výpočetní instance Azure Machine Learning [(preview)](concept-compute-instance.md) je zabezpečená cloudová pracovní stanice Azure, která poskytuje datovým vědcům notebookový server Jupyter, JupyterLab a plně připravené prostředí ML.

Neexistuje nic k instalaci nebo konfiguraci pro výpočetní instanci.  Vytvořte si ho kdykoli v pracovním prostoru Azure Machine Learning. Zadejte pouze název a zadejte typ virtuálního počítače Azure. Vyzkoušejte to nyní s tímto [kurzem: Nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

Přečtěte si další informace o [výpočetních instancích](concept-compute-instance.md).

Chcete-li zastavit poplatky za výpočetní výkon, [zastavte výpočetní instanci](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Virtuální počítač pro datové vědy

DSVM je přizpůsobená image virtuálního počítače (VM). Je určen pro práci datové vědy, která je předem nakonfigurovaná pomocí:

  - Balíčky jako TensorFlow, PyTorch, Scikit-learn, XGBoost a Azure Machine Learning SDK
  - Populární nástroje pro datovou vědu, jako je Spark Standalone a Drill
  - Nástroje Azure, jako je Azure CLI, AzCopy a Průzkumník úložiště
  - Integrovaná vývojová prostředí (IDE), jako je Visual Studio Code a PyCharm
  - Jupyter Notebook Server

Sada Azure Machine Learning SDK funguje na ubuntu nebo windows verzi DSVM. Ale pokud máte v plánu použít DSVM jako výpočetní cíl, je podporováno pouze Ubuntu.

Použití DSVM jako vývojového prostředí:

1. Vytvořte DSVM v jednom z následujících prostředí:

    * Portál Azure:

        * [Vytvoření prostředí Data Science Virtual Machine s Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Vytvoření prostředí Data Science Virtual Machine s Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * The Azure CLI:

        > [!IMPORTANT]
        > * Když použijete azure cli, musíte se nejprve přihlásit `az login` k předplatnému Azure pomocí příkazu.
        >
        > * Při použití příkazů v tomto kroku je nutné zadat název skupiny prostředků, název virtuálního počítače, uživatelské jméno a heslo.

        * Chcete-li vytvořit virtuální stroj pro datové vědy Ubuntu, použijte následující příkaz:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Chcete-li vytvořit virtuální počítač pro datové vědy systému Windows, použijte následující příkaz:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Sada Azure Machine Learning SDK je už nainstalovaná v dsvm. Chcete-li použít prostředí Conda, které obsahuje sadku SDK, použijte jeden z následujících příkazů:

    * Pro Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Pro systém Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. Chcete-li ověřit, zda máte přístup ke sady SDK a zkontrolovat verzi, použijte následující kód Pythonu:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Pokud chcete nakonfigurovat dsvm tak, aby používal váš pracovní prostor Azure Machine Learning, přečtěte si část [Vytvoření konfiguračního souboru pracovního prostoru.](#workspace)

Další informace naleznete v [tématu Virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Místní počítač

Pokud používáte místní počítač (který může být také vzdálený virtuální počítač), vytvořte prostředí Anaconda a nainstalujte sadu SDK. Tady je příklad:

1. Stáhněte a nainstalujte [Anaconda](https://www.anaconda.com/distribution/#download-section) (verze Pythonu 3.7), pokud ji ještě nemáte.

1. Otevřete výzvu Anaconda a vytvořte prostředí s následujícími příkazy:

    Chcete-li vytvořit prostředí, spusťte následující příkaz.

    ```bash
    conda create -n myenv python=3.6.5
    ```

    Pak aktivujte prostředí.

    ```bash
    conda activate myenv
    ```

    Tento příklad vytvoří prostředí pomocí pythonu 3.6.5, ale lze zvolit všechny konkrétní podverze. Kompatibilita s sadou SDK nemusí být u některých hlavních verzí zaručena (doporučuje se 3.5+) a doporučujeme vyzkoušet jinou verzi/podverzi v prostředí Anaconda, pokud narazíte na chyby. Vytvoření prostředí při stahování součástí a balíčků bude trvat několik minut.

1. Spusťte následující příkazy v novém prostředí, abyste povolili jádra IPython specifická pro dané prostředí. Tím zajistíte očekávané chování importu jádra a balíčku při práci s jupyterovými notebooky v prostředí Anaconda:

    ```bash
    conda install notebook ipykernel
    ```

    Potom spusťte následující příkaz k vytvoření jádra:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. K instalaci balíčků použijte následující příkazy:

    Tento příkaz nainstaluje základní Azure Machine Learning `automl` SDK s poznámkovým blokem a doplňky. Navíc `automl` je velká instalace a může být odebrána z držáků, pokud nemáte v úmyslu spustit automatizované experimenty strojového učení. Další `automl` zahrnuje také Azure Machine Learning Data Prep SDK ve výchozím nastavení jako závislost.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Pokud se zobrazí zpráva, že program PyYAML nelze odinstalovat, použijte místo toho následující příkaz:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Počínaje macOS Catalina, zsh (Z shell) je výchozí přihlašovací prostředí a interaktivní shell. V zsh použijte následující příkaz, který unikne závorky s "\\" (zpětné lomítko):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Instalace sady SDK bude trvat několik minut. Další informace o možnostech instalace naleznete v [průvodci instalací](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Nainstalujte další balíčky pro experimentování se strojového učením.

    Použijte některý z následujících příkazů a nahraďte * \<nový balíček>* balíčkem, který chcete nainstalovat. Instalace balíčků `conda install` prostřednictvím vyžaduje, aby balíček byl součástí aktuálních kanálů (nové kanály mohou být přidány v Anaconda Cloud).

    ```bash
    conda install <new package>
    ```

    Alternativně můžete instalovat balíčky přes `pip`.

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter notebooky

Jupyter Notebooky jsou součástí [projektu Jupyter](https://jupyter.org/). Poskytují interaktivní kódování prostředí, kde můžete vytvářet dokumenty, které kombinují živý kód s narativní text a grafiku. Jupyter Notebooky jsou také skvělý způsob, jak sdílet své výsledky s ostatními, protože můžete uložit výstup oddílů kódu v dokumentu. Poznámkové bloky Jupyter můžete nainstalovat na různých platformách.

Postup v části [Místní počítač](#local) nainstaluje nezbytné součásti pro spouštění poznámkových bloků Jupyter v prostředí Anaconda.

Povolení těchto součástí v prostředí poznámkového bloku Jupyter:

1. Otevřete výzvu Anaconda a aktivujte prostředí.

    ```bash
    conda activate myenv
    ```

1. Klonujte [úložiště GitHub](https://aka.ms/aml-notebooks) pro sadu ukázkových poznámkových bloků.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte jupyterový notebook s následujícím příkazem:

    ```bash
    jupyter notebook
    ```

1. Chcete-li ověřit, zda jupyterový poznámkový blok může používat sdk, vytvořte **nový** poznámkový blok, vyberte **Python 3** jako jádro a v buňce poznámkového bloku spusťte následující příkaz:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pokud narazíte na problémy s `ModuleNotFoundError`importem modulů a obdržíte , ujistěte se, že jádro Jupyter je připojeno ke správné cestě pro vaše prostředí spuštěním následujícího kódu v buňce poznámkového bloku.

    ```python
    import sys
    sys.path
    ```

1. Pokud chcete nakonfigurovat poznámkový blok Jupyter tak, aby používal pracovní prostor Azure Machine Learning, přejděte do části [Vytvořit konfigurační soubor pracovního prostoru.](#workspace)

### <a name="visual-studio-code"></a><a id="vscode"></a>Kód visual studia

Visual Studio Code je velmi populární editor kódu pro různé platformy, který podporuje rozsáhlou sadu programovacích jazyků a nástrojů prostřednictvím rozšíření dostupných na [trhu Visual Studio](https://marketplace.visualstudio.com/vscode). [Rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) nainstaluje rozšíření [Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro kódování ve všech typech prostředí Pythonu (virtuální, Anaconda atd.). Kromě toho poskytuje komfortní funkce pro práci s prostředky Azure Machine Learning a spuštění experimentů Azure Machine Learning vše bez opuštění Visual Studio Code.

Použití kódu sady Visual Studio pro vývoj:

1. Nainstalujte rozšíření Azure Machine Learning pro visual studio kód, najdete v [tématu Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Další informace najdete [v tématu Použití Azure Machine Learning pro Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Naučte se používat visual studio kód pro jakýkoli typ vývoje Pythonu, najdete v [tématu Začínáme s Pythonem ve VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Chcete-li vybrat prostředí SDK Python obsahující sdk, otevřete VS Code a pak vyberte Ctrl+Shift+P (Linux a Windows) nebo Command+Shift+P (Mac).
        - Otevře se __paleta příkazů.__

    - Zadejte __Python: Vyberte interpreta__a pak vyberte vhodné prostředí

1. Chcete-li ověřit, zda můžete použít sadu SDK, vytvořte nový soubor Pythonu (.py), který obsahuje následující kód:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Spusťte tento kód kliknutím na "Spustit buňku" CodeLens nebo jednoduše stiskněte shift-enter.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Poskytuje společné prostředí založené na poznámkových blocích s výpočetním clusterem založeným na procesoru nebo GPU.

Jak Azure Databricks funguje s Azure Machine Learning:
+ Model můžete trénovat pomocí Spark MLlib a nasadit model do ACI/AKS z Azure Databricks.
+ Můžete také použít [funkce automatizovaného strojového učení](concept-automated-ml.md) ve speciální azure ml sdk s Azure Databricks.
+ Azure Databricks můžete použít jako výpočetní cíl z [kanálu Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Nastavení clusteru Databricks

Vytvořte [cluster Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Některá nastavení platí pouze v případě, že nainstalujete sadu SDK pro automatizované strojové učení na Databricks.
**Vytvoření clusteru bude trvat několik minut.**

Použijte tato nastavení:

| Nastavení |Platí pro| Hodnota |
|----|---|---|
| Název clusteru |Vždy| název clusteru |
| Modul runtime Databricks |Vždy|Runtime bez ML 6.0 (scala 2.11, jiskra 2.4.3) |
| Verze Pythonu |Vždy| 3 |
| Pracovníků |Vždy| 2 nebo vyšší |
| Typy virtuálních počítačů pracovních uzlů <br>(určuje maximální počet souběžných iterací) |Automatizované strojové učení<br>Pouze| Upřednostňovaný virtuální počítač optimalizovaný pro paměť |
| Povolit automatické škálování |Automatizované strojové učení<br>Pouze| Zrušte zaškrtnutí políčka  |

Počkejte, až cluster běží, než budete pokračovat dále.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instalace správné sady SDK do knihovny Databricks
Po spuštění clusteru [vytvořte knihovnu a](https://docs.databricks.com/user-guide/libraries.html#create-a-library) připojte k vašemu clusteru příslušný balíček Sady Azure Machine Learning SDK.

1. Klikněte pravým tlačítkem myši na aktuální složku pracovního prostoru, do které chcete knihovnu uložit. Vyberte **Vytvořit** > **knihovnu**.

1. Zvolte **pouze jednu** možnost (není podporována žádná jiná instalace sady SDK).

   |Doplňky balíčků&nbsp;&nbsp;Sady SDK|Zdroj|Název PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Pro Databricks| Nahrát Python vejce nebo PyPI | azureml-sdk[datové cihly]|
   |Pro Databricks -s-<br> automatizované funkce ML| Nahrát Python vejce nebo PyPI | azureml-sdk[automl]|

   > [!Warning]
   > Nelze nainstalovat žádné další doplňky sady SDK. Zvolte pouze jednu z předchozích možností [databricks] nebo [automl].

   * Nevybírejte **automaticky Připojit ke všem clusterům**.
   * Vyberte **Připojit** vedle názvu clusteru.

1. Sledujte chyby, dokud se stav nezmění na **Připojené**, což může trvat několik minut.  Pokud se tento krok nezdaří:

   Zkuste cluster restartovat takto:
   1. V levém podokně vyberte **Clustery**.
   1. V tabulce vyberte název clusteru.
   1. Na kartě **Knihovny** vyberte **Restartovat**.

   Zvažte také:
   + V konfiguraci AutoML při použití Azure Databricks přidejte následující parametry:
       1. ```max_concurrent_iterations```je založen na počtu pracovních uzlů v clusteru.
        2. ```spark_context=sc```je založen na výchozím kontextu jiskry.
   + Nebo pokud máte starou verzi sady SDK, odznačte ji z nainstalovaných libs clusteru a přesuňte se do koše. Nainstalujte novou verzi sady SDK a restartujte cluster. Pokud se po restartování problém, odpojte a znovu připojte cluster.

Pokud byla instalace úspěšná, importovaná knihovna by měla vypadat jako jedna z těchto:

Sada SDK pro datové cihly **_bez_** automatizovaného strojového učení ![Azure Machine Learning SDK pro datové cihly](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Sada SDK pro datové cihly **s** automatickou sadou SDK pro strojové učení ![s nainstalovaným automatizovaným strojovým učením nainstalovaným na Datebricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Zahájení průzkumu

Vyzkoušejte si to:
+ Zatímco mnoho ukázkových poznámkových bloků je k dispozici, **pouze [tyto ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fungují s Azure Databricks.**

+ Importujte tyto ukázky přímo z pracovního prostoru. Viz níže: ![](./media/how-to-configure-environment/azure-db-screenshot.png)
![Vyberte panel Import importu](./media/how-to-configure-environment/azure-db-import.png)

+ Naučte se, jak [vytvořit kanál s Databricks jako trénovací výpočetní .](how-to-create-your-first-pipeline.md)

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Vytvoření konfiguračního souboru pracovního prostoru

Konfigurační soubor pracovního prostoru je soubor JSON, který říká sdk, jak komunikovat s pracovním prostorem Azure Machine Learning. Soubor má název *config.json*a má následující formát:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Tento soubor JSON musí být ve struktuře adresářů, která obsahuje skripty Pythonu nebo poznámkové bloky Jupyter. Může být ve stejném adresáři, podadresáři s názvem *.azureml*nebo v nadřazeném adresáři.

Chcete-li použít tento soubor `ws=Workspace.from_config()`z kódu, použijte . Tento kód načte informace ze souboru a připojí se k pracovnímu prostoru.

Konfigurační soubor můžete vytvořit třemi způsoby:

* **Použijte [ws.write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)**: k napsání souboru *config.json.* Soubor obsahuje informace o konfiguraci pracovního prostoru. *Config.json* můžete stáhnout nebo zkopírovat do jiných vývojových prostředí.

* **Stáhněte si soubor**: Na [webu Azure Portal](https://ms.portal.azure.com)vyberte stáhnout **config.json** z části **Přehled** pracovního prostoru.

     ![portál Azure](./media/how-to-configure-environment/configure.png)

* **Vytvoření souboru programově**: V následujícím fragmentu kódu se připojíte k pracovnímu prostoru poskytnutím ID předplatného, skupiny prostředků a názvu pracovního prostoru. Potom uloží konfiguraci pracovního prostoru do souboru:

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

    Tento kód zapíše konfigurační soubor do souboru *.azureml/config.json.*

## <a name="next-steps"></a>Další kroky

- [Trénování modelu v](tutorial-train-models-with-aml.md) Azure Machine Learning pomocí datové sady MNIST
- Zobrazení referenční sady [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
