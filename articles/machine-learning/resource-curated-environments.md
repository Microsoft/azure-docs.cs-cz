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
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 9d0964fb28451abe0a77276d6f9d605fcaac2514
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156424"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning v nich uspořádaná prostředí

V tomto článku jsou uvedena seznamovaná prostředí v Azure Machine Learning a balíčky a kanály, které jsou v nich předem nainstalované.

> [!NOTE]
> Tento seznam se aktualizuje od června 2020. Seznam nejvyšších aktualizovaných seznamů získáte pomocí sady Python SDK. Další informace najdete v článku o [prostředích](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azure-automl"></a>AutoML Azure

- [AutoML](#azureml-automl)
- [PROCESOR AzureML AutoML](#azureml-automl-gpu)
- [AutoML DNN](#azureml-automl-dnn)
- [DNN GPU AzureML AutoML](#azureml-automl-dnn-gpu)
- [Grafický procesor Azure AutoML DNN Vision](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AutoML

kanály balíčků:
- Anaconda
- conda – zfalšovat
- pytorch

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML-Pipeline-Core = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-Interpret = = 1.8.0
  - AzureML-vysvětlit-model = = 1.8.0
  - AzureML-automl-Core = = 1.8.0
  - AzureML-automl-runtime = = 1.8.0
  - AzureML-vlak-automl-Client = = 1.8.0
  - AzureML-vlak-automl-runtime = = 1.8.0. post1
  - odvození – schéma
  - pyarrow = = 0.17.0
  - py – cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- PANDAS>= 0.21.0, <= 0.23.4
- py – xgboost<= 0,90
- fbprophet = = 0.5
- setuptools – Git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>PROCESOR AzureML AutoML

kanály balíčků:
- Anaconda
- conda – zfalšovat
- pytorch

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML-Pipeline-Core = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-Interpret = = 1.8.0
  - AzureML-vysvětlit-model = = 1.8.0
  - AzureML-automl-Core = = 1.8.0
  - AzureML-automl-runtime = = 1.8.0
  - AzureML-vlak-automl-Client = = 1.8.0
  - AzureML-vlak-automl-runtime = = 1.8.0. post1
  - odvození – schéma
  - pyarrow = = 0.17.0
  - py – cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- PANDAS>= 0.21.0, <= 0.23.4
- fbprophet = = 0.5
- setuptools – Git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>AutoML DNN

kanály balíčků:
- Anaconda
- conda – zfalšovat
- pytorch

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML-Pipeline-Core = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-Interpret = = 1.8.0
  - AzureML-vysvětlit-model = = 1.8.0
  - AzureML-automl-Core = = 1.8.0
  - AzureML-automl-runtime = = 1.8.0
  - AzureML-vlak-automl-Client = = 1.8.0
  - AzureML-vlak-automl-runtime = = 1.8.0. post1
  - odvození – schéma
  - pytorch-transformes = = 1.0.0
  - spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py – cpuinfo = = 5.0.0
- PANDAS>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- py – xgboost<= 0,90
- fbprophet = = 0.5
- setuptools – Git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>DNN GPU AzureML AutoML

kanály balíčků:
- Anaconda
- conda – zfalšovat
- pytorch

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML-Pipeline-Core = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-Interpret = = 1.8.0
  - AzureML-vysvětlit-model = = 1.8.0
  - AzureML-automl-Core = = 1.8.0
  - AzureML-automl-runtime = = 1.8.0
  - AzureML-vlak-automl-Client = = 1.8.0
  - AzureML-vlak-automl-runtime = = 1.8.0. post1
  - odvození – schéma
  - horovod = = 0.19.4
  - pytorch-transformes = = 1.0.0
  - spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py – cpuinfo = = 5.0.0
- PANDAS>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- fbprophet = = 0.5
- setuptools – Git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>AutoML DNN Vision – GPU

závislosti
- Python = 3.7
- PIP
  - AzureML-automl-Core = = 1.8.0
  - AzureML-Core = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-automl-Client = = 1.8.0
  - AzureML-automl-runtime = = 1.8.0
  - AzureML-Interpret = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-vysvětlit-model = = 1.8.0
  - AzureML-vlak-automl-runtime = = 1.8.0. post1
  - AzureML-vlak-automl = = 1.8.0
  - AzureML-contrib-DataSet = = 1.8.0
  - AzureML-Pipeline-Core = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - AzureML-Pipeline-kroky = = 1.8.0
  - AzureML-Pipeline = = 1.8.0
  - AzureML-vlak = = 1.8.0
  - AzureML-SDK = = 1.8.0
  - AzureML-contrib-automl-DNN-Vision = = 1.8.0

## <a name="azure-ml-designer"></a>Návrhář Azure ML

- [Návrhář AzureML](#azureml-designer)
- [ŽIVOTOPIS návrháře AzureML](#azureml-designer-cv)
- [Transformace ŽIVOTOPISu nástroje AzureML Designer](#azureml-designer-cv-transform)
- [V/v návrháře nástroje AzureML](#azureml-designer-io)
- [NLP návrháře nástroje AzureML](#azureml-designer-nlp)
- [PyTorch návrháře nástroje AzureML](#azureml-designer-pytorch)
- [Výukový program nástroje AzureML Designer PyTorch](#azureml-designer-pytorch-train)
- [Návrhář AzureML R](#azureml-designer-r)
- [Doporučení nástroje AzureML Designer](#azureml-designer-recommender)
- [Skóre návrháře AzureML](#azureml-designer-score)
- [Transformace návrháře AzureML](#azureml-designer-transform)

### <a name="azureml-designer"></a>Návrhář AzureML

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.8
- scikit-neočekávaně = 1.0.6
- PIP
  - AzureML-Designer-Classic-modules = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - spacy = = 2.1.7

### <a name="azureml-designer-cv"></a>ŽIVOTOPIS návrháře AzureML

kanály balíčků:
- výchozí hodnoty

závislosti
- Python = 3.6.8
- PIP
  - AzureML-Designer-CV-modules = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>Transformace ŽIVOTOPISu nástroje AzureML Designer

kanály balíčků:
- výchozí hodnoty

závislosti
- Python = 3.6.8
- PIP
  - AzureML-Designer-CV-modules [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>V/v návrháře nástroje AzureML

kanály balíčků:
- výchozí hodnoty

závislosti
- Python = 3.6.8
- PIP
  - AzureML-dataprep>= 1,6
  - AzureML-Designer-DataIO-modules = 0.0.30

### <a name="azureml-designer-nlp"></a>NLP návrháře nástroje AzureML

kanály balíčků:
- výchozí hodnoty

závislosti
- Python = 3.6.8
- PIP
  - AzureML-Designer-Classic-modules = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - spacy = = 2.1.7

### <a name="azureml-designer-pytorch"></a>PyTorch návrháře nástroje AzureML

kanály balíčků:
- výchozí hodnoty

závislosti
- Python = 3.6.8
- PIP
  - AzureML-Designer-pytorch-modules = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>Výukový program nástroje AzureML Designer PyTorch

kanály balíčků:
- výchozí hodnoty

závislosti
- Python = 3.6.8
- PIP
  - AzureML-Designer-pytorch-modules = 0.0.8

### <a name="azureml-designer-r"></a>Návrhář AzureML R

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.8
- r-stříška = 6.0
- r-catools = 1.17.1
- r-cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-forcats = 0.5.0
- r-předpověď = 8.12
- r-glmnet = 2.0
- r-igraph = 1.2.4
- r-Matrix = 1.2
- r-mclust = 5.4.6
- r-mgcv = 1.8
- r-nlme = 3.1
- r-sladký = 7.3
- r-plyr = 1.8.6
- r-randomforest = 4.6
- r-reticulate = 1.12
- r-ROCR = 1.0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-Stringer = 1.4.0
- r-tidyverse = 1.2.1
- r-timedate = 3043.102
- r-tseries = 0.10
- r = 3.5.1
- PIP
  - AzureML-Designer-Classic-modules = = 0.0.124

### <a name="azureml-designer-recommender"></a>Doporučení nástroje AzureML Designer

kanály balíčků:
- výchozí hodnoty

závislosti
- Python = 3.6.8
- PIP
  - AzureML-Designer-modules = = 0.0.5

### <a name="azureml-designer-score"></a>Skóre návrháře AzureML

kanály balíčků:
- výchozí hodnoty

závislosti
- conda
- Python = 3.6.8
- PIP
  - AzureML-Designer-skore-modules = = 0.0.5

### <a name="azureml-designer-transform"></a>Transformace návrháře AzureML

kanály balíčků:
- výchozí hodnoty

závislosti
- Python = 3.6.8
- PIP
  - AzureML-Designer-datatransformes-modules = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>Hyperdrive ForecastDNN

závislosti
- Python = 3.7
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML-Pipeline-Core = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-automl-Core = = 1.8.0
  - AzureML-automl-runtime = = 1.8.0
  - AzureML-vlak-automl-Client = = 1.8.0
  - AzureML-vlak-automl-runtime = = 1.8.0. post1
  - AzureML-contrib-automl-DNN-FORECAST = = 1.8.0

## <a name="azureml-minimal"></a>AzureML minimální

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0

## <a name="azureml-sidecar"></a>AzureML

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2

## <a name="azureml-tutorial"></a>Kurz AzureML

kanály balíčků:
- Anaconda
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - AzureML-widgets = = 1.8.0
  - AzureML-Pipeline-Core = = 1.8.0
  - AzureML-Pipeline-kroky = = 1.8.0
  - AzureML-opendatasets = = 1.8.0
  - AzureML-automl-Core = = 1.8.0
  - AzureML-automl-runtime = = 1.8.0
  - AzureML-vlak-automl-Client = = 1.8.0
  - AzureML-vlak-automl-runtime = = 1.8.0. post1  
  - AzureML-vlak-automl = = 1.8.0
  - AzureML-vlak = = 1.8.0
  - AzureML-SDK = = 1.8.0
  - AzureML-Interpret = = 1.8.0
  - AzureML-tensorboard = = 1.8.0
  - AzureML-mlflow = = 1.8.0
  - mlflow
  - skriptu sklearn – PANDAS
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>VowpalWabbit 8.8.0

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-dataprep [pojistka, PANDAS]

## <a name="dask"></a>Dask

- [PROCESOR AzureML dAsK](#azureml-dask-cpu)
- [PROCESOR AzureML dAsK](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>PROCESOR AzureML dAsK

kanály balíčků:
- conda – zfalšovat
- pytorch
- výchozí hodnoty

závislosti
- Python = 3.6.9
- PIP
  - adlfs
  - AzureML-Core = = 1.8.0
  - azureml-dataprep
  - dAsK [dokončeno]
  - dAsK-ml [dokončeno]
  - místě
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - poznámkového bloku
  - pyarrow

### <a name="azureml-dask-gpu"></a>PROCESOR AzureML dAsK

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.9
- PIP
  - AzureML – výchozí = = 1.8.0
  - adlfs
  - AzureML-Core = = 1.8.0
  - azureml-dataprep
  - dAsK [dokončeno]
  - dAsK-ml [dokončeno]
  - místě
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - poznámkového bloku
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [CPU 5.1.0 chainer](#azureml-chainer-510-cpu)
- [5.1.0 GPU řetězu AzureML](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>CPU 5.1.0 chainer

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - chainer = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>5.1.0 GPU řetězu AzureML

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - chainer = = 5.1.0
  - Cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>AzureML PySpark MmlSpark 0,15

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [PROCESOR AzureML PyTorch 1,0](#azureml-pytorch-10-cpu)
- [PROCESOR AzureML PyTorch 1,0](#azureml-pytorch-10-gpu)
- [PROCESOR AzureML PyTorch 1,1](#azureml-pytorch-11-cpu)
- [PROCESOR AzureML PyTorch 1,1](#azureml-pytorch-11-gpu)
- [PROCESOR AzureML PyTorch 1,2](#azureml-pytorch-12-cpu)
- [PROCESOR AzureML PyTorch 1,2](#azureml-pytorch-12-gpu)
- [PROCESOR AzureML PyTorch 1,3](#azureml-pytorch-13-cpu)
- [PROCESOR AzureML PyTorch 1,3](#azureml-pytorch-13-gpu)
- [PROCESOR AzureML PyTorch 1,4](#azureml-pytorch-14-cpu)
- [PROCESOR AzureML PyTorch 1,4](#azureml-pytorch-14-gpu)
- [PROCESOR AzureML PyTorch 1,5](#azureml-pytorch-15-cpu)
- [PROCESOR AzureML PyTorch 1,5](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>PROCESOR AzureML PyTorch 1,0

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1,0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>PROCESOR AzureML PyTorch 1,0

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1,0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>PROCESOR AzureML-PyTorch 1,1

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>PROCESOR AzureML PyTorch 1,1

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>PROCESOR AzureML PyTorch 1,2

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>PROCESOR AzureML PyTorch 1,2

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>PROCESOR AzureML PyTorch 1,3

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>PROCESOR AzureML PyTorch 1,3

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>PROCESOR AzureML PyTorch 1,4

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>PROCESOR AzureML PyTorch 1,4

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>PROCESOR AzureML PyTorch 1,5

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>PROCESOR AzureML PyTorch 1,5

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - budoucnost = = 0.17.1

## <a name="scikit-learn"></a>Scikit – informace

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit – Přečtěte si 0.20.3

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - scikit-učení = = 0.20.3
  - scipy = = 1.2.1
  - joblib = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [PROCESOR Azure ML TensorFlow 1,10](#azureml-tensorflow-110-cpu)
- [Grafický procesor Azure ML TensorFlow 1,10](#azureml-tensorflow-110-gpu)
- [PROCESOR Azure ML TensorFlow 1,12](#azureml-tensorflow-112-cpu)
- [Grafický procesor Azure ML TensorFlow 1,12](#azureml-tensorflow-112-gpu)
- [PROCESOR Azure ML TensorFlow 1,13](#azureml-tensorflow-113-cpu)
- [Grafický procesor Azure ML TensorFlow 1,13](#azureml-tensorflow-113-gpu)
- [PROCESOR Azure ML TensorFlow 2,0](#azureml-tensorflow-20-cpu)
- [Grafický procesor Azure ML TensorFlow 2,0](#azureml-tensorflow-20-gpu)
- [PROCESOR Azure ML TensorFlow 2,1](#azureml-tensorflow-21-cpu)
- [Grafický procesor Azure ML TensorFlow 2,1](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>PROCESOR AzureML TensorFlow 1,10

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow = = 1,10
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>PROCESOR AzureML TensorFlow 1,10

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow-GPU = = 1.10.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>PROCESOR AzureML TensorFlow 1,12

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow = = 1.12
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>PROCESOR AzureML TensorFlow 1,12

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow-GPU = = 1.12.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>PROCESOR AzureML-TensorFlow 1,13

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>PROCESOR AzureML TensorFlow 1,13

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow-GPU = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>PROCESOR AzureML TensorFlow 2,0

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow = = 2.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>PROCESOR AzureML TensorFlow 2,0

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow-GPU = = 2.0.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>PROCESOR AzureML TensorFlow 2,1

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow = = 2.1.0
  - horovod = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>PROCESOR AzureML TensorFlow 2,1

kanály balíčků:
- conda – zfalšovat

závislosti
- Python = 3.6.2
- PIP
  - AzureML-Core = = 1.8.0
  - AzureML – výchozí = = 1.8.0
  - AzureML-telemetrie = = 1.8.0
  - AzureML-vlak-restclients-Hyperdrive = = 1.8.0
  - AzureML-vlak-Core = = 1.8.0
  - tensorflow-GPU = = 2.1.0
  - horovod = = 0.19.1

