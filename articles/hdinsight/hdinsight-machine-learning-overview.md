---
title: Přehled služby Machine learning – Azure HDInsight
description: Popisuje strojového učení možnosti v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: hrasheed
ms.openlocfilehash: 7e5f56251e00b59a6e6d3be40612f4c8e381dff1
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742996"
---
# <a name="machine-learning-on-hdinsight"></a>Strojové učení v HDInsight

HDInsight umožňuje machine learning s velkými objemy dat, poskytuje schopnosti získávat cenné informace z velkých objemů (petabajty nebo dokonce exabajtů) strukturovaných, nestrukturovaných a rychlé tempo data. Existuje několik strojového učení možnosti v HDInsight:  Ve SparkML a Apache Spark MLlib, R, Apache Hivu a sady Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>Ve SparkML a MLlib

[HDInsight Spark](spark/apache-spark-overview.md) je nabídka hostovaných v Azure z [Apache Spark](https://spark.apache.org/), sjednoceném open source, rozhraní paralelní zpracování dat podporuje zpracování v paměti pro zvýšení analýzy velkých objemů dat. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Spark v paměti distribuovanou výpočetní možnosti usnadňují dobrou volbou pro iterativní algoritmy používané ve výpočtech machine learning a grafů. Existují dvě škálovatelné machine learning knihovny, které přinášejí možnosti vylepšením modelování do této distribuované prostředí: MLlib a ve SparkML. MLlib obsahuje původní rozhraní API, které jsou postavené na Rdd. Ve SparkML je novější balíček, který poskytuje vyšší úrovně rozhraní API pro vytváření kanálů ML postavené na datových rámců. Ve SparkML zatím nepodporuje všechny funkce MLlib, ale nahrazuje MLlib jako Spark standardní knihovnou pro machine learning.

Knihovna Microsoft Machine Learning pro Apache Spark je [MMLSpark](https://github.com/Azure/mmlspark). Tato knihovna je určená produktivnější odborníci přes data ve Sparku, zvýšit frekvenci experimentování a využít techniky nejmodernější strojového učení, včetně obsáhlého learningu na velmi rozsáhlých datových sadách. MMLSpark poskytuje vrstvu nad nízké úrovně rozhraní API v ve SparkML při sestavování škálovatelných modelů ML, jako jsou indexování řetězce podřízenému data do rozložení očekává počítačem učení se supervizí a propojením vektory funkce. Knihovna MMLSpark zjednodušuje tyto a další běžné úlohy vytváření modelů v PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) je aktuálně nejoblíbenější statistický programovací jazyk na světě. Je open source nástroj vizualizace dat s komunitou uživatelů více než 2,5 milionu a stále se rozšiřující. R s jeho všechny uživatelské základny a víc než 8 000 přidružených balíčků, je pravděpodobně volba pro mnoho společností, kteří potřebují strojového učení. Můžete vytvořit cluster služby HDInsight s připravená k použití s velkými datovými sadami a modely služby ML. Tato funkce poskytuje odborníkům přes data a statistikům pomocí známých rozhraní R, kterou chcete škálovat na vyžádání prostřednictvím HDInsight, bez režie nastavení clusteru a údržby.

![Školení pro předpovědi s R serverem](./media/hdinsight-machine-learning-overview/r-training.png)

Hraničním uzlu clusteru poskytuje praktické místo k připojení ke clusteru a ke spuštění skriptů jazyka R.  Máte také možnost spouštět skripty jazyka R na uzlech clusteru s použitím jeho ScaleR Hadoop Map Reduce nebo kontexty služby compute Spark.

Pomocí služby ML na HDInsight se Spark můžete paralelizovat školení na uzlech clusteru s použitím výpočetním kontextu Spark. Spouštění skriptů R přímo na hraničním uzlu, pomocí všechna dostupná jádra paralelně, podle potřeby. Alternativně můžete spustit váš kód z hraničního uzlu aktivovala zpracování, která je distribuovaná napříč všemi uzly v clusteru. Služby ML na HDInsight se Spark paralelního funkce z balíčků opensourcového jazyka R, taky umožňuje v případě potřeby.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning a Apache Hive

Azure Machine Learning poskytuje nástroje, které model prediktivní analýzy, stejně jako plně spravovaná služba, které můžete své prediktivní modely nasazovat jako připravené využívání webových služeb. Azure Machine Learning je kompletní prediktivní analytická řešení v cloudu, ve kterém můžete vytvářet, testovat, zprovoznit a spravovat prediktivní modely. Vyberte od velké knihovny algoritmů, použít studio založeného na webu pro vytváření modelů a snadno model nasadit jako webovou službu.

![Provádění pokročilé analýzy, které jsou dostupné k systému Hadoop pomocí Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Vytvoření funkcí pro data v Hadoop HDInsight clusteru s použitím [dotazů Hive](../machine-learning/team-data-science-process/create-features-hive.md). *Konstrukce funkcí* pokusí sofistikované prediktivní učení se supervizí vytvořením funkce z nezpracovaných dat, která usnadňují proces učení. Můžete spouštět dotazy HiveQL z Azure ML a přístup k datům zpracovány v Hive a uložená v blob storage s použitím [modulu Import dat](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Obsáhlý learning](https://www.microsoft.com/en-us/research/group/dltc/) je větev služby machine learning, která používá neuronových sítí INSPIROVANÉ biologické procesy lidský mozek. Mnoho výzkumní pracovníci naleznete v tématu obsáhlý learning jako slibně přístup pro zlepšení umělé inteligence. Příkladem obsáhlý learning jsou mluvený jazyk překladatele, systémy rozpoznávání obrazu a posuzování počítače.

Abyste předem své vlastní práci v obsáhlého learningu, společnost Microsoft vyvinula open-source zdarma, snadno použitelný, [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Tato sada nástrojů používá celou řadu produktů společnosti Microsoft, společností po celém světě s museli nasazovat hloubkového učení ve velkém měřítku a studenty zájem o nejnovější algoritmy a postupy. 

## <a name="see-also"></a>Další informace najdete v tématech

### <a name="scenarios"></a>Scénáře

* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu stavební teploty pomocí dat HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generování filmových doporučení pomocí Mahout Apache](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive a Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive nebo Azure Machine Learning end-to-end](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Strojové učení s Apache Spark v HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Prostředky obsáhlého learningu

* [Hloubkové učení toolkit s Apache Sparkem](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Klasifikace obrázků jednoduše paralelně zpracovatelné pomocí Cognitive toolkit + Tensorflow na Apache Sparku](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
