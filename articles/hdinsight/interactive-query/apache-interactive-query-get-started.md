---
title: Co je interaktivní dotaz ve službě Azure HDInsight?
description: Úvod do interaktivního dotazu, označovaného taky Apache Hive LLAP, ve službě Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: e133e08e333cb478269a93cce963566e195d6949
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "78271953"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Co je interaktivní dotaz ve službě Azure HDInsight

Interaktivní dotaz (označovaný také jako Apache Hive LLAP nebo [Analytical Processing s nízkou latencí](https://cwiki.apache.org/confluence/display/Hive/LLAP)) je [typ clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)Azure HDInsight. Interaktivní dotaz podporuje ukládání do mezipaměti v paměti, což dává Apache Hive dotazy rychleji a mnohem více interaktivních. Zákazníci používají interaktivní dotaz k dotazování dat uložených ve službě Azure Storage & Azure Data Lake Storage velmi rychle. Interaktivní dotaz vývojářům a datům usnadňuje práci s velkými objemy dat pomocí nástrojů BI, které mají nejvíc. Interaktivní dotaz HDInsight podporuje snadnější přístup k velkým objemům dat několika různými nástroji.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Cluster interaktivních dotazů se liší od Apache Hadoopho clusteru. Obsahuje pouze službu podregistr.

Ke službě podregistru můžete přistupovat v clusteru interaktivních dotazů jenom přes zobrazení podregistru Apache Ambari, Beeline a Microsoft podregistr Open Database Connectivity (podregistr ODBC). K němu nemáte přístup prostřednictvím konzoly pro podregistrování, Templeton, rozhraní příkazového řádku Azure Classic nebo Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Vytvoření clusteru interaktivních dotazů

Informace o vytvoření clusteru HDInsight najdete v tématu věnovaném [vytváření Apache Hadoop clusterů ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Vyberte typ clusteru interaktivní dotaz.

> [!IMPORTANT]
> Minimální velikost hlavnímu uzlu pro clustery interaktivních dotazů je Standard_D13_v2. Další informace najdete v [grafu o velikosti virtuálních počítačů Azure](../../cloud-services/cloud-services-sizes-specs.md#dv2-series).

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Spustit Apache Hive dotazy z interaktivního dotazu

K provedení dotazů na podregistr máte následující možnosti:

|Metoda |Popis |
|---|---|
|Microsoft Power BI|Podívejte se, jak [vizualizovat interaktivní dotazy Apache Hive data pomocí Power BI ve službě Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md)a [vizualizovat velké objemy dat pomocí Power BI ve službě Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).|
|Visual Studio|Přečtěte si téma [připojení ke službě Azure HDInsight a spuštění dotazů Apache Hive pomocí data Lakech nástrojů pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).|
|Visual Studio Code|Viz [použití Visual Studio Code pro Apache Hive, LLAP nebo pySpark](../hdinsight-for-vscode.md).|
|Zobrazení podregistru Apache Ambari|Viz [použití Apache Hive zobrazení pomocí Apache Hadoop ve službě Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). Zobrazení podregistru není pro HDInsight 4,0 dostupné.|
|Apache Beeline|Viz [použití Apache Hive s Apache Hadoop ve službě HDInsight s Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). Beeline můžete použít buď z hlavního uzlu, nebo z prázdného hraničního uzlu. Doporučujeme používat Beeline z prázdného hraničního uzlu. Informace o vytvoření clusteru HDInsight pomocí prázdného hraničního uzlu najdete v tématu [použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md).|
|Rozhraní ODBC pro podregistr|V tématu [připojení aplikace Excel k Apache Hadoop s ovladačem Microsoft PODREGISTR ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Vyhledání připojovacího řetězce připojení k databázi Java (JDBC):

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` umístění, kde `CLUSTERNAME` je název vašeho clusteru.
1. Pokud chcete zkopírovat adresu URL, vyberte ikonu schránky:

   ![Interaktivní dotaz Hadoop pro HDInsight LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvářet clustery interaktivních dotazů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Naučte [se vizualizovat velké objemy dat pomocí Power BI ve službě Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Naučte se [používat Apache Zeppelin ke spouštění dotazů Apache Hive v Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
