---
title: '& Rozhraní AI pro hloubkové učení'
titleSuffix: Azure Data Science Virtual Machine
description: Dostupné architektury a nástroje pro hloubkové učení na platformě Azure Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390599"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Rozhraní pro hloubkové učení a AI pro Azure Data Science VM
Architektury hloubkového učení na DSVM jsou uvedené níže.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Podporované verze | |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Caffe je nainstalován v `/opt/caffe`.   Ukázky jsou v `/opt/caffe/examples`.|
| Jak ji spustit      | pomocí X2Go se přihlaste k VIRTUÁLNÍmu počítači a potom spusťte nový terminál a zadejte následující:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Otevře se nové okno prohlížeče s ukázkové poznámkové bloky. Binární soubory jsou nainstalovány v /opt/caffe/build/install/bin.<br/><br/>Nainstalovaná verze Caffe vyžaduje Python 2,7 a nebude fungovat s Python 3,5, který je ve výchozím nastavení aktivovaný. Pokud chcete přejít na Python 2,7, spusťte `source activate root` a přepněte se do prostředí Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Podporované verze | |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Caffe2 je nainstalován v prostředí [Python 2,7 (root) conda. |
| Jak ji spustit      | Terminál: Spusťte Python a importujte Caffe2. <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom v adresáři Caffe2 vyhledejte ukázkové poznámkové bloky. Některé poznámkových bloků vyžadují kořenový Caffe2 možné nastavit v kódu Pythonu; Zadejte /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 5.2 |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | V Pythonu 3,5 se instaluje chainer. |
| Jak ji spustit      | Terminál: aktivujte prostředí Python 3,5, spusťte `python`a potom `import chainer`. <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a pak vyhledejte ukázkové poznámkové bloky v adresáři chainer.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, ovladač NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Podporované verze | 10.0.130|
| Podporované edice DSVM      | Windows a Linux   |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  |na systémové cestě je k dispozici _NVIDIA-SMI_ .  |
| Jak ji spustit      | Otevřete příkazový řádek (ve Windows) nebo terminálu (na platformě Linux) a potom spusťte _NVIDIA-SMI_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Podporované verze | 0.16.1|
| Podporované edice DSVM      | Linux (Ubuntu)   |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Horovod je nainstalován v Pythonu 3,5 |
| Jak ji spustit      | Aktivujte správné prostředí v terminálu a pak spusťte Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 2.2.4 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Keras je nainstalovaný v Pythonu 3,6 ve Windows a v Pythonu 3,5u v systému Linux. |
| Jak ji spustit      | Aktivujte správné prostředí v terminálu a pak spusťte Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 2.5.1 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | CNTK je nainstalovaný v Pythonu 3,6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a v Pythonu pro [Linux](./dsvm-tools-languages.md#python-linux-edition)3,5.) |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a spusťte Python. <br/>Jupyter: Připojte se k [Jupyter](provision-vm.md) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom otevřete adresář CNTK pro ukázky. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Podporované verze | 1.3.0 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | MXNet se instaluje v `C:\dsvm\tools\mxnet` ve Windows a `/dsvm/tools/mxnet` na Ubuntu. Vazby Pythonu jsou nainstalované v Pythonu 3,6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a Python 3,5 on [Linux](./dsvm-tools-languages.md#python-linux-edition)) vazby R jsou taky součástí Ubuntu DSVM. |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí conda a pak spusťte `import mxnet`. <br/>Jupyter: Připojte se k [Jupyter](provision-vm.md#access-the-dsvm) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a otevřete `mxnet` adresář pro ukázky. |

## <a name="mxnet-model-server"></a>[Server MXNet modelů](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1.0.1 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | MXNet model Server je nainstalovaný v Pythonu 3,6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a v Pythonu pro [Linux](./dsvm-tools-languages.md#python-linux-edition)3,5.) |
| Jak ji spustit      | Terminál: Spusťte `sudo systemctl stop jupyterhub`, aby se služba JupyterHub nejdřív zastavila, protože naslouchá na stejném portu. Pak aktivujte správné prostředí conda a spusťte `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Rozhraní NVIDIA – SMI (System Management Interface)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Podporované verze |  |
| Podporované edice DSVM      | Windows a Linux   |
| K čemu slouží? | NVIDIA nástroj pro dotazování aktivita GPU |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | `nvidia-smi` je v systémové cestě. |
| Jak ji spustit      | Na virtuálním počítači **s grafickým procesorem**otevřete příkazový řádek (ve Windows) nebo terminálu (na platformě Linux) a potom spusťte `nvidia-smi`. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1.2.0 (Ubuntu 16,04, Windows 2016), 1.4.0 (Ubuntu 18,04, Windows 2019) |
| Podporované edice DSVM      | Linux |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Nainstalováno v [pythonu 3,5](dsvm-tools-languages.md#python-linux-edition). Obsahuje ukázky Jupyter poznámkových bloků a ukázky jsou v/dsvm/Samples/pytorch. |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a pak spusťte Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Connect a pak otevřete adresář PyTorch pro ukázky.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1,13 |
| Podporované edice DSVM      | Windows, Linux |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Nainstalováno v Pythonu 3,5 v systémech [Linux](dsvm-tools-languages.md#python-linux-edition) a Python 3,6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a pak spusťte Python. <br/> * Jupyter: Připojte se k [Jupyter](provision-vm.md) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom otevřete adresář TensorFlow pro ukázky.   |

## <a name="tensorflow-serving"></a>[TensorFlow obsluhující](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1,12 |
| Podporované edice DSVM      | Linux |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | tensorflow_model_server je k dispozici v terminálu. |
| Jak ji spustit      |  Ukázky jsou k dispozici [online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1.0.3 |
| Podporované edice DSVM      | Linux |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  |Theano je nainstalován v Pythonu 2,7 (_root_) a v prostředí Python 3,5 (_py35_). |
| Jak ji spustit      |  Terminál: aktivujte požadovanou verzi Pythonu (root nebo py35), spusťte Python a pak importujte Theano.<br/>* Jupyter: vyberte jádro Python 2,7 nebo 3,5 a pak importujte Theano.  <br/>Chcete-li vyřešit poslední chybu MKL (Math kernel Library), musíte nejprve nastavit vrstvu vláken MKL následujícím způsobem:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |