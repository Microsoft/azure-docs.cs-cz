---
title: Nastavení vývojového prostředí v jazyce Python
titleSuffix: Azure Machine Learning
description: Nastavte Azure Machine Learning vývojové prostředí Pythonu v poznámkových blocích Jupyter, Visual Studio Code, Azure Databricks a datové vědy Virtual Machines.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 03/22/2021
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 18b23dc8b7ed8b074ac3afbc7d44e5d132a2ee58
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890073"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Nastavení vývojového prostředí v Pythonu pro Azure Machine Learning

Naučte se konfigurovat vývojové prostředí Python pro Azure Machine Learning.

Následující tabulka uvádí každé vývojové prostředí popsané v tomto článku spolu s odborníky a nevýhody.

| Prostředí | Výhody | Nevýhody |
| --- | --- | --- |
| [Místní prostředí](#local) | Úplná kontrola nad vývojovým prostředím a závislostmi. Spusťte s libovolným nástrojem sestavení, prostředím nebo IDE dle vašeho výběru. | Začne trvat déle. Musí být nainstalované potřebné balíčky sady SDK a prostředí, pokud ho ještě nemáte, musí být nainstalované i v případě, že ho ještě nemáte. |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Podobně jako u cloudové výpočetní instance (Python a SDK jsou předem nainstalované), ale mají předem nainstalované i další oblíbené datové vědy a nástroje pro strojové učení. Snadné škálování a kombinování s dalšími vlastními nástroji a pracovními postupy. | V porovnání s cloudovou výpočetní instancí je pomalejší prostředí Začínáme. |
| [Výpočetní instance Azure Machine Learningu](#compute-instance) | Nejjednodušší způsob, jak začít. Celá sada SDK je už ve vašem VIRTUÁLNÍm počítači pracovního prostoru nainstalovaná a kurzy poznámkových bloků jsou předem naklonované a připravené ke spuštění. | Nedostatečná kontrola nad vývojovým prostředím a závislostmi. Dodatečné náklady vzniklé pro virtuální počítač Linux (virtuální počítač se dá zastavit, pokud se nepoužívá), aby se předešlo poplatkům. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | Ideální pro spouštění vysoce škálovatelných pracovních postupů strojového učení na škálovatelné Apache Spark platformě. | Přehnaně důkladné se na experimentální strojové učení nebo experimenty s menším rozsahem a pracovní postupy. Dodatečné náklady vzniklé za Azure Databricks. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/databricks/). |

Tento článek také nabízí další tipy k používání následujících nástrojů:

* Jupyter poznámkové bloky: Pokud už používáte poznámkové bloky Jupyter, sada SDK obsahuje několik dalších doplňků, které byste měli nainstalovat.

* Visual Studio Code: Používáte-li Visual Studio Code, zahrnuje [rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) rozsáhlou jazykovou podporu Pythonu a také funkce, které umožňují pracovat s Azure Machine Learning mnohem pohodlnější a produktivní.

## <a name="prerequisites"></a>Požadavky

* Azure Machine Learning pracovní prostor. Pokud ho ještě nemáte, můžete vytvořit Azure Machine Learning pracovní prostor prostřednictvím šablon [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)a [Azure Resource Manager](how-to-create-workspace-template.md).

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> Pouze místní a DSVM: vytvoření konfiguračního souboru pracovního prostoru

Konfigurační soubor pracovního prostoru je soubor JSON, který oznamuje sadě SDK, jak komunikovat s vaším pracovním prostorem Azure Machine Learning. Soubor má název *config.jsv* a má následující formát:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Tento soubor JSON musí být ve struktuře adresáře, která obsahuje vaše skripty Pythonu nebo Jupyter Poznámkový blok. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML* nebo v nadřazeném adresáři.

Chcete-li použít tento soubor z kódu, použijte [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace#from-config-path-none--auth-none---logger-none---file-name-none-) metodu. Tento kód načte informace ze souboru a připojí se k vašemu pracovnímu prostoru.

Vytvořte konfigurační soubor pracovního prostoru jedním z následujících způsobů:

* portál Azure

    **Stažení souboru**: v [Azure Portal](https://ms.portal.azure.com)vyberte  **Stáhnout config.jsv** části **Přehled** v pracovním prostoru.

    ![portál Azure](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning Python SDK

    Vytvořte skript pro připojení k Azure Machine Learningmu pracovnímu prostoru a použijte [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace#write-config-path-none--file-name-none-) metodu k vygenerování souboru a uložte ho jako *. azureml/config.jsna*. Nezapomeňte nahradit `subscription_id` , `resource_group` a `workspace_name` vlastníky.

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

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>Místní počítač nebo prostředí vzdáleného virtuálního počítače

Prostředí můžete nastavit v místním počítači nebo vzdáleném virtuálním počítači, jako je například Azure Machine Learning výpočetní instance nebo Data Science VM. 

Konfigurace místního vývojového prostředí nebo vzdáleného virtuálního počítače:

1. Vytvořte virtuální prostředí Python (virtualenv, conda).

    > [!NOTE]
    > I když to není nutné, doporučujeme pro správu virtuálních prostředí Pythonu a instalaci balíčků používat [Anaconda](https://www.anaconda.com/download/) nebo [Miniconda](https://www.anaconda.com/download/) .

    > [!IMPORTANT]
    > Pokud pracujete v systému Linux nebo macOS a používáte jiné prostředí než bash (například ZSH), může dojít k chybám při spuštění některých příkazů. Pokud chcete tento problém obejít, pomocí `bash` příkazu spusťte nové prostředí bash a spusťte příkazy tam.

1. Aktivujte nově vytvořené virtuální prostředí Python.
1. Nainstalujte [sadu Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install).
1. Pokud chcete nakonfigurovat místní prostředí tak, aby používalo pracovní prostor Azure Machine Learning, [vytvořte konfigurační soubor pracovního prostoru](#workspace) nebo použijte existující.

Teď, když máte nastavené místní prostředí, jste připraveni začít pracovat s Azure Machine Learning. Začínáme najdete v [příručce Začínáme v Azure Machine Learning Pythonu](tutorial-1st-experiment-sdk-setup-local.md) .

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter poznámkové bloky

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

> [!NOTE]
> Úložiště příkladů založené na komunitě najdete na adrese https://github.com/Azure/azureml-examples .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Použití Visual Studio Code pro vývoj:

1. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/Download).
1. Nainstalujte [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md) (Preview).

Jakmile je rozšíření Visual Studio Code nainstalované, použijte ho k těmto akcím:

* [Správa prostředků Azure Machine Learning](how-to-manage-resources-vscode.md)
* [Připojení k Azure Machine Learning výpočetní instance](how-to-set-up-vs-code-remote.md)
* [Spustit a ladit experimenty](how-to-debug-visual-studio-code.md)
* [Nasaďte školené modely](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Výpočetní instance Azure Machine Learningu

Instance služby Azure Machine Learning [COMPUTE](concept-compute-instance.md) je zabezpečená cloudová pracovní stanice Azure, která poskytuje odborníky na data s Jupyter Notebookm serverem, JupyterLab a plně spravovaným prostředím strojového učení.

Pro výpočetní instanci není nic k instalaci ani konfiguraci.  

Vytvořte si ji kdykoli v pracovním prostoru Azure Machine Learning. Zadejte jenom název a zadejte typ virtuálního počítače Azure. Vyzkoušejte si to hned v tomto [kurzu: nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

Další informace o výpočetních instancích, včetně postupu instalace balíčků, najdete v tématu [Vytvoření a správa Azure Machine Learning výpočetní instance](how-to-create-manage-compute-instance.md).

> [!TIP]
> Chcete-li zabránit vzniku poplatků za nepoužitou výpočetní instanci, [zastavte výpočetní instanci](how-to-create-manage-compute-instance.md#manage).

Kromě Jupyter Notebook serveru a JupyterLab můžete použít výpočetní instance v [integrované funkci poznámkového bloku v rámci nástroje Azure Machine Learning Studio](how-to-run-jupyter-notebooks.md).

K [připojení ke vzdálené výpočetní instanci pomocí vs Code](how-to-set-up-vs-code-remote.md)můžete použít také rozšíření Azure Machine Learning Visual Studio Code.

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Virtuální počítač pro datové vědy

Data Science VM je přizpůsobená image virtuálního počítače (VM), kterou můžete použít jako vývojové prostředí. Je navržená pro práci v oblasti datových vědy, která je předem nakonfigurované nástroje a software, jako je:

  - Balíčky jako TensorFlow, PyTorch, Scikit-učení, XGBoost a sada SDK pro Azure Machine Learning
  - Oblíbené nástroje pro datové vědy, jako je Spark Standalone a procházení
  - Nástroje Azure, jako jsou Azure CLI, AzCopy a Průzkumník služby Storage
  - Integrovaná vývojová prostředí (IDEs), například Visual Studio Code a PyCharm
  - Server Jupyter Notebook

Komplexnější seznam nástrojů najdete v [příručce k nástrojům pro data Science VM](data-science-virtual-machine/tools-included.md).

> [!IMPORTANT]
> Pokud plánujete použít Data Science VM jako [výpočetní cíl](concept-compute-target.md) pro úlohy školení nebo Inferencing, podporuje se jenom Ubuntu.

Použití Data Science VM jako vývojového prostředí:

1. Vytvořte Data Science VM pomocí jedné z následujících metod:

    * K vytvoření [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) nebo [Windows](data-science-virtual-machine/provision-vm.md) DSVM použijte Azure Portal.
    * [Vytvořte data Science VM pomocí šablon ARM](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Použití Azure CLI

        Pokud chcete vytvořit Data Science VM Ubuntu, použijte následující příkaz:

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
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

    * Pro Ubuntu Data Science VM:

        ```bash
        conda activate py36
        ```

    * Pro Windows Data Science VM:

        ```bash
        conda activate AzureML
        ```

1. Chcete-li nakonfigurovat Data Science VM k používání pracovního prostoru Azure Machine Learning, [vytvořte konfigurační soubor pracovního prostoru](#workspace) nebo použijte existující.

Podobně jako u místních prostředí můžete k interakci s Azure Machine Learning používat Visual Studio Code a [rozšíření Azure Machine Learning Visual Studio Code](#vscode) .

Další informace najdete v tématu [Virtual Machines pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).


## <a name="next-steps"></a>Další kroky

- [Výuku modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning s datovou sadou mnist ručně zapsaných
- Referenční informace o [sadě Azure Machine Learning SDK pro Python](/python/api/overview/azure/ml/intro)najdete v tématu. 
