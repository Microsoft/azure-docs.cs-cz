---
title: Trénovat modely ML s odhady
titleSuffix: Azure Machine Learning
description: Naučte se provádět jednouzdná a distribuovaná školení tradičních modelů strojového učení a hloubkového učení pomocí třídy Azure Machine Learning Estimator
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 678af1855baf52efa727444236de8a1724a7d0b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078480"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Trénování modelů s Azure Machine Learning pomocí odhadu
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Pomocí Azure Machine Learning můžete snadno odeslat trénovací skript do [různých výpočetních cílů](how-to-set-up-training-targets.md#compute-targets-for-training), pomocí [objektu RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) a [objektu ScriptRunConfig](how-to-set-up-training-targets.md#submit). Tento vzor vám dává velkou flexibilitu a maximální kontrolu.

Pro usnadnění školení modelu hlubokého učení poskytuje Sada Python SDK Azure Machine Learning Python alternativní abstrakce vyšší úrovně, třídu odhadu, která uživatelům umožňuje snadno vytvářet konfigurace spuštění. Můžete vytvořit a použít obecný [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odeslání školicího skriptu pomocí libovolného výukového rámce, který zvolíte (například scikit-learn) na libovolném výpočetním cíli, který zvolíte, ať už je to váš místní počítač, jeden virtuální počítač v Azure nebo cluster GPU v Azure. Pro úlohy PyTorch, TensorFlow a Chainer poskytuje Azure Machine Learning také příslušné odhady [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) pro zjednodušení používání těchto architektur.

## <a name="train-with-an-estimator"></a>Vlak s odhadcem

Po vytvoření [pracovního prostoru](concept-workspace.md) a nastavení [vývojového prostředí](how-to-configure-environment.md)zahrnuje školení modelu v Azure Machine Learning následující kroky:  
1. Vytvoření [vzdáleného výpočetního cíle](how-to-set-up-training-targets.md) (všimněte si, že jako výpočetní cíl můžete použít také místní počítač)
2. Nahrání [trénovacích dat](how-to-access-data.md) do úložiště dat (volitelné)
3. Vytvoření [školicího skriptu](tutorial-train-models-with-aml.md#create-a-training-script)
4. Vytvoření objektu `Estimator`
5. Odeslání odhadu do objektu experimentu v pracovním prostoru

Tento článek se zaměřuje na kroky 4-5. Kroky 1-3 naleznete na [příkladu v kurzu pro vlak.](tutorial-train-models-with-aml.md)

### <a name="single-node-training"></a>Školení s jedním uzlem

Použijte `Estimator` pro školení s jedním uzlem na vzdálených výpočetních výkonech v Azure pro model scikit-learn. Měli jste již vytvořit cílový `compute_target` [objekt výpočetního](how-to-set-up-training-targets.md#amlcompute) `ds`prostředí a objekt [FileDataset](how-to-create-register-datasets.md) .

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Tento fragment kódu určuje následující parametry konstruktoru. `Estimator`

Parametr | Popis
--|--
`source_directory`| Místní adresář, který obsahuje veškerý kód potřebný pro úlohu školení. Tato složka se zkopíruje z místního počítače do vzdáleného výpočetního prostředí.
`script_params`| Slovník určující argumenty příkazového řádku, které mají `entry_script`být předávány trénovacímu skriptu , ve formě `<command-line argument, value>` dvojic. Chcete-li zadat podrobný `script_params`příznak `<command-line argument, "">`v , použijte .
`compute_target`| Vzdálený výpočetní cíl, na který se bude spouštět trénovací skript, v tomto případě cluster Azure Machine Learning Compute[(AmlCompute).](how-to-set-up-training-targets.md#amlcompute) (Všimněte si, že i když je cluster AmlCompute běžně používaným cílem, je také možné zvolit jiné typy cílů výpočetních prostředků, jako jsou virtuální počítače Azure nebo dokonce místní počítač.)
`entry_script`| Cesta souboru (vzhledem `source_directory`k ) trénovacího skriptu, který má být spuštěn na vzdáleném výpočetním počítači. Tento soubor a všechny další soubory, na kterých závisí, by měly být umístěny v této složce.
`conda_packages`| Seznam balíčků Pythonu, které mají být nainstalovány prostřednictvím conda potřebného pro váš školicí skript.  

Konstruktor má jiný `pip_packages` parametr, který používáte pro všechny pip balíčky potřebné.

Nyní, když jste `Estimator` vytvořili objekt, odešlete úlohu školení, která `submit` má být spuštěna na vzdáleném výpočetním počítači, s voláním funkce v objektu `experiment` [Experiment](concept-azure-machine-learning-architecture.md#experiments) . 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciální složky** Dvě složky, *výstupy* a *protokoly*, obdrží speciální zacházení azure machine learning. Během školení, když píšete soubory do složek s názvem *výstupy* `./logs`a *protokoly,* které jsou vzhledem k kořenovému adresáři (`./outputs` a , v uvedeném pořadí), soubory se automaticky nahrají do historie spuštění, takže k nim budete mít přístup po dokončení spuštění.
>
> Chcete-li vytvořit artefakty během trénování (například soubory modelu, kontrolní `./outputs` body, datové soubory nebo vykreslené obrázky), zapište je do složky.
>
> Podobně můžete zapsat všechny protokoly z trénovacího běhu do `./logs` složky. Chcete-li využít [integrace Tentenzonboard](https://aka.ms/aml-notebook-tb) azure machine learningu, ujistěte se, že do této složky napíšete protokoly TensorBoard. Během spuštění budete moci spustit TensorBoard a streamovat tyto protokoly.  Později budete také moci obnovit protokoly z některého z předchozích spuštění.
>
> Chcete-li například stáhnout soubor zapsaný do složky *výstupy* do místního počítače po vzdáleném trénovacím spuštění:`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuované školení a vlastní iimage Dockeru

Existují dva další scénáře školení, které `Estimator`můžete provést pomocí :
* Použití vlastní image Dockeru
* Distribuované školení v clusteru s více uzlovacími aplikacemi

Následující kód ukazuje, jak provádět distribuované školení pro model Keras. Kromě toho místo použití výchozí image Azure Machine Learning určuje vlastní image `continuumio/miniconda` dockeru z Docker Hubu pro školení.

Měli jste již vytvořit cílový `compute_target`objekt výpočetní ch [odjaku](how-to-set-up-training-targets.md#amlcompute) . Odhad vytvoříte následujícím způsobem:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

Výše uvedený kód zpřístupňuje konstruktoru `Estimator` následující nové parametry:

Parametr | Popis | Výchozí
--|--|--
`custom_docker_image`| Název obrázku, který chcete použít. Poskytněte jenom image dostupná ve veřejných úložištích dockeru (v tomto případě Docker Hub). Chcete-li použít bitovou kopii z úložiště privátního dockeru, použijte místo toho parametr konstruktoru. `environment_definition` [Viz příklad](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Počet uzlů, které chcete použít pro vaši tréninkovou práci. | `1`
`process_count_per_node`| Počet procesů (nebo "pracovníků") ke spuštění v každém uzlu. V takovém případě použijete gpu, které `2` jsou k dispozici na každém uzlu.| `1`
`distributed_training`| [OBJEKT MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) pro spuštění distribuovaného školení pomocí back-endu MPI.  | `None`


Nakonec předloží školení:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registrace modelu

Po vyškolení modelu jej můžete uložit a zaregistrovat do pracovního prostoru. Registrace modelu umožňuje ukládat a nakládat s modely v pracovním prostoru a zjednodušit [tak správu a nasazení modelu](concept-model-management-and-deployment.md).

Spuštěním následujícího kódu zaregistrujete model do pracovního prostoru a zpřístupníte jej k odkazu podle názvu ve vzdálených výpočetních kontextech nebo skriptech nasazení. Další [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) informace a další parametry naleznete v referenčních dokumentech.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub sledování a integrace

Když spustíte trénovací běh, kde zdrojový adresář je místní úložiště Git, informace o úložišti jsou uloženy v historii spuštění. Další informace najdete v [tématu Integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Příklady
Poznámkový blok, který zobrazuje základy vzoru odhadu, najdete v následujících tématech:
* [jak použít azureml/školení s hloubkovým učením/odhadem použití](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

U poznámkového bloku, který trénuje model scikit-learn pomocí odhadu, najdete v tématu:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

U poznámkových bloků na trénovacích modelech pomocí specifických odhadů pro hluboké učení se rámci naleznete v tématu:

* [jak použít azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* [Sledovat metriky běhu během tréninku](how-to-track-experiments.md)
* [Trénování modelů PyTorch](how-to-train-pytorch.md)
* [Trénování modelů TensorFlow](how-to-train-tensorflow.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
* [Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md)