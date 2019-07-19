---
title: Řešení potíží s registrací pomocí Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se práce s Apache Hive a Azure HDInsight.
keywords: Azure HDInsight, podregistr, nejčastější dotazy, Průvodce odstraňováním potíží, běžné otázky
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 91e6803e0a1302a33a3bf176ad84d0b0e0c8c5b6
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875928"
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

Kód v krocích řešení předpokládá, že cesty k datům v novém clusteru jsou stejné jako cesty k datům v původním clusteru. Pokud se cesty k datům liší, můžete ručně upravit vygenerovaný soubor alltables. SQL tak, aby odrážel všechny změny.

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

4. Chcete-li zobrazit protokoly Hiveserver, použijte následující příkaz:

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


### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](hdinsight-troubleshoot-guide.md)




