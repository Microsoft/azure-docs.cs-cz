---
title: Migrace z odhady na ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Průvodce migrací pro migraci z odhady do ScriptRunConfig pro konfiguraci školicích úloh.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 64c03b1c9fc18a4e78af9914b893599683069ced
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632777"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migrace z odhady na ScriptRunConfig

Až do té doby existovala více metod konfigurace školicích úloh v Azure Machine Learning prostřednictvím sady SDK, včetně odhady, ScriptRunConfig a RunConfiguration nižší úrovně.   Abychom vyřešili tuto nejednoznačnost a nekonzistenci, zjednodušili jsme proces konfigurace úloh v Azure ML.  Nyní byste měli používat ScriptRunConfig jako doporučenou možnost pro konfiguraci školicích úloh. 

Odhady jsou zastaralé ve verzi sady Python SDK pro 1.19.0. Obecně byste se měli také vyhnout explicitnímu vytvoření instance objektu RunConfiguration sami a místo toho nakonfigurovat úlohu pomocí třídy ScriptRunConfig.

Tento článek se věnuje běžným hlediskům při migraci z odhady na ScriptRunConfig.

> [!IMPORTANT]
> Pokud chcete migrovat na ScriptRunConfig z odhady, ujistěte se, že používáte >= 1.15.0 sady Python SDK.

## <a name="scriptrunconfig-documentation-and-samples"></a>Dokumentace a ukázky ScriptRunConfig
Dokumentace a ukázky Azure Machine Learning byly aktualizovány tak, aby používaly [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) pro konfiguraci a odeslání úlohy.

Informace o použití ScriptRunConfig najdete v následující dokumentaci:
* [Konfigurace a odesílání trénovacích spuštění](how-to-set-up-training-targets.md)
* [Konfigurace spuštění školení PyTorch](how-to-train-pytorch.md)
* [Konfigurace spuštění školení TensorFlow](how-to-train-tensorflow.md)
* [Konfigurace scikitch školicích běhů](how-to-train-scikit-learn.md)

Kromě toho si přečtěte následující ukázky & kurzy:
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/AzureML – příklady](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>Definování školicího prostředí
I když různé odhady architektury mají předem nakonfigurovaná prostředí, která jsou zálohovaná imagemi Docker, fázemi pro tyto image jsou privátní.  Z toho důvodu nemusíte mít k dispozici velkou transparentnost z toho, co tyto prostředí obsahují. Kromě toho odhady převezme konfigurace týkající se prostředí jako jednotlivé parametry (například `pip_packages` , `custom_docker_image` ) na příslušných konstruktorech.

Při použití ScriptRunConfig jsou všechny konfigurace související s prostředím zapouzdřeny v `Environment` objektu, který je předán do `environment` parametru konstruktoru ScriptRunConfig. Pokud chcete nakonfigurovat školicí úlohu, poskytněte prostředí, které obsahuje všechny závislosti vyžadované pro školicí skript. Pokud není k dispozici žádné prostředí, Azure ML použije jeden ze základních imagí Azure ML, konkrétně z toho definovaného `azureml.core.environment.DEFAULT_CPU_IMAGE` jako výchozí prostředí. Existuje několik způsobů, jak poskytnout prostředí:

* Ve výchozím nastavení jsou předdefinovaná prostředí, [která](how-to-use-environments.md#use-a-curated-environment) jsou v pracovním prostoru k dispozici ve výchozím prostředí. Pro každou z předkonfigurovaných imagí rozhraní Docker/Version, které byly na jednotlivé architektury Estimator, existuje odpovídající prostředí.
* [Definování vlastního prostředí](how-to-use-environments.md)

Tady je příklad použití prostředí s PyTorch 1,6 pro školení:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Pokud chcete zadat **proměnné prostředí** , které se nastaví na procesu, kde se spustí školicí skript, použijte objekt prostředí:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Informace o konfiguraci a správě prostředí Azure ML najdete v těchto tématech:
* [Jak používat prostředí](how-to-use-environments.md)
* [Kurátorovaná prostředí](resource-curated-environments.md)
* [Výuka s vlastní imagí Docker](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Používání dat pro školení
### <a name="datasets"></a>Datové sady
Pokud používáte datovou sadu Azure ML pro školení, předejte datovou sadu jako argument skriptu pomocí `arguments` parametru. Díky tomu získáte cestu k datům (bod připojení nebo cestu ke stažení) ve školicím skriptu pomocí argumentů.

Následující příklad nakonfiguruje školicí úlohu, kde se datová sada objektu `mnist_ds` připojí ke vzdálenému výpočtu.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (staré)
I když doporučujeme používat datové sady Azure ML přes starý DataReference, pokud stále používáte DataReference z jakéhokoli důvodu, musíte úlohu nakonfigurovat následujícím způsobem:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Další informace o používání dat pro školení najdete v těchto tématech:
* [Výuka s datovými sadami v Azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)

## <a name="distributed-training"></a>Distribuované trénování
Pokud potřebujete nakonfigurovat distribuovanou úlohu pro školení, udělejte to tak, že zadáte `distributed_job_config` parametr v konstruktoru ScriptRunConfig. Předejte [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true), [PyTorchConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?view=azure-ml-py&preserve-view=true)nebo [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) pro distribuované úlohy příslušných typů.

V následujícím příkladu je nakonfiguruje školicí úkol PyTorch pro použití distribuovaného školení s MPI/Horovod:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Další informace najdete tady:
* [Distribuované trénování s využitím PyTorchu](how-to-train-pytorch.md#distributed-training)
* [Distribuované školení pomocí TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Různé
Pokud potřebujete získat přístup k základnímu objektu RunConfiguration z jakéhokoli důvodu ScriptRunConfig, můžete to udělat takto:
```
src.run_config
```

## <a name="next-steps"></a>Další kroky

* [Konfigurace a odesílání trénovacích spuštění](how-to-set-up-training-targets.md)
