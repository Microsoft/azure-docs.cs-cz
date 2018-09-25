---
title: Trénování modelů strojového učení pomocí Azure Machine Learning
description: Zjistěte, jak provést jeden uzel nebo pro distribuované trénování tradiční strojové učení a obsáhlý learning modely pomocí služby Azure Machine Learning
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983578"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Trénování modelů Azure Machine Learning

Trénování modelů strojového učení, zejména hluboké neuronové sítě, je často náročné na čas a výpočetní úlohy. Po dokončení zápisu cvičný skript a běží na malou podmnožinu dat na místním počítači, bude pravděpodobně chcete vertikálně navýšit kapacitu úloh.

Pro usnadnění vzdělávání, Azure Machine Learning Python SDK poskytuje vysokou úroveň abstrakce, Estimator třídy, která umožňuje uživatelům snadné trénování svých modelů v ekosystému Azure. Můžete vytvořit a použít objekt Estimator odesílat žádný školení kód, který chcete spustit na vzdálený výpočetní, ať už jde o napříč clusteru GPU s jedním uzlem spuštění nebo distribuované trénování. Pro úlohy PyTorch a TensorFlow Azure Machine Learning poskytuje také odpovídající vlastní PyTorch a odhady TensorFlow, které usnadňují použití těchto rozhraní.

## <a name="train-with-an-estimator"></a>Trénování s odhadu

Po vytvoření vaší [pracovní prostor](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace) a nastavit váš [vývojové prostředí](how-to-configure-environment.md), trénování modelu ve službě Azure Machine Learning zahrnuje následující kroky:  
1. [Vytvořte vzdálené výpočetní cíl](how-to-set-up-training-targets.md)
2. [Nahrát trénovacích dat](how-to-access-data.md) (volitelné)
3. Vytvoření trénovací skript
4. Vytvoření objektu odhad
5. Odeslání úlohy trénování

Tento článek se zaměřuje na kroky 4 až 5. Kroky 1 až 3, najdete to [kurzu](tutorial-train-models-with-aml.md) příklad.

### <a name="single-node-training"></a>Trénování jedním uzlem

Následující kód provede jedním uzlem školení spouštět vzdálený výpočetní prostředky v Azure pro scikit-informace modelu. Měli jste již vytvořili vaše [cílové výpočetní prostředí](how-to-set-up-training-targets.md#batch) objekt `compute_target` a [úložiště dat](how-to-access-data.md) objekt `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Výše uvedeném fragmentu kódu určuje následující parametry pro konstruktor Estimator:
* `source_directory`: Místní adresář, který obsahuje vše potřebné pro trénovací úlohu kódu. Tato složka se zkopíruje z místního počítače pro vzdálený výpočetní 
* `script_params`: Zadání argumentů příkazového řádku pro cvičný skript slovník `entry_script`, ve formě < argument příkazového řádku, hodnota > páry
* `compute_target`: Vzdálený výpočetní cvičný skript se spustí, v tomto případě [služby Batch AI](how-to-set-up-training-targets.md#batch) clusteru
* `entry_script`: Cesta k souboru (relativně k `source_directory`) z trénovací skript ke spuštění na vzdálené výpočetní prostředky. Tento soubor a další soubory, na kterých závisí, se musí nacházet v této složce
* `conda_packages`: Seznam balíčků Python nainstalovat přes conda vyžadované cvičný skript.  
Konstruktor má jiný parametr s názvem `pip_packages` , můžete použít pro všechny balíčky pip potřeby

Teď, když jste vytvořili svůj odhad objekt, můžete odeslat úlohu učení k použití na vzdálený výpočetní prostřednictvím volání `submit` fungovat na vaše [Experiment](concept-azure-machine-learning-architecture.md#experiment) objekt `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Speciální složky** dvě složky *výstupy* a *protokoly*, zvláštní zacházení technologii Azure Machine Learning. Při školení, když zapíšete soubory do složky s názvem *výstupy* a *protokoly* , které jsou relativní vzhledem k kořenový adresář (`./outputs` a `./logs`v uvedeném pořadí), se zobrazí tyto soubory automaticky nahraje vaše historie spouštění tak, že budete mít přístup k nim po dokončení spuštění. 
>
> Pro přístup k zápisu artefakty vytvořené během cvičení (například soubory modelu kontrolní body, datové soubory, Image nebo možná vykreslená) do `./outputs` složky.
>
> Podobně můžete napsat všechny protokoly z trénování spustit `./logs` složky. Aby se začala používat Azure Machine Learning [TensorBoard integrace](https://aka.ms/aml-notebook-tb) Ujistěte se, že napíšete TensorBoard protokolů do této složky. Během spuštění, bude moct spustit TensorBoard a streamování tyto protokoly.  Později budou také budete moci obnovit protokoly z některého z předchozích spuštění.
>
> Například stáhnout soubor zapsán do *výstupy* složky do místního počítače po trénování vzdálené spuštění: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuované trénování a vlastní Image Dockeru

Existují dva další školicí scénáře, které můžete provádět pomocí odhadu:
1. Použití vlastní image Dockeru
2. Distribuované trénování na clusteru s několika uzly

Následující kód ukazuje, jak provádět distribuované trénování CNTK modelu. Kromě toho místo použití výchozí Image Azure Machine Learning, předpokládá se, že máte vlastní vlastní image dockeru, který chcete použít k trénování.

Měli jste již vytvořili vaše [cílové výpočetní prostředí](how-to-set-up-training-targets.md#batch) objekt `compute_target`. Můžete vytvořit odhadu takto:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

Výše uvedený kód zveřejňuje následující nové parametry pro konstruktor Estimator:
* `custom_docker_base_image`: Název image, kterou chcete použít. Můžete poskytnout Image v docker veřejných úložišť (v tomto případě Docker Hubu). Chcete-li použít některou image z docker privátního úložiště, použijte konstruktoru `environment_definition` parametr místo
* `node_count`: Počet uzlů pro trénovací úlohu. Výchozí hodnota tohoto argumentu `1`
* `process_count_per_node`: Počet procesů (nebo "pracovníky") pro spuštění na každý uzel. V tomto případě použijete `2` grafickými procesory, které jsou k dispozici na všech uzlech. Výchozí hodnota tohoto argumentu `1`
* `distributed_backend`: Back-endem pro spouštění distribuovaných školení, která nabízí odhadu prostřednictvím MPI. Výchozí hodnota tohoto argumentu `None`. Pokud chcete provést paralelní nebo distribuované trénování (třeba `node_count`> 1 nebo `process_count_per_node`> 1 nebo oba), nastavte `distributed_backend='mpi'`. Je MPI implementace používaná třídou AML [otevřít MPI](https://www.open-mpi.org/).

Nakonec odešlete úlohu učení:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Příklady
Kurz předvádějící trénujete model pro skriptu sklearn najdete v tématu:
* `tutorials/01.train-models.ipynb`

Kurz týkající se distribuované CNTK pomocí vlastní dockeru naleznete v tématu:
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

Získejte tyto poznámkové bloky:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Trénování modelů PyTorch](how-to-train-pytorch.md)
* [Trénování TensorFlow modelů](how-to-train-tensorflow.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
