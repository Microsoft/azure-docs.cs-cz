---
title: Řešení potíží s Hive pomocí Azure HDInsight
description: Získejte odpovědi na běžné dotazy týkající se práce s Apache Hive nebo Azure HDInsight.
keywords: HDInsight Hive, nejčastější dotazy k Azure, odstraňováním, nejčastější dotazy
services: hdinsight
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dharmeshkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 832fab6c4f183ddad512c5e6e4309d70938a316b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600019"
---
# <a name="troubleshoot-hive-by-using-azure-hdinsight"></a>Řešení potíží s Hive pomocí Azure HDInsight

Další informace o častých dotazů a jejich řešení při práci s Apache Hive datové části v Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak export metastoru Hive a importovat na jiném clusteru?


### <a name="resolution-steps"></a>Postup řešení

1. Připojení ke clusteru HDInsight pomocí klienta Secure Shell (SSH). Další informace najdete v tématu [další čtení](#additional-reading-end).

2. Spusťte následující příkaz v clusteru HDInsight, ze kterého chcete exportovat úložiště metadat:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

  Tento příkaz vygeneruje soubor s názvem allatables.sql.

3. Zkopírujte soubor alltables.sql do nového clusteru HDInsight a pak spusťte následující příkaz:

  ```apache
  hive -f alltables.sql
  ```

Kód v kroků pro řešení předpokládá, že cesty k datům v novém clusteru jsou stejné jako cesty k datům v původním clusteru. Pokud cesty k datům se liší, můžete ručně upravit soubor generovaný alltables.sql tak, aby odrážela všechny změny.

### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru služby HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Kde najdu protokoly Hive v clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Připojení ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu **další čtení**.

2. Pokud chcete zobrazit protokoly Hive klienta, použijte následující příkaz:

  ```apache
  /tmp/<username>/hive.log 
  ```

3. Pokud chcete zobrazit protokoly metastore Hive, použijte následující příkaz:

  ```apache
  /var/log/hive/hivemetastore.log 
  ```

4. Pokud chcete zobrazit protokoly Hiveserver, použijte následující příkaz:

  ```apache
  /var/log/hive/hiveserver2.log 
  ```

### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru služby HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak můžu spustit prostředí Hive s konkrétní konfigurací v clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Zadejte pár klíč hodnota konfigurace při spuštění prostředí Hive. Další informace najdete v tématu [další čtení](#additional-reading-end).

  ```apache
  hive -hiveconf a=b 
  ```

2. Chcete-li vypsat všechny platné konfigurace v prostředí Hive, použijte následující příkaz:

  ```apache
  hive> set;
  ```

  Například použijte následující příkaz ke spuštění prostředí Hive s protokolováním ladění povoleno v konzole:

  ```apache
  hive -hiveconf hive.root.logger=ALL,console 
  ```

### <a name="additional-reading"></a>Další čtení

- [Vlastnosti konfigurace Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak mohu analyzovat data Tez DAG na clusteru kritickou cestu?


### <a name="resolution-steps"></a>Postup řešení
 
1. K analýze Apache Tez orientovaného acyklického grafu (DAG) na clusteru pro chod grafu, připojení ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [další čtení](#additional-reading-end).

2. Na příkazovém řádku spusťte následující příkaz:
   
  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
  ```

3. Seznam dalších analyzátorů, které můžete použít k analýze Tez DAG, použijte následující příkaz:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
  ```

  Ukázkový program je nutné zadat jako první argument.

  Program platné názvy patří:
    - **ContainerReuseAnalyzer**: Tisknout podrobnosti o opakované použití kontejneru ve skupině DAG
    - **CriticalPath**: najít kritické cesty DAG
    - **LocalityAnalyzer**: vytisknout podrobnosti lokality ve skupině DAG
    - **ShuffleTimeAnalyzer**: Analýza podrobností o času shuffle ve skupině DAG
    - **SkewAnalyzer**: Analýza zkosení podrobností ve skupině DAG
    - **SlowNodeAnalyzer**: Tisknout podrobnosti o uzlu ve skupině DAG
    - **SlowTaskIdentifier**: tisk podrobnosti pomalé úlohy ve skupině DAG
    - **SlowestVertexAnalyzer**: tisk nejpomalejší podrobnosti vrcholu ve skupině DAG
    - **SpillAnalyzer**: tisk přelití podrobnosti ve skupině DAG
    - **TaskConcurrencyAnalyzer**: tisk souběžnosti podrobnosti úlohy ve skupině DAG
    - **VertexLevelCriticalPathAnalyzer**: najít kritickou cestu na úrovni vrcholů ve skupině DAG


### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru služby HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Jak stáhnout Tez DAG data z clusteru?


#### <a name="resolution-steps"></a>Postup řešení

Existují dva způsoby, jak shromažďovat data Tez DAG:

- Z příkazového řádku:
 
    Připojení ke clusteru HDInsight pomocí SSH. Na příkazovém řádku spusťte následující příkaz:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Použití zobrazení Ambari Tez:
   
  1. Přejdete na Ambari. 
  2. Přejděte do zobrazení Tez (podle dlaždice ikonu v pravém horním rohu). 
  3. Vyberte DAG, které chcete zobrazit.
  4. Vyberte **stáhnout data**.

### <a name="additional-reading-end"></a>Další čtení

[Připojení ke clusteru služby HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](hdinsight-troubleshoot-guide.md)




