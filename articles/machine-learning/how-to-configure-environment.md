---
title: Nastavení vývojového prostředí | Python
titleSuffix: Azure Machine Learning
description: Naučte se nastavit prostředí pro vývoj v Pythonu pro Azure Machine Learning. Používejte prostředí Conda, vytvářejte konfigurační soubory a nakonfigurujete svůj vlastní cloudový server, Poznámkový blok Jupyter, Azure Databricks, IDEs, editory kódu a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 54c607ebac02a9d7e534d24656a8687e9ff39725
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533175"
---
# <a name="set-up-a-development-environment-for-azure-machine-learning"></a>Nastavení vývojového prostředí pro Azure Machine Learning

Naučte se konfigurovat vývojové prostředí Python pro Azure Machine Learning.

Následující tabulka uvádí každé vývojové prostředí popsané v tomto článku spolu s odborníky a nevýhody.

| Prostředí | Výhody | Nevýhody |
| --- | --- | --- |
| [Místní prostředí](#local) | Úplná kontrola nad vývojovým prostředím a závislostmi. Spusťte s libovolným nástrojem sestavení, prostředím nebo IDE dle vašeho výběru. | Začne trvat déle. Musí být nainstalované potřebné balíčky sady SDK a prostředí, pokud ho ještě nemáte, musí být nainstalované i v případě, že ho ještě nemáte. |
| [Výpočetní instance Azure Machine Learningu](#compute-instance) | Nejjednodušší způsob, jak začít. Celá sada SDK je už ve vašem VIRTUÁLNÍm počítači pracovního prostoru nainstalovaná a kurzy poznámkových bloků jsou předem naklonované a připravené ke spuštění. | Nedostatečná kontrola nad vývojovým prostředím a závislostmi. Dodatečné náklady vzniklé pro virtuální počítač Linux (virtuální počítač se dá zastavit, pokud se nepoužívá), aby se předešlo poplatkům. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](#aml-databricks) | Ideální pro spouštění vysoce škálovatelných pracovních postupů strojového učení na škálovatelné Apache Spark platformě. | Přehnaně důkladné se na experimentální strojové učení nebo experimenty s menším rozsahem a pracovní postupy. Dodatečné náklady vzniklé za Azure Databricks. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Podobně jako u cloudové výpočetní instance (Python a SDK jsou předem nainstalované), ale mají předem nainstalované i další oblíbené datové vědy a nástroje pro strojové učení. Snadné škálování a kombinování s dalšími vlastními nástroji a pracovními postupy. | V porovnání s cloudovou výpočetní instancí je pomalejší prostředí Začínáme. |

Tento článek také nabízí další tipy k používání následujících nástrojů:

* Jupyter poznámkové bloky: Pokud už používáte poznámkové bloky Jupyter, sada SDK obsahuje několik dalších doplňků, které byste měli nainstalovat.

* Visual Studio Code: Používáte-li Visual Studio Code, zahrnuje [rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) rozsáhlou jazykovou podporu Pythonu a také funkce, které umožňují pracovat s Azure Machine Learning mnohem pohodlnější a produktivní.

## <a name="prerequisites"></a>Předpoklady

* Azure Machine Learning pracovní prostor. Pokud ho nemáte, můžete vytvořit Azure Machine Learning pracovní prostor prostřednictvím šablon [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)a [Azure Resource Manager](how-to-create-workspace-template.md).

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> (Jenom místní a DSVM) Vytvoření konfiguračního souboru pracovního prostoru

Konfigurační soubor pracovního prostoru je soubor JSON, který oznamuje sadě SDK, jak komunikovat s vaším pracovním prostorem Azure Machine Learning. Soubor má název *config.jsv*a má následující formát:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Tento soubor JSON musí být ve struktuře adresáře, která obsahuje vaše skripty Pythonu nebo Jupyter Poznámkový blok. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři.

Chcete-li použít tento soubor z kódu, použijte [`Workspace.from_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) metodu. Tento kód načte informace ze souboru a připojí se k vašemu pracovnímu prostoru.

Vytvořte konfigurační soubor pracovního prostoru jedním z následujících způsobů:

* portál Azure

    **Stažení souboru**: v [Azure Portal](https://ms.portal.azure.com)vyberte  **Stáhnout config.jsv** části **Přehled** v pracovním prostoru.

    ![portál Azure](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning Python SDK

    Vytvořte skript pro připojení k Azure Machine Learningmu pracovnímu prostoru a použijte [`write_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) metodu k vygenerování souboru a uložte ho jako *. azureml/config.jsna*. Nezapomeňte nahradit `subscription_id` , `resource_group` a `workspace_name` vlastníky.

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

## <a name="local-computer"></a><a id="local"></a>Místní počítač

Konfigurace místního vývojového prostředí (což může být také vzdálený virtuální počítač, například Azure Machine Learning výpočetní instance nebo DSVM):

1. Vytvořte virtuální prostředí Python (virtualenv, conda).

    > [!NOTE]
    > I když to není nutné, doporučujeme pro správu virtuálních prostředí Pythonu a instalaci balíčků používat [Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://www.anaconda.com/download/) .

    > [!IMPORTANT]
    > Pokud pracujete v systému Linux nebo macOS a používáte jiné prostředí než bash (například ZSH), může dojít k chybám při spuštění některých příkazů. Pokud chcete tento problém obejít, pomocí `bash` příkazu spusťte nové prostředí bash a spusťte příkazy tam.

1. Aktivujte nově vytvořené virtuální prostředí Python.
1. Nainstalujte [sadu Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
1. Pokud chcete nakonfigurovat místní prostředí tak, aby používalo pracovní prostor Azure Machine Learning, [vytvořte konfigurační soubor pracovního prostoru](#workspace) nebo použijte existující.

Teď, když máte nastavené místní prostředí, jste připraveni začít pracovat s Azure Machine Learning. Začínáme najdete v [příručce Začínáme v Azure Machine Learning Pythonu](tutorial-1st-experiment-sdk-setup-local.md) .

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Poznámkové bloky Jupyter

Pokud používáte místní Jupyter Notebook Server, doporučujeme pro své virtuální prostředí Pythonu vytvořit jádro IPython. To pomáhá zajistit očekávané chování při importu jádra a balíčku.

1. Povolit jádra IPython konkrétního prostředí

    ```bash
    conda install notebook ipykernel
    ```

1. Vytvořte jádro pro virtuální prostředí Pythonu. Ujistěte se, že jste nahradili `<myenv>` názvem vašeho virtuálního prostředí Python.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Spustit Jupyter Notebook Server

Pokud chcete začít pracovat s poznámkovým blokem Azure Machine Learning a Jupyter, podívejte se do [úložiště poznámkových bloků Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks) .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Použití Visual Studio Code pro vývoj:

1. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/Download).
1. Nainstalujte [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md) (Preview).

Po instalaci rozšíření Visual Studio Code můžete spravovat [prostředky Azure Machine Learning](how-to-manage-resources-vscode.md), [spouštět a ladit experimenty](how-to-debug-visual-studio-code.md)a [nasazovat školené modely](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Výpočetní instance Azure Machine Learningu

Instance služby Azure Machine Learning [COMPUTE](concept-compute-instance.md) je zabezpečená cloudová pracovní stanice Azure, která poskytuje odborníky na data s Jupyter Notebookm serverem, JupyterLab a plně spravovaným prostředím strojového učení.

Pro výpočetní instanci není nic k instalaci ani konfiguraci.  

Vytvořte si ji kdykoli v pracovním prostoru Azure Machine Learning. Zadejte jenom název a zadejte typ virtuálního počítače Azure. Vyzkoušejte si to hned v tomto [kurzu: nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

Další informace o výpočetních instancích, včetně postupu instalace balíčků, najdete v tématu [výpočetní instance](concept-compute-instance.md).

> [!TIP]
> Chcete-li zabránit vzniku poplatků za nepoužitou výpočetní instanci, [zastavte výpočetní instanci](tutorial-1st-experiment-bring-data.md#clean-up-resources).

Kromě Jupyter Notebook serveru a JupyterLab můžete použít výpočetní instance v [integrované funkci poznámkového bloku v rámci nástroje Azure Machine Learning Studio](how-to-run-jupyter-notebooks.md).

Pomocí rozšíření Azure Machine Learning Visual Studio Code můžete také [nakonfigurovat instanci služby Azure Machine Learning COMPUTE jako server vzdáleného Jupyter notebook](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Virtuální počítač pro datové vědy

DSVM je přizpůsobená image virtuálního počítače (VM). Je navržená pro práci v oblasti datových vědy, která je předem nakonfigurované nástroje a software, jako je:

  - Balíčky jako TensorFlow, PyTorch, Scikit-učení, XGBoost a sada SDK pro Azure Machine Learning
  - Oblíbené nástroje pro datové vědy, jako je Spark Standalone a procházení
  - Nástroje Azure, jako jsou Azure CLI, AzCopy a Průzkumník služby Storage
  - Integrovaná vývojová prostředí (IDEs), například Visual Studio Code a PyCharm
  - Server Jupyter Notebook

Komplexnější seznam nástrojů najdete v [příručce k nástrojům zahrnutým v DSVM](data-science-virtual-machine/tools-included.md).

> [!IMPORTANT]
> Pokud plánujete používat DSVM jako [výpočetní cíl](concept-compute-target.md) pro úlohy školení nebo Inferencing, je podporována pouze podpora Ubuntu.

Použití DSVM jako vývojového prostředí

1. Vytvořte DSVM pomocí jedné z následujících metod:

    * K vytvoření [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) nebo [Windows](data-science-virtual-machine/provision-vm.md) DSVM použijte Azure Portal.
    * [Vytvořte DSVM pomocí šablon ARM](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Použití Azure CLI

        Chcete-li vytvořit Ubuntu DSVM, použijte následující příkaz:

        ```azurecli-interactive
        # create a Ubuntu DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Chcete-li vytvořit Windows DSVM, použijte následující příkaz:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Aktivujte prostředí conda obsahující sadu SDK Azure Machine Learning.

    * Pro Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Pro Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. Pokud chcete nakonfigurovat DSVM pro použití vašeho pracovního prostoru Azure Machine Learning, [vytvořte konfigurační soubor pracovního prostoru](#workspace) nebo použijte existující.

Podobně jako u místních prostředí můžete k interakci s Azure Machine Learning používat Visual Studio Code a [rozšíření Azure Machine Learning Visual Studio Code](#vscode) .

Další informace najdete v tématu [Virtual Machines pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="azure-databricks"></a><a name="aml-databricks"></a> Azure Databricks

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Nabízí prostředí založené na poznámkovém bloku s PROCESORem nebo výpočetním clusterem založeným na GPU.

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

## <a name="next-steps"></a>Další kroky

- [Výuka modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning s využitím datové sady mnist ručně zapsaných
- Referenční informace o sadě [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)
