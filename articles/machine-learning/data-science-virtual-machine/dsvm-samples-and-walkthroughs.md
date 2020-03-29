---
title: Ukázkové programy & návody ml
titleSuffix: Azure Data Science Virtual Machine
description: Prostřednictvím těchto ukázek a návodů se dozvíte, jak zpracovat běžné úkoly a scénáře pomocí virtuálního počítače pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900050"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Ukázky na virtuálních počítačích Azure Data Science

Virtuální počítače Azure Data Science (DSVM) obsahují komplexní sadu ukázkového kódu. Tyto ukázky zahrnují poznámkové bloky Jupyter a skripty v jazycích, jako je Python a R.
> [!NOTE]
> Další informace o tom, jak spustit poznámkové bloky Jupyter na virtuálních počítačích datové vědy, najdete v části [Access Jupyter.](#access-jupyter)

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit tyto ukázky, musíte mít zřízena virtuální počítač datové vědy. Podívejte se na rychlé starty pro [Windows](./provision-vm.md) a [Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Dostupné ukázky
| Kategorie vzorků | Popis | Umístění |
| ------------- | ------------- | ------------- |
| Jazyk R  | Ukázky ilustrují scénáře, jako je například připojení s cloudovými úložišti založených na Azure a jak porovnat open source R a Microsoft Machine Learning Server. Vysvětlují také, jak zprovoznit modely na Microsoft Machine Learning Server a SQL Server. <br/> [Jazyk R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Jazyk Python  | Ukázky vysvětlují scénáře, jako je připojení s cloudovými úložišti založených na Azure a jak pracovat s Azure Machine Learning.  <br/> [Jazyk Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julijský jazyk  | Poskytuje podrobný popis vykreslování a hluboké učení v Julia. Také vysvětluje, jak volat C a Python z Julie. <br/> [Julijský jazyk](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ukazuje, jak vytvářet modely strojového učení a hloubkového učení pomocí machine learningu. Nasazujte modely kdekoli. Používejte automatizované strojové učení a inteligentní hyperparametrické ladění. Používejte také správu modelů a distribuované školení. <br/> [Strojové učení](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebooky PyTorch  | Ukázky hlubokého učení, které používají neuronové sítě založené na PyTorch. Notebooky se pohybují od začátečníků až po pokročilé scénáře.  <br/> [Notebooky PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Různé vzorky a techniky neuronové sítě implementované pomocí architektury TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Ukázky hloubkového učení publikované týmem Cognitive Toolkit ve společnosti Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Ukázky hlubokého učení, které používají neuronové sítě založené na Caffe2. Několik notebooků seznamuje uživatele s Caffe2 a jak ji efektivně používat. Příklady zahrnují předběžné zpracování obrázků a vytváření datové sady. Patří mezi ně také regrese a jak používat předem trénované modely. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Ukázky založené na Pythonu, které používají H2O pro skutečné scénáře problémů. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Jazyk SparkML  | Ukázky, které používají funkce sady nástrojů Apache Spark MLLib prostřednictvím pySpark a MMLSpark: Microsoft Machine Learning for Apache Spark na Apache Spark 2.x.  <br/> [Jazyk SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standardní ukázky strojového učení v XGBoost pro scénáře, jako je klasifikace a regrese. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Přístup k Jupyterovi 

Chcete-li získat přístup k Jupyteru, vyberte ikonu **Jupyter** v nabídce plochy nebo aplikace. Můžete také přistupovat Jupyter na linuxové edici DSVM. Chcete-li získat vzdálený přístup z `https://<Full Domain Name or IP Address of the DSVM>:8000` webového prohlížeče, přejděte na Ubuntu.

Chcete-li přidat výjimky a zpřístupnit přístup jupyteru prostřednictvím prohlížeče, použijte následující pokyny:


![Povolit výjimku Jupyter](./media/ubuntu-jupyter-exception.png)


Přihlaste se stejným heslem, které používáte k přihlášení k virtuálnímu počítači datové vědy.
<br/>

**Jupyter domů**
<br/>![Jupyter domů](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Jazyk R 
<br/>![Ukázky R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Jazyk Python
<br/>![Ukázky Pythonu](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julijský jazyk 
<br/>![Ukázky Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Ukázky Azure Machine Learning](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Vzorky PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Vzorky TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Vzorky CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 vzorky](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Vzorky H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Vzorky SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Ukázky XGBoost](./media/xgboost-samples.png)<br/>

