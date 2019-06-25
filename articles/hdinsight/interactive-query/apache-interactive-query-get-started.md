---
title: Co je v Azure HDInsight Interactive Query?
description: Úvod do interaktivního dotazu v Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/14/2019
ms.openlocfilehash: ea17ddeab21c371f41cc57115df4dd91277c3c42
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151197"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Co je interaktivní dotaz v Azure HDInsight

Interactive Query (také nazývané Apache Hive LLAP nebo [s nízkou latencí analytické zpracování](https://cwiki.apache.org/confluence/display/Hive/LLAP)) Azure HDInsight je [typ clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interactive Query podporuje ukládání do mezipaměti v paměti, takže se dotazy Apache Hive, rychlejší a mnohem vyšší interaktivitou. Zákazníci pomocí interaktivního dotazu data dotazů uložená v Azure storage a Azure Data Lake Storage mimořádně rychlým způsobem. Interaktivní dotaz usnadňuje vývoj a data mezi odborníky přes pro práci s velkými objemy dat pomocí nástrojů BI, která mají rádi na maximum. HDInsight Interactive Query podporuje několik nástrojů pro přístup k velké objemy dat snadno způsobem.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Cluster Interactive Query se liší od cluster Apache Hadoop. Obsahuje jedinou službou Hive.

Můžete přístup ke službě Hive v clusteru Interactive Query pouze prostřednictvím zobrazení Ambari Hive Apache, Beeline a ovladače Microsoft Hive Open Database Connectivity (rozhraní ODBC Hive). Nelze k němu přístup přes konzolu Hive, Templeton, rozhraní příkazového řádku Azure Classic nebo Azure Powershellu.

## <a name="create-an-interactive-query-cluster"></a>Vytvořte cluster Interactive Query

Informace o vytváření clusteru HDInsight najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Zvolte typ clusteru Interactive Query.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Spustit dotazy Apache Hive v Interactive Query

Ke spouštění dotazů Hive, máte následující možnosti:

* Použít Microsoft Power BI

    Zobrazit [vizualizace dat Interactive Query Apache Hive pomocí Power BI v Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) naleznete v tématu [vizualizovat velké objemy dat v Power BI v Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Použití Visual Studia

    Zobrazit [připojení k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Použijte Visual Studio Code

    Zobrazit [použití nástroje Visual Studio Code pro Apache Hive, LLAP nebo pySpark](../hdinsight-for-vscode.md).
* Spusťte Apache Hive pomocí zobrazení Hive Apache Ambari.
  
    Zobrazit [zobrazení Apache Hive pomocí Apache Hadoop v Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Použití Beeline spustíte Apache Hive.
  
    Zobrazit [použití Apache Hivu s Apache Hadoop v HDInsight s Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Můžete Beeline z hlavního uzlu nebo z prázdných hraničních uzlů. Doporučujeme používat Beeline z prázdných hraničních uzlů. Informace o vytváření clusteru služby HDInsight pomocí prázdných hraničních uzlů najdete v tématu [použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md).
* Spusťte Apache Hivu pomocí Hive ODBC.
  
    Zobrazit [připojení Excelu k systému Apache Hadoop pomocí ovladače Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Chcete-li nalezen připojovací řetězec připojení k databázi Java (JDBC):

1. Přihlaste se k Apache Ambari pomocí následující adresy URL: `https://<cluster name>.AzureHDInsight.net`.
2. V nabídce vlevo vyberte **Hive**.
3. Zkopírujte adresu URL, vyberte ikonu schránky:

   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [v HDInsight vytvořit clusterům Interactive Query](../hdinsight-hadoop-provision-linux-clusters.md).
* Zjistěte, jak [vizualizovat velké objemy dat v Power BI v Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Zjistěte, jak [použití Apache Zeppelinu ke spuštění dotazy Apache Hive v HDInsight Azure](../interactive-query/hdinsight-connect-hive-zeppelin.md).
