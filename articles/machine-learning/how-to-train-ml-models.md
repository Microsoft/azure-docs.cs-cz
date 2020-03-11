---
title: Modely vlakových ML pomocí odhady
titleSuffix: Azure Machine Learning
description: Naučte se provádět jednoduché a distribuované školení tradičních modelů strojového učení a hloubkového učení pomocí Azure Machine Learning třídy Estimator
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
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79078480"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Výuka modelů pomocí Azure Machine Learning s využitím Estimator
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Pomocí Azure Machine Learning můžete snadno odeslat školicí skript do [různých výpočetních cílů](how-to-set-up-training-targets.md#compute-targets-for-training), a to pomocí [objektu RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) a [objektu ScriptRunConfig](how-to-set-up-training-targets.md#submit). Tento model poskytuje značnou flexibilitu a maximální kontrolu.

Aby se usnadnilo školení modelu hloubkového učení, Azure Machine Learning Python SDK poskytuje alternativní abstrakci vyšší úrovně, třídu Estimator, která umožňuje uživatelům snadno sestavit konfigurace spuštění. Můžete vytvořit a použít obecné [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odeslání školicího skriptu pomocí libovolného vzdělávacího rozhraní, které zvolíte (například scikit-učení) na jakémkoli cílovém výpočetním prostředí, ať už se jedná o místní počítač, jeden virtuální počítač v Azure nebo cluster GPU v Azure. Pro PyTorch, TensorFlow a úlohy zřetězení Azure Machine Learning poskytuje také příslušné [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chain](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) odhady pro zjednodušení používání těchto rozhraní.

## <a name="train-with-an-estimator"></a>Trénování s odhadu

Po vytvoření [pracovního prostoru](concept-workspace.md) a nastavení [vývojového prostředí](how-to-configure-environment.md)v Azure Machine Learning zahrnuje následující kroky:  
1. Vytvořit [vzdálený cíl výpočtů](how-to-set-up-training-targets.md) (Poznámka: můžete použít také místní počítač jako cíl výpočtů)
2. Nahrajte [školicí data](how-to-access-data.md) do úložiště dat (volitelné).
3. Vytvoření [školicího skriptu](tutorial-train-models-with-aml.md#create-a-training-script)
4. Vytvoření objektu `Estimator`
5. Odeslání Estimator do objektu experimentu v pracovním prostoru

Tento článek se zaměřuje na kroky 4 až 5. Postup 1-3 najdete v [kurzu o výukovém](tutorial-train-models-with-aml.md) programu pro model.

### <a name="single-node-training"></a>Trénování jedním uzlem

Použití `Estimator` pro školení s jedním uzlem se spouští ve vzdálené výpočetní službě Azure pro model scikit-učení. Měli byste už mít vytvořený objekt [cíle výpočtů](how-to-set-up-training-targets.md#amlcompute) `compute_target` a objekt [DataSet](how-to-create-register-datasets.md) `ds`.

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

Tento fragment kódu určuje následující parametry konstruktoru `Estimator`.

Parametr | Popis
--|--
`source_directory`| Místní adresář, který obsahuje vše potřebné pro trénovací úlohu kódu. Tato složka se zkopíruje z místního počítače do vzdálené výpočetní služby.
`script_params`| Slovník určující argumenty příkazového řádku, které se mají předat vašemu školicímu skriptu `entry_script`ve formě dvojice `<command-line argument, value>` Chcete-li zadat příznak verbose v `script_params`, použijte `<command-line argument, "">`.
`compute_target`| Vzdálený výpočetní cíl, na kterém se váš školicí skript spustí, v tomto případě cluster Azure Machine Learning COMPUTE ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Pamatujte na to, že cluster AmlCompute se běžně používá, je také možné zvolit jiné typy výpočetních cílů, jako jsou například virtuální počítače Azure nebo i místní počítač.)
`entry_script`| FilePath (vzhledem k `source_directory`) školicího skriptu, který se má spustit na vzdáleném výpočetním prostředí. Tento soubor a všechny další soubory, na kterých závisí, by měly být umístěny v této složce.
`conda_packages`| Seznam balíčků Python nainstalovat přes conda vyžadované cvičný skript.  

Konstruktor má jiný parametr nazvaný `pip_packages`, který používáte pro všechny potřebné balíčky PIP.

Teď, když jste vytvořili objekt `Estimator`, odešlete úlohu školení, která se spustí ve vzdáleném výpočetním prostředí, a voláním funkce `submit` na svém objektu [experimentu](concept-azure-machine-learning-architecture.md#experiments) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciální složky** Dvě složky, *výstupy* a *protokoly*dostanou zvláštní zacházení Azure Machine Learning. Když při výuce zapisujete soubory do složek s názvem *výstupy* a *protokoly* , které jsou relativní vzhledem ke kořenovému adresáři (`./outputs` a `./logs`, v uvedeném pořadí), soubory se automaticky nahrají do historie spuštění, abyste k nim měli přístup až po dokončení běhu.
>
> Chcete-li během školení vytvořit artefakty (například soubory modelů, kontrolní body, datové soubory nebo vykreslené bitové kopie), zapište je do složky `./outputs`.
>
> Podobně můžete v rámci školicích běhů zapisovat libovolné protokoly do složky `./logs`. Aby bylo možné využívat Azure Machine Learning [integrace TensorBoard](https://aka.ms/aml-notebook-tb) , nezapomeňte zapisovat do této složky protokoly TensorBoard. Během spuštění, bude moct spustit TensorBoard a streamování tyto protokoly.  Později budou také budete moci obnovit protokoly z některého z předchozích spuštění.
>
> Například pro stažení souboru zapsaného do složky *výstupy* do místního počítače po spuštění vzdáleného školení: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuované trénování a vlastní Image Dockeru

Existují dva další školicí scénáře, které můžete provádět s `Estimator`:
* Použití vlastní image Dockeru
* Distribuované trénování na clusteru s několika uzly

Následující kód ukazuje, jak provést distribuované školení pro model Keras. Kromě toho se místo používání výchozích Azure Machine Learning imagí určuje vlastní image Docker z Docker Hub `continuumio/miniconda` pro školení.

Měli byste už mít vytvořený objekt [cíle výpočetního](how-to-set-up-training-targets.md#amlcompute) objektu `compute_target`. Vytvoření odhadu následujícím způsobem:

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

Výše uvedený kód zpřístupňuje následující nové parametry konstruktoru `Estimator`:

Parametr | Popis | Výchozí
--|--|--
`custom_docker_image`| Název image, kterou chcete použít. Zadejte jenom Image v docker veřejných úložišť (v tomto případě Docker Hubu). Pokud chcete použít image z privátního úložiště Docker, použijte místo toho parametr `environment_definition` konstruktoru. [Viz příklad](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Počet uzlů pro trénovací úlohu. | `1`
`process_count_per_node`| Počet procesů (nebo "pracovníky") pro spuštění na každý uzel. V takovém případě použijete `2` GPU dostupné na každém uzlu.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objekt pro spouštění distribuovaného školení pomocí back-endu MPI.  | `None`


Nakonec odešlete úlohu učení:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registrace modelu

Po proškolení modelu ho můžete uložit a zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

Spuštění následujícího kódu zaregistruje model do vašeho pracovního prostoru a zpřístupní ho pro referenci podle názvu ve vzdálených výpočetních kontextech nebo ve skriptech nasazení. Další informace a další parametry naleznete v tématu [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) v referenční dokumentaci.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Sledování a integrace GitHubu

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Příklady
Poznámkový blok, který zobrazuje základy Estimator vzoru, najdete v těchto tématech:
* [Postupy: použití-AzureML/školení – s využitím hloubkového učení/postupy-použití-Estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Pro Poznámkový blok, který používá scikit model pomocí Estimator, se podívejte na:
* [kurzy/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Poznámkové bloky na školicích modelech pomocí odhady specifických pro rozsáhlou výuku najdete v tématech:

* [Postupy: použití-AzureML/ml – rozhraní](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Výukové modely PyTorch](how-to-train-pytorch.md)
* [Výukové modely TensorFlow](how-to-train-tensorflow.md)
* [Ladit parametry](how-to-tune-hyperparameters.md)
* [Nasazení trained model](how-to-deploy-and-where.md)
* [Vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md)