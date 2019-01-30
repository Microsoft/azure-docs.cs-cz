---
title: Trénování modelů s PyTorch
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak spustit jeden uzel nebo pro distribuované trénování modelů PyTorch s PyTorch estimator
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e5528cdfc2efa2d5c257732c8b6b6df117421839
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250023"
---
# <a name="train-pytorch-models-with-azure-machine-learning-service"></a>Trénování modelů PyTorch službou Azure Machine Learning

Pro výuku hluboké neuronové sítě (DNN) s využitím PyTorch, Azure Machine Learning nabízí vlastní `PyTorch` třídu `Estimator`. Azure SDK `PyTorch` estimator vám umožní snadno odesílat úlohy trénování PyTorch pro spuštění jedním uzlem a distribuované na výpočetní prostředky Azure.

## <a name="single-node-training"></a>Trénování jedním uzlem
Školení s `PyTorch` odhad je podobný používání [základní `Estimator` ](how-to-train-ml-models.md), proto nejdřív přečíst článek s postupy a ujistěte se, že rozumíte konceptům uvedeným existuje.
  
Ke spuštění úlohy PyTorch, vytvořit instanci `PyTorch` objektu. Měli jste již vytvořili vaše [cílové výpočetní prostředí](how-to-set-up-training-targets.md#amlcompute) objekt `compute_target` a [úložiště dat](how-to-access-data.md) objekt `ds`.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

Jsme zde, zadejte následující parametry pro konstruktor PyTorch:
Parametr | Popis
--|--
`source_directory` |  Místní adresář, který obsahuje vše potřebné pro trénovací úlohu kódu. Tato složka se zkopíruje z místního počítače pro vzdálený výpočetní
`script_params` |  Slovník zadání argumentů příkazového řádku pro cvičný skript `entry_script`, ve formě < argument příkazového řádku, hodnota > páry
`compute_target` |  Cílové vzdálené výpočetní prostředí, který cvičný skript se spustí, v tomto případě Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) clusteru
`entry_script` |  Cesta k souboru (vzhledem k `source_directory`) z trénovací skript ke spuštění na vzdálené výpočetní prostředky. Tento soubor a další soubory, na kterých závisí, se musí nacházet v této složce
`conda_packages` |  Seznam balíčků Python nainstalovat přes conda vyžadované cvičný skript. Konstruktor má jiný parametr s názvem `pip_packages` , můžete použít pro všechny balíčky pip potřeby
`use_gpu` |  Tento příznak nastavit `True` využití GPU pro vzdělávání. Výchozí hodnota je `False`

Vzhledem k tomu, že používáte `PyTorch` odhaduje, kontejner použitý k trénování zahrne PyTorch balíčku a související závislosti nutné ke školení na procesory a grafickými procesory.

Pak odešlete úlohu PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Distribuované trénování
`PyTorch` Estimator můžete použít k trénování modelů ve velkém měřítku napříč clustery CPU a GPU virtuálních počítačů Azure. Můžete snadno spouštět distribuované trénování PyTorch se několik volání rozhraní API, zatímco bude Azure Machine Learning spravovat na pozadí, infrastruktury a potřeby provádět tyto úlohy Orchestrace.

Azure Machine Learning v současné době podporuje MPI distribuované tréninku na základě sady pomocí rozhraní Horovod PyTorch.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) je kanál – allreduce open source platforma pro distribuované trénování vypracovanou organizací cccppf Uber.

Ke spuštění distribuovaných PyTorch pomocí rozhraní Horovod, vytvořte objekt PyTorch následujícím způsobem:

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

Tento kód zveřejňuje následující nové parametry pro konstruktor PyTorch:
Parametr | Popis | Výchozí
--|--|--
`node_count` |  Počet uzlů pro trénovací úlohu. | `1`
`process_count_per_node` |  Počet procesů (nebo "pracovníky") pro spuštění na každý uzel. | `1`
`distributed_backend` |  Back-endu pro spouštění distribuovaných školení, která nabízí odhadu prostřednictvím MPI.  K provádění paralelních nebo distribuované trénování (třeba `node_count`> 1 nebo `process_count_per_node`> 1 nebo obě) MPI (a Horovod), nastavte `distributed_backend='mpi'`. Implementace MPI používá technologii Azure Machine Learning je [otevřít MPI](https://www.open-mpi.org/). | `None`

Výše uvedený příklad spustí distribuované trénování s dva pracovní procesy, jeden pracovní proces na jeden uzel.

Horovod a jeho závislosti se nainstaluje za vás, takže ho můžete jednoduše importovat v cvičný skript `train.py` následujícím způsobem:
```Python
import torch
import horovod
```

Nakonec odeslání distribuované PyTorch úlohy:
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>Příklady

Poznámkové bloky v distribuované obsáhlého learningu naleznete v tématu:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup
* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
