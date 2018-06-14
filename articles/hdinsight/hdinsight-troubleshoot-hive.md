---
title: Řešení potíží Hive pomocí Azure HDInsight | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se práce s Apache Hive a Azure HDInsight.
keywords: Azure HDInsight Hive, – nejčastější dotazy, řešení potíží s průvodce, časté otázky
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: ''
editor: ''
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: dharmeshkakadia
ms.openlocfilehash: d397552285466dc038fe580a084e2d1d0d69bfe2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407693"
---
# <a name="troubleshoot-hive-by-using-azure-hdinsight"></a>Řešení potíží Hive pomocí Azure HDInsight

Další informace o nejvyšší otázky a jejich řešení při práci s Apache Hive datové části v Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak exportovat metaúložiště Hive a importovat na jiný cluster?


### <a name="resolution-steps"></a>Postup řešení

1. Připojte se ke clusteru HDInsight pomocí klienta Secure Shell (SSH). Další informace najdete v tématu [další čtení](#additional-reading-end).

2. Na clusteru HDInsight, ze kterého chcete provést export metaúložiště spusťte následující příkaz:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

  Tento příkaz vytvoří soubor s názvem allatables.sql.

3. Zkopírujte soubor alltables.sql do nového clusteru HDInsight a pak spusťte následující příkaz:

  ```apache
  hive -f alltables.sql
  ```

Kód v kroky řešení předpokládá, že cesty k datům v novém clusteru jsou stejné jako cest k datům v původním clusteru. Pokud cest k datům liší, můžete ručně upravit soubor generovaný alltables.sql tak, aby odrážela všechny změny.

### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru HDInsight pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Jak najít protokoly Hive v clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Připojte ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu **další čtení**.

2. Chcete-li zobrazit protokoly Hive klienta, použijte následující příkaz:

  ```apache
  /tmp/<username>/hive.log 
  ```

3. Pokud chcete zobrazit protokoly metaúložiště Hive, použijte následující příkaz:

  ```apache
  /var/log/hive/hivemetastore.log 
  ```

4. Pokud chcete zobrazit protokoly Hiveserver, použijte následující příkaz:

  ```apache
  /var/log/hive/hiveserver2.log 
  ```

### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru HDInsight pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak spuštění prostředí Hive s konkrétní konfigurací v clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Zadejte pár klíč hodnota konfigurace při spuštění prostředí Hive. Další informace najdete v tématu [další čtení](#additional-reading-end).

  ```apache
  hive -hiveconf a=b 
  ```

2. K zobrazení seznamu všech efektivní konfigurace v prostředí Hive, použijte následující příkaz:

  ```apache
  hive> set;
  ```

  Spuštění prostředí Hive se protokolování ladění na konzole například použijte následující příkaz:

  ```apache
  hive -hiveconf hive.root.logger=ALL,console 
  ```

### <a name="additional-reading"></a>Další čtení

- [Vlastnosti konfigurace Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak můžu analyzovat data Tez DAG v clusteru kritické cestě?


### <a name="resolution-steps"></a>Postup řešení
 
1. Pro analýzu Apache Tez necyklicky (DAG) na clusteru kritické graf, připojte ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [další čtení](#additional-reading-end).

2. Na příkazovém řádku spusťte následující příkaz:
   
  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
  ```

3. Seznam dalších analyzátory, které lze použít k analýze Tez DAG, použijte následující příkaz:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
  ```

  Ukázkový program je nutné zadat jako první argument.

  Program platné názvy patří:
    - **ContainerReuseAnalyzer**: tisk podrobností o opakované použití kontejneru v DAG
    - **CriticalPath**: najít kritické cestu DAG
    - **LocalityAnalyzer**: tisk polohu podrobností v DAG
    - **ShuffleTimeAnalyzer**: analyzovat podrobnosti času náhodně v DAG
    - **SkewAnalyzer**: Analýza zkosení podrobnosti v DAG
    - **SlowNodeAnalyzer**: tisk podrobností uzlu v DAG
    - **SlowTaskIdentifier**: tisk podrobnosti pomalé úlohy v DAG
    - **SlowestVertexAnalyzer**: tisk nejpomalejší vrchol podrobností v DAG
    - **SpillAnalyzer**: tisk přepadového podrobnosti v DAG
    - **TaskConcurrencyAnalyzer**: tisk souběžnosti podrobnosti úlohy v DAG
    - **VertexLevelCriticalPathAnalyzer**: najít kritické cesty na úrovni vrchol v DAG


### <a name="additional-reading"></a>Další čtení

- [Připojení ke clusteru HDInsight pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Jak lze stáhnout Tez DAG data z clusteru?


#### <a name="resolution-steps"></a>Postup řešení

Ke shromažďování dat Tez DAG dvěma způsoby:

- Z příkazového řádku:
 
    Připojte ke clusteru HDInsight pomocí SSH. Na příkazovém řádku spusťte následující příkaz:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Použijte zobrazení Ambari Tez:
   
  1. Přejděte na Ambari. 
  2. Přejděte do zobrazení Tez (pod ikonu dlaždice v pravém horním rohu). 
  3. Vyberte DAG, které chcete zobrazit.
  4. Vyberte **stahování dat**.

### <a name="additional-reading-end"></a>Další čtení

[Připojení ke clusteru HDInsight pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](hdinsight-troubleshoot-guide.md)




