---
title: '& Rozhraní AI pro hloubkové učení'
titleSuffix: Azure Data Science Virtual Machine
description: Dostupné architektury a nástroje pro hloubkové učení na platformě Azure Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: fd38bf1f7741c4d610ef43a12d90533d4ac7b703
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802417"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Rozhraní pro hloubkové učení a AI pro Azure Data Science VM
Architektury hloubkového učení na DSVM jsou uvedené níže.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Podporované verze | |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Caffe je nainstalován v `/opt/caffe`.   Ukázky jsou v `/opt/caffe/examples`.|
| Jak ji spustit      | pomocí X2Go se přihlaste k VIRTUÁLNÍmu počítači a potom spusťte nový terminál a zadejte následující:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Otevře se nové okno prohlížeče s ukázkami poznámkových bloků. Binární soubory jsou nainstalované v/opt/Caffe/Build/Install/bin.<br/><br/>Nainstalovaná verze Caffe vyžaduje Python 2,7 a nebude fungovat s Python 3,5, který je ve výchozím nastavení aktivovaný. Pokud chcete přejít na Python 2,7, spusťte `source activate root` a přepněte se do prostředí Anaconda.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Podporované verze | |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Caffe2 je nainstalován v prostředí [Python 2,7 (root) conda. |
| Jak ji spustit      | Terminál: Spusťte Python a importujte Caffe2. <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom v adresáři Caffe2 vyhledejte ukázkové poznámkové bloky. Některé poznámkové bloky vyžadují, aby byl v kódu Pythonu nastavený kořen Caffe2. Zadejte/opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 5,2 |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | V Pythonu 3,5 se instaluje chainer. |
| Jak ji spustit      | Terminál: aktivujte prostředí Python 3,5, spusťte `python` a pak `import chainer`. <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a pak vyhledejte ukázkové poznámkové bloky v adresáři chainer.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA, cuDNN, ovladač NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Podporované verze | 10.0.130|
| Podporované edice DSVM      | Windows a Linux   |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  |na systémové cestě je k dispozici _NVIDIA-SMI_ .  |
| Jak ji spustit      | Otevřete příkazový řádek (ve Windows) nebo terminálu (na platformě Linux) a potom spusťte _NVIDIA-SMI_. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Podporované verze | 0.16.1|
| Podporované edice DSVM      | Linux (Ubuntu)   |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Horovod je nainstalován v Pythonu 3,5 |
| Jak ji spustit      | Aktivujte správné prostředí v terminálu a pak spusťte Python. |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 2.2.4 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Keras je nainstalovaný v Pythonu 3,6 ve Windows a v Pythonu 3,5u v systému Linux. |
| Jak ji spustit      | Aktivujte správné prostředí v terminálu a pak spusťte Python. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 2.5.1 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | CNTK je nainstalovaný v Pythonu 3,6 ve [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) a v Pythonu pro [Linux](./dsvm-languages.md#python-linux-edition)3,5.) |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a spusťte Python. <br/>Jupyter: Připojte se k [Jupyter](provision-vm.md) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom otevřete adresář CNTK pro ukázky. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1.2.0 |
| Podporované edice DSVM      | Linux |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Nainstalováno v [pythonu 3,5](dsvm-languages.md#python-linux-edition). Obsahuje ukázky Jupyter poznámkových bloků a ukázky jsou v/dsvm/Samples/pytorch. |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a pak spusťte Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Připojte se a otevřete adresář PyTorch pro ukázky.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1,13 |
| Podporované edice DSVM      | Windows, Linux |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Nainstalováno v Pythonu 3,5 v systémech [Linux](dsvm-languages.md#python-linux-edition) a Python 3,6 ve [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a pak spusťte Python. <br/> * Jupyter: Připojte se k [Jupyter](provision-vm.md) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a potom otevřete adresář TensorFlow pro ukázky.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow obsluhující](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1,12 |
| Podporované edice DSVM      | Linux |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | tensorflow_model_server je k dispozici v terminálu. |
| Jak ji spustit      |  Ukázky jsou k dispozici [online](https://www.tensorflow.org/serving/).   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Podporované verze | 1.0.3 |
| Podporované edice DSVM      | Linux |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  |Theano je nainstalován v Pythonu 2,7 (_root_) a v prostředí Python 3,5 (_py35_). |
| Jak ji spustit      |  Terminál: aktivujte požadovanou verzi Pythonu (root nebo py35), spusťte Python a pak importujte Theano.<br/>* Jupyter: vyberte jádro Python 2,7 nebo 3,5 a pak importujte Theano.  <br/>Chcete-li vyřešit poslední chybu MKL (Math kernel Library), musíte nejprve nastavit vrstvu vláken MKL následujícím způsobem:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |