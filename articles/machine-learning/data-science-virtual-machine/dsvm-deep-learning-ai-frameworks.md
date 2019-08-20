---
title: '& Rozhraní AI pro hloubkové učení'
titleSuffix: Azure Data Science Virtual Machines
description: Architektury a nástroje pro hloubkové učení na platformě Azure Data Science Virtual Machine, včetně TensorFlow, PyTorch, Keras, Caffe, MXNet, Horovod, Theano, chainer a dalších.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: de407f4b4de58abebf87b82ff6f76342a0e9f7e0
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575044"
---
# <a name="deep-learning-and-ai-frameworks"></a>Architektura AI a hloubkového učení
[Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) podporuje řadu prostorů pro hloubkové učení, které vám pomůžou sestavovat aplikace umělal Intelligence (AI) s využitím prediktivních analýz a funkcí rozpoznávání, jako je obrázek a porozumění jazyka.

K dispozici jsou DSVM architektury pro hloubkové učení, které zahrnují:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ Server MXNet modelu
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Hloubkové vody
+ NVIDIA ČÍSLIC
+ NVIDIA smi
+ Obsluha TensorFlow
+ TensorRT
+ Microsoft Cognitive Toolkit

|Nástroje&nbsp;DL&nbsp;naDSVM&nbsp;|Windows|Linux|Poznámky&nbsp;k použití|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/) | Ano (Windows 2016) | Ano |Instaluje se v Pythonu 3,5 v systémech [Linux a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3,6 ve [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Ukázky Jupyter poznámkových bloků jsou součástí DSVM.<br/><br/>**Pro spuštění**:<br/>* Terminál: Aktivujte správné prostředí a pak spusťte Python. <br/> Jupyter Připojte se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)a pak otevřete adresář TensorFlow pro ukázky.  |
|[PyTorch](https://pytorch.org/)| Ne | Ano |Nainstalováno v [pythonu 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Obsahuje ukázky Jupyter poznámkových bloků a ukázky jsou v/dsvm/Samples/pytorch.    <br/><br/>**Spuštění**<br/>* Terminál: Aktivujte správné prostředí a pak spusťte Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Připojte se a otevřete adresář PyTorch pro ukázky.  |
|[Keras](https://keras.io/)| Ano | Ano |Rozhraní API je nainstalované v Pythonu 3,5 v systémech [Linux a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3,6 ve [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Ukázky](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**Pro spuštění**:<br/>* Terminál: Aktivujte správné prostředí a pak spusťte Python. <br/> Jupyter Stáhněte si ukázky z umístění GitHubu, připojte se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)a pak otevřete ukázkový adresář. |
|[Caffe](https://github.com/caffe2/caffe2) | Ne |Ano (Ubuntu)|Je nainstalovaný Caffe `/opt/caffe`.   Ukázky jsou v `/opt/caffe/examples`. <br/><br/>Pokud **ho chcete spustit**, přihlaste se k virtuálnímu počítači pomocí X2Go, potom spusťte nového terminálu a zadejte:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Otevře se nové okno prohlížeče s ukázkové poznámkové bloky. Binární soubory jsou nainstalovány v /opt/caffe/build/install/bin.<br/><br/>Nainstalovaná verze Caffe vyžaduje Python 2.7 a nebude fungovat s Python 3.5 aktivovat ve výchozím nastavení. Pokud chcete přepnout na Python 2,7, `source activate root` spusťte příkaz a přepněte prostředí Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | Ne |Ano (Ubuntu)|Je nainstalovaný Caffe2 [prostředí conda Python 2.7 (uživatel root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Zdroj je v `/opt/caffe2`.<br/>JupyterHub jsou součástí ukázkové poznámkové bloky.<br/><br/>**Pro spuštění**:<br/>* V terminálu: aktivovat [prostředí Pythonu kořenové](dsvm-languages.md#python-linux-and-windows-server-2012-edition)spuštění Pythonu a importovat caffe2. <br/> * V JupyterHub: [připojit k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), pak přejděte do adresáře Caffe2 najít ukázkové poznámkové bloky. Některé poznámkových bloků vyžadují kořenový Caffe2 možné nastavit v kódu Pythonu; Zadejte /opt/caffe2. |
|[Torch](http://torch.ch/) | Ne |Ano (Ubuntu)|Je nainstalovaný svítilnou `/dsvm/tools/torch`. PyTorch je nainstalovaný Python 2.7 (_kořenové_), a také Python 3.5 (_py35_) prostředí. Ukázky Torch jsou v `/dsvm/samples/torch` a PyTorch ukázky jsou v `/dsvm/samples/pytorch`. |
|[MXNet](https://mxnet.io/) | Ano (Windows 2016) | Ano|Je nainstalovaný MXNet `C:\dsvm\tools\mxnet` na Windows a `/dsvm/tools/mxnet` v Linuxu. Python vazby na nainstalovaných v Python 3.5 [operačních systémů Linux a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Vazby R jsou také nainstalované na Ubuntu.<br/><br/>Ukázkové poznámkové bloky Jupyter jsou zahrnuty. <br/><br/>**Pro spuštění**:<br/>Terminálovému Aktivujte správné prostředí a pak spusťte Python. <br/> Jupyter Připojte se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)a pak otevřete adresář mxnet pro ukázky.|
|[Server MXNet modelů](https://github.com/awslabs/mxnet-model-server) | Ne | Ano |Server pro vytváření koncových bodů HTTP pro modely MXNet a ONNX. _mxnet-model-server_ je k dispozici na na terminálu. Ukázky na [stránce serveru modelu MXNet](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Ne | Ano (Ubuntu) |Distribuované prostředí hloubkového učení pro TensorFlow. Horovod je nainstalován Python 3.5 na [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Zobrazit ukázky](https://github.com/uber/horovod/tree/master/examples)<br/><br/>Pokud **ho chcete spustit**, aktivujte v terminálu správné prostředí a pak spusťte Python. |
|[Theano](https://github.com/Theano/Theano) | Ne | Ano (Ubuntu) |Theano je nainstalovaný Python 2.7 (_kořenové_), a také Python 3.5 (_py35_) prostředí.<br/><br/>**Pro spuštění**: <br/>Terminálovému Aktivujte si požadovanou verzi Pythonu (root nebo py35), spusťte Python a pak importujte Theano.<br/>Jupyter Vyberte jádro Python 2,7 nebo 3,5 a pak importujte Theano.  <br/>Poslední MKL chybu obejít, musíte nejdřív nastavit MKL dělení na vlákna vrstvy:<br/><br/>_Export MKL_THREADING_LAYER = GNU_|
|[Chainer](https://chainer.org/) |Ne | Ano |Je nainstalovaný chainer [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Nainstaluje se také ChainerRL a ChainerCV. <br/><br/>JupyterHub jsou součástí ukázkové poznámkové bloky.<br/><br/>**Pro spuštění**: <br/>Terminálovému Aktivujte prostředí [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , spusťte _Python_a pak importujte chainer. <br/> * JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)a potom přejděte do adresáře chainer, kde najdete ukázkové poznámkové bloky.|
|[Číslice NVidia](https://github.com/NVIDIA/DIGITS) | Ne | Ano (Ubuntu) |Systém hloubkového učení z NVIDIA pro rychlé školení modelů hloubkového učení. ČÍSLICE se instaluje v `/dsvm/tools/DIGITS` a je k dispozici služba volá _číslic_.  <br/><br/>**Pro spuštění**: <br/>Přihlaste se k virtuálnímu počítači s X2Go. V terminálu spusťte službu ```sudo systemctl start digits```. <br/><br/>Službu spusťte trvá přibližně jednu minutu. Spusťte webový prohlížeč a přejděte do `http://localhost:5000`. Mějte na paměti, že ČÍSLIC neposkytuje zabezpečené přihlášení a by neměly být vystaveny mimo virtuální počítač.|
|[CUDA, cuDNN, ovladač NVIDIA](https://developer.nvidia.com/cuda-toolkit) |Ano | Ano | |
|NVIDIA smi|Ano | Ano |Nástroj NVIDIA pro dotazování aktivity GPU _NVIDIA smi_ je k dispozici v systémové cestě. <br/><br/>Spuštění příkazového řádku (na Windows) nebo terminálu (v Linuxu) a potom spusťte _nvidia smi_.|
|[TensorFlow obsluhující](https://www.tensorflow.org/serving/) | Ne | Ano |Server, který se má odvozovat na TensorFlow modelu. _tensorflow_model_server_ je k dispozici, v terminálu. Ukázky jsou k dispozici [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Ne | Ano (Ubuntu) |Hloubkové učení server odvození z NVIDIA. TensorRT se nainstaluje jako _apt_ balíčku. Ukázky jsou k dispozici [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Ano | Ano | Instaluje se v Pythonu 3,5 v systémech [Linux a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a Python 3,6 ve [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Ukázky Jupyter poznámkových bloků jsou součástí DSVM. <br/><br/>**Pro spuštění**: <br/>Terminálovému Aktivujte správné prostředí a spusťte Python. <br/>Jupyter Připojte se k [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) nebo [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)a pak otevřete adresář CNTK pro ukázky. |
|Hloubkové vody|Ne | Ano (Ubuntu) |Architektura hloubkového učení pro Pro3,5, hluboká voda [](dsvm-languages.md#python-linux-and-windows-server-2012-edition) je nainstalovaná v pythonu `/dsvm/tools/deep_water`a je také dostupná v. JupyterHub jsou součástí ukázkové poznámkové bloky. Hloubkové vody vyžaduje CUDA 8 s cuDNN 5.1. Toto není v cestě knihovny ve výchozím nastavení, jak používat další architektury hloubkového učení CUDA 9 a cuDNN 7. Pro účely hloubkové vody CUDA 8 + cuDNN 5.1:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Použití obsáhlého vody:<br/>* Terminál: aktivujte prostředí [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) a pak spusťte _Python_. <br/>* JupyterHub: [Připojte se k JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)a pak přejděte do adresáře deep_water, kde najdete ukázkové poznámkové bloky.|
