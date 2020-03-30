---
title: Co je interaktivní dotaz v Azure HDInsight?
description: Úvod do interaktivního dotazu, nazývaný také Apache Hive LLAP, v Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: e133e08e333cb478269a93cce963566e195d6949
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271953"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Co je interaktivní dotaz v Azure HDInsight

Interaktivní dotaz (nazývaný také Apache Hive LLAP nebo [analytické zpracování s nízkou latencí)](https://cwiki.apache.org/confluence/display/Hive/LLAP)je typ [clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)Azure HDInsight . Interaktivní dotaz podporuje ukládání do mezipaměti v paměti, což činí dotazy Apache Hive rychlejší a mnohem interaktivnější. Zákazníci používají interaktivní dotaz k dotazování dat uložených v úložišti Azure & Azure Data Lake Storage super rychlým způsobem. Interaktivní dotaz usnadňuje vývojářům a datovým vědcům práci s velkými objemy dat pomocí nástrojů BI, které mají nejvíce rádi. INTERAKTIVNÍ DOTAZ HDInsight podporuje několik nástrojů pro snadný přístup k velkým datům.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Cluster interaktivních dotazů se liší od clusteru Apache Hadoop. Obsahuje pouze službu Hive.

Ke službě Hive v clusteru interaktivních dotazů můžete přistupovat pouze prostřednictvím zobrazení Apache Ambari Hive View, Beeline a ovladače připojení otevřené databáze Microsoft Hive (Hive ODBC). Nemůžete k němu přistupovat přes konzolu Hive, Templeton, Azure Classic CLI nebo Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Vytvoření clusteru interaktivních dotazů

Informace o vytvoření clusteru HDInsight najdete [v tématu Vytvoření clusterů Apache Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Zvolte typ clusteru interaktivních dotazů.

> [!IMPORTANT]
> Minimální velikost headnode pro clustery interaktivních dotazů je Standard_D13_v2. Další informace najdete v [tabulce velikosti virtuálního počítače Azure.](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Spouštění dotazů Apache Hive z interaktivního dotazu

Chcete-li spustit dotazy Hive, máte následující možnosti:

|Metoda |Popis |
|---|---|
|Microsoft Power BI|Podívejte se na [viz Visualize Interactive Query Apache Hive data s Power BI v Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md)a [Visualize velkých objemů dat s Power BI v Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).|
|Visual Studio|Viz [Připojení k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů data lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).|
|Visual Studio Code|Viz [Použití kódu Visual Studia pro Apache Hive, LLAP nebo pySpark](../hdinsight-for-vscode.md).|
|Pohled na hive Apache Ambari|Viz [Použití Apache Hive View s Apache Hadoop v Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). Zobrazení hive není k dispozici pro hdinsight 4.0.|
|Apache Včela|Viz [Použití Apache Hive s Apache Hadoop v HDInsight s Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). Můžete použít Beeline z hlavního uzlu nebo z prázdného hraničního uzlu. Doporučujeme používat Beeline z prázdného hraničního uzlu. Informace o vytvoření clusteru HDInsight pomocí prázdného uzlu okraje najdete v tématu [Použití prázdných hraničních uzlů ve službě HDInsight](../hdinsight-apps-use-edge-node.md).|
|Úl ODBC|Viz [Připojení Excelu k Apache Hadoop s ovladačem Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Vyhledání připojovacího řetězce Připojení k databázi Java (JDBC):

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`přejděte `CLUSTERNAME` na , kde je název clusteru.
1. Chcete-li adresu URL zkopírovat, vyberte ikonu schránky:

   ![HDInsight Hadoop interaktivní dotaz LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit clustery interaktivních dotazů v HDInsightu](../hdinsight-hadoop-provision-linux-clusters.md).
* Přečtěte si, jak [vizualizovat velká data pomocí Power BI v Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Přečtěte si, jak [používat Apache Zeppelin ke spouštění dotazů Apache Hive v Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
