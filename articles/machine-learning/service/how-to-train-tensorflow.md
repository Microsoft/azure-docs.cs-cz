---
title: Trénování modelů s TensorFlow & Keras
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak spustit jeden uzel nebo pro distribuované trénování TensorFlow a Keras modelů s TensorFlow a Keras odhady
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: c8865c851f394d73b5446ac159b5a7799c0c9ed2
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192355"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Trénování TensorFlow a Keras modelů pomocí služby Azure Machine Learning

Můžete snadno spouštět úlohy trénování TensorFlow na výpočetní prostředky Azure s použitím [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator třídy v sadě SDK Azure Machine Learning. `TensorFlow` Estimator přesměruje služby Azure Machine Learning na spuštění úlohy na kontejner TensorFlow povolena pro hluboké Neuronové sítě (DNN) školení.

`TensorFlow` Estimator také poskytuje abstrakční vrstvu nad spouštěním, což znamená, že můžete snadno nakonfigurovat parametry spuštění s různými výpočetními cíli beze změny trénovací skripty.

## <a name="getting-started"></a>Začínáme

Odeslání úlohy se `TensorFlow` odhad je podobný používání základní [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py). Proto doporučujeme, začněte tím, že čtení [Estimator postupy báze Microsoft Knowledge base](how-to-train-ml-models.md) koncepce zastřešujícího nejprve.

Pokud chcete začít pracovat se službou Azure Machine Learning, [dokončení tohoto rychlého startu](quickstart-run-cloud-notebook.md). Budete mít pracovního prostředí se všemi načíst naše [ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml).

## <a name="single-node-training"></a>Trénování jedním uzlem

Ke spuštění úlohy TensorFlow, vytvořit instanci [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) objektu a odeslat ho jako experiment.

Následující kód vytvoří instanci TensorFlow odhad a odesílá ji jako experiment. Trénovací skript `train.py` se spustí pomocí parametrů skriptu. Úloha se spustí na podporou grafického procesoru [cílové výpočetní prostředí](how-to-set-up-training-targets.md)a scikit-informace se nainstaluje jako závislost pro `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Distribuované trénování

[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Estimator také podporuje distribuované trénování napříč clustery CPU a GPU. Můžete snadno spouštět distribuované TensorFlow úlohy a služby Azure Machine Learning bude spravovat infrastrukturu a Orchestrace za vás.

Služba Azure Machine Learning podporuje dvě metody distribuované trénování TensorFlow:

* [Na základě MPI](https://www.open-mpi.org/) distribuované trénování pomocí [Horovod](https://github.com/uber/horovod) framework
* Nativní [distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed) pomocí parametru metody serveru

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) je open-source platforma pro distribuované trénování vypracovanou organizací cccppf Uber. Nabízí Snadná cesta k distribuované úlohy GPU TensorFlow.

Následující ukázka spouští distribuované trénovací úlohu pomocí Horovod dva pracovní procesy rozdělené mezi dva uzly.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod a jeho závislosti se nainstaluje za vás, tak importujte ji do vašeho skriptu školení.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Server parametrů

Můžete také spustit [nativní distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed), model serveru parametr, který používá. V této metodě trénování napříč clusterem parametr serverů a pracovních procesů. Zaměstnanci vypočítat přechody během cvičení, zatímco parametr servery agregovat přechody.

Následující ukázka spouští distribuované trénovací úlohy pomocí metody serveru parametr čtyři pracovní procesy rozdělené mezi dva uzly.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Poznámka: na `TF_CONFIG`

Budete také potřebovat síťové adresy a porty u clusteru [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), takže se nastaví Azure Machine Learning `TF_CONFIG` proměnnou prostředí za vás.

`TF_CONFIG` Proměnná prostředí je řetězec formátu JSON. Tady je příklad proměnné pro parametr serveru:

```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Pro TensorFlow na vysoké úrovni [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) rozhraní API, TensorFlow, provede analýzu to `TF_CONFIG` proměnné a sestavení clusteru spec za vás.

TensorFlow na nižší úrovni základní rozhraní API pro trénování, analyzovat `TF_CONFIG` proměnné a sestavení `tf.train.ClusterSpec` ve vašem kódu školení. V [v tomto příkladu](https://aka.ms/aml-notebook-tf-ps), provedli byste proto v **cvičný skript** následujícím způsobem:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Podpora Keras

[Keras](https://keras.io/) je Oblíbené, vysoké úrovně DNN rozhraní Python API, která podporuje TensorFlow, CNTK a Theano jako back-EndY. Pokud používáte jako back-end TensorFlow, Keras přidání je snadné – stačí včetně `pip_package` parametr konstruktoru.

Následující příklad vytvoří instanci [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) odhad a odesílá ji jako experiment. Odhadu spouští skript školení Keras `keras_train.py`. Úloha se spustí na podporou grafického procesoru [cílové výpočetní prostředí](how-to-set-up-training-targets.md) s Keras nainstalována jako závislost pomocí pip.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Exportovat do ONNX

Chcete-li získat optimalizované odvozování s [ONNX Runtime](concept-onnx.md), TensorFlow trénovaného modelu můžete převést do formátu ONNX. Zobrazit [příklad](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Příklady

Pro jeden uzel a distribuované provádění TensorFlow pomocí různých platforem na najdete ukázky kódu pracovní [naši stránku Githubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Další postup

* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)