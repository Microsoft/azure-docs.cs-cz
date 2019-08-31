---
title: Ukázky & návody
titleSuffix: Azure Data Science Virtual Machine
description: Pomocí těchto ukázek a návodů se dozvíte, jak zpracovávat běžné úlohy a scénáře pomocí Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f3d683ffb91639db64000efa2d9791d13d84b79b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195674"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Ukázky Virtual Machines pro datové vědy v Azure

Azure Data věda Virtual Machines (DSVMs) zahrnuje komplexní sadu vzorového kódu. Tyto ukázky zahrnují Jupyter poznámkové bloky a skripty v jazycích, jako je Python a R.
> [!NOTE]
> Další informace o tom, jak spustit poznámkové bloky Jupyter na virtuálních počítačích pro datové vědy, najdete v části [Access Jupyter](#access-jupyter) .

## <a name="available-samples"></a>Dostupné ukázky
| Ukázky kategorie | Popis | Umístění |
| ------------- | ------------- | ------------- |
| Jazyk R  | Ukázky ilustrují scénáře, jako je například připojení k úložištím cloudových dat založených na Azure a postup porovnání Open Source R a Microsoft Machine Learning Server. Také vysvětlují, jak zprovoznění modely na Microsoft Machine Learning Server a SQL Server. <br/> [Jazyk R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Jazyk Python  | Ukázky popisují scénáře, jak se připojit k úložištím cloudových dat založenými na Azure a jak pracovat s Azure Machine Learning.  <br/> [Jazyk Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Jazyk Julia  | Poskytuje podrobný popis vykreslování a hloubkového učení v Helena. Vysvětluje také, jak volat C a Python z Helena. <br/> [Jazyk Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ukazuje, jak sestavovat modely strojového učení a hloubkového učení pomocí Machine Learning. Nasazení modelů kdekoli. Využijte automatické strojové učení a inteligentní hyperparametrů. Také pomocí správy modelů a distribuované trénování. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Poznámkové bloky PyTorch  | Ukázky pro hloubkové učení, které používají sítě neuronové založené na PyTorch Poznámkové bloky v rozsahu od Začátečník až po pokročilé scénáře.  <br/> [Poznámkové bloky PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Celá řada neuronovéch síťových ukázek a techniků implementovaných pomocí architektury TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Ukázky hloubkového učení publikované týmem Cognitive Toolkit v Microsoftu.  <br/> [Sada cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Ukázky pro hloubkové učení, které používají sítě neuronové založené na Caffe2 Několik poznámkových bloků seznámí uživatele s Caffe2 a jeho efektivním využitím. Mezi příklady patří předzpracování obrázku a vytváření datových sad. Zahrnují také regresi a používání předpoužívaných modelů. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Ukázky založené na Pythonu, které využívají nerealizovanou situaci v reálných scénářích. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Ve SparkML jazyka  | Ukázky, které používají funkce Apache Spark MLLib Toolkit prostřednictvím pySpark a MMLSpark: Microsoft Machine Learning pro Apache Spark na Apache Spark 2. x.  <br/> [Ve SparkML jazyka](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standardní ukázky strojového učení v XGBoost pro scénáře, jako je klasifikace a regrese. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Přístup Jupyter 

Pro přístup k Jupyter vyberte v nabídce plocha nebo aplikace ikonu **Jupyter** . Můžete také přistupovat k Jupyter na edici DSVM systému Linux. Pokud chcete vzdáleně přistupovat z webového prohlížeče, přejděte na `https://<Full Domain Name or IP Address of the DSVM>:8000` web na webu Ubuntu.

Chcete-li přidat výjimky a zpřístupnit Jupyter k dispozici prostřednictvím prohlížeče, použijte následující pokyny:


![Povolit výjimku Jupyter](./media/ubuntu-jupyter-exception.png)


Přihlaste se se stejným heslem, které používáte k přihlášení do Data Science Virtual Machine.
<br/>

**Domácí Jupyter**
<br/>![Domácí Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Jazyk R 
<br/>![Ukázky jazyka R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Jazyk Python
<br/>![Ukázky Pythonu](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Jazyk Julia 
<br/>![Helena ukázky](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Ukázky Azure Machine Learning](./media/azureml-samples.png)<br/>

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

