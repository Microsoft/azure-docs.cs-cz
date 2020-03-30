---
title: Přehled strojového učení – Azure HDInsight
description: Přehled možností strojového učení velkých objemů dat pro clustery v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 6c7f50e4ba3803b5cb929b75f5ad1127f7f063d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931679"
---
# <a name="machine-learning-on-hdinsight"></a>Strojové učení na HDInsight

HDInsight umožňuje strojové učení s velkými objemy dat, poskytuje možnost získat cenné poznatky z velkého množství (petabajty, nebo dokonce exabajty) strukturovaných, nestrukturovaných a rychle se pohybujících dat. Existuje několik možností strojového učení v HDInsight: SparkML a Apache Spark MLlib, R, Apache Hive a Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML a MLlib

[HDInsight Spark](spark/apache-spark-overview.md) je nabídka hostovaná v Azure [apache spark](https://spark.apache.org/), sjednocené, open source, paralelní zpracování dat architektury podporující zpracování v paměti pro zvýšení analýzy velkých objemů dat. Modul pro zpracování Spark je navržen pro rychlost, snadné použití a sofistikované analýzy. Možnosti distribuovaného výpočtu sparku v paměti z něj činí dobrou volbu pro iterativní algoritmy používané ve strojovém učení a výpočtech grafů. Existují dvě škálovatelné knihovny strojového učení, které přinášejí možnosti algoritmického modelování do tohoto distribuovaného prostředí: MLlib a SparkML. MLlib obsahuje původní rozhraní API postavené na rdds. SparkML je novější balíček, který poskytuje rozhraní API vyšší úrovně postavené na dataframe pro vytváření kanálů ML. SparkML ještě nepodporuje všechny funkce MLlib, ale nahrazuje MLlib jako standardní knihovnu strojového učení Spark.

Knihovna Microsoft Machine Learning pro Apache Spark je [MMLSpark](https://github.com/Azure/mmlspark). Tato knihovna je navržena tak, aby se datoví vědci na Sparku zvýšili, zvýšili rychlost experimentování a využili špičkové techniky strojového učení, včetně hloubkového učení, na velmi velkých datových sadách. MMLSpark poskytuje vrstvu nad nízkoúrovňovými rozhraními API SparkML při vytváření škálovatelných modelů ML, jako jsou indexovací řetězce, vynucení dat do rozložení očekávaného algoritmy strojového učení a vytváření vektorů funkcí. Knihovna MMLSpark zjednodušuje tyto a další běžné úkoly pro vytváření modelů v PySkaparku.

## <a name="r"></a>R

[R](https://www.r-project.org/) je v současné době nejpopulárnější statistický programovací jazyk na světě. Jedná se o nástroj pro vizualizaci dat s otevřeným zdrojovým kódem s komunitou více než 2,5 milionu uživatelů a roste. Díky své prosperující uživatelské základně a více než 8 000 balíčkům, které přispěly, je R pravděpodobnou volbou pro mnoho společností, které potřebují strojové učení. Můžete vytvořit cluster HDInsight se službami ML, které jsou připraveny k použití s masivními datovými sadami a modely. Tato funkce poskytuje datovým vědcům a statistikům známé rozhraní R, které lze škálovat na vyžádání prostřednictvím hdinsightu, bez režie nastavení a údržby clusteru.

![Školení pro předpověď s R serverem](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

Hraniční uzel clusteru poskytuje vhodné místo pro připojení ke clusteru a spuštění skriptů R.  Skripty R můžete také spouštět v uzlech clusteru pomocí výpočetních kontextů Hadoop Map Reduce nebo Spark společnosti ScaleR.

Se službami ML services na HDInsight u Spark můžete paralelizovat školení napříč uzly clusteru pomocí výpočetního kontextu Spark. Skripty R můžete spustit přímo na hraničním uzlu, pomocí všech dostupných jader paralelně, podle potřeby. Alternativně můžete spustit kód z hraničního uzlu a zahájit zpracování, které je distribuováno ve všech uzlech v clusteru. Ml Services na HDInsight s Spark také umožňuje paralelní funkce z open source balíčků R, pokud je to žádoucí.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning a Apache Hive

Azure Machine Learning poskytuje nástroje pro modelování prediktivní analýzy a plně spravovanou službu, kterou můžete použít k nasazení prediktivních modelů jako webových služeb připravených ke spotřebování. Azure Machine Learning je kompletní prediktivní analytické řešení v cloudu, které můžete použít k vytváření, testování, zprovoznění a správě prediktivních modelů. Vyberte si z velké knihovny algoritmů, použijte webové studio pro vytváření modelů a snadno nasaďte model jako webovou službu.

![Přehled strojového učení Microsoft Azure](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Vytvářejte funkce pro data v clusteru HDInsight Hadoop pomocí [dotazů Hive](../machine-learning/team-data-science-process/create-features-hive.md). *Funkce inženýrství* se snaží zvýšit prediktivní sílu učení algoritmy vytvořením funkce z nezpracovaných dat, které usnadňují proces učení. Dotazy HiveQL můžete spouštět z Azure Machine Learning Studio (klasické) a přistupovat k datům zpracovávaným v Hive a uloženým v úložišti objektů blob pomocí [modulu Import data](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Hluboké učení](https://www.microsoft.com/en-us/research/group/dltc/) je odvětví strojového učení, které využívá neuronové sítě inspirované biologickými procesy lidského mozku. Mnozí badatelé považují hluboké učení za slibný přístup ke zlepšení umělé inteligence. Příklady hlubokého učení jsou překladatelé mluveného jazyka, systémy rozpoznávání obrazu a strojové uvažování.

Společnost Microsoft vyvinula bezplatnou a snadno použitelnou sadu [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)s otevřeným zdrojovým kódem, aby pomohla pokročit ve své práci v hlubokém učení. Tuto sadu nástrojů používá široká škála produktů společnosti Microsoft, společnosti po celém světě s potřebou nasazení hloubkového učení ve velkém měřítku a studenti, kteří se zajímají o nejnovější algoritmy a techniky.

## <a name="see-also"></a>Viz také

### <a name="scenarios"></a>Scénáře

* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generování filmových doporučení s Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive a Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive a Azure Machine Learning od konce](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Strojové učení s Apache Spark na HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Zdroje pro hloubkové vzdělávání

* [Použití modelu hloubkového učení Microsoft Cognitive Toolkit s clusterem Azure HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Hloubkové učení a AI rámce na virtuálním počítači datové vědy (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
