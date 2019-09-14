---
title: Přehled Machine Learning – Azure HDInsight
description: Přehled možností strojového učení pro velké objemy dat pro clustery ve službě Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: hrasheed
ms.openlocfilehash: 0fecb41c3417d5b43e0638815d6a7d274ba461b0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961975"
---
# <a name="machine-learning-on-hdinsight"></a>Machine Learning v HDInsight

HDInsight umožňuje strojové učení s velkými objemy dat a poskytuje možnost získat hodnotný přehled z velkých objemů (petabajty nebo i Exabyte) strukturovaných, nestrukturovaných a rychlých přesunů dat. Ve službě HDInsight je několik možností strojového učení:  SparkML a Apache Spark MLlib, R, Apache Hive a Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML a MLlib

[HDInsight Spark](spark/apache-spark-overview.md) je [Apache Spark](https://spark.apache.org/)nabídka hostované v Azure, která představuje jednotnou Open Source architekturu paralelního zpracování dat, která podporuje zpracování v paměti, aby se zvýšila analýza velkých objemů dat. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Spark v paměti distribuovanou výpočetní možnosti usnadňují dobrou volbou pro iterativní algoritmy používané ve výpočtech machine learning a grafů. Existují dvě škálovatelné knihovny strojového učení, které přinášejí funkce pro modelování algoritmu do tohoto distribuovaného prostředí: MLlib a SparkML. MLlib obsahuje původní rozhraní API postavené nad RDD. SparkML je novější balíček, který poskytuje rozhraní API vyšší úrovně postavené nad datovými snímky pro sestavování kanálů ML. SparkML ještě nepodporuje všechny funkce MLlib, ale nahrazuje MLlib jako standardní knihovnu strojového učení Spark.

Knihovna Microsoft Machine Learning pro Apache Spark je [MMLSpark](https://github.com/Azure/mmlspark). Tato knihovna je navržená tak, aby byly vědečtí data ve Sparku větší produktivita, zvýšila se rychlost experimentu a využila špičkové techniky strojového učení, včetně hloubkového učení, u velmi rozsáhlých datových sad. MMLSpark poskytuje vrstvu nad rozhraními API nízké úrovně SparkML při sestavování škálovatelných modelů ML, jako jsou indexování řetězců, vynucený data do rozložení očekávaného algoritmy strojového učení a nastavování vektorů funkcí. MMLSpark Library zjednodušuje tyto a další běžné úlohy pro vytváření modelů v PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) je aktuálně nejoblíbenější statistická programovací jazyk na světě. Jedná se o open source nástroj pro vizualizaci dat s komunitou více než 2 500 000 uživatelů a rostoucím. Díky svému prosperující uživatelské základu a více než 8 000 balíčkům, R je pravděpodobně volba pro mnoho společností, které potřebují strojové učení. Můžete vytvořit cluster HDInsight se službami ML, který je připravený k použití s obrovskými datovými sadami a modely. Tato funkce poskytuje odborníci na data a statistiku se známým rozhraním jazyka R, které umožňuje škálování na vyžádání prostřednictvím služby HDInsight bez režie instalace a údržby clusteru.

![Školení pro předpověď s R serverem](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

Hraniční uzel clusteru poskytuje vhodné místo pro připojení ke clusteru a spouštění skriptů jazyka R.  Máte také možnost spouštět v uzlech clusteru skripty jazyka R pomocí výpočetních kontextů rozšíření Hadoop mapy Hadoop nebo Spark.

Pomocí služby ML Services ve službě HDInsight se systémem Spark můžete paralelizovat školení napříč uzly clusteru pomocí výpočetního kontextu Spark. V případě potřeby můžete spouštět skripty jazyka R přímo na hraničním uzlu, a to pomocí všech dostupných jader současně. Alternativně můžete spustit kód z hraničního uzlu pro vypnutí zpracování, které je distribuováno napříč všemi uzly v clusteru. Služba ML Services ve službě HDInsight se systémem Spark také v případě potřeby povoluje funkce virtuálního z open source balíčků R.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning a Apache Hive

Azure Machine Learning poskytuje nástroje pro modelování prediktivních analýz a také plně spravovanou službu, kterou můžete použít k nasazení prediktivních modelů jako připravených webových služeb. Azure Machine Learning je kompletní řešení prediktivní analýzy v cloudu, které můžete použít k vytváření, testování, zprovoznění a správě prediktivních modelů. Vyberte si z velké knihovny algoritmů, použijte webové Studio pro vytváření modelů a jednoduše nasaďte model jako webovou službu.

![Zajištění dostupnosti pokročilých analýz pro Hadoop pomocí Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Vytvářejte funkce pro data v clusteru HDInsight Hadoop pomocí [dotazů na podregistry](../machine-learning/team-data-science-process/create-features-hive.md). Konstrukce *funkcí* se snaží zvýšit prediktivní výkon výukových algoritmů vytvořením funkcí z nezpracovaných dat, která usnadňují proces učení. Pomocí [modulu import dat](../machine-learning/studio/import-data.md)můžete spouštět dotazy HiveQL z aplikace Azure Machine Learning Studio a přistupovat k datům zpracovávaným v podregistru a ukládat je do úložiště objektů BLOB.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Obsáhlý Learning](https://www.microsoft.com/en-us/research/group/dltc/) je větev strojového učení, která používá sítě neuronové, nechte inspirovat biologických procesů lidského mozku. Mnoho výzkumných pracovníků zobrazuje obsáhlý Learning jako slibový přístup pro zlepšení umělých inteligentních poznatků. Příklady obsáhlého učení jsou mluvené překlady jazyka, systémy pro rozpoznávání obrázků a důvody pro počítače.

Společnost Microsoft vyvinula bezplatný a snadno použitelný [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)open source, aby mohla lépe využít svou vlastní práci v obsáhlém učení. Tato sada nástrojů je využívána širokou škálou produktů společnosti Microsoft po celém světě s potřebou nasazení hloubkového učení ve velkém měřítku a studentům, kteří mají zájem o nejnovější algoritmy a techniky.

## <a name="see-also"></a>Viz také:

### <a name="scenarios"></a>Scénáře

* [Apache Spark s Machine Learning: Použití Sparku ve službě HDInsight k analýze teploty budovy pomocí dat TVK](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Předpověď výsledků kontroly potravin pomocí Sparku v HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generování filmových doporučení pomocí Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive a Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive a Azure Machine Learning od začátku do konce](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine Learning s Apache Spark ve službě HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Materiály pro hloubkové učení

* [Použití Microsoft Cognitive Toolkit modelu hloubkového učení s clusterem Azure HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Použití Caffe na Azure HDInsight Spark pro distribuované hloubkové učení](spark/apache-spark-deep-learning-caffe.md)
* [Rozhraní pro hloubkové učení a AI na Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks.md)
