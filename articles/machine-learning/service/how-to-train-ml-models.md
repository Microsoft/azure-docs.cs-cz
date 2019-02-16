---
title: Trénování modelů ML s odhady
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak provést jeden uzel nebo pro distribuované trénování tradiční strojové učení a obsáhlý learning modely s využitím Azure Machine Learning services Estimator třídy
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 2/14/2019
ms.custom: seodec18
ms.openlocfilehash: 58dd96b079dda50faa17a52782a79db83a0141bd
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330065"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Trénování modelů Azure Machine Learning pomocí odhad

Pomocí služby Azure Machine Learning můžete snadno odeslat skript školení k [různých cílových výpočetních prostředí](how-to-set-up-training-targets.md#compute-targets-for-training)s použitím [RunConfiguration objekt](how-to-set-up-training-targets.md#whats-a-run-configuration) a [ScriptRunConfig objekt](how-to-set-up-training-targets.md#submit). Tento vzor vám přináší značnou flexibilitu a maximální ovládacího prvku.

Pro usnadnění hloubkového učení tréninku modelu, Azure Machine Learning Python SDK poskytuje alternativní vyšší úrovni abstrakce, estimator třídy, která umožňuje uživatelům snadno konstrukce spuštění konfigurace. Můžete vytvořit a použít obecný [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) odeslat cvičný skript pomocí jakoukoli architekturu learningu zvolíte (například scikit-informace) chcete spustit na všechny cílové výpočetní prostředí, ať už jde o místní počítač, jeden virtuální počítač v Azure nebo grafického procesoru cluster v Azure. Pro úlohy PyTorch a TensorFlow, Chainer, Azure Machine Learning poskytuje také příslušné [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) a [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) odhady pro zjednodušení použití těchto rozhraní.

## <a name="train-with-an-estimator"></a>Trénování s odhadu

Po vytvoření vaší [pracovní prostor](concept-azure-machine-learning-architecture.md#workspace) a nastavit váš [vývojové prostředí](how-to-configure-environment.md), trénování modelu ve službě Azure Machine Learning zahrnuje následující kroky:  
1. Vytvoření [cílové výpočetní prostředí vzdálené](how-to-set-up-training-targets.md) (Poznámka: místního počítače můžete také použít jako cílové výpočetní prostředí)
2. Nahrajte vaše [trénovacích dat](how-to-access-data.md) do úložiště dat (volitelné)
3. Vytvoření vašeho [cvičný skript](tutorial-train-models-with-aml.md#create-a-training-script)
4. Vytvoření `Estimator` objektu
5. Odeslat odhad na objekt experiment v části pracovní prostor

Tento článek se zaměřuje na kroky 4 až 5. Kroky 1 až 3, najdete [trénování modelu kurzu](tutorial-train-models-with-aml.md) příklad.

### <a name="single-node-training"></a>Trénování jedním uzlem

Použití `Estimator` pro trénování jedním uzlem, spouštět vzdálený výpočetní prostředky v Azure pro scikit-informace modelu. Měli jste již vytvořili vaše [cílové výpočetní prostředí](how-to-set-up-training-targets.md#amlcompute) objekt `compute_target` a [úložiště dat](how-to-access-data.md) objekt `ds`.

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

Tento fragment kódu určuje následující parametry `Estimator` konstruktoru.

Parametr | Popis
--|--
`source_directory`| Místní adresář, který obsahuje vše potřebné pro trénovací úlohu kódu. Tato složka se zkopíruje z místního počítače pro vzdálený výpočetní 
`script_params`| Slovník zadání argumentů příkazového řádku pro cvičný skript `entry_script`, ve formě < argument příkazového řádku, hodnota > páry
`compute_target`| Cílové vzdálené výpočetní prostředí, který cvičný skript se spustí, v tomto případě Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) clusteru. (Poznámka: Přestože AmlCompute clusteru je běžně používané cílem, je také možné vybrat další výpočetní cílové typy, jako jsou virtuální počítače Azure nebo dokonce místní počítač.)
`entry_script`| Cesta k souboru (vzhledem k `source_directory`) z trénovací skript ke spuštění na vzdálené výpočetní prostředky. Tento soubor a další soubory, na kterých závisí, se musí nacházet v této složce
`conda_packages`| Seznam balíčků Python nainstalovat přes conda vyžadované cvičný skript.  
Konstruktor má jiný parametr s názvem `pip_packages` , který používáte pro všechny balíčky pip potřeby

Teď, když jste vytvořili vaší `Estimator` objektu, odešlete úlohu školení k použití na vzdálený výpočetní voláním `submit` fungovat na vašich [Experiment](concept-azure-machine-learning-architecture.md#experiment) objekt `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciální složky** dvě složky *výstupy* a *protokoly*, zvláštní zacházení technologii Azure Machine Learning. Během cvičení, při zápisu do složky s názvem souborů *výstupy* a *protokoly* , které jsou relativní vzhledem k kořenový adresář (`./outputs` a `./logs`v uvedeném pořadí), soubory se automaticky Nahrajte vaše historie spuštění, abyste měli přístup k nim po dokončení spuštění.
>
> Chcete-li vytvořit artefakty během cvičení (například soubory modelu kontrolní body, datové soubory, Image nebo možná vykreslená) zapisovat do `./outputs` složky.
>
> Podobně můžete napsat všechny protokoly z trénování spustit `./logs` složky. Aby se začala používat Azure Machine Learning [TensorBoard integrace](https://aka.ms/aml-notebook-tb) Ujistěte se, že napíšete TensorBoard protokolů do této složky. Během spuštění, bude moct spustit TensorBoard a streamování tyto protokoly.  Později budou také budete moci obnovit protokoly z některého z předchozích spuštění.
>
> Například stáhnout soubor zapsán do *výstupy* složky do místního počítače po trénování vzdálené spuštění: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuované trénování a vlastní Image Dockeru

Existují dva další školicí scénáře, které se provádějí pomocí `Estimator`:
* Použití vlastní image Dockeru
* Distribuované trénování na clusteru s několika uzly

Následující kód ukazuje, jak provádět distribuované trénování modelu Keras. Kromě toho místo použití výchozí Image Azure Machine Learning, určuje docker vlastní image z Docker Hubu `continuumio/miniconda` pro vzdělávání.

Měli jste již vytvořili vaše [cílové výpočetní prostředí](how-to-set-up-training-targets.md#amlcompute) objekt `compute_target`. Vytvoření odhadu následujícím způsobem:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_base_image='continuumio/miniconda')
```

Výše uvedený kód poskytuje následující nové parametry pro `Estimator` konstruktor:

Parametr | Popis | Výchozí
--|--|--
`custom_docker_base_image`| Název image, kterou chcete použít. Zadejte jenom Image v docker veřejných úložišť (v tomto případě Docker Hubu). Chcete-li použít některou image z docker privátního úložiště, použijte konstruktoru `environment_definition` parametr místo. [Viz příklad](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Počet uzlů pro trénovací úlohu. | `1`
`process_count_per_node`| Počet procesů (nebo "pracovníky") pro spuštění na každý uzel. V tomto případě použijete `2` grafickými procesory, které jsou k dispozici na všech uzlech.| `1`
`distributed_backend`| Back-endu pro spouštění distribuovaných školení, která nabízí odhadu prostřednictvím MPI.  K provádění paralelních nebo distribuované trénování (třeba `node_count`> 1 nebo `process_count_per_node`> 1 nebo oba), nastavte `distributed_backend='mpi'`. Je MPI implementace používaná třídou AML [otevřít MPI](https://www.open-mpi.org/).| `None`

Nakonec odešlete úlohu učení:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="examples"></a>Příklady
Poznámkový blok, který zobrazuje základní informace o odhadu vzor naleznete v tématu:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Poznámkový blok, který trénovat scikit-informace modelu pomocí odhad, naleznete v tématu:
* [kurzy/img – klasifikace – část 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Poznámkové bloky na trénování modelů pomocí konkrétní odhady architekturu obsáhlého learningu naleznete v tématu:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Trénování modelů PyTorch](how-to-train-pytorch.md)
* [Trénování TensorFlow modelů](how-to-train-tensorflow.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
