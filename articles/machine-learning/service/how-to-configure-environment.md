---
title: Nastavení vývojového prostředí v Pythonu
titleSuffix: Azure Machine Learning service
description: Naučte se konfigurovat vývojové prostředí při práci se službou Azure Machine Learning. V tomto článku se dozvíte, jak používat prostředí Conda, vytvářet konfigurační soubory a konfigurovat vlastní cloudový notebook Jupyter, poznámkové bloky, Azure Databricks, Azure Notebooks, IDEs, editory kódu a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 220f68461d47293e9f43a650e4fa5d1d59bce02f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128345"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurace prostředí pro vývoj pro Azure Machine Learning

V tomto článku se dozvíte, jak nakonfigurovat vývojové prostředí pro práci s Azure Machine Learning služby. Služba Azure Machine Learning je nezávislá platformy. Jediným z požadavků pro vaše vývojové prostředí je Python 3. Doporučuje se také izolované prostředí, jako je Anaconda nebo virtualenv.

Následující tabulka uvádí každé vývojové prostředí popsané v tomto článku spolu s odborníky a nevýhody.

| Prostředí | V oblasti IT | Nevýhody |
| --- | --- | --- |
| [Cloudový virtuální počítač poznámkového bloku](#notebookvm) | Nejjednodušší způsob, jak začít. Celá sada SDK je už ve vašem VIRTUÁLNÍm počítači pracovního prostoru nainstalovaná a kurzy poznámkových bloků jsou předem naklonované a připravené ke spuštění. | Nedostatečná kontrola nad vývojovým prostředím a závislostmi. Dodatečné náklady vzniklé pro virtuální počítač Linux (virtuální počítač se dá zastavit, pokud se nepoužívá), aby se předešlo poplatkům. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Místní prostředí](#local) | Úplná kontrola nad vývojovým prostředím a závislostmi. Spusťte s libovolným nástrojem sestavení, prostředím nebo IDE dle vašeho výběru. | Začne trvat déle. Musí být nainstalované potřebné balíčky sady SDK a prostředí, pokud ho ještě nemáte, musí být nainstalované i v případě, že ho ještě nemáte. |
| [Azure Databricks](#aml-databricks) | Ideální pro spouštění vysoce škálovatelných pracovních postupů strojového učení na škálovatelné Apache Spark platformě. | Přehnaně důkladné se na experimentální strojové učení nebo experimenty s menším rozsahem a pracovní postupy. Dodatečné náklady vzniklé za Azure Databricks. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Podobně jako u cloudového poznámkového bloku (Python a SDK jsou předem nainstalované), ale mají předinstalované i další oblíbené datové vědy a nástroje pro strojové učení. Snadné škálování a kombinování s dalšími vlastními nástroji a pracovními postupy. | V porovnání s cloudovým VIRTUÁLNÍm počítačem poznámkového bloku se pomaleji Začínáme. |
| [Azure Notebooks](#aznotebooks) | Bezplatné a odlehčené prostředí Začínáme s předinstalovaným Pythonem a sadou SDK. | V porovnání s cloudovým virtuálním počítačem poznámkového bloku jsou dostupné méně výkonné virtuální počítače. Izolované od pracovního prostoru a dalších prostředků. |

Tento článek také nabízí další tipy k používání následujících nástrojů:

* [Jupyter poznámkové bloky](#jupyter): Pokud už Jupyter Notebook používáte, sada SDK obsahuje několik dalších doplňků, které byste měli nainstalovat.

* [Visual Studio Code](#vscode): Pokud používáte Visual Studio Code, má několik užitečných rozšíření, která můžete nainstalovat.

## <a name="prerequisites"></a>Požadavky

Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [Vytvoření pracovního prostoru služby Azure Machine Learning](how-to-manage-workspace.md). Pracovní prostor je vše, co potřebujete, abyste mohli začít s [](#notebookvm)vlastním cloudovým notebookem, [DSVM](#dsvm), [Azure Databricks](#aml-databricks)nebo [Azure Notebooks](#aznotebooks).

Pokud chcete nainstalovat prostředí SDK pro svůj [místní počítač](#local), [Jupyter Notebook Server](#jupyter) nebo [Visual Studio Code](#vscode) budete také potřebovat:

- Buď správce balíčků [Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://conda.io/miniconda.html) .

- V systému Linux nebo macOS potřebujete prostředí bash.

    > [!TIP]
    > Pokud pracujete v systému Linux nebo macOS a používáte jiné prostředí než bash (například ZSH), může dojít k chybám při spuštění některých příkazů. Chcete-li tento problém obejít, použijte `bash` příkaz ke spuštění nového prostředí bash a spusťte příkazy existuje.

- Na Windows musíte příkazový řádek nebo Anaconda řádek (nainstalovaných Anaconda a Miniconda).

## <a id="notebookvm"></a>Vlastní virtuální počítač s notebookem na bázi cloudu

Virtuální počítač s poznámkovým blokem (Preview) je zabezpečená cloudová pracovní stanice Azure, která poskytuje odborníky na data pomocí serveru Jupyter notebook, JupyterLab a plně připraveného prostředí ML.

Virtuální počítač poznámkového bloku je:

+ **Zabezpečení**. Vzhledem k tomu, že je přístup k virtuálnímu počítači a poznámkovému bloku zabezpečený pomocí protokolu HTTPS a Azure Active Directory ve výchozím nastavení, můžou IT specialisté snadno vymáhat jednotné přihlašování a další funkce zabezpečení, jako je vícefaktorové ověřování.

+ **Předem nakonfigurovaná**. Toto plně připravené prostředí Python ML vykreslí svůj kmen z oblíbených IaaS Data Science VM a zahrnuje:
  + Azure ML Python SDK (nejnovější)
  + Automatická konfigurace pro práci s vaším pracovním prostorem
  + Server Jupyter notebook
  + IDE JupyterLab Poznámkový blok
  + Předem nakonfigurované ovladače GPU
  + Výběr architektur hloubkového učení


  Pokud jste v kódu, obsahuje tento virtuální počítač kurzy a ukázky, které vám pomůžou prozkoumat a zjistit, jak používat službu Azure Machine Learning. Ukázkové poznámkové bloky se ukládají do účtu Azure Blob Storage ve vašem pracovním prostoru, aby je mohli sdílet napříč virtuálními počítači. Při spuštění mají také přístup k úložištím dat a výpočetním prostředkům vašeho pracovního prostoru.

+ **Jednoduché nastavení**: Vytvořte si ji kdykoli v pracovním prostoru Azure Machine Learning. Zadejte jenom název a zadejte typ virtuálního počítače Azure. Vyzkoušejte si to hned v [tomto kurzu: Nastavte prostředí a pracovní](tutorial-1st-experiment-sdk-setup.md)prostor.

+ **Přizpůsobitelná**. I když máte spravovanou a zabezpečenou nabídku virtuálních počítačů, zachováte plný přístup k hardwarovým funkcím a přizpůsobíte ji na přání srdce. Můžete například rychle vytvořit nejnovější virtuální počítač s technologií NVidia V100 a provést tak podrobné ladění nové architektury sítě neuronové.

Pokud chcete zastavit poplatky za virtuální počítač poznámkového bloku, zastavte [virtuální počítač pro notebook](tutorial-1st-experiment-sdk-setup.md#stop-the-notebook-vm). 

## <a id="dsvm"></a>Virtuální počítač pro datové vědy

DSVM je přizpůsobená image virtuálního počítače (VM). Je navržená pro práci v oblasti datové vědy, která je předem nakonfigurovaná:

  - Balíčky jako TensorFlow, PyTorch, Scikit-učení, XGBoost a sada SDK pro Azure Machine Learning
  - Oblíbené nástroje pro datové vědy, jako je Spark Standalone a procházení
  - Nástroje Azure, jako jsou Azure CLI, AzCopy a Průzkumník služby Storage
  - Integrovaná vývojová prostředí (IDEs), například Visual Studio Code a PyCharm
  - Server poznámkového bloku Jupyter

Sada SDK pro Azure Machine Learning funguje v DSVM ve verzi Ubuntu nebo Windows. Ale pokud plánujete používat DSVM jako cíl výpočtů, podporuje se jenom Ubuntu.

Pokud chcete použít DSVM jako vývojové prostředí, udělejte toto:

1. Vytvořte DSVM v jednom z následujících prostředí:

    * Azure Portal:

        * [Vytvoření Data Science Virtual Machine Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Vytvoření Data Science Virtual Machine Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Rozhraní příkazového řádku Azure:

        > [!IMPORTANT]
        > * Když použijete rozhraní příkazového řádku Azure, musíte se nejdřív přihlásit k předplatnému Azure pomocí `az login` příkazu.
        >
        > * Když použijete příkazy v tomto kroku, musíte zadat název skupiny prostředků, název virtuálního počítače, uživatelské jméno a heslo.

        * Pokud chcete vytvořit Data Science Virtual Machine Ubuntu, použijte následující příkaz:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Chcete-li vytvořit Data Science Virtual Machine Windows, použijte následující příkaz:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Sada Azure Machine Learning SDK je už na DSVM nainstalovaná. Pokud chcete používat prostředí Conda, který obsahuje sadu SDK, použijte jednu z následujících příkazů:

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

1. Pokud chcete nakonfigurovat DSVM pro používání pracovního prostoru služby Azure Machine Learning, přečtěte si část [vytvoření konfiguračního souboru pracovního prostoru](#workspace) .

Další informace najdete v tématu [Virtual Machines pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Místní počítač

Pokud používáte místní počítač (který může být také vzdáleným virtuálním počítačem), vytvořte prostředí Anaconda a nainstalujte sadu SDK pomocí následujícího postupu:

1. Stáhněte a nainstalujte [Anaconda](https://www.anaconda.com/distribution/#download-section) (verze Python 3,7), pokud ji ještě nemáte.

1. Otevřete příkazový řádek Anaconda a vytvořte prostředí pomocí následujících příkazů:

    Spuštěním následujícího příkazu vytvořte prostředí.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Pak aktivujte prostředí.

    ```shell
    conda activate myenv
    ```

    Tento příklad vytvoří prostředí pomocí Python 3.6.5, ale lze zvolit jakékoli konkrétní podverze. Kompatibilita sady SDK nemusí být zaručená u určitých hlavních verzí (doporučuje se 3,5 +) a při spuštění chyb se doporučuje vyzkoušet jinou verzi/podverzi v prostředí Anaconda. Vytvoření prostředí během stahování komponent a balíčků bude trvat několik minut.

1. Spuštěním následujících příkazů v novém prostředí povolíte jádra IPython pro konkrétní prostředí. Tím se zajistí očekávané chování při importu jádra a balíčku při práci s poznámkovým blokům Jupyter v prostředích Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Pak spuštěním následujícího příkazu vytvořte jádro:

    ```shell
    ipython kernel install --user
    ```

1. K instalaci balíčků použijte následující příkazy:

    Tento příkaz nainstaluje základní Azure Machine Learning SDK s poznámkovým blokem a automl. `automl` Extra je velká instalace a je možné ji odebrat z závorek, pokud nechcete spouštět automatizované experimenty strojového učení. `automl` Navíc zahrnuje také Azure Machine Learning sadu SDK pro přípravu dat jako závislost.

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Pokud se zobrazí zpráva, že PyYAML nelze odinstalovat, použijte místo toho následující příkaz:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Počínaje macOS Catalina, zsh (prostředí Z) je výchozí prostředí pro přihlášení a interaktivní prostředí. V ZSH použijte následující příkaz, který řídí hranaté závorky\\"" (zpětné lomítko):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   Instalace sady SDK bude trvat několik minut. Další informace o možnostech instalace najdete v příručce pro [instalaci](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Nainstalujte další balíčky pro experimentování ve službě Machine Learning.

    Použijte některý z následujících příkazů a nahraďte  *\<> nového balíčku* balíčkem, který chcete nainstalovat. Instalace balíčků přes `conda install` vyžaduje, aby byl balíček součástí současných kanálů (nové kanály je možné přidat do Anaconda cloudu).

    ```shell
    conda install <new package>
    ```

    Alternativně můžete balíčky instalovat prostřednictvím `pip`nástroje.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Poznámkové bloky Jupyter

Poznámkové bloky Jupyter jsou součástí [Jupyter projektu](https://jupyter.org/). Poskytuje interaktivní prostředí pro psaní kódu ve kterém vytvoříte dokumenty, které kombinovat živého kódu s vyprávěného textu a grafiky. Jupyter poznámkové bloky jsou také skvělým způsobem, jak sdílet výsledky s ostatními, protože výstup částí kódu v dokumentu můžete uložit. Poznámkové bloky Jupyter můžete nainstalovat na různých platformách.

Postup v části [místní počítač](#local) nainstaluje potřebné komponenty pro spouštění notebooků Jupyter v prostředí Anaconda. Pokud chcete povolit tyto součásti v prostředí Jupyter Notebook, udělejte toto:

1. Otevřete příkazový řádek Anaconda a aktivujte své prostředí.

    ```shell
    conda activate myenv
    ```
    
1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks) pro sadu ukázkových poznámkových bloků.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte Jupyter Notebook Server pomocí následujícího příkazu:

    ```shell
    jupyter notebook
    ```

1. Chcete-li ověřit, zda Jupyter Notebook může použít sadu SDK, vytvořte **Nový** Poznámkový blok, jako jádro vyberte **Python 3** a potom v buňce poznámkového bloku spusťte následující příkaz:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pokud narazíte na problémy s importem modulů `ModuleNotFoundError`a přijmete, ujistěte se, že je váš Jupyter jádro připojený ke správné cestě pro vaše prostředí, a to spuštěním následujícího kódu v buňce poznámkového bloku.

    ```python
    import sys
    sys.path
    ```
    
1. Pokud chcete nakonfigurovat Jupyter Notebook, aby používala pracovní prostor služby Azure Machine Learning, přečtěte si část [vytvoření konfiguračního souboru pracovního prostoru](#workspace) .


### <a id="vscode"></a>Visual Studio Code

Visual Studio Code je editor kódu napříč platformami. Spoléhá na místní Python 3 a Conda instalace podpory Pythonu, ale poskytuje dodatečné nástroje pro práci s umělou Inteligencí. Také poskytuje podporu pro výběr prostředí Conda z editoru kódu.

Pokud chcete použít Visual Studio Code pro vývoj, udělejte toto:

1. Informace o tom, jak používat Visual Studio Code pro vývoj v Pythonu, najdete v tématu Začínáme [s Pythonem v VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Pokud chcete vybrat prostředí Conda, otevřete VS Code a pak vyberte CTRL + SHIFT + P (Linux a Windows) nebo Command + Shift + P (Mac).
    __Paleta příkazu__ se otevře.

1. Zadejte __Python: Vyberte Interpret__a pak vyberte prostředí conda.

1. Chcete-li ověřit, zda můžete použít sadu SDK, vytvořte a potom spusťte nový soubor Pythonu (. py), který obsahuje následující kód:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pokud chcete nainstalovat rozšíření Azure Machine Learning pro Visual Studio Code, přečtěte si téma [Nástroje pro AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Další informace najdete v tématu [použití Azure Machine Learning pro Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Poskytuje prostředí pro spolupráci na bázi poznámkového bloku s PROCESORem nebo výpočetním clusterem založeným na GPU.

Jak Azure Databricks spolupracuje se službou Azure Machine Learning:
+ Model můžete vytvořit pomocí Spark MLlib a model nasadit do ACI/AKS v rámci Azure Databricks.
+ Automatické funkce strojového [učení](concept-automated-ml.md) můžete použít také ve speciální sadě SDK pro Azure ML s Azure Databricks.
+ Azure Databricks můžete použít jako cíl pro výpočetní prostředky z [kanálu Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Nastavení clusteru datacihly

Vytvořte [cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)datacihly. Některá nastavení platí jenom v případě, že nainstalujete sadu SDK pro automatizované Machine Learning na datacihly.
**Vytvoření clusteru bude trvat několik minut.**

Použijte tato nastavení:

| Nastavení |Platná pro| Value |
|----|---|---|
| Název clusteru |vždy| yourclustername |
| Modul runtime Databricks |vždy| Libovolný běhový modul bez ML (bez ML 4. x, 5. x) |
| Verze Pythonu |vždy| 3 |
| Pracovní procesy |vždy| 2 nebo vyšší |
| Typy virtuálních počítačů uzlu pracovního procesu <br>(určuje maximální počet souběžných iterací) |Automatizované strojové učení<br>pouze| Preferovaný virtuální počítač pro optimalizaci paměti |
| Povolení automatického škálování |Automatizované strojové učení<br>pouze| Zrušte zaškrtnutí políčka |

Než budete pokračovat, počkejte, dokud cluster neběží.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Nainstalujte správnou sadu SDK do knihovny datacihly.
Po spuštění clusteru [vytvořte knihovnu](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , která připojí příslušný balíček sady Azure Machine Learning SDK k vašemu clusteru.

1. Zvolit **jenom jednu** možnost (žádná jiná instalace sady SDK není podporovaná)

   |Doplňky&nbsp;balíčku&nbsp;sady SDK|Source|Název&nbsp;PyPI&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Pro datacihly| Nahrát vejce Pythonu nebo PyPI | AzureML-SDK [datacihly]|
   |Pro datacihly – with-<br> Automatické možnosti ML| Nahrát vejce Pythonu nebo PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Nelze nainstalovat žádné další doplňky sady SDK. Vyberte jenom jednu z předchozích možností [datacihly] nebo [automl_databricks].

   * Nevybírejte možnost **Připojit automaticky ke všem clusterům**.
   * Vedle názvu clusteru vyberte **připojit** .

1. Monitoruje chyby, dokud se změnystavu nepřipojí, což může trvat několik minut.  Pokud tento krok neproběhne úspěšně, ověřte následující:

   Zkuste cluster restartovat pomocí:
   1. V levém podokně vyberte clustery.
   1. V tabulce vyberte název vašeho clusteru.
   1. Na kartě **knihovny** vyberte **restartovat**.

   Zvažte také:
   + V AutoML config při použití Azure Databricks přidejte následující parametry:
       1. ```max_concurrent_iterations```vychází z počtu pracovních uzlů v clusteru.
        2. ```spark_context=sc```je založen na výchozím kontextu Sparku.
   + Nebo, pokud máte starou verzi sady SDK, zrušte výběr z nainstalovaného knihovny clusteru a přesuňte se do koše. Nainstalujte novou verzi sady SDK a restartujte cluster. Pokud po restartování dojde k problému, odpojte a znovu připojte svůj cluster.

Pokud byla instalace úspěšná, importovaná knihovna by měla vypadat jako jedna z následujících:

Sada SDK pro datacihly **_bez_** automatizovaného ![strojového učení Azure Machine Learning SDK pro datacihly](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Sada SDK pro datacihly **pomocí** automatizovaného ![strojového učení sady SDK s nainstalovanou službou Automated Machine Learning pro datacihly](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Začít zkoumat

Vyzkoušet:
+ Stáhněte si [soubor archivu poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) pro sadu Azure Databricks/Azure Machine Learning SDK a [importujte soubor archivu](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) do svého clusteru datacihly.
  I když je k dispozici mnoho ukázkových poznámkových bloků, **fungují pouze [Tyto ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) s Azure Databricks.**

+ Naučte se [vytvářet kanály s datacihlami jako výpočetní](how-to-create-your-first-pipeline.md)prostředí.

## <a id="aznotebooks"></a>Poznámkových bloků Azure

[Azure Notebooks](https://notebooks.azure.com) (preview) je interaktivní vývojové prostředí v cloudu Azure. Je to snadný způsob, jak začít s vývojem Azure Machine Learning.

* Sada Azure Machine Learning SDK je už nainstalovaná.
* Po vytvoření pracovního prostoru služby Azure Machine Learning v Azure Portal můžete kliknutím na tlačítko automaticky nakonfigurovat prostředí poznámkového bloku Azure pro práci s pracovním prostorem.

Pomocí [Azure Portal](https://portal.azure.com) můžete začít s Azure Notebooks.  Otevřete pracovní prostor a v části **Přehled** vyberte **Začínáme v Azure Notebooks**.

Ve výchozím nastavení používá Azure Notebooks k dispozici bezplatnou úroveň služby, která je omezená na 4 GB paměti a 1 GB dat. Tyto limity však můžete odebrat připojením instance Data Science Virtual Machine k projektu Azure Notebooks. Další informace najdete v tématu [Správa a konfigurace Azure Notebooksch projektů – výpočetní vrstva](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="workspace"></a>Vytvořte konfigurační soubor pracovního prostoru

Konfigurační soubor pracovního prostoru je soubor JSON, který oznamuje sadě SDK, jak komunikovat s vaším pracovním prostorem Azure Machine Learning služby. Soubor má název *config. JSON*a má následující formát:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Tento soubor JSON musí být ve struktuře adresáře, která obsahuje vaše skripty Pythonu nebo Jupyter Poznámkový blok. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři.

Chcete-li použít tento soubor z vašeho kódu, použijte `ws=Workspace.from_config()`. Tento kód načte informace ze souboru a připojí k vašemu pracovnímu prostoru.

Konfigurační soubor můžete vytvořit třemi způsoby:

* **Použijte [WS. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** : pro zápis souboru *config. JSON* . Soubor obsahuje informace o konfiguraci pro váš pracovní prostor. *Config. JSON* si můžete stáhnout nebo zkopírovat do jiných vývojových prostředí.

* **Stáhněte si soubor**: V [Azure Portal](https://ms.portal.azure.com)v části **Přehled** pracovního prostoru vyberte **Stáhnout soubor config. JSON** .

     ![portál Azure](./media/how-to-configure-environment/configure.png)

* **Vytvořte soubor programově**: V následujícím fragmentu kódu se připojíte k pracovnímu prostoru zadáním ID předplatného, skupiny prostředků a názvu pracovního prostoru. Pak uloží konfiguraci pracovního prostoru do souboru:

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

    Tento kód zapíše konfigurační soubor do souboru *. AzureML/config. JSON* .


## <a name="next-steps"></a>Další postup

- [Výuka modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning s využitím datové sady mnist ručně zapsaných
- Referenční informace o sadě [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk)
- Další informace o [balíčku pro přípravu dat pro Azure Machine Learning](https://aka.ms/data-prep-sdk)
