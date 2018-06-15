---
title: Ukázky a návody pro Data vědecké účely virtuálního počítače – Azure | Microsoft Docs
description: Ukázky a návody pro Data virtuálního počítače vědecké účely.
keywords: datové vědy nástroje, datové vědy virtuálního počítače, nástroje pro vědecké zpracování dat, vědecké zpracování dat linux
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
ms.openlocfilehash: 3e3ee232b6342601e44d728148d32e70e6f3f00b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31419972"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Ukázky na datové vědě virtuálních počítačů (DSVM)

DSVMs se dodávají s uvedené ukázky plně fungovala na více systémů ve formě poznámkové bloky Jupyter a některé, které nejsou založené na Jupyter. Dostanete Jupyter kliknutím na `Jupyter` ikona v nabídce aplikace nebo plochy.  
> [!NOTE]
> Odkazovat na [přístup Jupyter](#access-jupyter) části povolit na vaše DSVM poznámkové bloky Jupyter.

## <a name="quick-reference-of-samples"></a>Stručná referenční příručka vzorků
| Kategorie – ukázky | Popis | Umístění |
| ------------- | ------------- | ------------- |
| **R** jazyk  | Ukázky v **R** vysvětlením scénáře jako připojení s daty cloudu Azure ukládá porovnávání otevřený zdroj R a Microsoft R & zprovozňování modely na Microsoft R Server nebo SQL Server. <br/> [snímek obrazovky](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** jazyk  | Ukázky v **Python** scénáře, jako je připojení k úložišti dat cloudu Azure a práce s vysvětlením **Azure Machine Learning**.  <br/> [snímek obrazovky](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Dita** jazyk  | Ukázka v **Dita** který podrobností Plotting v Dita, hluboké učení v Dita, volání C a Python z Dita atd. <br/> [snímek obrazovky](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Microsoft kognitivní Toolkit)  | Přímý učení ukázky publikováno kognitivní Toolkit týmu ve společnosti Microsoft.  <br/> [snímek obrazovky](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXNet** poznámkových bloků  | Hluboké učení ukázky použití **MXNet** na základě neuronové sítě. Existuje mnoho různých poznámkových bloků od Začátečník až pokročilé scénáře.  <br/> [snímek obrazovky](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | Interakci s **Azure Machine Learning** Studio a vytváření koncových bodů webové služby z místně trénované modely pro cloudové vyhodnocování pracovní postupy. <br/> [snímek obrazovky](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **Caffe2** | Hluboké učení ukázky použití **caffe2** na základě neuronové sítě. Existuje několik poznámkových bloků navržený tak, aby uživatelé seznamte s caffe2 a způsobu jeho použití efektivně, včetně příkladů, jako jsou bitové kopie předem zpracování, vytváření datovou sadu, regrese a pomocí předem Trénink modely. <br/> [snímek obrazovky](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Na základě Python ukázky použití **H2O** řada problémů scénářem z reálného prostředí. <br/> [snímek obrazovky](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** jazyk  | Ukázka použití funkcí a možností Spark **MLlib** toolkit prostřednictvím **pySpark 2.0** na **Apache Spark 2.0**.  <br/> [snímek obrazovky](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| **MMLSpark** jazyk  | Celou řadu ukázky použití **MMLSpark - Microsoft Machine Learning pro Apache Spark**, což je rozhraní, které poskytuje řadu hloubkové učení a vědecké účely nástroje data pro **Apache Spark**. <br/> [snímek obrazovky](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Více různých Neuronové sítě ukázky a techniky implementovaná pomocí **TensorFlow** framework. <br/> [snímek obrazovky](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Ukázky standardní Machine Learning v **XGBoost** pro scénáře, jako je klasifikace, regrese atd. <br/> [snímek obrazovky](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Přístup k Jupyter 

Navštivte Jupyter domovské přechodem na **`https://localhost:9999`** v systému Windows nebo **`https://localhost:8000`** na Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Povolení Jupyter přístup z prohlížeče

**Windows DSVM**

Spustit **`Jupyter SetPassword`** ze zástupce na ploše a postupujte podle řádku sadu nebo resetování hesla pro Jupyter a spustit Jupyter zpracovat. 
<br/>![Povolit výjimky Jupyter](./media/jupyter-setpassword.png)<br/>
Jakmile Jupyter proces byl úspěšně spuštěn na vašem virtuálním počítači navštivte stránky dostanete domovské Jupyter **`https://localhost:9999`** v prohlížeči. Podívejte se snímek obrazovky Přidat výjimku a umožnit Jupyter přístup přes prohlížeč
<br/>![Povolit výjimky Jupyter](./media/windows-jupyter-exception.png)<br/>
Přihlaste se pomocí nové heslo, které jste právě nastavili.
<br/>
**Linux DSVM**

Jupyter domovské můžete přejít na vašem virtuálním počítači pomocí navštívíte **`https://localhost:8000`** v prohlížeči. Podívejte se snímek obrazovky Přidat výjimku a umožnit Jupyter přístup přes prohlížeč.
<br/>![Povolit výjimky Jupyter](./media/ubuntu-jupyter-exception.png)<br/>
Přihlaste se pomocí stejné heslo jako vaše přihlašovací údaje pro DSVM.
<br/>

**Domovské Jupyter**
<br/>![Domovské Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Jazyk R 
<br/>![R – ukázky](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Jazyk Python
<br/>![Ukázky jazyka Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Dita jazyk 
<br/>![Dita – ukázky](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![Ukázky CNTK](./media/cntk-samples2.png)<br/>
<br/>![Ukázky CNTK](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![Ukázky MXnet](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Ukázky AzurekML](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>Caffe2 
<br/>![Ukázky caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Ukázky H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Ukázky SparkML](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Ukázky TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Ukázky XGBoost](./media/xgboost-samples.png)<br/>

