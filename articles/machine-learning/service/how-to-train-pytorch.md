---
title: Trénování modelů PyTorch službou Azure Machine Learning
description: Zjistěte, jak spustit jeden uzel nebo pro distribuované trénování modelů PyTorch s PyTorch estimator
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e569b63f676fb750bcbab88dda6cda39156d41f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977024"
---
# <a name="how-to-train-pytorch-models"></a>Trénování modelů PyTorch

Pro výuku hluboké neuronové sítě (DNN) s využitím PyTorch, Azure Machine Learning poskytuje vlastní třídu PyTorch odhadu. Odhad PyTorch sady Azure SDK vám umožní snadno odesílat úlohy trénování PyTorch pro spuštění jedním uzlem a distribuované na výpočetní prostředky Azure.

## <a name="single-node-training"></a>Trénování jedním uzlem
Školení s PyTorch Estimator je podobný používání [základní Estimator](how-to-train-ml-models.md), proto nejdřív přečíst článek s postupy a ujistěte se, že rozumíte konceptům uvedeným existuje.
  
Ke spuštění úlohy PyTorch, vytvořit instanci `PyTorch` objektu. Měli jste již vytvořili vaše [cílové výpočetní prostředí](how-to-set-up-training-targets.md#batch) objekt `compute_target` a [úložiště dat](how-to-access-data.md) objekt `ds`.

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
* `source_directory`: Místní adresář, který obsahuje vše potřebné pro trénovací úlohu kódu. Tato složka se zkopíruje z místního počítače pro vzdálený výpočetní
* `script_params`: Zadání argumentů příkazového řádku pro cvičný skript slovník `entry_script`, ve formě < argument příkazového řádku, hodnota > páry
* `compute_target`: Vzdálený výpočetní cvičný skript se spustí, v tomto případě [služby Batch AI](how-to-set-up-training-targets.md#batch) clusteru
* `entry_script`: Cesta k souboru (relativně k `source_directory`) z trénovací skript ke spuštění na vzdálené výpočetní prostředky. Tento soubor a další soubory, na kterých závisí, se musí nacházet v této složce
* `conda_packages`: Seznam balíčků Python nainstalovat přes conda vyžadované cvičný skript.
Konstruktor má jiný parametr s názvem `pip_packages` , můžete použít pro všechny balíčky pip potřeby
* `use_gpu`: Tento příznak nastavit `True` využití GPU pro vzdělávání. Výchozí hodnota je `False`

Vzhledem k tomu, že používáte PyTorch odhad, bude použita výchozí kontejner použitý k trénování zahrnout PyTorch balíček a související závislosti nutné ke školení na procesory a grafickými procesory.

Pak odešlete úlohu PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Distribuované trénování
Odhad PyTorch také umožňuje trénování modelů ve velkém měřítku napříč clustery CPU a GPU virtuálních počítačů Azure. Můžete snadno spouštět distribuované trénování PyTorch se několik volání rozhraní API, zatímco bude Azure Machine Learning spravovat na pozadí, infrastruktury a potřeby provádět tyto úlohy Orchestrace.

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

Výše uvedený kód zveřejňuje následující nové parametry pro konstruktor PyTorch:
* `node_count`: Počet uzlů pro trénovací úlohu. Výchozí hodnota tohoto argumentu `1`
* `process_count_per_node`: Počet procesů (nebo "pracovníky") pro spuštění na každý uzel. Výchozí hodnota tohoto argumentu `1`
* `distributed_backend`: Back-endem pro spouštění distribuovaných školení, která nabízí odhadu prostřednictvím MPI. Výchozí hodnota tohoto argumentu `None`. Pokud chcete provést paralelní nebo distribuované trénování (třeba `node_count`> 1 nebo `process_count_per_node`> 1 nebo obě) MPI (a Horovod), nastavte `distributed_backend='mpi'`. Implementace MPI používá technologii Azure Machine Learning je [otevřít MPI](https://www.open-mpi.org/).

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
Kurz týkající se školení PyTorch jedním uzlem naleznete v tématu:
* `training/01.train-tune-deploy-pytorch/01.train-tune-deploy-pytorch.ipynb`

Kurz týkající se distribuované PyTorch s Horovod naleznete v tématu:
* `training/02.distributed-pytorch-with-horovod/02.distributed-pytorch-with-horovod.ipynb`

Získejte tyto poznámkové bloky:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup
* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
