---
title: Ukázky a návody pro virtuální počítače pro datové vědy – Azure | Dokumentace Microsoftu
description: Další informace o ukázky a návody, které vás naučí, jak provádět běžné úlohy a scénáře s virtuální počítač pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: 64be6af340aa02c6c0b094013d2cbd286286aca7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101980"
---
# <a name="samples-on-data-science-virtual-machines"></a>Ukázky na virtuální počítače pro datové vědy

Azure virtuální počítače pro datové vědy zahrnuje komplexní sadu ukázkový kód. Ukázkový kód je ve formě poznámkové bloky Jupyter a skripty v jazycích, jako je Python a R. 
> [!NOTE]
> Další informace o tom, jak spouštět poznámkové bloky Jupyter na vaše virtuální počítače pro datové vědy, najdete v článku [přístup Jupyter](#access-jupyter) oddílu.

## <a name="quick-reference-of-samples"></a>Stručná referenční příručka vzorků
| Ukázky kategorie | Popis | Umístění |
| ------------- | ------------- | ------------- |
| Jazyk R  | Ukázky v jazyce R popisují scénáře, jako je jak se připojit pomocí Azure cloudovými datovými úložišti. Jsou také vysvětlují, jak porovnat open-source jazyka R a Microsoft R. A že popisují, jak zprovoznit modely na Microsoft R serveru nebo systému SQL Server. <br/> [Jazyk R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Jazyk Python  | Ukázky v Pythonu popisují scénáře, jako je připojení pomocí Azure cloudovými úložišti dat a práci s Azure Machine Learning.  <br/> [Jazyk Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Jazyk Julia  | Ukázka v Julia s podrobnostmi o plotting a obsáhlého learningu v Julie. Od Julie také vysvětluje volání jazyka C a Python. <br/> [Jazyk Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Vytvářejte, strojové učení a hloubkového učení modely Machine Learning. Nasazení modelů kdekoli. Využijte automatické strojové učení a inteligentní hyperparametrů. Také pomocí správy modelů a distribuované trénování. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Poznámkové bloky PyTorch  | Hloubkové učení ukázek, které využívají na základě PyTorch neuronových sítí. Poznámkové bloky v rozsahu od Začátečník až po pokročilé scénáře.  <br/> [Poznámkové bloky PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Ukázky různých neuronové sítě a techniky, které jsou implementovány pomocí rozhraní TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Hloubkové učení ukázky publikoval(a) Cognitive Toolkit týmu ve společnosti Microsoft.  <br/> [Sada cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Hloubkové učení ukázek, které využívají na základě caffe2 neuronových sítí. Několik poznámkových bloků seznámit s caffe2 a tom, jak efektivně používat uživatelé. Mezi příklady patří vytvoření bitové kopie úkony předběžného zpracování a datové sady. Také obsahují přepsání a použití předem natrénovaných modelů. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Ukázky založenou na jazyce Python, používající H2O scénářem z reálného prostředí problémů. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Ve SparkML jazyka  | Ukázky, které používají funkce sady nástrojů knihovna Spark MLLib prostřednictvím pySpark a MMLSpark – Microsoft Machine Learning pro Apache Spark na Apache Sparku 2.x.  <br/> [Ve SparkML jazyka](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standardní strojového učení vzorků v XGBoost scénáře, jako je klasifikačních a regresních. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Přístup Jupyter 

Chcete-li získat přístup k Jupyter, vyberte `Jupyter` ikona v nabídce plochy nebo aplikace. Můžete také přejít Jupyter v edicích systému Linux z virtuálního počítače pro datové vědy. Můžete přistupovat vzdáleně z webového prohlížeče návštěvou `https://<Full Domain Name or IP Address of the DSVM>:8000` na Ubuntu.

Přidat výjimky a zpřístupněte Jupyter přístup v prohlížeči najdete v tématu na následujícím snímku obrazovky.


![Povolit výjimku Jupyter](./media/ubuntu-jupyter-exception.png)


Přihlaste se pomocí stejné heslo jako vaše přihlašovací údaje pro virtuální počítače pro datové vědy.
<br/>

**Domácí Jupyter**
<br/>![Domácí Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Jazyk R 
<br/>![Ukázky jazyka R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Jazyk Python
<br/>![Ukázky Pythonu](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Jazyk Julia 
<br/>![Helena ukázky](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Ukázky AzurekML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Ukázky PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Ukázky TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Ukázky CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Ukázky caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Ukázky H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>Ve SparkML 
<br/>![Ukázky ve SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Ukázky XGBoost](./media/xgboost-samples.png)<br/>

