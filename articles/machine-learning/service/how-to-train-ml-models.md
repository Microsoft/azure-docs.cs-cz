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
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 73887c39ebcee2efc4a31925f4aacfffb3c53ca7
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828060"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Výuka modelů pomocí Azure Machine Learning s využitím Estimator

Pomocí Azure Machine Learning můžete snadno odeslat školicí skript do [různých výpočetních cílů](how-to-set-up-training-targets.md#compute-targets-for-training), a to pomocí [objektu RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) a [objektu ScriptRunConfig](how-to-set-up-training-targets.md#submit). Tento model poskytuje značnou flexibilitu a maximální kontrolu.

Aby se usnadnilo školení modelu hloubkového učení, Azure Machine Learning Python SDK poskytuje alternativní abstrakci vyšší úrovně, třídu Estimator, která umožňuje uživatelům snadno sestavit konfigurace spuštění. Můžete vytvořit a použít obecné [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odeslání školicího skriptu pomocí libovolného vzdělávacího rozhraní, které zvolíte (například scikit-učení) na jakémkoli cílovém výpočetním prostředí, ať už se jedná o místní počítač, jeden virtuální počítač v Azure nebo cluster GPU v Azure. Pro PyTorch, TensorFlow a úlohy zřetězení Azure Machine Learning poskytuje také příslušné [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chain](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) odhady pro zjednodušení používání těchto rozhraní.

## <a name="train-with-an-estimator"></a>Výuka s Estimator

Po vytvoření [pracovního prostoru](concept-workspace.md) a nastavení [vývojového prostředí](how-to-configure-environment.md)v Azure Machine Learning zahrnuje následující kroky:  
1. Vytvořit [vzdálený cíl výpočtů](how-to-set-up-training-targets.md) (Poznámka: můžete použít také místní počítač jako cíl výpočtů)
2. Nahrajte [školicí data](how-to-access-data.md) do úložiště dat (volitelné).
3. Vytvoření [školicího skriptu](tutorial-train-models-with-aml.md#create-a-training-script)
4. Vytvoření objektu `Estimator`
5. Odeslání Estimator do objektu experimentu v pracovním prostoru

Tento článek se zaměřuje na kroky 4-5. Postup 1-3 najdete v [kurzu o výukovém](tutorial-train-models-with-aml.md) programu pro model.

### <a name="single-node-training"></a>Školení s jedním uzlem

Použití `Estimator` pro školení s jedním uzlem spouštěné ve vzdálené výpočetní službě v Azure pro model scikit-učení. Měl by již být vytvořen objekt [cíle výpočtů](how-to-set-up-training-targets.md#amlcompute) `compute_target` a objekt [úložiště dat](how-to-access-data.md) `ds`.

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

Tento fragment kódu určuje následující parametry konstruktoru `Estimator`.

Parametr | Popis
--|--
`source_directory`| Místní adresář, který obsahuje veškerý váš kód potřebný pro školicí úlohu. Tato složka se zkopíruje z místního počítače do vzdálené výpočetní služby.
`script_params`| Slovník určující argumenty příkazového řádku, které se mají předat skriptu pro školení `entry_script` ve formě párů `<command-line argument, value>`. Chcete-li zadat příznak verbose v `script_params`, použijte `<command-line argument, "">`.
`compute_target`| Vzdálený výpočetní cíl, na kterém se váš školicí skript spustí, v tomto případě cluster Azure Machine Learning COMPUTE ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Pamatujte na to, že cluster AmlCompute se běžně používá, je také možné zvolit jiné typy výpočetních cílů, jako jsou například virtuální počítače Azure nebo i místní počítač.)
`entry_script`| FilePath (vzhledem k `source_directory`) školicího skriptu, který se má spustit na vzdáleném výpočetním prostředí. Tento soubor a všechny další soubory, na kterých závisí, by měly být umístěny v této složce.
`conda_packages`| Seznam balíčků Pythonu, které se mají nainstalovat přes Conda, kterou vyžaduje váš školicí skript.  

Konstruktor má jiný parametr s názvem `pip_packages`, který používáte pro všechny potřebné balíčky PIP.

Teď, když jste vytvořili objekt `Estimator`, odešlete školicí úlohu, která se spustí ve vzdáleném výpočetním prostředí, s voláním funkce `submit` v objektu [experimentu](concept-azure-machine-learning-architecture.md#experiments) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciální složky** Dvě složky, *výstupy* a *protokoly*dostanou zvláštní zacházení Azure Machine Learning. Když při školení zapisujete soubory do složek s názvem *výstupy* a *protokoly* , které jsou relativní vzhledem ke kořenovému adresáři (`./outputs` a `./logs`, v uvedeném pořadí), soubory se automaticky nahrají do historie spuštění, abyste k nim měli přístup jenom jednou. vaše spuštění bylo dokončeno.
>
> Chcete-li během školení vytvořit artefakty (například soubory modelů, kontrolní body, datové soubory nebo vykreslené bitové kopie), zapište je do složky `./outputs`.
>
> Podobně můžete do složky `./logs` zapisovat libovolné protokoly z vašeho školicího běhu. Aby bylo možné využívat Azure Machine Learning [integrace TensorBoard](https://aka.ms/aml-notebook-tb) , nezapomeňte zapisovat do této složky protokoly TensorBoard. Během probíhajícího běhu budete moct spouštět TensorBoard a streamovat tyto protokoly.  Později také budete moci obnovit protokoly z libovolného z předchozích spuštění.
>
> Například pro stažení souboru zapsaného do složky *výstupy* do místního počítače po spuštění vzdáleného školení: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuované školení a vlastní image Docker

Existují dva další školicí scénáře, které můžete provést s `Estimator`:
* Použití vlastní image Docker
* Distribuované školení v clusteru s více uzly

Následující kód ukazuje, jak provést distribuované školení pro model Keras. Kromě toho místo používání výchozích Azure Machine Learning imagí určuje vlastní Docker image z Docker Hub `continuumio/miniconda` pro školení.

Měli byste už mít vytvořený cílový objekt služby [compute](how-to-set-up-training-targets.md#amlcompute) `compute_target`. Estimator vytvoříte takto:

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
`custom_docker_image`| Název obrázku, který chcete použít. Poskytněte jenom image dostupné ve veřejných úložištích Docker (v tomto případě Docker Hub). Pokud chcete použít image z privátního úložiště Docker, použijte místo toho parametr `environment_definition` konstruktoru. [Viz příklad](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Počet uzlů, které se mají použít pro úlohu školení | `1`
`process_count_per_node`| Počet procesů (nebo "pracovní procesy"), které mají být spuštěny na jednotlivých uzlech. V takovém případě použijete rozhraní GPU `2` dostupné na každém uzlu.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objekt pro spouštění distribuovaného školení pomocí back-endu MPI.  | `None`


Nakonec odešlete školicí úlohu:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>Sledování a integrace GitHubu

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Například aktuální ID potvrzení pro úložiště je protokolováno jako součást historie.

## <a name="examples"></a>Příklady
Poznámkový blok, který zobrazuje základy Estimator vzoru, najdete v těchto tématech:
* [Postupy: použití-AzureML/školení – s využitím hloubkového učení/postupy-použití-Estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Pro Poznámkový blok, který používá scikit model pomocí Estimator, se podívejte na:
* [kurzy/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Poznámkové bloky na školicích modelech pomocí odhady specifických pro rozsáhlou výuku najdete v tématech:
* [Postupy: použití-AzureML/školení – s využitím hloubkového učení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Výukové modely PyTorch](how-to-train-pytorch.md)
* [Výukové modely TensorFlow](how-to-train-tensorflow.md)
* [Ladit parametry](how-to-tune-hyperparameters.md)
* [Nasazení trained model](how-to-deploy-and-where.md)
