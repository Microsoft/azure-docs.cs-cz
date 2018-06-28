---
title: Přehled Machine learning - Azure HDInsight | Microsoft Docs
description: Popisuje strojového učení možnosti v HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: 6ccdb1fff5ecdcb61beedf1b12904d3785d4921d
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048496"
---
# <a name="machine-learning-on-hdinsight"></a>Strojového učení v HDInsight

HDInsight umožňuje machine learning s velké objemy dat, zajištění své schopnosti získávat cenné informace z velké objemy (petabajty nebo dokonce exabajtů) strukturovaných a nestrukturovaných a přesunutí fast data. Existuje několik strojové učení možností v HDInsight: SparkML a MLlib, R, Hive a kognitivní nástrojů Microsoft.

## <a name="sparkml-and-mllib"></a>SparkML a MLlib

[HDInsight Spark](spark/apache-spark-overview.md) je nabídky Azure hostovaná z [Spark](http://spark.apache.org/), a jednotnou open source, framework paralelní zpracování dat, které podporuje zpracování v paměti pro zvýšení analýzy velkých objemů dat. Modul zpracování Spark je vytvořené pro rychlost, snadné použití a sofistikované analytics. Možnosti v paměti distribuované výpočtů Spark díky správnou volbu pro iterativní algoritmy použité v machine learning a grafů výpočty. Existují dvě knihovny learning škálovatelné počítače, které přinášejí algoritmické modelování možnosti do této distribuované prostředí: MLlib a SparkML. MLlib obsahuje původní API postavená na RDDs. SparkML je novější balíček, který poskytuje vyšší úrovně rozhraní API pro vytváření kanálů ML postavená na DataFrames. SparkML zatím nepodporuje všechny funkce MLlib, ale nahrazuje MLlib jako Spark standardní knihovnou machine learning.

Knihovna Microsoft Machine Learning pro Apache Spark je [MMLSpark](https://github.com/Azure/mmlspark). Tato knihovna je určená proveďte datových vědců zvýšit produktivitu na Spark, zvýšit počet experimentování a využít techniky nejmodernější strojové učení, včetně hloubkové learning na velmi rozsáhlých datových sad. MMLSpark zajišťuje, že vrstvu nad nízké úrovně rozhraní API pro SparkML při sestavování škálovatelné ML modely, jako jsou třeba indexování řetězce vynucený data do očekávaného machine learning algoritmy a ty se funkce vektory rozložení. Knihovna MMLSpark zjednodušuje těchto a dalších běžných úloh pro vytváření modelů v PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) právě nejoblíbenější statistické programovací jazyk na světě. Je nástroj pro vizualizaci dat s otevřeným zdrojem s komunitou uživatelů přes 2,5 milionu a rozšiřujících se. S jeho thriving uživatelské základny a více než 8000 přidružených balíčků R je pravděpodobně volbou pro mnoho společností, kteří potřebují strojové učení. Můžete vytvořit cluster služby HDInsight službou ML připravené pro použití s velkých datových sad a modely. Tato funkce poskytuje datových vědců a statistikami s známé rozhraní R, který můžete škálovat na vyžádání prostřednictvím HDInsight, bez nutnosti instalace a údržby.

![Školení pro předpovědi s R server](./media/hdinsight-machine-learning-overview/r-training.png)

Hraničního uzlu clusteru poskytuje vhodné místo pro připojení ke clusteru a spustit skripty R.  Máte také možnost spouštět skripty R mezi uzly clusteru pomocí ScaleR na Hadoop mapy snížit nebo výpočetní kontexty Spark.

S ML služeb v prostředí HDInsight pomocí Spark vám může paralelními školení mezi uzly clusteru s podporou pomocí kontextu výpočtů Spark. Skripty R můžete spustit přímo v uzlu edge pomocí všechny dostupné jader paralelně, podle potřeby. Alternativně můžete spustit kód z uzlu edge na ji zpracování, který je distribuován do všech uzlů v clusteru. ML služeb v prostředí HDInsight pomocí Spark paralelního funkce z balíčků R s otevřeným zdrojem, taky umožňuje v případě potřeby.

## <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning a Hive

Azure Machine Learning poskytuje nástroje ke model prediktivní analýzy, jakož i plně spravované služby, které můžete použít k nasazení své prediktivní modely jako připravené využívají webové služby. Azure Machine Learning je řešení dokončení prediktivní analýzy v cloudu, které můžete použít k vytvoření, testování, zprovoznění a správa prediktivní modelů. Vyberte z velké knihovny algoritmů, použijte přes webové studio pro vytváření modelů a snadno nasadit model jako webovou službu.

![Provádění pokročilou analýzu dostupné k systému Hadoop pomocí Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Vytvoření funkce pro data v HDInsight Hadoop clusteru pomocí [dotazy Hive](../machine-learning/team-data-science-process/create-features-hive.md). *Konstruování* pokusí zvýšit předpovídat učení algoritmy vytvořením funkce z nezpracovaná data, která usnadňují proces učení. Můžete spouštět dotazy HiveQL z Azure ML a přístup k datům v Hive zpracovat a uloženy v blob storage, pomocí [importovat Data modulu](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Hloubkové learning](https://www.microsoft.com/en-us/research/group/dltc/) je větev strojové učení, používající neuronové sítě vycházející biologické procesy lidského mozku. Mnoho výzkumných pracovníků najdete hloubkové learning jako Slíbení přístup rozšíření umělé inteligence. Příkladem hloubkové learning jsou překladatelé mluvené jazyk, systémy rozpoznávání bitové kopie a reasoning počítače.

Pomoc při přechodu vlastní pracovní hloubkové dozvědět, Microsoft vyvinul open-source zdarma, který se snadno použitelný, [kognitivní nástrojů Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/). Tato sada nástrojů používá pro celou řadu produktů společnosti Microsoft, společnostmi po celém světě s potřeba nasadit přímý learning ve velkém měřítku a studenty zájem o nejnovější algoritmy a postupy. 

## <a name="see-also"></a>Další informace najdete v tématech

### <a name="scenarios"></a>Scénáře

* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generování doporučení s Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive a Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive a Azure Machine Learning klient server](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Strojového učení s Spark v HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Hloubkové materiály

* [Sada nástrojů hloubkové learning s Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Jednoduše paralelně zpracovatelné image klasifikaci s kognitivní toolkit + Tensorflow na Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
