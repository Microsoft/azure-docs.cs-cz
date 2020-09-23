---
title: 'Kurz: Začínáme se strojovým učením – Python'
titleSuffix: Azure Machine Learning
description: V tomto kurzu se naučíte používat sadu SDK Azure Machine Learning Python spuštěnou ve vašem osobním vývojovém prostředí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946499"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Kurz: Začínáme s Azure Machine Learning ve vývojovém prostředí (část 1 ze 4)

V této **čtyři sérii kurzů**se seznámíte se základy Azure Machine Learning a dokončení úloh Python ml na základě úloh v cloudu Azure, včetně těchto:

1. Nastavte pracovní prostor a své místní vývojové prostředí Machine Learning.
2. Spusťte kód v cloudu pomocí sady Python SDK pro Azure Machine Learning.
3. Spravujte prostředí Pythonu, které používáte pro školení modelů.
4. Nahrajte data do Azure a využijte tato data při školení.

V **části 1 této série kurzů**budete:

> [!div class="checklist"]
> * Instalace sady Azure Machine Learning SDK
> * Nastavení adresářové struktury pro kód
> * Vytvoření pracovního prostoru Azure Machine Learning
> * Konfigurace místního vývojového prostředí
> * Nastavení výpočetního clusteru

>[!NOTE]
> Tato série kurzů se zaměřuje na Azure Machine Learning koncepty __založené__ na úlohách strojového učení v Pythonu, které jsou náročné na výpočetní výkon a/nebo vyžadují reprodukovatelnost. Pokud se vaše úkoly strojového učení nevejdou do tohoto profilu, použijte [funkci Jupyter nebo RStudio na instanci služby Azure Machine Learning COMPUTE](tutorial-1st-experiment-sdk-setup.md) k připojení k Azure Machine Learning.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet. Vyzkoušejte [Azure Machine Learning](https://aka.ms/AMLFree) dnes.
- Seznamte se s koncepty Pythonu a [Machine Learning](concept-azure-machine-learning-architecture.md). Například prostředí, školení, bodování a tak dále.
- Místní vývojové prostředí – notebook s nainstalovaným Pythonem a vaše oblíbené integrované vývojové prostředí (např.: VSCode, PyCharm, Jupyter atd.).

## <a name="install-the-azure-machine-learning-sdk"></a>Instalace sady Azure Machine Learning SDK

V celém tomto kurzu používáme sadu Azure Machine Learning Python SDK.

Můžete použít nástroje, které jsou pro vás známé, například: Conda, PIP a tak dále, aby bylo možné nastavit prostředí pro použití v celém tomto kurzu. Do prostředí nainstalujte Azure Machine Learning Python SDK přes PIP:

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>Vytvořit adresářovou strukturu pro kód
Doporučujeme, abyste pro tento kurz nastavili následující jednoduchý adresář:

```markdown
tutorial
└──.azureml
```

- **kurz** (adresář nejvyšší úrovně projektu)
- **. AzureML** (skrytý podadresář kurzu): `.azureml` adresář se používá k ukládání Azure Machine Learning konfiguračních souborů.

## <a name="create-an-azure-machine-learning-workspace"></a>Vytvoření pracovní prostor Azure Machine Learning

Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning a je centralizované místo pro:

- Správa prostředků, jako jsou výpočetní prostředky
- Ukládání prostředků, jako jsou poznámkové bloky, prostředí, datové sady, kanály, modely, koncové body atd.
- Spolupráce s ostatními členy týmu

V horním nadřazeném adresáři – `tutorial` přidejte nový soubor Pythonu s názvem `01-create-workspace.py` s následujícím kódem. Přizpůsobte parametry (název, ID předplatného, skupina prostředků a [umístění](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) podle vašich požadavků).

Kód můžete spustit v interaktivní relaci nebo jako soubor Pythonu.

>[!NOTE]
> Při použití místního vývojového prostředí (např. přenosný počítač) budete požádáni o ověření v pracovním prostoru pomocí *kódu zařízení* při prvním spuštění kódu uvedeného níže. Postupujte podle pokynů na obrazovce.

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

Po spuštění výše uvedeného fragmentu kódu bude struktura složek vypadat takto:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Tento soubor `.azureml/config.json` obsahuje metadata nutná pro připojení k pracovnímu prostoru Azure Machine Learning – konkrétně ID předplatného, název skupiny prostředků a pracovní prostor. 

> [!NOTE]
> Obsah `config.json` není tajných kódů – je zcela jemný, aby bylo možné tyto podrobnosti sdílet.
> K interakci s pracovním prostorem Azure Machine Learning je ještě vyžadováno ověřování.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Vytvoření clusteru Azure Machine Learning COMPUTE

Vytvořte skript v jazyce Python v `tutorial` adresáři nejvyšší úrovně s názvem `02-create-compute.py` a naplňte do něj následující kód, který vytvoří Azure Machine Learning výpočetní cluster, který se automaticky škáluje mezi 0 a čtyřmi uzly:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Spusťte soubor Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Po vytvoření clusteru bude mít 0 uzlů zřízených. Proto cluster neúčtuje **does not** náklady, dokud neodešlete úlohu. Pokud bude tento cluster nečinný po 2400 sekund (40 minut), bude se škálovat dolů.

Vaše struktura složek teď bude vypadat takto:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu instalace máte následující:

- Vytvořil se pracovní prostor Azure Machine Learning.
- Nastavení místního vývojového prostředí
- Vytvořili jste výpočetní cluster Azure Machine Learning.

V dalším kurzu vás provedete odesláním skriptu do Azure Machine Learning výpočetnímu clusteru.

> [!div class="nextstepaction"]
> [Kurz: spuštění skriptu Pythonu "Hello World" v Azure](tutorial-1st-experiment-hello-world.md)
