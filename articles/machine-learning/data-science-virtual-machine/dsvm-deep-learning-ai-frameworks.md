---
title: Architektura AI a hloubkového učení – Azure | Dokumentace Microsoftu
description: Další informace o hloubkového učení, architekturami a nástroji, které jsou podporovány na virtuální počítač pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 610b140fd6049842b993c17fd53bebf799f83f5c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453140"
---
# <a name="deep-learning-and-ai-frameworks"></a>Architektura AI a hloubkového učení
[Virtuální počítač pro datové vědy](https://aka.ms/dsvm) (DSVM) a [hloubkového učení VM](https://aka.ms/dsvm/deeplearning) podporuje řadu architektury hloubkového učení, které vám pomůžou vytvářet aplikace umělou inteligenci (AI) k prediktivním analýzám a kognitivní možnosti, jako jsou bitové kopie a umožňující porozumět jazyku.

Tady jsou uvedené podrobnosti o všech hloubkového učení architektur, které jsou k dispozici na datové VĚDY.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Microsoft Cognitive Toolkit (CNTK) je nainstalován Python 3.5 na [operačních systémů Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Odkazy na ukázky      | Ukázkové poznámkové bloky Jupyter jsou zahrnuty.     |
| Související nástroje na datové VĚDY      | Keras      |
| Jak používat nebo ji spustit?    | * V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/> * V Jupyter: Připojte se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), otevřete CNTK adresář pro ukázky. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | TensorFlow je nainstalován Python 3.5 na [operačních systémů Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Odkazy na ukázky      | Ukázkové poznámkové bloky Jupyter jsou zahrnuty.     |
| Související nástroje na datové VĚDY      | Keras      |
| Jak používat nebo ji spustit?    | * V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/> * V Jupyter: Připojte se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), otevřete TensorFlow adresář pro ukázky.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Co je to?   | Architektura Distribued hloubkového učení pro TensorFlow      |
| Podporované DSVM edice      | Ubuntu     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Horovod je nainstalován Python 3.5 na [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Odkazy na ukázky      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Související nástroje na datové VĚDY      | TensorFlow      |
| Jak používat nebo ji spustit?    | V terminálu: aktivovat správné prostředí a potom spusťte Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní API vysoké úrovně obsáhlého learningu      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | TensorFlow je nainstalován Python 3.5 na [operačních systémů Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Odkazy na ukázky      | https://github.com/fchollet/keras/tree/master/examples      |
| Související nástroje na datové VĚDY      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Jak používat nebo ji spustit?    | * V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/> * V Jupyter: Stáhnout ukázky z Githubu umístění, připojte se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak otevřete adresář vzorku. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Ubuntu     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Je nainstalovaný Caffe `/opt/caffe`.    |
| Jak přepnout na Python 2.7 | Spusťte `source activate root`. |
| Odkazy na ukázky      | Ukázky jsou součástí `/opt/caffe/examples`.      |
| Související nástroje na datové VĚDY      | Caffe2      |

### <a name="how-to-use--run-it"></a>Jak používat nebo ji spustit?

Přihlaste se k virtuálnímu počítači, pak spusťte nové zařízení a zadejte pomocí X2Go

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Otevře se nové okno prohlížeče s ukázkové poznámkové bloky.

Binární soubory jsou nainstalovány v /opt/caffe/build/install/bin.

Nainstalovaná verze Caffe vyžaduje Python 2.7 a nebude fungovat s Python 3.5 aktivovat ve výchozím nastavení. Spustit `source activate root` přepnout Anaconda prostředí.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Ubuntu     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Je nainstalovaný Caffe2 [prostředí conda Python 2.7 (uživatel root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Zdroj je v `/opt/caffe2`. |
| Odkazy na ukázky      | JupyterHub jsou součástí ukázkové poznámkové bloky. |
| Související nástroje na datové VĚDY      | Caffe      |
| Jak používat nebo ji spustit?    | * V terminálu: aktivovat [prostředí Pythonu kořenové](dsvm-languages.md#python-linux-and-windows-server-2012-edition)spuštění Pythonu a importovat caffe2. <br/> * V JupyterHub: [připojit k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak přejděte do adresáře Caffe2 najít ukázkové poznámkové bloky. Některé poznámkových bloků vyžadují kořenový Caffe2 možné nastavit v kódu Pythonu; Zadejte /opt/caffe2. |
| Vytváření poznámky | Caffe2 je sestaven ze zdroje v Linuxu a zahrnuje CUDA, cuDNN a Intel MKL. Aktuální potvrzení je 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, který jste vybrali pro zvýšení stability na všech GPU a ukázky otestovat. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Je nainstalovaný chainer [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Nainstaluje se také ChainerRL a ChainerCV.   |
| Odkazy na ukázky      | JupyterHub jsou součástí ukázkové poznámkové bloky. |
| Související nástroje na datové VĚDY      | Caffe      |
| Jak používat nebo ji spustit?  | * V terminálu: aktivovat [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) prostředí, spusťte _python_, importujte chainer. <br/> * V JupyterHub: [připojit k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak přejděte do adresáře, Chainer najít ukázkové poznámkové bloky.


## <a name="deep-water"></a>Hloubkové vody

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní hloubkového učení pro H2O      |
| Podporované DSVM edice      | Ubuntu     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Hloubkové Water se instaluje v [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a je také k dispozici v `/dsvm/tools/deep_water`.   |
| Odkazy na ukázky      | JupyterHub jsou součástí ukázkové poznámkové bloky.      |
| Související nástroje na datové VĚDY      | H2O Sparkling Water      |

### <a name="how-to-use--run-it"></a>Jak používat nebo ji spustit?

Hloubkové vody vyžaduje CUDA 8 s cuDNN 5.1. Toto není v cestě knihovny ve výchozím nastavení, jak používat další architektury hloubkového učení CUDA 9 a cuDNN 7. Pro účely hloubkové vody CUDA 8 + cuDNN 5.1:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Použití obsáhlého vody:
* V terminálu: aktivovat [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) prostředí, spusťte _python_. <br/>
* V JupyterHub: [připojit k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak přejděte do adresáře deep_water najít ukázkové poznámkové bloky.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Je nainstalovaný MXNet `C:\dsvm\tools\mxnet` na Windows a `/dsvm/tools/mxnet` v Linuxu. Python vazby na nainstalovaných v Python 3.5 [operačních systémů Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Vazby R jsou také nainstalované na Ubuntu.   |
| Odkazy na ukázky      | Ukázkové poznámkové bloky Jupyter jsou zahrnuty.    |
| Související nástroje na datové VĚDY      | Keras      |
| Jak používat nebo ji spustit?    | * V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/> * V Jupyter: Připojte se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), otevřete mxnet adresář pro ukázky.  |
 | Vytváření poznámky | MXNet je sestaven ze zdroje v Linuxu. Toto sestavení obsahuje CUDA, cuDNN, NCCL a MKL. |

## <a name="nvidia-digits"></a>NVIDIA ČÍSLIC

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení systém NVIDIA pro rychlé školení modely obsáhlého learningu      |
| Podporované DSVM edice      | Ubuntu     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | ČÍSLICE se instaluje v `/dsvm/tools/DIGITS` a je k dispozici služba volá _číslic_.   |

### <a name="how-to-use--run-it"></a>Jak používat nebo ji spustit?

Přihlaste se k virtuálnímu počítači s X2Go. V terminálu spusťte službu:

    sudo systemctl start digits

Službu spusťte trvá přibližně jednu minutu. Spusťte webový prohlížeč a přejděte do `http://localhost:5000`. Mějte na paměti, že ČÍSLIC neposkytuje zabezpečené přihlášení a by neměly být vystaveny mimo virtuální počítač.



## <a name="nvidia-smi"></a>NVIDIA smi

|    |           |
| ------------- | ------------- |
| Co je to?   | NVIDIA nástroj pro dotazování aktivita GPU      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | _NVIDIA smi_ je k dispozici v systémové cestě.   |
| Jak používat nebo ji spustit? | Spuštění příkazového řádku (na Windows) nebo terminálu (v Linuxu) a potom spusťte _nvidia smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Ubuntu     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Theano je nainstalovaný Python 2.7 (_kořenové_), a také Python 3.5 (_py35_) prostředí.   |
| Související nástroje na datové VĚDY      | Keras      |
| Jak používat nebo ji spustit?    | * V terminálu aktivujte verzi Pythonu (kořenové nebo py35), spuštění python a pak importovat theano. <br/> * V Jupyter vyberte Python 2.7 nebo 3.5 jádra a poté importovat theano.  <br/>Poslední MKL chybu obejít, musíte nejdřív nastavit MKL dělení na vlákna vrstvy:<br/><br/>_export MKL_THREADING_LAYER=GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Ubuntu     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Je nainstalovaný svítilnou `/dsvm/tools/torch`. PyTorch je nainstalovaný Python 2.7 (_kořenové_), a také Python 3.5 (_py35_) prostředí.   |
| Odkazy na ukázky      | Jsou umístěny na ukázky svítilnou `/dsvm/samples/torch`. Jsou umístěny na ukázky PyTorch `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení      |
| Podporované DSVM edice      | Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Je nainstalovaný PyTorch [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Odkazy na ukázky      | Jsou zahrnuty ukázkové poznámkové bloky Jupyter a ukázky najdete také v /dsvm/samples/pytorch.      |
| Související nástroje na datové VĚDY      | Torch      |
| Jak používat nebo ji spustit |
* V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/> * V Jupyter: Připojte se k [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), otevřete PyTorch adresář pro ukázky.  |

## <a name="mxnet-model-server"></a>Server MXNet modelu

|    |           |
| ------------- | ------------- |
| Co je to?   | Server k vytvoření koncových bodů HTTP pro modely MXNet a ONNX      |
| Podporované DSVM edice      | Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | _mxnet-model-server_ je k dispozici na na terminálu.   |
| Odkazy na ukázky      | Hledejte aktuální ukázky na [stránka serveru modelu MXNet](https://github.com/awslabs/mxnet-model-server).    |
| Související nástroje na datové VĚDY      | MXNet      |

## <a name="tensorflow-serving"></a>Obsluha TensorFlow

|    |           |
| ------------- | ------------- |
| Co je to?   | Server pro spuštění v rámci modelu TensorFlow odvozování      |
| Podporované DSVM edice      | Linux     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | _tensorflow_model_server_ je k dispozici, v terminálu.   |
| Odkazy na ukázky      | Ukázky jsou k dispozici [online](https://www.tensorflow.org/serving/).      |
| Související nástroje na datové VĚDY      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové učení server odvození z NVIDIA. |
| Podporované DSVM edice      | Ubuntu     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | TensorRT se nainstaluje jako _apt_ balíčku.   |
| Odkazy na ukázky      | Ukázky jsou k dispozici [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Související nástroje na datové VĚDY      | TensorFlow obsluhuje, MXNet Model serveru  |



