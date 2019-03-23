---
title: Nastavení vývojového prostředí Pythonu
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak nakonfigurovat prostředí pro vývoj, při práci se službou Azure Machine Learning. V tomto článku se dozvíte, jak používat prostředí Conda, vytvořit konfigurační soubory a konfigurace poznámkové bloky Jupyter, poznámkových bloků Azure, Azure Databricks, Integrovaná vývojová prostředí, editory kódu a virtuální počítač pro datové vědy.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: 1d4144a2a6cf41d594ee096d8802ccc5b29009a5
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361791"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurace prostředí pro vývoj pro Azure Machine Learning

V tomto článku se dozvíte, jak nakonfigurovat vývojové prostředí pro práci se službou Azure Machine Learning. Služba Machine Learning je pro více platforem.

Jediným požadavkem pro vaše vývojové prostředí je Python 3, Anaconda (pro izolované prostředí) a konfigurační soubor, který obsahuje informace o pracovním prostoru Azure Machine Learning.

Tento článek se zaměřuje na tyto prostředí a nástroje:

* Poznámkových bloků Azure: Poznámkové bloky Jupyter služba, která je hostovaná v cloudu Azure. Je nejjednodušší způsob, jak začít, protože sada SDK Azure Machine Learning je už nainstalovaný.

* [Virtuální počítač pro datové vědy (DSVM)](#dsvm): Předem nakonfigurované vývoje služby experimentování ve službě prostředí nebo v cloudu Azure, která je navržená pro datové vědy práce a mohou být nasazeny na procesoru pouze instance virtuálních počítačů nebo instancí založený na grafickém procesoru. Python 3, Conda, poznámkové bloky Jupyter a sady SDK Azure Machine Learning jsou již nainstalovány. Virtuální počítač se dodává s oblíbenými strojové učení a obsáhlý learning architektur, nástrojů a editory pro vývoj řešení strojového učení. To je pravděpodobně nejkompletnější vývojové prostředí pro strojové učení na platformě Azure.

* [Poznámkový blok Jupyter](#jupyter): Pokud už používáte Poznámkový blok Jupyter, sady SDK má některé funkce, které byste měli nainstalovat.

* [Visual Studio Code](#vscode): Pokud používáte Visual Studio Code, má některá užitečná rozšíření, které můžete nainstalovat.

* [Azure Databricks](#aml-databricks): Oblíbené platformě pro analýzu dat, která je založená na Apache Sparku. Zjistěte, jak získat Azure Machine Learning SDK do vašeho clusteru tak, že můžete nasadit modely.

Pokud už máte prostředí Python 3, nebo chcete jenom základní kroky pro instalaci sady SDK, přečtěte si článek [místního počítače](#local) oddílu.

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, najdete v článku [vytvořit pracovní prostor služby Azure Machine Learning](setup-create-workspace.md).

- Buď [Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://conda.io/miniconda.html) Správce balíčků.

    > [!IMPORTANT]
    > Anaconda a Miniconda nejsou vyžadovány, při použití poznámkových bloků Azure.

- V systému Linux nebo macOS budete potřebovat prostředí bash.

    > [!TIP]
    > Pokud jste v systému Linux nebo macOS a využívat prostředí, než je bash (například zsh) může dojít k chybám při spuštění některé příkazy. Chcete-li tento problém obejít, použijte `bash` příkaz ke spuštění nového prostředí bash a spusťte příkazy existuje.

- Na Windows musíte příkazový řádek nebo Anaconda řádek (nainstalovaných Anaconda a Miniconda).

## <a id="aznotebooks"></a>Poznámkových bloků Azure

[Azure Notebooks](https://notebooks.azure.com) (preview) je interaktivní vývojové prostředí v cloudu Azure. Je nejjednodušší způsob, jak začít s vývojem pro Azure Machine Learning.

* Sada SDK Azure Machine Learning je již nainstalována.
* Po vytvoříte pracovní prostor služby Azure Machine Learning na webu Azure Portal, můžete kliknout na tlačítko pro automatickou konfiguraci prostředí Azure poznámkového bloku pro práci s pracovním prostorem.

Vývoj s využitím poznámkových bloků Azure, najdete v článku [Začínáme se službou Azure Machine Learning](quickstart-run-cloud-notebook.md).

Ve výchozím nastavení používá vrstvy bezplatná služba, která je omezena na 4GB paměti a 1GB dat poznámkových bloků Azure. Tato omezení můžete, ale odebrat připojením k instanci virtuálního počítače pro datové vědy do projektu poznámkových bloků Azure. Další informace najdete v tématu [spravovat a nakonfigurovat projekty poznámkových bloků Azure – výpočetní vrstva](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="dsvm"></a>Virtuální počítač pro datové vědy

Datové VĚDY je přizpůsobenou image virtuálního počítače (VM). Je navržen pro práci vědy data, která je předem nakonfigurovaným programem:

  - Balíčky, jako je TensorFlow, PyTorch, Scikit poučení, XGBoost a Azure Machine Learning SDK
  - Oblíbenými datovými vědy nástrojů, jako je Spark samostatné a zotavení po havárii
  - Nástroje Azure, jako je Azure CLI, AzCopy a Průzkumníka služby Storage
  - Integrované vývojové prostředí (IDE), jako je Visual Studio Code a PyCharm
  - Server poznámkového bloku Jupyter

Azure Machine Learning SDK funguje na Ubuntu nebo Windows verzi datové VĚDY. Ale pokud máte v plánu používat jako cílové výpočetní prostředí a datové VĚDY, se podporuje jen Ubuntu.

Pokud chcete používat datové VĚDY jako vývojové prostředí, postupujte takto:

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

Při použití místního počítače (který může být také vzdálené virtuálního počítače), vytvořte Anaconda prostředí a nainstalujte sadu SDK následujícím způsobem:

1. Stáhněte a nainstalujte [Anaconda](https://www.anaconda.com/distribution/#download-section) (verze Python 3.7) Pokud jste ho už nemáte.

1. Otevřít řádek Anaconda a vytvořit prostředí pomocí následujících příkazů:

    Spusťte následující příkaz k vytvoření prostředí.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Potom aktivujte prostředí.

    ```shell
    conda activate myenv
    ```

    Tento příklad vytvoří prostředí, které používá python 3.6.5, ale je možné zvolit všechny konkrétní subversions. Kompatibilita sady SDK nemůže zaručit s některé hlavní verze (3.5 + je doporučeno) a doporučujeme vyzkoušet různé verze a podverze ve vašem prostředí Anaconda, pokud dojde k chybám. To bude trvat několik minut vytvořit prostředí, zatímco se stáhnou součásti a balíčky.

1. Spuštěním následujících příkazů v novém prostředí umožňující specifických pro prostředí ipython jádrech. Tím se zajistí, že očekávané jádra a balíčku importovat chování při práci s poznámkovými bloky Jupyter v rámci programu Anaconda prostředí:

    ```shell
    conda install notebook ipykernel
    ```

    Pak spuštěním následujícího příkazu vytvořte jádra:

    ```shell
    ipython kernel install --user
    ```

1. Pomocí následujících příkazů nainstalujte balíčky:

    Tento příkaz nainstaluje základní sadu SDK Azure Machine Learning pomocí poznámkového bloku a automl funkce. `automl` Velmi velké instalace je a může být odebrán z uvedených v závorkách, pokud se nechystáte automatizované strojovým učením. `automl` Navíc také obsahuje sadu SDK pro Azure Machine Learning Data Prep ve výchozím nastavení jako závislost.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Chcete-li nainstalovat sadu SDK pro Azure Machine Learning Data Prep sama o sobě, použijte tento příkaz:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Pokud se zobrazí zpráva, že PyYAML nelze odinstalovat, použijte následující příkaz:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   To bude trvat několik minut nainstalovat sadu SDK. Zobrazit [Instalační příručka](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) pro další informace o možnostech instalace.

1. Instalace dalších balíčků pro strojového učení experimentování.

    Použijte kterýkoli z následujících příkazů a nahraďte  *\<nový balíček >* názvem balíčku, kterou chcete nainstalovat. Instalace balíčků prostřednictvím `conda install` vyžaduje, že balíček je součástí aktuální kanálů (nových kanálů můžete přidat v cloudu Anaconda).

    ```shell
    conda install <new package>
    ```

    Alternativně můžete nainstalovat balíčky prostřednictvím `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Poznámkové bloky Jupyter

Poznámkové bloky Jupyter jsou součástí [Jupyter projektu](https://jupyter.org/). Poskytuje interaktivní prostředí pro psaní kódu ve kterém vytvoříte dokumenty, které kombinovat živého kódu s vyprávěného textu a grafiky. Poznámkové bloky Jupyter jsou také skvělý způsob, jak sdílet s ostatními, vaše výsledky, protože výstupní kód oddíly můžete uložit v dokumentu. Poznámkové bloky Jupyter můžete nainstalovat na různých platformách.

Podle postupu v [místního počítače](#local) části nainstaluje komponenty potřebné ke spuštění poznámkové bloky Jupyter v prostředí Anaconda. Pokud chcete povolit tyto komponenty ve vašem prostředí poznámkového bloku Jupyter, postupujte takto:

1. Otevřít řádek Anaconda a aktivovat vaše prostředí.

    ```shell
    conda activate myenv
    ```

1. Spusťte Poznámkový blok Jupyter server pomocí následujícího příkazu:

    ```shell
    jupyter notebook
    ```

1. Pokud chcete ověřit, že sadu SDK můžete použít Poznámkový blok Jupyter, vytvořit **nový** Poznámkový blok, vyberte **Python 3** jako jádra a pak spusťte následující příkaz v buňce Poznámkový blok:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pokud narazíte na problémy importu modulů a přijímat `ModuleNotFoundError`, zkontrolujte, že vaše jádra Jupyter je připojení na správnou cestu pro vaše prostředí spuštěním následujícího kódu do buňky poznámkového bloku.

    ```python
    import sys
    sys.path
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
Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Poskytuje prostředí poznámkového bloku na základě spolupráci s clusterem výpočetní prostředky na základě CPU nebo GPU.

Jak funguje Azure Databricks se službou Azure Machine Learning:
+ Můžete trénování modelu pomocí Spark MLlib a nasadit model s ACI a AKS z Azure databricks. 
+ Můžete také použít [automatizované strojového učení](concept-automated-ml.md) možnosti ve speciální sadu SDK ML Azure s Azure Databricks.
+ Azure Databricks můžete použít jako cílové výpočetní prostředí z [kanálu Azure Machine Learning](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Nastavení clusteru Databricks

Vytvoření [Databricks pro cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Některá nastavení platí jenom v případě, že instalace sady SDK pro automatizované strojové učení v Databricks.
**Bude trvat několik minut pro vytvoření clusteru.**

Pomocí těchto nastavení:

| Nastavení |Platí pro| Hodnota |
|----|---|---|
| Název clusteru |vždy| yourclustername |
| Modul runtime Databricks |vždy| Any non ML runtime (non ML 4.x, 5.x) |
| Verze Pythonu |vždy| 3 |
| Pracovní procesy |vždy| 2 nebo vyšší |
| Typy virtuálních počítačů uzlů pracovního procesu <br>(určuje maximální počet souběžných iterací) |Automatizované ML<br>Pouze| Optimalizované pro paměť virtuálního počítače upřednostňované |
| Povolení automatického škálování |Automatizované ML<br>Pouze| Zrušte zaškrtnutí políčka |

Počkejte, než je cluster spuštěn, než budete pokračovat.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Nainstalujte správnou sadu SDK do knihovny Databricks
Jakmile je cluster spuštěn, [vytvořit knihovnu](https://docs.databricks.com/user-guide/libraries.html#create-a-library) připojit odpovídající balíček sady SDK Azure Machine Learning k vašemu clusteru. 

1. Zvolte **pouze jeden** možnost (žádné další instalace sady SDK jsou podporovány)

   |Sada SDK&nbsp;balíčku&nbsp;funkce|Zdroj|PyPi&nbsp;název&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Pro Databricks| Nahrát Python Egg nebo PyPI | azureml-sdk[databricks]|
   |Pro Databricks - with-<br> automatizované funkce ML| Nahrát Python Egg nebo PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Je možné nainstalovat žádný další funkce sady SDK. Zvolte pouze jeden z předchozích možností [databricks] nebo [automl_databricks].

   * Nesmí být zvolen **automaticky připojí na všech clusterech**.
   * Vyberte **připojit** vedle názvu vašeho clusteru.

1. Sledování chyb, dokud se stav změní na **připojené**, což může trvat několik minut.  Pokud tento krok nezdaří, zkontrolujte následující: 

   Zkuste restartovat svůj cluster prostřednictvím:
   1. V levém podokně vyberte **clustery**.
   1. V tabulce vyberte název vašeho clusteru.
   1. Na **knihovny** kartu, vyberte možnost **restartovat**.
      
   Zvažte také:
   + Některé balíčky, jako například `psutil`, může způsobit konflikty Databricks během instalace. Nechcete-li tyto chyby, nainstalovat balíčky zmrazení lib verzí, jako je `pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0`. 
   + Nebo, pokud máte starší verzi sady SDK, zrušte zaškrtnutí možnosti z nainstalovaných knihoven clusteru a přesunout do koše. Nainstalujte novou verzi sady SDK a restartujte cluster. Pokud po této dochází k nějakému problému, odpojit a znovu ho připojte svůj cluster.

Pokud byla instalace úspěšná, importované knihovny by měl vypadat jako jednu z těchto:
   
Sada SDK pro Databricks **_bez_** automatizované strojového učení ![Azure Machine Learning SDK for Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Sada SDK pro Databricks **WITH** automatizované strojového učení ![SDK s automatizované strojového učení, které jsou nainstalované v Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Začít prozkoumávat

Vyzkoušejte si to:
+ Stáhněte si [poznámkového bloku archivní soubor](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) pro sadu SDK Azure Databricks a Azure Machine Learning a [importovat soubor archivu](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) do vašeho clusteru Databricks.  
  Mnoho ukázkové poznámkové bloky jsou k dispozici, **pouze [tyto ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fungují s Azure Databricks.**
  
+ Zjistěte, jak [vytvoření kanálu s Databricks jako výpočetní školení](how-to-create-your-first-pipeline.md).

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

* **Postupujte podle kroků v [vytvořit pracovní prostor služby Azure Machine Learning](setup-create-workspace.md#sdk)**: A *config.json* vytvoří soubor v knihovně poznámkových bloků Azure. Soubor obsahuje informace o konfiguraci pro váš pracovní prostor. Stáhněte nebo zkopírujte *config.json* do jiných vývojových prostředích.

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

- [Trénování modelu](tutorial-train-models-with-aml.md) v Azure Machine Learning s datovou sadou mnist ručně
- Zobrazení [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) odkaz
- Další informace o [sady SDK pro přípravu dat Azure Machine Learning](https://aka.ms/data-prep-sdk)
