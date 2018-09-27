---
title: Ukázky a návody pro virtuální počítač pro datové vědy – Azure | Dokumentace Microsoftu
description: Ukázky a návody pro virtuální počítač pro datové vědy.
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
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392561"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Ukázky na virtuální počítače (DSVM) pro datové vědy

Datové zahrnuje komplexní sadu ukázkový kód v podobě poznámkové bloky Jupyter a také skripty v jazycích, jako je Python a R.    
> [!NOTE]
> Odkazovat na [přístup Jupyter](#access-jupyter) části Další informace o spouštění poznámkových bloků Jupyter na vaše DSVM.

## <a name="quick-reference-of-samples"></a>Stručná referenční příručka vzorků
| Ukázky kategorie | Popis | Umístění |
| ------------- | ------------- | ------------- |
| **R** jazyka  | Ukázky v **R** s vysvětlením, scénáře, jako jsou propojení s daty v cloudu Azure ukládá porovnání Open Source systému R a Microsoft R & až po zprovoznění modelů na Microsoft R serveru nebo systému SQL Server. <br/> [Snímek obrazovky](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** jazyka  | Ukázky v **Python** s vysvětlením, scénáře, jako jsou připojení k Azure cloudovými úložišti dat a práci s **Azure Machine Learning**.  <br/> [Snímek obrazovky](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Helena** jazyka  | Ukázky v **Julie** , které podrobně popisují Plotting v Helena, obsáhlý learning v Helena, volání jazyka C a Python od Julie atd. <br/> [Snímek obrazovky](#julia-language) |<br/> **Windows:**<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux:**<br/> `~notebooks/julia`<br/><br/> |
| **Azure Machine Learning** Azure ml  | Vytvářejte ML a hloubkové učení modely s **Azure Machine Learning** Service a nasazení modelů kdekoli. Využití možností jako automatické ML inteligentní hyper parametr optimalizace správy modelů, distribuované trénování. <br/> [Snímek obrazovky](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| **PyTorch** poznámkové bloky  | Obsáhlý Learning ukázky využitím **PyTorch** na základě neuronových sítí. Existuje široká škála poznámkových bloků od Začátečník až po pokročilé scénáře.  <br/> [Snímek obrazovky](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | Více různých ukázky Neuronové sítě a techniky, které jsou implementované pomocí **TensorFlow** rozhraní framework. <br/> [Snímek obrazovky](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Hloubkové učení ukázky publikoval(a) Cognitive Toolkit týmu ve společnosti Microsoft.  <br/> [Snímek obrazovky](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux:**<br/> `~notebooks/CNTK`<br/> <br/>|
| **Caffe2** | Obsáhlý Learning ukázky využitím **caffe2** na základě neuronových sítí. Existuje několik poznámkových bloků navržené tak, aby uživatelé seznámit s caffe2 a jak ji používat efektivně, včetně příkladů, jako je image předběžného zpracování vytvoření datové sady, regrese a pomocí předem vycvičené modely. <br/> [Snímek obrazovky](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | Ukázky založenou na jazyce Python využívající **H2O** mnoho problémů scénářem z reálného prostředí. <br/> [Snímek obrazovky](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **Ve SparkML** jazyka  | Ukázka použití funkcí a možností Spark **MLlib** toolkit prostřednictvím **pySpark** a **MMLSpark – Microsoft Machine Learning pro Apache Spark** na **Apache Sparku 2.x**.  <br/> [Snímek obrazovky](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | Machine Learning úrovně Standard – ukázky v **XGBoost** pro scénáře, jako je klasifikace, regrese atd. <br/> [Snímek obrazovky](#xgboost) | <br/>**Windows:**<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Přístup Jupyter 

Jupyter se zpřístupní po kliknutí na `Jupyter` ikona v nabídce plochy nebo aplikace. Můžete také přístup k Jupyter v Linuxu edice DSVM vzdáleně z webového prohlížeče návštěvou **`https://<Full Domain Name or IP Address of the DSVM>:8000`** na Ubuntu.

Viz snímek obrazovky přidejte výjimku a povolení přístupu Jupyter v prohlížeči.


![Povolit výjimku Jupyter](./media/ubuntu-jupyter-exception.png)


Přihlaste se pomocí stejné heslo jako vaše přihlašovací údaje pro datové VĚDY.
<br/>

**Domovská stránka Jupyter**
<br/>![Domovská stránka Jupyter](./media/jupyter-home.png)<br/>

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

