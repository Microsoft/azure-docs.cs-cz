---
title: Hloubkové učení a AI architektury - Azure | Microsoft Docs
description: Architektura AI a hloubkového učení
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: cd8579fe29282f3875ecfddf67a34444e3de9ef5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Architektura AI a hloubkového učení
[Datové vědy virtuálního počítače](http://aka.ms/dsvm) (DSVM) a [počítač hloubkového učení](http://aka.ms/dsvm/deeplearning) podporuje mnoho hloubkové learning rozhraní k sestavení aplikace umělé Intelligence (AI) s prediktivní analýzy a kognitivní možnosti jako bitovou kopii a znalosti jazyka. 

Zde jsou uvedeny podrobnosti na všechny hloubkového učení rozhraní, které jsou k dispozici na DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Microsoft kognitivní Toolkit (CNTK) je nainstalována v Python 3.5 na [Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Odkazy na ukázky      | Ukázka poznámkové bloky Jupyter jsou zahrnuty.     |
| Na DSVM souvisejících nástrojích      | Keras      |
| Jak se použít nebo ji spustit?    | * V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/>
 * V Jupyter: Připojení k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), otevřete adresář CNTK pro ukázky. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | TensorFlow je nainstalován v Python 3.5 na [Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Odkazy na ukázky      | Ukázka poznámkové bloky Jupyter jsou zahrnuty.     |
| Na DSVM souvisejících nástrojích      | Keras      |
| Jak se použít nebo ji spustit?    | * V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/>
 * V Jupyter: Připojení k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), otevřete adresář TensorFlow pro ukázky.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Co je to?   | Architektura hloubkové learning Distribued pro TensorFlow      |
| Podporované DSVM edice      | Ubuntu     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Horovod je nainstalován v Python 3.5 na [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Odkazy na ukázky      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Na DSVM souvisejících nástrojích      | TensorFlow      |
| Jak se použít nebo ji spustit?    | V terminálu: aktivovat správné prostředí a potom spusťte Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | TensorFlow je nainstalován v Python 3.5 na [Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Odkazy na ukázky      | https://github.com/fchollet/keras/tree/master/examples      |
| Na DSVM souvisejících nástrojích      | Microsoft kognitivní Toolkit, TensorFlow, Theano      |
| Jak se použít nebo ji spustit?    | * V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/>
 * V Jupyter: Stáhnout ukázky z Githubu umístění, připojí se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak otevřete adresář ukázka. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Ubuntu     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Je nainstalovaný Caffe `/opt/caffe`.    |
| Jak přepnout na Python 2.7 | Spusťte `source activate root`. |
| Odkazy na ukázky      | Ukázky jsou součástí `/opt/caffe/examples`.      |
| Na DSVM souvisejících nástrojích      | Caffe2      |
### <a name="how-to-use--run-it"></a>Jak se použít nebo ji spustit?  

Přihlaste se k virtuálnímu počítači, pak spusťte nový terminál a zadat pomocí X2Go

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Otevře se nové okno prohlížeče s ukázka poznámkových bloků.

Binární soubory jsou nainstalovány v /opt/caffe/build/install/bin.

Nainstalovaná verze Caffe vyžaduje Python 2.7 a nebude fungovat se ve výchozím nastavení aktivované 3.5 Python. Spustit `source activate root` přepnout Anaconda prostředí. 

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Ubuntu     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Je nainstalovaný Caffe2 [conda prostředí Python 2.7 (uživatel root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Zdroj je v `/opt/caffe2`. |
| Odkazy na ukázky      | Ukázka poznámkových bloků jsou součástí JupyterHub. |
| Na DSVM souvisejících nástrojích      | Caffe      |
| Jak se použít nebo ji spustit?    | * V terminálu: aktivovat [prostředí Python kořenové](dsvm-languages.md#python-linux-and-windows-server-2012-edition)spusťte Python a importovat caffe2. <br/> * V JupyterHub: [připojit k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak přejděte do adresáře Caffe2 najít ukázkové poznámkových bloků. Některé poznámkových bloků vyžadují kořenu Caffe2 ve kód Python; Zadejte /opt/caffe2. |
| Poznámky k sestavení | Caffe2 vychází ze zdroje v Linuxu a zahrnuje CUDA, cuDNN a Intel MKL. Aktuální potvrzení je 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, který jste vybrali pro stabilitu na všech grafickými procesory a ukázky testována. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Zřetězeného souboru je nainstalován v [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL a ChainerCV jsou také nainstalované.   |
| Odkazy na ukázky      | Ukázka poznámkových bloků jsou součástí JupyterHub. |
| Na DSVM souvisejících nástrojích      | Caffe      |
| Jak se použít nebo ji spustit?  | * V terminálu: aktivovat [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) prostředí, spusťte _python_, pak importovat zřetězeného souboru. <br/>
* V JupyterHub: [připojit k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak přejděte do adresáře zřetězeného souboru najít ukázkové poznámkových bloků.


## <a name="deep-water"></a>Hloubkové horních

|    |           |
| ------------- | ------------- |
| Co je to?   | Architektura hloubkové learning pro H2O      |
| Podporované DSVM edice      | Ubuntu     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Hloubkové horních je nainstalován v [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a je také dostupná v `/dsvm/tools/deep_water`.   |
| Odkazy na ukázky      | Ukázka poznámkových bloků jsou součástí JupyterHub.      |
| Na DSVM souvisejících nástrojích      | H2O, šumivého horních      |

### <a name="how-to-use--run-it"></a>Jak se použít nebo ji spustit?  

Hloubkové horních vyžaduje CUDA 8 s cuDNN 5.1. Toto není na cestě knihovny, ve výchozím nastavení, jako ostatní platformy hloubkové learning použijte CUDA 9 a cuDNN 7. Použití CUDA 8 + cuDNN 5.1 pro přímý horních:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Použití hloubkové horních:
* V terminálu: aktivovat [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) prostředí, spusťte _python_. <br/>
* V JupyterHub: [připojit k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak přejděte do adresáře deep_water najít ukázkové poznámkových bloků.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Je nainstalovaný MXNet `C:\dsvm\tools\mxnet` v systému Windows a `/dsvm/tools/mxnet` v systému Linux. Python vazby na nainstalovaných v Python 3.5 [Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Vazby R jsou také nainstalované na Ubuntu.   |
| Odkazy na ukázky      | Ukázka poznámkové bloky Jupyter jsou zahrnuty.    |
| Na DSVM souvisejících nástrojích      | Keras      |
| Jak se použít nebo ji spustit?    | * V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/>
 * V Jupyter: Připojení k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), otevřete adresář mxnet pro ukázky.  |
 | Poznámky k sestavení | MXNet vychází ze zdroje v systému Linux. Toto sestavení obsahuje CUDA, cuDNN, NCCL a MKL. |

## <a name="nvidia-digits"></a>NVIDIA ČÍSLIC

|    |           |
| ------------- | ------------- |
| Co je to?   | Přímý učení systému z NVIDIA pro rychlé cvičení hloubkové learning modely      |
| Podporované DSVM edice      | Ubuntu     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | ČÍSLIC je nainstalován v `/dsvm/tools/DIGITS` a je k dispozici služba byla požádána _číslic_.   |
### <a name="how-to-use--run-it"></a>Jak se použít nebo ji spustit?  

Přihlaste se k virtuálnímu počítači pomocí X2Go. V terminálu spusťte službu:

    sudo systemctl start digits

Službu spusťte trvá přibližně jednu minutu. Spustit webový prohlížeč a přejděte do `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| Co je to?   | NVIDIA nástroj pro dotazování aktivita GPU      |
| Podporované DSVM edice      | Windows, Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | _NVIDIA smi_ je k dispozici na cestě k systému souborů.   |
| Jak se použít nebo ji spustit? | Spustit příkazový řádek (v systému Windows) nebo terminál (v systému Linux), a poté spusťte _nvidia smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Ubuntu     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Theano je nainstalován v Python 2.7 (_kořenové_), a také Python 3.5 (_py35_) prostředí.   |
| Na DSVM souvisejících nástrojích      | Keras      |
| Jak se použít nebo ji spustit?    | * V terminálu aktivujte verzi Pythonu (kořenové nebo py35), spusťte python a pak importovat theano. <br/> 
* V Jupyter vyberte Python 2.7 nebo 3.5 jádra a pak importovat theano.  
<br/>
Obejít poslední chyby MKL, musíte nejprve nastavit MKL dělení na vlákna vrstvy:<br/><br/>
_Export MKL_THREADING_LAYER = GNU_
|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Ubuntu     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Je nainstalovaný svítilnou `/dsvm/tools/torch`. PyTorch je nainstalován v Python 2.7 (_kořenové_), a také Python 3.5 (_py35_) prostředí.   |
| Odkazy na ukázky      | Ukázky svítilnou jsou umístěné na `/dsvm/samples/torch`. Ukázky PyTorch jsou umístěné na `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Co je to?   | Hloubkové learning framework      |
| Podporované DSVM edice      | Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | Je nainstalovaný PyTorch [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Odkazy na ukázky      | Ukázka poznámkové bloky Jupyter jsou zahrnuty a ukázky najdete také v /dsvm/samples/pytorch.      |
| Na DSVM souvisejících nástrojích      | Torch      |
| Postup použití možnosti / ji spustit | 
* V terminálu: aktivovat správné prostředí a potom spusťte Python. <br/>
 * V Jupyter: Připojení k [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), otevřete adresář PyTorch pro ukázky.  |

## <a name="mxnet-model-server"></a>MXNet Model serveru

|    |           |
| ------------- | ------------- |
| Co je to?   | Serveru k vytvoření koncových bodů protokolu HTTP pro MXNet a ONNX modely      |
| Podporované DSVM edice      | Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | _mxnet. model serveru_ je k dispozici v terminálu.   |
| Odkazy na ukázky      | Vyhledejte aktuální ukázky na [stránka MXNet Model serveru](https://github.com/awslabs/mxnet-model-server).    |
| Na DSVM souvisejících nástrojích      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow slouží

|    |           |
| ------------- | ------------- |
| Co je to?   | Serveru ke spuštění inferencing na TensorFlow modelu      |
| Podporované DSVM edice      | Linux     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | _tensorflow_model_server_ jsou k dispozici v terminálu.   |
| Odkazy na ukázky      | Ukázky jsou k dispozici [online](https://www.tensorflow.org/serving/).      |
| Na DSVM souvisejících nástrojích      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Co je to?   | Přímý server odvozená z NVIDIA učení. |
| Podporované DSVM edice      | Ubuntu     |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?  | TensorRT je nainstalován jako _výstižný_ balíčku.   |
| Odkazy na ukázky      | Ukázky jsou k dispozici [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Na DSVM souvisejících nástrojích      | TensorFlow obsluhuje, MXNet Model serveru  |



