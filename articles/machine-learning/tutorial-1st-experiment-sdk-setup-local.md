---
title: 'Kurz: Začínáme se službou Machine Learning – Python'
titleSuffix: Azure Machine Learning
description: V tomto kurzu se naučíte používat sadu SDK Azure Machine Learning pro Python spuštěnou ve vašem osobním vývojovém prostředí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 05ac0f78345e1c1d7643f24410d53b209ab7c375
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574152"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Kurz: Začínáme s Azure Machine Learning ve vývojovém prostředí (část 1 ze 4)

V této *čtyři sérii kurzů* se naučíte základy Azure Machine Learning a dokončují úlohy strojového učení v Pythonu založené na úlohách na cloudové platformě Azure. 

V části 1 této série kurzů budete:

> [!div class="checklist"]
> * Nainstalujte sadu Azure Machine Learning SDK.
> * Nastavte adresářovou strukturu pro kód.
> * Vytvořte pracovní prostor Azure Machine Learning.
> * Nakonfigurujte místní vývojové prostředí.
> * Nastavte výpočetní cluster.

> [!NOTE]
> Tato série kurzů se zaměřuje na Azure Machine Learning koncepty *založené* na úlohách strojového učení v Pythonu, které jsou náročné na výpočetní výkon a/nebo vyžadují reprodukovatelnost. Pokud máte více zajímat se o průzkumné pracovní postup, můžete místo toho použít [Jupyter nebo RStudio na instanci služby compute Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet. Zkuste [Azure Machine Learning](https://aka.ms/AMLFree).
- Seznamte se s koncepty Pythonu a [Machine Learning](concept-azure-machine-learning-architecture.md). Mezi příklady patří prostředí, školení a bodování.
- Místní vývojové prostředí, například Visual Studio Code, Jupyter nebo PyCharm.
- Python (verze 3,5 až 3,7).


## <a name="install-the-azure-machine-learning-sdk"></a>Instalace sady Azure Machine Learning SDK

V celém tomto kurzu používáme sadu SDK Azure Machine Learning pro Python.

K nastavení prostředí Python pro použití v celém tomto kurzu můžete použít nástroje, které jsou pro vás známé (například conda a PIP). Do prostředí Pythonu nainstalujte Azure Machine Learning SDK pro Python prostřednictvím PIP:

```bash
pip install azureml-sdk
```

> [!div class="nextstepaction"]
> [Nainstaloval (a) jsem sadu SDK](?success=install-sdk#dir) [do problému](https://www.research.net/r/7C8Z3DN?issue=install-sdk) .

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Vytvoření adresářové struktury pro kód
Pro tento kurz doporučujeme nastavit následující jednoduchou strukturu adresářů:

```markdown
tutorial
└──.azureml
```

- `tutorial`: Adresář nejvyšší úrovně projektu.
- `.azureml`: Skrytý podadresář pro ukládání Azure Machine Learning konfiguračních souborů.


> [!div class="nextstepaction"]
> [Vytvořili jsem adresář,](?success=create-dir#workspace) [na který jsem narazil](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Vytvoření pracovního prostoru Azure Machine Learning

Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning a je centralizované místo pro:

- Správa prostředků, jako jsou výpočetní prostředky.
- Uložte prostředky, jako jsou poznámkové bloky, prostředí, datové sady, kanály, modely a koncové body.
- Spolupracujte s ostatními členy týmu.

V adresáři nejvyšší úrovně `tutorial` přidejte nový soubor Pythonu s názvem `01-create-workspace.py` pomocí následujícího kódu. S vašimi preferencemi Přizpůsobte parametry (název, ID předplatného, skupina prostředků a [umístění](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)).

Kód můžete spustit v interaktivní relaci nebo jako soubor Pythonu.

>[!NOTE]
> Pokud používáte místní vývojové prostředí (například počítač), budete požádáni o ověření v pracovním prostoru pomocí *kódu zařízení* při prvním spuštění následujícího kódu. Postupujte podle pokynů na obrazovce.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Spustit tento kód z `tutorial` adresáře:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Pokud při spuštění tohoto kódu dojde k chybě, že nemáte přístup k předplatnému, přečtěte si téma [Vytvoření pracovního prostoru](how-to-manage-workspace.md?tab=python#create-multi-tenant) , kde najdete informace o možnostech ověřování.


Po úspěšném spuštění *01-Create-Workspace.py* bude struktura vaší složky vypadat takto:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Soubor `.azureml/config.json` obsahuje metadata nutná pro připojení k pracovnímu prostoru Azure Machine Learning. Konkrétně obsahuje ID vašeho předplatného, skupinu prostředků a název pracovního prostoru. 

> [!NOTE]
> Obsah `config.json` není tajných kódů. Tyto podrobnosti je dobré sdílet.
>
> K interakci s pracovním prostorem Azure Machine Learning je ještě vyžadováno ověřování.

> [!div class="nextstepaction"]
> [Vytvořili jsem pracovní prostor,](?success=create-workspace#cluster) [který jsem narazil na problém](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Vytvoření clusteru Azure Machine Learning COMPUTE

Vytvořte skript Pythonu v `tutorial` adresáři nejvyšší úrovně s názvem `02-create-compute.py` . Naplňte ho pomocí následujícího kódu, abyste vytvořili Azure Machine Learning výpočetní cluster, který se automaticky škáluje mezi nulami a čtyřmi uzly:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Spusťte soubor Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Po vytvoření clusteru bude mít 0 uzlů zřízených. *Cluster* neúčtuje náklady, dokud úlohu neodešlete. Pokud bude tento cluster nečinný po 2 400 sekund (40 minut), bude se škálovat dolů.

Vaše struktura složek teď bude vypadat takto:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

> [!div class="nextstepaction"]
> [Vytvářený výpočetní cluster](?success=create-compute-cluster#next-steps) [jsem narazil na problém](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="next-steps"></a>Další kroky

V tomto kurzu instalace máte následující:

- Byl vytvořen Azure Machine Learning pracovní prostor.
- Nastavte své místní vývojové prostředí.
- Vytvořili jste výpočetní cluster Azure Machine Learning.

V ostatních částech tohoto kurzu se naučíte:

* 2. část: Spusťte kód v cloudu pomocí sady Azure Machine Learning SDK pro Python.
* 3. část: Spravujte prostředí Pythonu, které používáte pro školení modelů.
* 4. část: Nahrajte data do Azure a využijte tato data při školení.

Přejděte k dalšímu kurzu a Projděte si odeslání skriptu do Azure Machine Learning výpočetnímu clusteru.

> [!div class="nextstepaction"]
> [Kurz: spuštění "Hello World!" Skript Pythonu v Azure](tutorial-1st-experiment-hello-world.md)
