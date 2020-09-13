---
title: Nastavení vývojového prostředí v Pythonu
titleSuffix: Azure Machine Learning
description: Naučte se konfigurovat vývojové prostředí pro Azure Machine Learning. Používejte prostředí Conda, vytvářejte konfigurační soubory a nakonfigurujete svůj vlastní cloudový server, Poznámkový blok Jupyter, Azure Databricks, IDEs, editory kódu a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8ad22abdf17c68c93a6189db839fe357e7acc91e
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650823"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurace vývojového prostředí pro Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak nakonfigurovat vývojové prostředí pro práci s Azure Machine Learning. Azure Machine Learning nezávislá platformy. Jediným z požadavků pro vaše vývojové prostředí je Python 3. Doporučuje se také izolované prostředí, jako je Anaconda nebo virtualenv.

Následující tabulka uvádí každé vývojové prostředí popsané v tomto článku spolu s odborníky a nevýhody.

| Prostředí | Výhody | Nevýhody |
| --- | --- | --- |
| [Cloudová Azure Machine Learning výpočetní instance](#compute-instance) | Nejjednodušší způsob, jak začít. Celá sada SDK je už ve vašem VIRTUÁLNÍm počítači pracovního prostoru nainstalovaná a kurzy poznámkových bloků jsou předem naklonované a připravené ke spuštění. | Nedostatečná kontrola nad vývojovým prostředím a závislostmi. Dodatečné náklady vzniklé pro virtuální počítač Linux (virtuální počítač se dá zastavit, pokud se nepoužívá), aby se předešlo poplatkům. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Místní prostředí](#local) | Úplná kontrola nad vývojovým prostředím a závislostmi. Spusťte s libovolným nástrojem sestavení, prostředím nebo IDE dle vašeho výběru. | Začne trvat déle. Musí být nainstalované potřebné balíčky sady SDK a prostředí, pokud ho ještě nemáte, musí být nainstalované i v případě, že ho ještě nemáte. |
| [Azure Databricks](#aml-databricks) | Ideální pro spouštění vysoce škálovatelných pracovních postupů strojového učení na škálovatelné Apache Spark platformě. | Přehnaně důkladné se na experimentální strojové učení nebo experimenty s menším rozsahem a pracovní postupy. Dodatečné náklady vzniklé za Azure Databricks. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Podobně jako u cloudové výpočetní instance (Python a SDK jsou předem nainstalované), ale mají předem nainstalované i další oblíbené datové vědy a nástroje pro strojové učení. Snadné škálování a kombinování s dalšími vlastními nástroji a pracovními postupy. | V porovnání s cloudovou výpočetní instancí je pomalejší prostředí Začínáme. |

Tento článek také nabízí další tipy k používání následujících nástrojů:

* [Jupyter poznámkové bloky](#jupyter): pokud už Jupyter notebook používáte, sada SDK obsahuje několik dalších doplňků, které byste měli nainstalovat.

* [Visual Studio Code](#vscode): Používáte-li Visual Studio Code, zahrnuje [rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) rozsáhlou jazykovou podporu Pythonu a také funkce, které umožňují pracovat s Azure Machine Learning mnohem pohodlnější a produktivní.

## <a name="prerequisites"></a>Požadavky

Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](how-to-manage-workspace.md). Pracovní prostor je vše, co potřebujete, abyste mohli začít s vlastním [cloudovým notebookem](#compute-instance), [DSVM](#dsvm)nebo [Azure Databricks](#aml-databricks).

Pokud chcete nainstalovat prostředí SDK pro svůj [místní počítač](#local), [Jupyter Notebook Server](#jupyter) nebo [Visual Studio Code](#vscode) budete také potřebovat:

- Buď správce balíčků [Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://conda.io/miniconda.html) .

- V systému Linux nebo macOS potřebujete prostředí bash.

    > [!TIP]
    > Pokud pracujete v systému Linux nebo macOS a používáte jiné prostředí než bash (například ZSH), může dojít k chybám při spuštění některých příkazů. Pokud chcete tento problém obejít, pomocí `bash` příkazu spusťte nové prostředí bash a spusťte příkazy tam.

- V systému Windows potřebujete příkazový řádek nebo Anacondaový dotaz (instalovaný Anaconda a Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Vaše vlastní cloudová instance COMPUTE

Instance služby Azure Machine Learning [COMPUTE](concept-compute-instance.md) je zabezpečená cloudová pracovní stanice Azure, která poskytuje odborníky na data pomocí serveru Jupyter notebook, JupyterLab a plně připraveného prostředí ml.

Pro výpočetní instanci není nic k instalaci ani konfiguraci.  Vytvořte si ji kdykoli v pracovním prostoru Azure Machine Learning. Zadejte jenom název a zadejte typ virtuálního počítače Azure. Vyzkoušejte si to hned v tomto [kurzu: nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

Další informace o výpočetních instancích, včetně postupu instalace balíčků, najdete v tématu [výpočetní instance](concept-compute-instance.md).

Pokud chcete zastavit náklady na výpočetní výkon, [zastavte výpočetní instanci](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Virtuální počítač pro datové vědy

DSVM je přizpůsobená image virtuálního počítače (VM). Je navržená pro práci v oblasti datové vědy, která je předem nakonfigurovaná:

  - Balíčky jako TensorFlow, PyTorch, Scikit-učení, XGBoost a sada SDK pro Azure Machine Learning
  - Oblíbené nástroje pro datové vědy, jako je Spark Standalone a procházení
  - Nástroje Azure, jako jsou Azure CLI, AzCopy a Průzkumník služby Storage
  - Integrovaná vývojová prostředí (IDEs), například Visual Studio Code a PyCharm
  - Server Jupyter Notebook

Sada SDK pro Azure Machine Learning funguje v DSVM ve verzi Ubuntu nebo Windows. Ale pokud plánujete používat DSVM jako cíl výpočtů, podporuje se jenom Ubuntu.

Použití DSVM jako vývojového prostředí:

1. Vytvořte DSVM v jednom z následujících prostředí:

    * Azure Portal:

        * [Vytvoření Data Science Virtual Machine Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Vytvoření prostředí Data Science Virtual Machine s Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Rozhraní příkazového řádku Azure:

        > [!IMPORTANT]
        > * Když použijete rozhraní příkazového řádku Azure, musíte se nejdřív přihlásit k předplatnému Azure pomocí `az login` příkazu.
        >
        > * Když použijete příkazy v tomto kroku, musíte zadat název skupiny prostředků, název virtuálního počítače, uživatelské jméno a heslo.

        * Pokud chcete vytvořit Data Science Virtual Machine Ubuntu, použijte následující příkaz:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Chcete-li vytvořit Data Science Virtual Machine Windows, použijte následující příkaz:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Sada Azure Machine Learning SDK je už na DSVM nainstalovaná. Chcete-li použít prostředí Conda, které obsahuje sadu SDK, použijte jeden z následujících příkazů:

    * Pro Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Pro Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. Pokud chcete ověřit, že máte přístup k sadě SDK, a podívejte se na verzi, použijte následující kód Pythonu:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Pokud chcete nakonfigurovat DSVM pro použití vašeho pracovního prostoru Azure Machine Learning, přečtěte si část [vytvoření konfiguračního souboru pracovního prostoru](#workspace) .

Další informace najdete v tématu [Virtual Machines pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Místní počítač

Pokud používáte místní počítač (který může být také vzdáleným virtuálním počítačem), vytvořte prostředí Anaconda a nainstalujte sadu SDK. Tady je příklad:

1. Stáhněte a nainstalujte [Anaconda](https://www.anaconda.com/distribution/#download-section) (verze Python 3,7), pokud ji ještě nemáte.

1. Otevřete příkazový řádek Anaconda a vytvořte prostředí pomocí následujících příkazů:

    Spuštěním následujícího příkazu vytvořte prostředí.

    ```bash
    conda create -n myenv python=3.7.7
    ```

    Pak aktivujte prostředí.

    ```bash
    conda activate myenv
    ```

    Tento příklad vytvoří prostředí pomocí Python 3.7.7, ale lze zvolit jakékoli konkrétní podverze. Kompatibilita sady SDK nemusí být zaručená u určitých hlavních verzí (doporučuje se 3,5 +) a při spuštění chyb se doporučuje vyzkoušet jinou verzi/podverzi v prostředí Anaconda. Vytvoření prostředí během stahování komponent a balíčků bude trvat několik minut.

1. Spuštěním následujících příkazů v novém prostředí povolíte jádro Pythonu specifické pro prostředí. Tím se zajistí očekávané chování při importu jádra a balíčku při práci s poznámkovým blokům Jupyter v prostředích Anaconda:

    ```bash
    conda install notebook ipykernel
    ```

    Pak spuštěním následujícího příkazu vytvořte jádro:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. K instalaci balíčků použijte následující příkazy:

    Tento příkaz nainstaluje základní sadu Azure Machine Learning SDK pomocí poznámkového bloku a `automl` dalších. `automl`Extra je velká instalace a je možné ji odebrat z závorek, pokud nechcete spouštět automatizované experimenty strojového učení. `automl`Navíc zahrnuje také Azure Machine Learning sadu SDK pro přípravu dat jako závislost.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Pokud se zobrazí zpráva, že PyYAML nelze odinstalovat, použijte místo toho následující příkaz:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Počínaje macOS Catalina, zsh (prostředí Z) je výchozí prostředí pro přihlášení a interaktivní prostředí. V ZSH použijte následující příkaz, který řídí hranaté závorky " \\ " (zpětné lomítko):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Instalace sady SDK bude trvat několik minut. Další informace o možnostech instalace najdete v příručce pro [instalaci](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).

1. Nainstalujte další balíčky pro experimentování ve službě Machine Learning.

    Použijte některý z následujících příkazů a nahraďte *\<new package>* ho balíčkem, který chcete nainstalovat. Instalace balíčků přes `conda install` vyžaduje, aby byl balíček součástí současných kanálů (nové kanály je možné přidat do Anaconda cloudu).

    ```bash
    conda install <new package>
    ```

    Alternativně můžete balíčky instalovat prostřednictvím nástroje `pip` .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Poznámkové bloky Jupyter

Jupyter poznámkové bloky jsou součástí [projektu Jupyter](https://jupyter.org/). Poskytují interaktivní kódování při vytváření dokumentů, které přinášejí živý kód pomocí mluveného textu a grafiky. Jupyter poznámkové bloky jsou také skvělým způsobem, jak sdílet výsledky s ostatními, protože výstup částí kódu v dokumentu můžete uložit. Poznámkové bloky Jupyter můžete nainstalovat na celou řadu platforem.

Postup v části [místní počítač](#local) nainstaluje potřebné komponenty pro spouštění notebooků Jupyter v prostředí Anaconda.

Postup povolení těchto součástí v prostředí Jupyter Notebook:

1. Otevřete příkazový řádek Anaconda a aktivujte své prostředí.

    ```bash
    conda activate myenv
    ```

1. Naklonujte [úložiště GitHub](https://github.com/Azure/MachineLearningNotebooks) pro sadu ukázkových poznámkových bloků.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte Jupyter Notebook Server pomocí následujícího příkazu:

    ```bash
    jupyter notebook
    ```

1. Chcete-li ověřit, zda Jupyter Notebook může použít sadu SDK, vytvořte **Nový** Poznámkový blok, jako jádro vyberte **Python 3** a potom v buňce poznámkového bloku spusťte následující příkaz:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pokud narazíte na problémy s importem modulů a přijmete `ModuleNotFoundError` , ujistěte se, že je váš Jupyter jádro připojený ke správné cestě pro vaše prostředí, a to spuštěním následujícího kódu v buňce poznámkového bloku.

    ```python
    import sys
    sys.path
    ```

1. Pokud chcete nakonfigurovat Jupyter Notebook k používání pracovního prostoru Azure Machine Learning, přečtěte si část [vytvoření konfiguračního souboru pracovního prostoru](#workspace) .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code je velmi oblíbený editor kódu pro různé platformy, který podporuje rozsáhlou sadu programovacích jazyků a nástrojů prostřednictvím rozšíření dostupných na [webu Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode). [Rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) nainstaluje [rozšíření Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro kódování ve všech typech prostředí Pythonu (Virtual, Anaconda atd.). Kromě toho poskytuje praktické funkce pro práci s Azure Machine Learning prostředky a spouštění Azure Machine Learning experimentů bez nutnosti opustit Visual Studio Code.

Použití Visual Studio Code pro vývoj:

1. Instalaci rozšíření Azure Machine Learning pro Visual Studio Code najdete v tématu [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Další informace najdete v tématu [použití Azure Machine Learning pro Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Naučte se používat Visual Studio Code pro jakýkoliv typ vývoje v Pythonu, přečtěte si téma Začínáme [s Pythonem v VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Pokud chcete vybrat prostředí SDK Pythonu obsahující sadu SDK, otevřete VS Code a pak vyberte CTRL + SHIFT + P (Linux a Windows) nebo Command + Shift + P (Mac).
        - Otevře se __paleta příkazů__ .

    - Zadejte __Python: vyberte Interpret__a pak vyberte příslušné prostředí.

1. Chcete-li ověřit, zda můžete použít sadu SDK, vytvořte nový soubor Pythonu (. py), který obsahuje následující kód:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Spusťte tento kód tak, že kliknete na "spustit buňku" CodeLens nebo jednoduše stisknete klávesu SHIFT + ENTER.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Poskytuje prostředí pro spolupráci na bázi poznámkového bloku s PROCESORem nebo výpočetním clusterem založeným na GPU.

Jak Azure Databricks pracuje s Azure Machine Learning:
+ Model můžete vytvořit pomocí Spark MLlib a model nasadit do ACI/AKS v rámci Azure Databricks.
+ [Automatické funkce strojového učení](concept-automated-ml.md) můžete použít také ve speciální sadě SDK pro Azure ML s Azure Databricks.
+ Azure Databricks můžete použít jako cíl pro výpočetní prostředky z [kanálu Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Nastavení clusteru datacihly

Vytvořte [cluster datacihly](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Některá nastavení platí jenom v případě, že nainstalujete sadu SDK pro automatizované Machine Learning na datacihly.
**Vytvoření clusteru bude trvat několik minut.**

Použijte tato nastavení:

| Nastavení |Platí pro| Hodnota |
|----|---|---|
| Název clusteru |stál| yourclustername |
| Modul runtime Databricks |stál|Běhový modul bez ML 6,5 (Scala 2,11, Spark 2.4.3) |
| Verze Pythonu |stál| 3 |
| Pracovníků |stál| 2 nebo vyšší |
| Typy virtuálních počítačů uzlu pracovního procesu <br>(určuje maximální počet souběžných iterací) |Automatizované strojové učení<br>pouze| Preferovaný virtuální počítač pro optimalizaci paměti |
| Povolit automatické škálování |Automatizované strojové učení<br>pouze| Zrušte zaškrtnutí políčka  |

Než budete pokračovat, počkejte, dokud cluster neběží.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Nainstalujte správnou sadu SDK do knihovny datacihly.
Po spuštění clusteru [vytvořte knihovnu](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , která připojí příslušný balíček sady Azure Machine Learning SDK k vašemu clusteru.

1. Klikněte pravým tlačítkem na složku aktuální pracovní prostor, kam chcete uložit knihovnu. Vyberte **vytvořit**  >  **knihovnu**.

1. Zvolit **jenom jednu** možnost (žádná jiná instalace sady SDK není podporovaná)

   |&nbsp;Doplňky balíčku sady SDK &nbsp;|Zdroj|&nbsp;Název PyPI&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Pro datacihly| Nahrát vejce Pythonu nebo PyPI | AzureML-SDK [datacihly]|
   |Pro datacihly – with-<br> Automatické možnosti ML| Nahrát vejce Pythonu nebo PyPI | `azureml-sdk[automl]`|

   > [!Warning]
   > Nelze nainstalovat žádné další doplňky sady SDK. Vyberte jenom jednu z předchozích možností [ `databricks` ] nebo [ `automl` ].

   * Nevybírejte možnost **Připojit automaticky ke všem clusterům**.
   * Vedle názvu clusteru vyberte  **připojit** .

1. Monitoruje chyby, dokud se změny stavu **nepřipojí**, což může trvat několik minut.  Pokud tento krok neproběhne úspěšně:

   Zkuste cluster restartovat pomocí:
   1. V levém podokně vyberte **clustery**.
   1. V tabulce vyberte název vašeho clusteru.
   1. Na kartě **knihovny** vyberte **restartovat**.

   Zvažte také:
   + V AutoML config při použití Azure Databricks přidejte následující parametry:
       1. ```max_concurrent_iterations``` vychází z počtu pracovních uzlů v clusteru.
        2. ```spark_context=sc``` je založen na výchozím kontextu Sparku.
   + Nebo, pokud máte starou verzi sady SDK, zrušte výběr z nainstalovaného knihovny clusteru a přesuňte se do koše. Nainstalujte novou verzi sady SDK a restartujte cluster. Pokud po restartování dojde k problému, odpojte a znovu připojte svůj cluster.

Pokud byla instalace úspěšná, importovaná knihovna by měla vypadat jako jedna z následujících:

Sada SDK pro datacihly **_bez_** automatizovaného strojového učení ![ Azure Machine Learning SDK pro datacihly](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Sada SDK pro datacihly **pomocí** automatizovaného strojového učení ![ sady SDK s nainstalovanou službou Automated Machine Learning pro datacihly](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Zahájení průzkumu

Vyzkoušet:
+ I když je k dispozici mnoho ukázkových poznámkových bloků, **fungují pouze [Tyto ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) s Azure Databricks.**

+ Tyto ukázky importujte přímo z pracovního prostoru. Viz níže: ![ Vyberte importovat ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ panel importu.](./media/how-to-configure-environment/azure-db-import.png)

+ Naučte se [vytvářet kanály s datacihlami jako výpočetní](how-to-create-your-first-pipeline.md)prostředí.

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Vytvoření konfiguračního souboru pracovního prostoru

Konfigurační soubor pracovního prostoru je soubor JSON, který oznamuje sadě SDK, jak komunikovat s vaším pracovním prostorem Azure Machine Learning. Soubor má název *config.jsv*a má následující formát:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Tento soubor JSON musí být ve struktuře adresáře, která obsahuje vaše skripty Pythonu nebo Jupyter Poznámkový blok. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři.

Chcete-li použít tento soubor ze svého kódu, použijte `ws=Workspace.from_config()` . Tento kód načte informace ze souboru a připojí se k vašemu pracovnímu prostoru.

Konfigurační soubor můžete vytvořit třemi způsoby:

* **Použijte  [ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)**: k zápisu *config.js* do souboru. Soubor obsahuje informace o konfiguraci pro váš pracovní prostor. *config.js* můžete stáhnout nebo zkopírovat do jiných vývojových prostředí.

* **Stažení souboru**: v [Azure Portal](https://ms.portal.azure.com)vyberte  **Stáhnout config.jsv** části **Přehled** v pracovním prostoru.

     ![portál Azure](./media/how-to-configure-environment/configure.png)

* **Vytvořte soubor programově**: v následujícím fragmentu kódu se připojíte k pracovnímu prostoru ZADÁNÍm ID předplatného, skupiny prostředků a názvu pracovního prostoru. Pak uloží konfiguraci pracovního prostoru do souboru:

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

    Tento kód zapíše konfigurační soubor do souboru *. AzureML/config.js* .

## <a name="next-steps"></a>Další kroky

- [Výuka modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning s využitím datové sady mnist ručně zapsaných
- Referenční informace o sadě [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)
