---
title: Použití interaktivního dotazu pomocí Azure HDInsight
description: Zjistěte, jak pomocí HDInsight Interactive Query (Hive LLAP).
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: e83d51a18c7ab5861699114e4622bda167dab41d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042093"
---
# <a name="use-interactive-query-with-hdinsight"></a>Pomocí HDInsight Interactive Query
Interactive Query (také nazývané Hive LLAP nebo [s nízkou latencí analytické zpracování](https://cwiki.apache.org/confluence/display/Hive/LLAP)) Azure HDInsight je [typ clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interactive Query podporuje ukládání do mezipaměti v paměti, takže se dotazy Hive mnohem více interaktivní a rychlejší.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Se liší od Hadoop cluster cluster Interactive Query. Obsahuje jedinou službou Hive. 

> [!NOTE]
> Můžete přístup ke službě Hive v clusteru Interactive Query pouze prostřednictvím zobrazení Ambari Hive, Beeline a ovladače Microsoft Hive Open Database Connectivity (rozhraní ODBC Hive). Nelze k němu přístup přes konzolu Hive, Templeton, nástroji příkazového řádku Azure (Azure CLI) nebo Azure Powershellu. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Vytvořte cluster Interactive Query
Informace o vytváření clusteru HDInsight najdete v tématu [vytváření clusterů Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Zvolte typ clusteru Interactive Query.

## <a name="execute-hive-queries-from-interactive-query"></a>Spouštění dotazů Hive v Interactive Query
Ke spouštění dotazů Hive, máte následující možnosti:

* Použití Power BI

    Zobrazit [data vizualizovat Interactive Query Hive pomocí Power BI v Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) naleznete v tématu [vizualizovat velké objemy dat v Power BI v Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Použití Zeppelin

    Zobrazit [použití Zeppelinu ke spouštění dotazů Hive v HDInsight Azure ](../hdinsight-connect-hive-zeppelin.md).

* Použití Visual Studia

    Zobrazit [připojení k Azure HDInsight a spouštět dotazy Hivu pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* Použijte Visual Studio Code

    Zobrazit [použití nástroje Visual Studio Code pro Hive, LLAP nebo pySpark](../hdinsight-for-vscode.md).
* Spuštění Hive pomocí zobrazení Ambari Hive.
  
    Zobrazit [použití zobrazení Hivu s Hadoopem ve Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Použití Beeline spustíte Hive.
  
    Zobrazit [použití Hivu s Hadoopem v HDInsight s Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Můžete Beeline z hlavního uzlu nebo z prázdných hraničních uzlů. Doporučujeme používat Beeline z prázdných hraničních uzlů. Informace o vytváření clusteru služby HDInsight pomocí prázdných hraničních uzlů najdete v tématu [použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md).
* Spusťte Hivu pomocí Hive ODBC.
  
    Zobrazit [připojení Excelu k Hadoopu pomocí ovladače Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Chcete-li nalezen připojovací řetězec připojení k databázi Java (JDBC):

1. Přihlaste se k Ambari pomocí následující adresy URL: https://\<název clusteru\>. AzureHDInsight.net.
2. V nabídce vlevo vyberte **Hive**.
3. Zkopírujte adresu URL, vyberte ikonu schránky:
   
   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [v HDInsight vytvořit clusterům Interactive Query](../hdinsight-hadoop-provision-linux-clusters.md).
* Zjistěte, jak [vizualizovat velké objemy dat v Power BI v Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Zjistěte, jak [použití Zeppelinu ke spouštění dotazů Hive v HDInsight Azure ](../hdinsight-connect-hive-zeppelin.md).
* Zjistěte, jak [spouštění dotazů Hive pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Zjistěte, jak [použití nástrojů HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* Zjistěte, jak [použijte zobrazení Hive se systémem Hadoop v HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Zjistěte, jak [odesílání dotazů Hive v HDInsight pomocí Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
* Zjistěte, jak [připojení Excelu k Hadoopu pomocí ovladače Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

