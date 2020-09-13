---
title: Modely vlakových ML pomocí odhady
titleSuffix: Azure Machine Learning
description: Naučte se provádět jednoduché a distribuované školení tradičních modelů strojového učení a hloubkového učení pomocí Azure Machine Learning třídy Estimator
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 69987210d69855b0fcaa676e406ec6a1c02a4d85
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650604"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Výuka modelů pomocí Azure Machine Learning s využitím Estimator
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Pomocí Azure Machine Learning můžete snadno odeslat školicí skript do [různých výpočetních cílů](how-to-set-up-training-targets.md), a to pomocí [objektu RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) a [objektu ScriptRunConfig](how-to-set-up-training-targets.md#submit). Tento model poskytuje značnou flexibilitu a maximální kontrolu.


Třída Estimator usnadňuje výuce modelů pomocí obsáhlého učení a posílení učení. Poskytuje abstrakci vysoké úrovně, která umožňuje snadno vytvořit konfiguraci spuštění. Můžete vytvořit a použít obecné [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py&preserve-view=true) k odeslání školicího skriptu pomocí libovolného vzdělávacího rozhraní, které zvolíte (například scikit-učení) na jakémkoli cílovém výpočetním prostředí, ať už se jedná o místní počítač, jeden virtuální počítač v Azure nebo cluster GPU v Azure. Pro PyTorch, TensorFlow, řetězení a posílení výukových úkolů Azure Machine Learning také poskytuje odpovídající [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py&preserve-view=true), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true), [chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py&preserve-view=true)a [výztuže Learning](how-to-use-reinforcement-learning.md) odhady, aby bylo možné tyto architektury zjednodušit.

## <a name="train-with-an-estimator"></a>Výuka s Estimator

Po vytvoření [pracovního prostoru](concept-workspace.md) a nastavení [vývojového prostředí](how-to-configure-environment.md)v Azure Machine Learning zahrnuje následující kroky:  
1. Vytvořte [vzdálený cíl výpočtů](how-to-create-attach-compute-sdk.md) (nebo můžete použít také místní počítač jako cíl výpočtů).
2. Nahrajte [školicí data](how-to-access-data.md) do úložiště dat (volitelné).
3. Vytvoření [školicího skriptu](tutorial-train-models-with-aml.md#create-a-training-script)
4. Vytvoření objektu `Estimator`
5. Odeslání Estimator do objektu experimentu v pracovním prostoru

Tento článek se zaměřuje na kroky 4-5. Postup 1-3 najdete v [kurzu o výukovém](tutorial-train-models-with-aml.md) programu pro model.

### <a name="single-node-training"></a>Školení s jedním uzlem

Použijte `Estimator` pro školení s jedním uzlem spuštění ve vzdálené výpočetní službě Azure pro model scikit-učení. Měli byste už mít vytvořený objekt [cíle výpočetního](how-to-create-attach-compute-sdk.md#amlcompute) objektu `compute_target` a objekt [DataSet](how-to-create-register-datasets.md) `ds` .

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

Tento fragment kódu určuje následující parametry `Estimator` konstruktoru.

Parametr | Popis
--|--
`source_directory`| Místní adresář, který obsahuje veškerý váš kód potřebný pro školicí úlohu. Tato složka se zkopíruje z místního počítače do vzdálené výpočetní služby.
`script_params`| Slovník, který určuje argumenty příkazového řádku, které se mají předat vašemu školicímu skriptu `entry_script` , ve formě `<command-line argument, value>` párů. K určení podrobného příznaku v `script_params` použijte `<command-line argument, "">` .
`compute_target`| Vzdálený výpočetní cíl, na kterém se váš školicí skript spustí, v tomto případě cluster Azure Machine Learning COMPUTE ([AmlCompute](how-to-create-attach-compute-sdk.md#amlcompute)). (Pamatujte na to, že cluster AmlCompute se běžně používá, je také možné zvolit jiné typy výpočetních cílů, jako jsou například virtuální počítače Azure nebo i místní počítač.)
`entry_script`| FilePath (vzhledem k `source_directory` ) školicímu skriptu, který se má spustit na vzdáleném výpočetním prostředí. Tento soubor a všechny další soubory, na kterých závisí, by měly být umístěny v této složce.
`conda_packages`| Seznam balíčků Pythonu, které se mají nainstalovat přes Conda, kterou vyžaduje váš školicí skript.  

Konstruktor má jiný parametr s názvem `pip_packages` , který používáte pro všechny potřebné balíčky PIP.

Teď, když jste vytvořili `Estimator` objekt, odešlete školicí úlohu, která se spustí ve vzdáleném výpočetním prostředí, se zavoláním `submit` funkce na vašem objektu [experimentu](concept-azure-machine-learning-architecture.md#experiments) `experiment` . 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciální složky** Dvě složky, *výstupy* a *protokoly*dostanou zvláštní zacházení Azure Machine Learning. Když při výuce zapisujete soubory do složek s názvem *výstupy* a *protokoly* , které jsou relativní vzhledem ke kořenovému adresáři ( `./outputs` a v `./logs` uvedeném pořadí), soubory se automaticky nahrají do historie spuštění, abyste k nim měli přístup, až se vaše spuštění dokončí.
>
> Chcete-li během školení vytvořit artefakty (například soubory modelů, kontrolní body, datové soubory nebo vykreslené bitové kopie), zapište je do `./outputs` složky.
>
> Podobně můžete do složky napsat jakékoli protokoly z školicích běhů `./logs` . Aby bylo možné využívat Azure Machine Learning [integrace TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) , nezapomeňte zapisovat do této složky protokoly TensorBoard. Během probíhajícího běhu budete moct spouštět TensorBoard a streamovat tyto protokoly.  Později také budete moci obnovit protokoly z libovolného z předchozích spuštění.
>
> Například pro stažení souboru zapsaného do složky *výstupy* do místního počítače po spuštění vzdáleného školení: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuované školení a vlastní image Docker

Existují dva další školicí scénáře, pomocí kterých můžete provádět tyto `Estimator` akce:
* Použití vlastní image Docker
* Distribuované školení v clusteru s více uzly

Následující kód ukazuje, jak provést distribuované školení pro model Keras. Kromě toho se místo použití výchozích Azure Machine Learning imagí určuje vlastní image z Docker Hub `continuumio/miniconda` pro účely školení.

Měli byste už mít vytvořený cílový objekt služby [COMPUTE](how-to-create-attach-compute-sdk.md#amlcompute) `compute_target` . Estimator vytvoříte takto:

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

Výše uvedený kód zpřístupňuje následující nové parametry `Estimator` konstruktoru:

Parametr | Popis | Výchozí
--|--|--
`custom_docker_image`| Název obrázku, který chcete použít. Poskytněte jenom image dostupné ve veřejných úložištích Docker (v tomto případě Docker Hub). Pokud chcete použít image z privátního úložiště Docker, použijte `environment_definition` místo toho parametr konstruktoru.| `None`
`node_count`| Počet uzlů, které se mají použít pro úlohu školení | `1`
`process_count_per_node`| Počet procesů (nebo "pracovní procesy"), které mají být spuštěny na jednotlivých uzlech. V takovém případě použijete `2` k dispozici GPU na jednotlivých uzlech.| `1`
`distributed_training`| [MPIConfiguration ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) objekt pro spouštění distribuovaného školení pomocí back-endu MPI.  | `None`


Nakonec odešlete školicí úlohu:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registrace modelu

Po proškolení modelu ho můžete uložit a zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

Spuštění následujícího kódu zaregistruje model do vašeho pracovního prostoru a zpřístupní ho pro referenci podle názvu ve vzdálených výpočetních kontextech nebo ve skriptech nasazení. [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueregister-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)Další informace a další parametry najdete v referenční dokumentaci.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Sledování a integrace GitHubu

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Příklady

Pro Poznámkový blok, který používá scikit model pomocí Estimator, se podívejte na:
* [kurzy/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

Poznámkové bloky na školicích modelech pomocí odhady specifických pro rozsáhlou výuku najdete v tématech:

* [Postupy: použití-AzureML/ml – rozhraní](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Trénování modelů PyTorch](how-to-train-pytorch.md)
* [Trénování modelů TensorFlow](how-to-train-tensorflow.md)
* [Školení o posílení neuronové sítě](how-to-use-reinforcement-learning.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Nasazení trained model](how-to-deploy-and-where.md)
* [Vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md)