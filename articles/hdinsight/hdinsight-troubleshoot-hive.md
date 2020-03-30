---
title: Poradce při potížích s Hive pomocí Azure HDInsight
description: Získejte odpovědi na časté otázky týkající se práce s Apache Hive a Azure HDInsight.
keywords: Azure HDInsight, Hive, nejčastější dotazy, průvodce odstraňováním problémů, běžné otázky
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895235"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Řešení potíží s Apache Hivem s využitím Azure HDInsightu

Získejte informace o hlavních otázkách a jejich předsevzetí při práci s datovými částmi Apache Hive v Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak exportuji metastore Hive a importuji ho do jiného clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Připojte se ke clusteru HDInsight pomocí klienta Secure Shell (SSH). Další informace naleznete v [tématu Další čtení](#additional-reading-end).

2. V clusteru HDInsight, ze kterého chcete metastore exportovat, spusťte následující příkaz:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Tento příkaz generuje soubor s názvem allatables.sql.

3. Zkopírujte soubor alltables.sql do nového clusteru HDInsight a spusťte následující příkaz:

    ```apache
    hive -f alltables.sql
    ```

Kód v krocích řešení předpokládá, že datové cesty v novém clusteru jsou stejné jako datové cesty ve starém clusteru. Pokud se datové cesty liší, můžete ručně upravit `alltables.sql` generovaný soubor tak, aby odrážel všechny změny.

### <a name="additional-reading"></a>Dodatečné čtení

- [Připojení ke clusteru HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Jak lze najít protokoly Hive v clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace naleznete v **tématu Další čtení**.

2. Chcete-li zobrazit protokoly klientů Hive, použijte následující příkaz:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Chcete-li zobrazit protokoly metaúložiště Hive, použijte následující příkaz:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Chcete-li zobrazit protokoly serveru Hive, použijte následující příkaz:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Dodatečné čtení

- [Připojení ke clusteru HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak spusťte prostředí Hive s konkrétními konfiguracemi v clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Při spuštění prostředí Hive zadejte dvojici konfiguračníklíč-hodnota. Další informace naleznete v [tématu Další čtení](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Chcete-li vypsat všechny efektivní konfigurace v prostředí Hive, použijte následující příkaz:

   ```apache
   hive> set;
   ```

   Pomocí následujícího příkazu můžete například spustit prostředí Hive s povoleným protokolováním ladění v konzole:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Dodatečné čtení

- [Vlastnosti konfigurace úlu](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak lze analyzovat data Apache Tez DAG na cestě kritické pro cluster?

### <a name="resolution-steps"></a>Postup řešení

1. Chcete-li analyzovat acyklický graf (DAG) řízeného Apache Tez na grafu kritickém pro cluster, připojte se ke clusteru HDInsight pomocí SSH. Další informace naleznete v [tématu Další čtení](#additional-reading-end).

2. Na příkazovém řádku spusťte následující příkaz:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Chcete-li uvést další analyzátory, které lze použít k analýze Tez DAG, použijte následující příkaz:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Jako první argument je nutné zadat ukázkový program.

   Mezi platné názvy programů patří:
    - **ContainerReuseAnalyzer**: Tisk kontejneru znovu použít podrobnosti v DAG
    - **CriticalPath**: Najít kritickou cestu DAG
    - **LocalityAnalyzer**: Tisk podrobností o lokalitě v DAG
    - **ShuffleTimeAnalyzer**: Analyzovat podrobnosti o čase náhodného přehrávání v DAG
    - **SkewAnalyzer**: Analýza detailů zkosení v DAG
    - **SlowNodeAnalyzer**: Tisk podrobností uzlu v DAG
    - **SlowTaskIdentifier**: Tisk pomalých podrobností úkolu v dag
    - **SlowestVertexAnalyzer**: Tisk nejpomalejších podrobností o vrcholu v DAG
    - **SpillAnalyzer**: Tisk podrobností o úniku v DAG
    - **TaskConcurrencyAnalyzer**: Tisk podrobností souběžnosti úkolu v dag
    - **VertexLevelCriticalPathAnalyzer**: Najít kritickou cestu na úrovni vrcholu v DAG

### <a name="additional-reading"></a>Dodatečné čtení

- [Připojení ke clusteru HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Jak lze stáhnout data Tez DAG z clusteru?

#### <a name="resolution-steps"></a>Postup řešení

Existují dva způsoby, jak shromažďovat data Tez DAG:

- Z příkazového řádku:

    Připojte se ke clusteru HDInsight pomocí SSH. Na příkazovém řádku spusťte následující příkaz:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Použijte zobrazení Ambari Tez:

  1. Jeď do Ambari.
  2. Přejděte do zobrazení Tez (pod ikonou dlaždic v pravém horním rohu).
  3. Vyberte dag, který chcete zobrazit.
  4. Vyberte **možnost Stáhnout data**.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Dodatečné čtení

[Připojení ke clusteru HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

- Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
