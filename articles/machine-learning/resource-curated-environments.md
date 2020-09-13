---
title: Poduspořádaná prostředí
titleSuffix: Azure Machine Learning
description: Kolekce podmnožinových prostředí dostupných v Azure Machine Learning
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 09/03/2020
ms.openlocfilehash: 4ae96976f81aab9a0949594551c82d3a3fec4f0a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662087"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning v nich uspořádaná prostředí

V tomto článku jsou uvedena seznamovaná prostředí v Azure Machine Learning a balíčky a kanály, které jsou v nich předem nainstalované. Dodaná prostředí jsou poskytována Azure Machine Learning a jsou ve výchozím nastavení k dispozici ve vašem pracovním prostoru. Jsou zajištěny pomocí imagí Docker uložených v mezipaměti, což snižuje náklady na přípravu za běhu a umožňují rychlejší nasazení. Pomocí těchto prostředí můžete rychle začít s různými architekturami strojového učení.

> [!NOTE]
> Tento seznam se aktualizuje od září 2020. Seznam nejvyšších aktualizovaných seznamů získáte pomocí sady Python SDK. Další informace najdete v článku o [prostředích](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azureml-automl"></a>AzureML-AutoML

**Kanály balíčků:**

* Anaconda
* conda – zfalšovat
* pytorch

**Conda balíčky:**

* python
* numpy
* scikit-learn
* pandas
* py – xgboost
* fbprophet
* svátků
* setuptools – Git
* psutil

**Balíčky PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* AzureML – výchozí
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* odvození – schéma
* py – cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**Kanály balíčků:**

* Anaconda
* conda – zfalšovat
* pytorch

**Conda balíčky:**

* python
* numpy
* scikit-learn
* pandas
* py – xgboost
* fbprophet
* svátků
* setuptools – Git
* pytorch
* cudatoolkit
* psutil

**Balíčky PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* AzureML – výchozí
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* odvození – schéma
* pytorch – transformátory
* spacy
* en_core_web_sm
* py – cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**Kanály balíčků:**

* Anaconda
* conda – zfalšovat
* pytorch

**Conda balíčky:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* svátků
* setuptools – Git
* pytorch
* cudatoolkit
* psutil

**Balíčky PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* AzureML – výchozí
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* odvození – schéma
* horovod
* pytorch – transformátory
* spacy
* en_core_web_sm
* py – cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-Vision-GPU

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML-DataSet – modul runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* AzureML – výchozí
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* AzureML-contrib-automl-DNN-Vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML – GPU

**Kanály balíčků:**

* Anaconda
* conda – zfalšovat
* pytorch

**Conda balíčky:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* svátků
* setuptools – Git
* psutil

**Balíčky PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* AzureML – výchozí
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* odvození – schéma
* py – cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-chainer – 5.1.0-CPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-chainer – 5.1.0-GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-dAsK-CPU

**Kanály balíčků:**

* conda – zfalšovat
* pytorch
* výchozí hodnoty

**Conda balíčky:**

* python

**Balíčky PIP:**

* adlfs
* azureml-core
* AzureML-DataSet – modul runtime
* dAsK [dokončeno]
* dAsK-ml [dokončeno]
* místě
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* poznámkového bloku
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-dAsK – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python
* matplotlib

**Balíčky PIP:**

* AzureML – výchozí
* adlfs
* azureml-core
* dAsK [dokončeno]
* dAsK-ml [dokončeno]
* místě
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* poznámkového bloku
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Hyperdrive-ForecastDNN

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* AzureML – výchozí
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* AzureML-contrib-automl-DNN-prognózování

## <a name="azureml-minimal"></a>AzureML-minimální

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0,15

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1,0 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1,0 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1,1 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1,1 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1,2 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1,2 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1,3 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1,3 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1,4 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1,4 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1,5 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1,5 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1,6 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1,6 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* pozdější

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-učení-0.20.3

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1,10 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1,10 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1,12 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1,12 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1,13 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1,13 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2,0 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2,0 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2,1 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2,1 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2,2 – procesor

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2,2 – GPU

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tutorial"></a>AzureML – kurz

**Kanály balíčků:**

* Anaconda
* conda – zfalšovat

**Conda balíčky:**

* python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* azureml-telemetry
* AzureML-vlak-restclients-HyperDrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* AzureML-vlak
* AzureML-SDK
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* skriptu sklearn – PANDAS

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**Kanály balíčků:**

* conda – zfalšovat

**Conda balíčky:**

* python

**Balíčky PIP:**

* azureml-core
* AzureML – výchozí
* AzureML-DataSet-runtime [zapékací, PANDAS]
