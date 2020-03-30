---
title: Rámce hloubkového učení & umělou id
titleSuffix: Azure Data Science Virtual Machine
description: Dostupné architektury a nástroje pro hloubkové učení na Virtuálním počítači Azure Data Science.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270066"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Hloubkové učení a architektury ai pro virtuální počítač Azure Data Science
Rámce hlubokého učení na DSVM jsou uvedeny níže.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak je konfigurován / nainstalován na DSVM?  | Caffe je `/opt/caffe`nainstalován v aplikaci .   Vzorky `/opt/caffe/examples`jsou v .|
| Jak ji spustit      | pomocí X2Go se přihlaste k virtuálnímu počítači a pak spusťte nový terminál a zadejte následující:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Otevře se nové okno prohlížeče s ukázkovými poznámkovými bloky. Binární soubory jsou nainstalovány v aplikaci /opt/caffe/build/install/bin.<br/><br/>Nainstalovaná verze Caffe vyžaduje Python 2.7 a nebude fungovat s Pythonem 3.5, který je ve výchozím nastavení aktivován. Chcete-li přepnout na `source activate root` Python 2.7, spusťte přepínač do prostředí Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak je konfigurován / nainstalován na DSVM?  | Caffe2 je nainstalován v prostředí [Python 2.7 (root) conda. |
| Jak ji spustit      | Terminál: Spusťte Python a importujte Caffe2. <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a pak přejděte do adresáře Caffe2 najít ukázkové notebooky. Některé poznámkové bloky vyžadují, aby byl kořen Caffe2 nastaven v kódu Pythonu; zadejte /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 5.2 |
| Podporované edice DSVM      | Linux (Ubuntu)     |
| Jak je konfigurován / nainstalován na DSVM?  | Chainer je nainstalován v Pythonu 3.5. |
| Jak ji spustit      | Terminál: Aktivujte prostředí Pythonu `python`3.5, spusťte a pak `import chainer`. <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a pak přejděte do adresáře Chainer najít ukázkové notebooky.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, ovladač NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 10.0.130|
| Podporované edice DSVM      | Windows a Linux   |
| Jak je konfigurován / nainstalován na DSVM?  |_nvidia-smi_ je k dispozici na systémové cestě.  |
| Jak ji spustit      | Otevřete příkazový řádek (v systému Windows) nebo terminál (na Linuxu) a poté spusťte _nvidia-smi_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 0.16.1|
| Podporované edice DSVM      | Linux (Ubuntu)   |
| Jak je konfigurován / nainstalován na DSVM?  | Horovod je nainstalován v Pythonu 3.5 |
| Jak ji spustit      | Aktivujte správné prostředí na terminálu a spusťte Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 2.2.4 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak je konfigurován / nainstalován na DSVM?  | Keras je nainstalován v Pythonu 3.6 v systému Windows a v Pythonu 3.5 v Linuxu |
| Jak ji spustit      | Aktivujte správné prostředí na terminálu a spusťte Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 2.5.1 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak je konfigurován / nainstalován na DSVM?  | CNTK je nainstalován v Pythonu 3.6 v [systému Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a v Pythonu 3.5 na [Linuxu](./dsvm-tools-languages.md#python-linux-edition)) |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a spusťte Python. <br/>Jupyter: Připojte se k [Jupyter](provision-vm.md) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a otevřete adresář CNTK pro ukázky. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 1.3.0 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak je konfigurován / nainstalován na DSVM?  | MXNet je `C:\dsvm\tools\mxnet` nainstalován v `/dsvm/tools/mxnet` systému Windows a ubuntu. Vazby Pythonu jsou nainstalované v Pythonu 3.6 v [systému Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a v Pythonu 3.5 na [Linuxu](./dsvm-tools-languages.md#python-linux-edition)) R vazby jsou také součástí Ubuntu DSVM. |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí conda a poté spusťte `import mxnet`. <br/>Jupyter: Připojte se k [Jupyter](provision-vm.md#access-the-dsvm) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)a otevřete `mxnet` adresář pro ukázky. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 1.0.1 |
| Podporované edice DSVM      | Windows a Linux   |
| Jak je konfigurován / nainstalován na DSVM?  | MXNet Model Server je nainstalovaný v Pythonu 3.6 v [systému Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) a v Pythonu 3.5 na [Linuxu](./dsvm-tools-languages.md#python-linux-edition)) |
| Jak ji spustit      | Terminál: `sudo systemctl stop jupyterhub` Spusťte nejprve zastavit službu JupyterHub, protože oba poslouchají na stejném portu. Poté aktivujte správné prostředí conda a spusťte`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Rozhraní pro správu systému NVidia (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) |  |
| Podporované edice DSVM      | Windows a Linux   |
| K čemu to je? | Nástroj NVIDIA pro dotazování na aktivitu GPU |
| Jak je konfigurován / nainstalován na DSVM?  | `nvidia-smi`je na cestě systému. |
| Jak ji spustit      | Na virtuálním počítači **s GPU**otevřete příkazový řádek (v systému Windows) nebo terminál (na Linuxu) a spusťte `nvidia-smi`. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Podporované edice DSVM      | Linux |
| Jak je konfigurován / nainstalován na DSVM?  | Instalováno v [Pythonu 3.5](dsvm-tools-languages.md#python-linux-edition). Ukázkové poznámkové bloky Jupyter jsou zahrnuty a vzorky jsou v /dsvm/samples/pytorch. |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a spusťte Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Připojte se a potom otevřete adresář PyTorch pro ukázky.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 1.13 |
| Podporované edice DSVM      | Windows, Linux |
| Jak je konfigurován / nainstalován na DSVM?  | Nainstalováno v Pythonu 3.5 v [Linuxu](dsvm-tools-languages.md#python-linux-edition) a Pythonu 3.6 ve [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Jak ji spustit      | Terminál: Aktivujte správné prostředí a spusťte Python. <br/> * Jupyter: Připojte se k [Jupyter](provision-vm.md) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a pak otevřete adresář TensorFlow pro ukázky.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 1.12 |
| Podporované edice DSVM      | Linux |
| Jak je konfigurován / nainstalován na DSVM?  | tensorflow_model_server je k dispozici na terminálu. |
| Jak ji spustit      |  Ukázky jsou k dispozici [online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Podporovaná verze (verze) | 1.0.3 |
| Podporované edice DSVM      | Linux |
| Jak je konfigurován / nainstalován na DSVM?  |Theano je nainstalován v Pythonu 2.7 _(root_) a v Pythonu 3.5 _(py35_) prostředí. |
| Jak ji spustit      |  Terminál: Aktivujte požadovanou verzi Pythonu (root nebo py35), spusťte Python a importujte Theano.<br/>* Jupyter: Vyberte jádro Pythonu 2.7 nebo 3.5 a pak importujte Theano.  <br/>Chcete-li obejít nedávnou chybu knihovny matematických jader (MKL), musíte nejprve nastavit vrstvu vláken MKL následujícím způsobem:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |