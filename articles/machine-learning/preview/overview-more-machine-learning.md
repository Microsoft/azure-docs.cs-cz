---
title: Co je Azure Machine Learning? | Dokumenty Microsoft
description: Přehled experimentování a správy modelů ve službě Azure Machine Learning, která představuje integrované, komplexní řešení datové vědy pro profesionální datové vědce sloužící k přípravě dat, experimentování a nasazování pokročilých analytických aplikací na úrovni cloudu.
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: 1c4826959bb92e2e515867261094b1154a3c805c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="machine-learning-solutions-from-microsoft"></a>Řešení strojového učení od Microsoftu

Kromě služby [Azure Machine Learning](overview-what-is-azure-ml.md) nabízí Microsoft celou řadu možností pro sestavení, nasazení a správu modelů strojového učení. 
* SQL Server Machine Learning Services
* Server Microsoft Machine Learning
* Služby Azure Machine Learning
* Azure Machine Learning Studio
* Virtuální počítač pro datové vědy
* Knihovna Spark MLLib ve službě HDInsight
* Služba Batch AI Training
* Microsoft Cognitive Toolkit
* Služba Microsoft Cognitive Services


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services
[Služby Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) umožňují spouštět, trénovat a nasazovat modely strojového učení pomocí jazyka R nebo Python. Můžete používat dat umístěná v místním počítači i v databázích systému SQL Server. 

Služby Microsoft Machine Learning můžete využívat k trénování nebo nasazení modelů místně nebo v rámci systému Microsoft SQL Server. Modely sestavené pomocí služby Microsoft Machine Learning se dají nasadit pomocí služby Správa modelů Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Server Microsoft Machine Learning 
[Server Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) je podnikový server pro hostování a správu paralelních a distribuovaných úloh procesů jazyka R a Python. Server Microsoft Machine Learning běží na systémech Linux, Windows, Hadoop a Apache Spark. Je také dostupný ve službě [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Poskytuje spouštěcí modul pro řešení sestavená pomocí [balíčků služby Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) a rozšiřuje jazyky R a Python s otevřeným zdrojem o podporu následujících scénářů:

- Vysoce výkonná analýza
- Statistická analýza
- Strojové učení
- Velmi rozsáhlé datové sady

Chráněné balíčky, které se se serverem instalují, poskytují funkce s přidanou hodnotou. Pro vývoj můžete používat integrovaná vývojová prostředí (IDE) jako [Nástroje R pro Visual Studio](https://www.visualstudio.com/vs/rtvs/) a [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Server Microsoft Machine Learning využijete při provádění těchto úkonů:

- Sestavování a nasazování modelů vytvořených pomocí jazyků R a Python na serveru
- Distribuce tréninku jazyka R a Python ve velkém měřítku v clusteru Hadoop nebo Spark

## <a name="data-science-virtual-machine"></a>Virtuální počítač pro datové vědy
[Virtuální počítač pro datové vědy](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) je přizpůsobená image virtuálního počítače v cloudu Azure Microsoftu vytvoření speciálně pro účely datových věd. Obsahuje řadu oblíbených předinstalovaných nástrojů datové vědy a dalších funkcí a je předem nakonfigurovaná, abyste mohli hned začít sestavovat inteligentní aplikace pro pokročilou analýzu. Je k dispozici pro Windows Server a Linux. Edici virtuálního počítače pro datové vědy pro Windows nabízíme ve verzích pro Server 2016 a Server 2012. Edici virtuálního počítače pro datové vědy pro Linux nabízíme ve verzích pro distribuce Linuxu založené na Ubuntu 16.04 LTS a OpenLogic 7.2 CentOS. 

Virtuální počítač pro datové vědy můžete využít, když potřebujete spustit nebo hostovat úlohy v jednom uzlu nebo když potřebujete vzdáleně vertikálně navýšit kapacitu zpracování v jednom počítači. Virtuální počítač pro datové vědy je podporovaný jako cíl pro služby Experimentování i Správa modelů ve službě Azure Machine Learning. 

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

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) je sjednocená sada nástrojů pro hloubkové učení, která popisuje neuronové sítě jako výpočetní kroky v řízeném grafu. V tomto řízeném grafu uzly typu list představují vstupní hodnoty nebo parametry sítě a ostatní uzly představují maticové operace prováděné na jejich vstupech. Sada Cognitive Toolkit umožňuje snadno realizovat a kombinovat oblíbené typy modelů, jako jsou dopředné hluboké neuronové sítě (DNN), konvoluční sítě (CNN) a rekurentní sítě (RNN/LSTM). Implementuje učení pomocí stochastického gradientního sestupu (SGD, zpětné šíření chyb) s automatickou diferenciací a paralelizací napříč více grafickými procesory a servery.

Sadu Cognitive Toolkit použijte v případě, že chcete sestavit model využívající hloubkové učení.  Sada Cognitive Toolkit se dá používat v kterékoli z předchozích služeb.

## <a name="microsoft-cognitive-services"></a>Služba Microsoft Cognitive Services
Microsoft Cognitive Services je sada 30 rozhraní API, která umožňuje sestavovat aplikace využívající přirozené metody komunikace. Díky těmto rozhraním API dokážou vaše aplikace vidět, slyšet, mluvit, chápat a interpretovat vaše potřeby prostřednictvím pouhých pár řádků kódu. Do svých aplikací můžete snadno přidávat inteligentní funkce, jako jsou tyto: 

- Rozpoznávání emocí a pocitů
- Rozpoznávání obrazu a řeči
- Rozpoznávání jazyka
- Znalosti a hledání

Služba Microsoft Cognitive Services se dá používat k vývoji aplikací napříč různými zařízeními a platformami. Rozhraní API se neustále zlepšují a je snadné je nastavit. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si přehled služby [Azure Machine Learning](overview-what-is-azure-ml.md).