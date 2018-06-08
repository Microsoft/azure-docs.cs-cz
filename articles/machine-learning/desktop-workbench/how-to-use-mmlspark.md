---
title: Jak používat MMLSpark Machine Learning | Microsoft Docs
description: Průvodce použití knihovny MMLSpark s Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 3c9bcce67bb802a8596416b55ef61a51ab2900bd
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831526"
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Jak používat Microsoft Machine Learning knihovny pro Apache Spark

## <a name="introduction"></a>Úvod

[Microsoft Machine Learning knihovny pro Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) poskytuje nástroje, které vám umožní snadno vytvářet škálovatelné machine learning modely pro rozsáhlých datových sad. Obsahuje integrace SparkML kanálů s [kognitivní nástrojů Microsoft ](https://github.com/Microsoft/CNTK) a [OpenCV](http://www.opencv.org/), umožňuje: 
 * Příjem příchozích dat a data před zpracování obrázku
 * Featurize obrázky a text použití předem vyškolení přímým učení modely
 * Trénování a stanovíte jeho skóre klasifikace a regrese modelů pomocí implicitní featurization.

## <a name="prerequisites"></a>Požadavky

Na krok tímto průvodcem postupy, budete muset:
- [Nainstalovat Azure Machine Learning Workbench](../service/quickstart-installation.md)
- [Nastavte cluster Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Spuštění experimentu v kontejner Docker

Vaše Azure Machine Learning Workbench je nakonfigurovaný na použití MMLSpark při spuštění experimenty v kontejner Docker místně nebo do vzdáleného virtuálního počítače. Tato funkce umožňuje snadno ladění a Experimentujte s modely PySpark, před jejich spuštěním na škálování na clusteru. 

Chcete-li začít používat příklad, vytvořte nový projekt a vyberte Galerie "MMLSpark na pro dospělé úplné zjišťování" příklad. Vyberte "Docker" jako kontext výpočetní na řídicím panelu projektu a klikněte na možnost "Spustit". Azure Machine Learning Workbench vytvoří kontejner Docker ke spuštění programu PySpark a poté spustí program.

Po dokončení spuštění, můžete zobrazit výsledky v zobrazení historie spouštění nástroje Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Nainstalujte MMLSpark na clusteru Azure HDInsight Spark.

K dokončení toto a následující krok, potřebujete první [vytvořit cluster Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Ve výchozím nastavení nainstaluje Azure Machine Learning Workbench MMLSpark balíček v clusteru, při spuštění experimentu. Můžete řídit toto chování a instalaci dalších balíčků Spark úpravou souboru s názvem _aml_config/spark_dependencies.yml_ ve složce projektu.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

MMLSpark můžete taky nainstalovat přímo na používání clusteru HDInsight Spark [akce skriptu](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Nastavení clusteru Azure HDInsight Spark jako výpočetní cíl

Otevřete okno příkazového řádku z Azure Machine Learning Workbench přechodem do nabídky "Soubor" a klikněte na tlačítko "spusťte příkazový řádek"

V okně příkazového řádku spusťte následující příkazy:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Nyní je k dispozici jako cíl pro projekt výpočetní cluster.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Spusťte experiment v clusteru Azure HDInsight Spark.

Přejděte zpět na řídicím panelu projektu příkladu "MMLSpark na pro dospělé úplné zjišťování". Vyberte jako cíl výpočetní cluster a klikněte na tlačítko spustit.

Azure Machine Learning Workbench odesílá úlohy spark pro cluster. Můžete monitorovat průběh a zobrazit výsledky v zobrazení historie spouštění.

## <a name="next-steps"></a>Další postup
Informace o MMLSpark knihovny a příklady naleznete v tématu [úložiště MMLSpark GitHub](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark a Spark® jsou registrované ochranné známky nebo ochranné známky Foundation softwaru Apache ve Spojených státech a dalších zemích.*
