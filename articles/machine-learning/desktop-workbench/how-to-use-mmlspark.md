---
title: Jak používat MMLSpark Machine Learning | Dokumentace Microsoftu
description: Příručka pro použití knihovny MMLSpark s Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 654b2559518cd52978153310fbb1e89a91838a8a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643419"
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Jak používat knihovna Microsoft Machine Learning pro Apache Spark

## <a name="introduction"></a>Úvod

[Knihovna Microsoft Machine Learning pro Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) poskytuje nástroje, které vám umožní snadno vytvářet modely škálovatelné strojového učení pro velké datové sady. To zahrnuje integraci kanálů ve SparkML pomocí [Microsoft Cognitive Toolkit ](https://github.com/Microsoft/CNTK) a [OpenCV](http://www.opencv.org/), umožňuje: 
 * Příchozí přenos dat a dat předběžného zpracování obrázků
 * Zpracování volných obrázků a textu s použitím předem natrénovaných modelů hloubkového učení
 * Trénování a stanovíte jeho skóre klasifikačních a regresních modelů pomocí implicitní snadné.

## <a name="prerequisites"></a>Požadavky

Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Instalace aplikace Azure Machine Learning Workbench](../service/quickstart-installation.md)
- [Nastavení clusteru Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Spusťte Experiment v kontejneru Dockeru

Azure Machine Learning Workbench je konfigurovaná pro použití MMLSpark při spouštět experimenty v kontejneru Dockeru, místně nebo ve vzdáleném virtuálním počítači. Díky této funkci můžete snadno ladit a experimentovat s vašimi modely PySpark, před spuštěním v měřítku v clusteru. 

Abyste mohli začít používat příklad, vytvořte nový projekt a vybrat, například "MMLSpark na dospělé sčítání" Galerie. Vyberte "Docker" jako výpočetní kontext z řídicího panelu Projekt a klikněte na možnost "Spustit". Azure Machine Learning Workbench sestaví kontejner Dockeru pro spuštění programu PySpark a poté spustí program.

Po dokončení spuštění se zobrazí výsledky v zobrazení historie spuštění aplikace Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>MMLSpark nainstalujte na clusteru Azure HDInsight Spark.

K dokončení toto a následující krok, musíte si nejdřív [vytvoření clusteru Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Ve výchozím nastavení nainstaluje aplikaci Azure Machine Learning Workbench balíček MMLSpark ve vašem clusteru, při spuštění experimentu. Můžete kontrolovat toto chování a nainstalovat další balíčky Spark úpravou souboru s názvem _aml_config/spark_dependencies.yml_ ve složce vašeho projektu.

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

MMLSpark můžete taky nainstalovat přímo na váš clusteru HDInsight Spark pomocí [akce skriptu](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Nastavení clusteru Azure HDInsight Spark jako cíl Compute

Otevřete okno CLI aplikace Azure Machine Learning Workbench tak, že přejdete do nabídky "File" a klikněte na tlačítko "otevřít příkazový řádek"

V okně příkazového řádku spusťte následující příkazy:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Teď je k dispozici, protože pro projekt cílové výpočetní prostředí clusteru.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Spusťte experiment v clusteru Azure HDInsight Spark.

Vraťte se do řídicího panelu Projekt příkladu "MMLSpark na dospělé sčítání". Vyberte svůj cluster jako cílové výpočetní prostředí a klikněte na tlačítko spustit.

Azure Machine Learning Workbench odešle úlohu spark do clusteru. Můžete sledovat průběh a výsledky zobrazit v zobrazení historie spuštění.

## <a name="next-steps"></a>Další postup
Informace o knihovna MMLSpark a příklady najdete v tématu [úložiště MMLSpark GitHub](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark a Spark® jsou registrované ochranné známky nebo ochranné známky nadace Apache Software ve Spojených státech amerických a dalších zemích.*
