---
title: Řešení potíží s registrací pomocí Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se práce s Apache Hive a Azure HDInsight.
keywords: Azure HDInsight, podregistr, nejčastější dotazy, Průvodce odstraňováním potíží, běžné otázky
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 9169bd9a63666238e9d6b97d86bf1e9e10312c1b
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076512"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Řešení potíží s Apache Hive pomocí Azure HDInsight

Přečtěte si o nejčastějších dotazech a jejich řešeních při práci s Apache Hivemi datovými částmi v Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Návody exportovat metastore Hive a naimportovat ho na jiný cluster?

### <a name="resolution-steps"></a>Postup řešení

1. Připojení ke clusteru HDInsight pomocí klienta Secure Shell (SSH). Další informace najdete v tématu [další čtení](#additional-reading-end).

2. Spusťte následující příkaz na clusteru HDInsight, ze kterého chcete exportovat metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Tento příkaz vygeneruje soubor s názvem allatables. SQL.

3. Zkopírujte soubor alltables. SQL do nového clusteru HDInsight a pak spusťte následující příkaz:

    ```apache
    hive -f alltables.sql
    ```

Kód v krocích řešení předpokládá, že cesty k datům v novém clusteru jsou stejné jako cesty k datům v původním clusteru. Pokud se cesty k datům liší, můžete vygenerovaný `alltables.sql` soubor ručně upravit tak, aby odrážel všechny změny.

### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Návody najít v clusteru protokoly podregistru?

### <a name="resolution-steps"></a>Postup řešení

1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu **další čtení**.

2. Chcete-li zobrazit protokoly klienta podregistru, použijte následující příkaz:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Chcete-li zobrazit protokoly metastore Hive, použijte následující příkaz:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Chcete-li zobrazit protokoly serveru pro podregistr, použijte následující příkaz:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Návody spustit prostředí pro podregistr s konkrétní konfigurací v clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Při spuštění prostředí podregistr zadejte dvojici klíč-hodnota konfigurace. Další informace najdete v tématu [další čtení](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Pokud chcete zobrazit seznam všech efektivních konfigurací v prostředí s podregistry, použijte následující příkaz:

   ```apache
   hive> set;
   ```

   Pomocí následujícího příkazu můžete například spustit prostředí registru s povoleným protokolováním ladění v konzole nástroje:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Další čtení

- [Vlastnosti konfigurace podregistru](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Návody analyzovat Apache Tez DAG data na cestě kritické pro cluster?

### <a name="resolution-steps"></a>Postup řešení

1. Pokud chcete analyzovat Apache Tez acyklického Graph (DAG) v grafu kritickém pro cluster, připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [další čtení](#additional-reading-end).

2. Na příkazovém řádku spusťte následující příkaz:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. K vypsání dalších analyzátorů, které se dají použít k analýze tez DAG, použijte následující příkaz:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Jako první argument musíte zadat vzorový program.

   Platné názvy programů zahrnují:
    - **ContainerReuseAnalyzer**: Tisk podrobností o opětovném použití kontejneru v DAG
    - **CriticalPath**: Vyhledání kritické cesty k DAG
    - **LocalityAnalyzer**: Tisk podrobností o národní prostředí v DAG
    - **ShuffleTimeAnalyzer**: Analyzovat podrobnosti o čase náhodného přehrávání v DAG
    - **SkewAnalyzer**: Analyzovat detaily zkosení v DAG
    - **SlowNodeAnalyzer**: Tisk podrobností uzlu v DAG
    - **SlowTaskIdentifier**: Tisk s pomalými podrobnostmi o úloze v DAG
    - **SlowestVertexAnalyzer**: Tisk nejpomalejších detailů vrcholu v DAG
    - **SpillAnalyzer**: Tisk podrobností o rozlití v DAG
    - **TaskConcurrencyAnalyzer**: Tisk podrobností o souběžnosti úkolu ve DAG
    - **VertexLevelCriticalPathAnalyzer**: Najde kritickou cestu na úrovni vrcholu v DAG.

### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Návody stáhnout data DAG z clusteru?

#### <a name="resolution-steps"></a>Postup řešení

Existují dva způsoby, jak shromažďovat data DAG tez:

- Z příkazového řádku:

    Připojte se ke clusteru HDInsight pomocí SSH. Na příkazovém řádku spusťte následující příkaz:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Použijte zobrazení Ambari tez:

  1. Přejít na Ambari.
  2. V pravém horním rohu přejdete do zobrazení tez (pod ikonou dlaždice).
  3. Vyberte DAG, který chcete zobrazit.
  4. Vyberte **stáhnout data**.

### <a name="additional-reading-end"></a>Další čtení

[Připojení ke clusteru HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
