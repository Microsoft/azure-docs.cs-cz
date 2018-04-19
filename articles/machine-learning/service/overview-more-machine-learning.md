---
title: Další strojového učení produkty společnosti Microsoft – Azure Machine Learning | Microsoft Docs
description: Kromě Azure Machine Learning existují různé možnosti v Microsoftu k vytvoření, nasazení a správě vašeho modelů strojového učení.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: 189f19a3a3f588df8844a552abda73827eb45191
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Další machine learning produkty a služby od Microsoftu

Kromě služby [Azure Machine Learning](overview-what-is-azure-ml.md) nabízí Microsoft celou řadu možností pro sestavení, nasazení a správu modelů strojového učení. 
* SQL Server Machine Learning Services
* Server Microsoft Machine Learning
* Virtuální počítač Azure dat vědecké účely
* Knihovna Spark MLLib ve službě HDInsight
* Služba Batch AI Training
* Sada nástrojů pro kognitivní (CNTK)
* Kognitivní služby Azure


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services
[SQL Server společnosti Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) umožňuje spouštět, školení a nasazovat modely machine learning pomocí R nebo Python. Můžete používat dat umístěná v místním počítači i v databázích systému SQL Server. 

Služby Microsoft Machine Learning můžete využívat k trénování nebo nasazení modelů místně nebo v rámci systému Microsoft SQL Server. Modely sestavené pomocí služby Microsoft Machine Learning se dají nasadit pomocí služby Správa modelů Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Server Microsoft Machine Learning 
[Server Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) je podnikový server pro hostování a správu paralelních a distribuovaných úloh procesů jazyka R a Python. Server Microsoft Machine Learning běží na systémech Linux, Windows, Hadoop a Apache Spark. Je také dostupný ve službě [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Poskytuje modul spuštění pro řešení vytvořená s využitím [Microsoft Machine Learning balíčky](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package), a rozšiřuje R a Python open source s podporu pro následující scénáře:

- Vysoce výkonná analýza
- Statistická analýza
- Strojové učení
- Velmi rozsáhlé datové sady

Další funkce je zajišťováno prostřednictvím proprietární balíčky, které instalace se serverem. Pro vývoj můžete používat integrovaná vývojová prostředí (IDE) jako [Nástroje R pro Visual Studio](https://www.visualstudio.com/vs/rtvs/) a [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Server Microsoft Machine Learning využijete při provádění těchto úkonů:

- Sestavování a nasazování modelů vytvořených pomocí jazyků R a Python na serveru
- Distribuce tréninku jazyka R a Python ve velkém měřítku v clusteru Hadoop nebo Spark

## <a name="azure-data-science-virtual-machine"></a>Virtuální počítač Azure dat vědecké účely
[Datové vědy virtuálního počítače](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) je prostředí přizpůsobené virtuální počítač v cloudu Microsoft Azure, které jsou vytvořené speciálně pro provádění vědecké zpracování dat. Obsahuje řadu oblíbených předinstalovaných nástrojů datové vědy a dalších funkcí a je předem nakonfigurovaná, abyste mohli hned začít sestavovat inteligentní aplikace pro pokročilou analýzu. Virtuální počítač vědecké účely dat je k dispozici ve verzích Windows Server 2016 a 2012 a v virtuálního počítače s Linuxem na Ubuntu 16.04 LTS a na základě OpenLogic CentOS 7.4 distribuce. 

Virtuální počítač vědecké účely dat použijte, pokud je potřeba spustit nebo hostovat vaše úlohy v jednom uzlu. nebo když potřebujete vzdáleně vertikálně navýšit kapacitu zpracování v jednom počítači. Virtuální počítač pro datové vědy je podporovaný jako cíl pro služby Experimentování i Správa modelů ve službě Azure Machine Learning. 

## <a name="spark-mllib-in-hdinsight"></a>Knihovna Spark MLLib ve službě HDInsight
[Knihovna Spark MLLib ve službě HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) umožňuje vytvářet modely v rámci úloh Spark, které se spouštějí na velkých datech. Spark umožňuje snadno transformovat a připravit data a pak rozšířit vytváření modelu v rámci jedné úlohy. Modely vytvořené pomocí knihovny Spark MLLib se dají nasazovat, spravovat a monitorovat pomocí služby Správa modelů služby Azure Machine Learning. Tréninková spuštění se dají odesílat a spravovat pomocí služby Experimentování ve službě Azure Machine Learning. Spark se dá také používat k horizontálnímu navýšení kapacity úloh přípravy dat vytvořených v aplikaci Machine Learning Workbench. 

Spark použijte v případě, že potřebujete horizontálně navýšit kapacitu zpracování dat a vytvářet modely v rámci datového kanálu. Úlohy Spark můžete vytvářet v jazycích Scala, Java, Python nebo R. 

## <a name="batch-ai-training"></a>Batch AI Training 
[Služba Azure Batch AI Training](https://aka.ms/batchaitraining) pomáhá souběžně experimentovat s vašimi modely AI pomocí jakékoli architektury a poté je trénovat ve velkém měřítku napříč grafickými procesory v clusteru. Vy popíšete požadavky na úlohy a konfiguraci ke spuštění a my se postaráme o všechno ostatní. 

Služba Batch AI Training umožňuje horizontálně navýšit kapacitu úloh hloubkového učení napříč grafickými procesory v clusteru pomocí architektur, jako jsou tyto:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Služba Správa modelů ve službě Azure Machine Learning se dá použít k převedení modelů ze služby Batch AI Training a k jejich nasazení, správě a monitorování.  Do budoucna se chystá integrace služby Batch AI Training do služby Experimentování ve službě Machine Learning. 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Sada nástrojů pro kognitivní (CNTK)
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) je sjednocená sada nástrojů pro hloubkové učení, která popisuje neuronové sítě jako výpočetní kroky v řízeném grafu. V tomto řízeném grafu uzly typu list představují vstupní hodnoty nebo parametry sítě a ostatní uzly představují maticové operace prováděné na jejich vstupech. Sada Cognitive Toolkit umožňuje snadno realizovat a kombinovat oblíbené typy modelů, jako jsou dopředné hluboké neuronové sítě (DNN), konvoluční sítě (CNN) a rekurentní sítě (RNN/LSTM). Implementuje učení pomocí stochastického gradientního sestupu (SGD, zpětné šíření chyb) s automatickou diferenciací a paralelizací napříč více grafickými procesory a servery.

Sadu Cognitive Toolkit použijte v případě, že chcete sestavit model využívající hloubkové učení.  Sada Cognitive Toolkit se dá používat v kterékoli z předchozích služeb.

## <a name="azure-cognitive-services"></a>Kognitivní služby Azure
[Služby Azure kognitivní](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) je sada o 30 rozhraní API, které umožňují vytvářet aplikace, které používají přirozené metod komunikace. Tato rozhraní API umožňují aplikací najdete v článku, nás, Předčítání, pochopit a interpretovat musí uživatel s několika řádků kódu. Do svých aplikací můžete snadno přidávat inteligentní funkce, jako jsou tyto: 

- Rozpoznávání emocí a pocitů
- Rozpoznávání obrazu a řeči
- Principy jazyka (LEOŠ)
- Znalosti a hledání

Kognitivní služby lze použít k vývoji aplikací mezi zařízeními a platformami. Rozhraní API se neustále zlepšují a je snadné je nastavit. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si přehled služby [Azure Machine Learning](overview-what-is-azure-ml.md).