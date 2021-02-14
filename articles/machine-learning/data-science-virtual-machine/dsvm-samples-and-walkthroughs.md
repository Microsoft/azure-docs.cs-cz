---
title: Ukázkové programy & návody ML
titleSuffix: Azure Data Science Virtual Machine
description: Pomocí těchto ukázek a návodů se dozvíte, jak zpracovávat běžné úlohy a scénáře pomocí Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cda5dfd936243602775e1f4f965032b9d746b0b7
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519758"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Ukázky Virtual Machines pro datové vědy v Azure

Azure Data věda Virtual Machines (DSVMs) zahrnuje komplexní sadu vzorového kódu. Tyto ukázky zahrnují Jupyter poznámkové bloky a skripty v jazycích, jako je Python a R.
> [!NOTE]
> Další informace o tom, jak spustit poznámkové bloky Jupyter na virtuálních počítačích pro datové vědy, najdete v části [Access Jupyter](#access-jupyter) .

## <a name="prerequisites"></a>Požadavky

Aby bylo možné spustit tyto ukázky, je nutné zřídit [Ubuntu Data Science Virtual Machine](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Dostupné ukázky
| Kategorie ukázek | Description | Umístění |
| ------------- | ------------- | ------------- |
| Jazyk R  | Ukázky ilustrují scénáře, jako je například připojení k úložištím cloudových dat založených na Azure a postup porovnání Open Source R a Microsoft Machine Learning Server. Také vysvětlují, jak zprovoznění modely na Microsoft Machine Learning Server a SQL Server. <br/> [Jazyk R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Jazyk Python  | Ukázky popisují scénáře, jak se připojit k úložištím cloudových dat založenými na Azure a jak pracovat s Azure Machine Learning.  <br/> [Jazyk Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Jazyk Helena  | Poskytuje podrobný popis vykreslování a hloubkového učení v Helena. Vysvětluje také, jak volat C a Python z Helena. <br/> [Jazyk Helena](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ukazuje, jak sestavovat modely strojového učení a hloubkového učení pomocí Machine Learning. Nasaďte modely kdekoli. Použití automatizovaného strojového učení a inteligentního ladění parametrů Také používejte správu modelů a distribuované školení. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch poznámkové bloky  | Ukázky pro hloubkové učení, které používají sítě neuronové založené na PyTorch Poznámkové bloky od začátečníka až po pokročilé scénáře.  <br/> [PyTorch poznámkové bloky](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Celá řada neuronovéch síťových ukázek a techniků implementovaných pomocí architektury TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Ukázky hloubkového učení publikované týmem Cognitive Toolkit v Microsoftu.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Ukázky pro hloubkové učení, které používají sítě neuronové založené na Caffe2 Několik poznámkových bloků seznámí uživatele s Caffe2 a jeho efektivním využitím. Mezi příklady patří předzpracování obrázku a vytváření datových sad. Zahrnují také regresi a používání předpoužívaných modelů. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Ukázky založené na Pythonu, které využívají nerealizovanou situaci v reálných scénářích. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML jazyk  | Ukázky, které používají funkce Apache Spark MLLib Toolkit prostřednictvím pySpark a MMLSpark: Microsoft Machine Learning pro Apache Spark na Apache Spark 2. x.  <br/> [SparkML jazyk](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standardní ukázky strojového učení v XGBoost pro scénáře, jako je klasifikace a regrese. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Přístup k Jupyter 

Pro přístup k Jupyter vyberte v nabídce plocha nebo aplikace ikonu **Jupyter** . Můžete také přistupovat k Jupyter na edici DSVM systému Linux. Pokud chcete vzdáleně přistupovat z webového prohlížeče, přejděte na web `https://<Full Domain Name or IP Address of the DSVM>:8000` na webu Ubuntu.

Chcete-li přidat výjimky a zpřístupnit Jupyter k dispozici prostřednictvím prohlížeče, použijte následující pokyny:


![Povolit výjimku Jupyter](./media/ubuntu-jupyter-exception.png)


Přihlaste se se stejným heslem, které používáte k přihlášení do Data Science Virtual Machine.
<br/>

**Domovská stránka Jupyter**
<br/>![Domovská stránka Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Jazyk R 
<br/>![Ukázky R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Jazyk Python
<br/>![Ukázky Pythonu](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Jazyk Helena 
<br/>![Ukázky Helena](./media/julia-samples.png)<br/>

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
<br/>![Vzorky vzorku](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Ukázky SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Ukázky XGBoost](./media/xgboost-samples.png)<br/>

