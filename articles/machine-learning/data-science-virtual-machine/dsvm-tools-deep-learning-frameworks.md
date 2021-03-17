---
title: '& rozhraní AI pro hloubkové učení'
titleSuffix: Azure Data Science Virtual Machine
description: Dostupné architektury a nástroje pro hloubkové učení na platformě Azure Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 0bfdea6c0bbbe40e4c72ab9da70a1b16d4bc4d3a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175664"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Rozhraní pro hloubkové učení a AI pro Azure Data Science VM
Architektury hloubkového učení na DSVM jsou uvedené níže.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | |
| Podporované edice DSVM      | Ubuntu 16.04    |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Caffe je nainstalován v `/opt/caffe` .   Ukázky jsou v `/opt/caffe/examples` .|
| Jak ji spustit      | pomocí X2Go se přihlaste k VIRTUÁLNÍmu počítači a potom spusťte nový terminál a zadejte následující:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Otevře se nové okno prohlížeče s ukázkami poznámkových bloků. Binární soubory jsou nainstalované v/opt/Caffe/Build/Install/bin..<br/><br/>Nainstalovaná verze Caffe vyžaduje Python 2,7 a nebude fungovat s Python 3,5, který je ve výchozím nastavení aktivovaný. Pokud chcete přejít na Python 2,7, spusťte příkaz `source activate root` a přepněte se do prostředí Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | |
| Podporované edice DSVM      | Ubuntu 16.04     |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Caffe2 je nainstalován v prostředí [Python 2,7 (root) conda. |
| Jak ji spustit      | Terminál: Spusťte Python a importujte Caffe2. <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom v adresáři Caffe2 vyhledejte ukázkové poznámkové bloky. Některé poznámkové bloky vyžadují, aby byl v kódu Pythonu nastavený kořen Caffe2. Zadejte/opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 5.2 |
| Podporované edice DSVM      | Ubuntu 16.04    |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | V Pythonu 3,5 se instaluje chainer. |
| Jak ji spustit      | Terminál: aktivujte prostředí Python 3,5, spusťte `python` a potom `import chainer` . <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a pak vyhledejte ukázkové poznámkové bloky v adresáři chainer.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, ovladač NVIDIA](https://developer.nvidia.com/cuda-toolkit)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 10.0.130|
| Podporované edice DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> Systém Windows 2016<br> Ubuntu 16.04  |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  |na systémové cestě je k dispozici _NVIDIA-SMI_ .  |
| Jak ji spustit      | Otevřete příkazový řádek (ve Windows) nebo terminálu (na platformě Linux) a potom spusťte _NVIDIA-SMI_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 0.16.1|
| Podporované edice DSVM      | Ubuntu 18.04<br> Ubuntu 16.04   |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Horovod je nainstalován v Pythonu 3,5 |
| Jak ji spustit      | Aktivujte správné prostředí v terminálu a pak spusťte Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 2.2.4 |
| Podporované edice DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> Systém Windows 2016 <br> Ubuntu 16.04   |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Keras je nainstalovaný v Pythonu 3,6 ve Windows a v Pythonu 3,5u v systému Linux. |
| Jak ji spustit      | Aktivujte správné prostředí v terminálu a pak spusťte Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](/cognitive-toolkit/)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 2.5.1 |
| Podporované edice DSVM      | Systém Windows 2016 <br> Ubuntu 16.04   |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | CNTK je nainstalovaný v Pythonu 3,6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a v Pythonu pro [Linux](./dsvm-tools-languages.md#python-linux-edition)3,5.) |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a spusťte Python. <br/>Jupyter: Připojte se k [Jupyter](provision-vm.md) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom otevřete adresář CNTK pro ukázky. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 1.3.0 |
| Podporované edice DSVM      | Systém Windows 2016 <br> Ubuntu 16.04    |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | MXNet se instaluje do `C:\dsvm\tools\mxnet` systému Windows a `/dsvm/tools/mxnet` na Ubuntu. Vazby Pythonu jsou nainstalované v Pythonu 3,6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a Python 3,5 on [Linux](./dsvm-tools-languages.md#python-linux-edition)) vazby R jsou taky součástí Ubuntu DSVM. |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí conda a potom spusťte příkaz `import mxnet` . <br/>Jupyter: Připojte se k [Jupyter](provision-vm.md#access-the-dsvm) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a otevřete `mxnet` adresář pro ukázky. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 1.0.1 |
| Podporované edice DSVM      | Systém Windows 2016 <br> Ubuntu 16.04    |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | MXNet model Server je nainstalovaný v Pythonu 3,6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a v Pythonu pro [Linux](./dsvm-tools-languages.md#python-linux-edition)3,5.) |
| Jak ji spustit      | Terminál: Spusťte `sudo systemctl stop jupyterhub` , aby se služba JupyterHub nejdřív zastavila, protože naslouchá na stejném portu. Pak aktivujte správné prostředí conda a spusťte `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Rozhraní NVIDIA – SMI (System Management Interface)](https://developer.nvidia.com/nvidia-system-management-interface)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze |  |
| Podporované edice DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> Systém Windows 2016<br> Ubuntu 16.04   |
| K čemu slouží? | Nástroj NVIDIA pro dotazování aktivity GPU |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | `nvidia-smi` je v systémové cestě. |
| Jak ji spustit      | Na virtuálním počítači **s grafickým procesorem** otevřete příkazový řádek (ve Windows) nebo terminálu (na platformě Linux) a potom spusťte příkaz `nvidia-smi` . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 1.2.0 (Ubuntu 16,04); 1.7.1 (Ubuntu 18,04, Windows 2019) |
| Podporované edice DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Instaluje se v [pythonu 3,7](dsvm-tools-languages.md#python-linux-edition) v prostředí py37_pytorch conda. Obsahuje ukázky Jupyter poznámkových bloků a ukázky jsou v/dsvm/Samples/pytorch.. |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a pak spusťte Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Připojte se a otevřete adresář PyTorch pro ukázky.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 1.13 |
| Podporované edice DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> Systém Windows 2016 <br> Ubuntu 16.04 |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Nainstalováno v Pythonu 3,5 v systémech [Linux](dsvm-tools-languages.md#python-linux-edition) a Python 3,6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a pak spusťte Python. <br/> * Jupyter: Připojte se k [Jupyter](provision-vm.md) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom otevřete adresář TensorFlow pro ukázky.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 1.12 |
| Podporované edice DSVM      | Ubuntu 16.04 |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | tensorflow_model_server je k dispozici v terminálu. |
| Jak ji spustit      |  Ukázky jsou k dispozici [online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované verze | 1.0.3 |
| Podporované edice DSVM      | Ubuntu 16.04 |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  |Theano je nainstalován v Pythonu 2,7 (_root_) a v prostředí Python 3,5 (_py35_). |
| Jak ji spustit      |  Terminál: aktivujte požadovanou verzi Pythonu (root nebo py35), spusťte Python a pak importujte Theano.<br/>* Jupyter: vyberte jádro Python 2,7 nebo 3,5 a pak importujte Theano.  <br/>Chcete-li vyřešit poslední chybu MKL (Math kernel Library), musíte nejprve nastavit vrstvu vláken MKL následujícím způsobem:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
