---
title: Poznámky k verzi pro komponent systému Hadoop v Azure HDInsight | Microsoft Docs
description: Nejnovější poznámky k verzi a verze komponent systému Hadoop pro Azure HDInsight. Získáte tipy pro vývoj a podrobnosti pro Spark, R Server, Hive a další.
services: hdinsight
documentationcenter: ''
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: nitinme
ms.openlocfilehash: 17ba4af006587dd1d34f61a0f5f77cbcd768fcf8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Poznámky k verzi pro komponent systému Hadoop v Azure HDInsight

Tento článek obsahuje informace o **nejnovější** Azure HDInsight verze aktualizace. Informace na starších verzích najdete v tématu [archivu poznámky k verzi HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [HDInsight verze článku](hdinsight-component-versioning.md).

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Poznámky pro 03/20/2018 - verzi 2.2 Spark v HDInsight 3.6

- Spark 2.2.0 zlepšuje stabilitu napříč Spark Core, SQL, ML a přináší strukturovaných streamování GA stavu. Spark 2.2.0 je nyní k dispozici na HDInsight 3.6.


## <a name="notes-for-08012017-release-of-hdinsight"></a>Poznámky k verzi 08/01/2017 HDInsight

| Nadpis | Popis | Ovlivněné oblasti  | Typ clusteru  | 
| --- | --- | --- | --- | --- |
| Verze systému Microsoft R Server 9.1 v HDInsight |Zřizování R Server 9.1 clustery HDInsight teď podporuje v HDInsight. Další informace o verzi Microsoft R Server 9.1, najdete v části [tomto blogu](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Služba |R Server |
| HDInsight 3.6 nyní zahrnuje novější verze do zásobníku Hadoop|<ul><li>Podrobný seznam aktualizované verze, najdete v části [verze součástí Hadoop v HDInsight k dispozici](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>Seznam opravených nejnovější verze do zásobníku Hadoop najdete v tématu [informace o opravě Apache](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Seznam nejnovější změny mezi HDP 2.6.1 (což je nyní k dispozici v HDInsight 3.6), naleznete v části [ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html ](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Seznam známých problémů v softwaru HDP 2.6.1 najdete v tématu [známé problémy](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Služba |Vše |neuvedeno |
| Aktualizace pro clustery interaktivní Hive (Preview) |<ul><li><b>Funkce zlepšování.</b> Implementace v mezipaměti metaúložiště, které snižuje zatížení na back-end SQL pomocí ukládání do mezipaměti metadat a zvyšuje výkon pro všechny operace metadata.  Tomuto vylepšení je nyní výchozí na všech clusterech interaktivní Hive. Další informace najdete na adrese [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Funkce zlepšování.</b> Načítání dynamických oddílů je optimalizovaná. Další informace najdete v tématu [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Funkce zlepšování.</b> Konfigurace optimalizace pro HDInsight v systému Linux.</li><li><b>Oprava chyby.</b> `CredentialProviderFactory$getProviders` není bezpečné pro přístup z více vláken. Tento problém je teď vyřešený. Další informace najdete na adrese [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Oprava chyby.</b> Vysoké využití procesoru s ovladačem WASB `liststatus` výsledkem špatný výkon ATS rozhraní API. Tento problém je teď vyřešený. Další informace najdete na adrese [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Služba |Interaktivní Hive (Preview) |
| Aktualizace pro clustery Hadoop |Templeton úlohy operace spolehlivost. Další informace najdete v tématu [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Služba |Hadoop |
| YARN aktualizace | HDInsight nyní vytvoří databázi Ambari 250 GB (bez zvýšit náklady), což vede k lepší prostředí pro zákazníky. Tato změna by měl zabránit ATS získávání naplněna a pravděpodobně mít lepší výkon. |Služba |Vše |
| Aktualizace Spark | Verze Spark 2.1.1. Další informace najdete v tématu [Spark verze 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Služba | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 - obecné dostupnosti HDInsight 3.6

* V této verzi se přidá Azure HDInsight verze 3.6, která je založena na HDP 2.6. Poznámky k verzi HDP 2.6 jsou k dispozici [sem](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) a naleznete další informace o verzích HDInsight [zde](hdinsight-component-versioning.md). HDInsight 3.6 je k dispozici pro následující úlohy:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interaktivní Hive v2.1.0

* **Podpora pro zobrazení Hive 2.0**. Tím by měl vylepšit činnost koncového uživatele pro interaktivní Hive. Další informace najdete v tématu [Hortonworks dokumentaci](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Vylepšení výkonu pomocí Hive LLAP**. Další informace najdete v tématu [Hortonworks dokumentaci](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nové funkce v podregistru**. V tématu [Hortonworks dokumentaci](https://hortonworks.com/apache/hive/#section_4).

* **Hive rozhraní příkazového řádku vyřazení**: Hive rozhraní příkazového řádku je zastaralá a je místo toho používají Beeline zákazníků. Další informace najdete v tématu [dokumentaci Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Pokyny týkající se používání Beeline s HDInsight naleznete v tématu [použít Beeline s HDInsight Hadoop clusterů](hadoop/apache-hadoop-use-hive-beeline.md).

* **Nové funkce v Apache Phoenix a HBase**.
    * Podpora kvótu úložiště: běžně používá v prostředích s více klienty umožňuje omezené úložný prostor na na každou tabulku a na úrovni oboru názvů.
    * Phoenix indexování vylepšení: vytvoření přírůstkové indexu a znovu sestavit nebo obnovit indexování z předchozí chyby.
    * Nástroj pro integritu dat Phoenix: podporuje ověřování schématu, index a další metadata.


* **Problém s HBase**: při spuštění hromadné načtení CSV MapReduce úlohy, následující syntaxi, může dojít k chybě.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Místo toho použijte následující syntaxi:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 - verzi 2.1 Spark v HDInsight 3.6 (Preview)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) zlepšuje mnoho stability a použitelnost problémy s předchozími verzemi. Mezi všechny úlohy Spark, například Spark Core, SQL, ML a Streaming přináší také nové funkce.
* Strukturované Streaming získá lepší škálovatelnost s podporou pro událost čas vodoznaky a Kafka 0.10 konektor.
* Vytváření oddílů Spark SQL je nyní ke zpracování pomocí nového mechanismu škálovatelné zpracování oddílu. Další podrobnosti najdete v části [sem](http://spark.apache.org/releases/spark-release-2-1-0.html) o postupu upgradu.
* 2.1 Spark v Azure HDInsight 3.6 Preview momentálně nepodporuje nástroj BI připojení pomocí ovladače ODBC.
* Přístup k Azure Data Lake Store z clustery Spark 2.1 není podporován v této verzi Preview.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016 - verzi 2.0.1 Spark v HDInsight 3.5
Spark 2.0.1 je nyní k dispozici na clustery Spark (HDInsight verze 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 - verzi serveru R 9.0 na HDInsight 3.5 (Spark 2.0)
*   R Server clustery nyní zahrnují možnost pro dvě verze: 9.0 serveru R na HDI 3.5 (Spark 2.0) a 8.0 serveru R na HDI 3.4 (Spark 1.6).
*   R Server 9.0 na HDI 3.5 (Spark 2.0) je založená na R 3.3.2 a zahrnuje nové ScaleR datový zdroj funkce volány RxHiveData a RxParquetData pro načítání dat z Hive a Parquet přímo na DataFrames Spark pro analýzu ScaleR. Další informace najdete v tématu nápovědy na tyto funkce v R prostřednictvím použití vložený **? RxHiveData** a **? RxParquetData** příkazy.
*   Edice community Rstudia Server je nyní nainstalován ve výchozím nastavení (s možností vyjádření výslovného nesouhlasu) v okně Konfigurace clusteru v rámci zřizování toku.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - verzi 2.0 Spark v HDInsight
* Clustery Spark 2.0 na HDInsight 3.5 teď podporují služby Livy a Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10/26/2016 - verzi R serverem v HDInsight
* Identifikátor URI pro přístup k uzlu edge se změnila na **clustername**-ed-ssh.azurehdinsight.net
* Bylo vylepšeno serveru R na zřizování clusteru HDInsight.
* R serverem v HDInsight je k dispozici jako regulární HDInsight "R Server" clusteru typu a již není nainstalován jako samostatné aplikace HDInsight. Hraniční uzel a binárních souborů serveru R jsou nyní zajištěna v rámci nasazení clusteru R Server. To zvyšuje rychlost a spolehlivost zřizování. Cenový model pro R Server se příslušným způsobem aktualizuje.
* R Server clusteru typu cena teď vychází z úrovně Standard cena plus R Server příplatek ceny. Tato změna nemá vliv efektivní ceny R Server; změní jenom jak poplatků prezentovány v kusovníku. Všechny existující clustery R Server pokračovat v práci a šablony Resource Manager i nadále fungovat až do vyřazení oznámení. **Doporučuje se i když k aktualizaci skriptované nasazení použít novou šablonu Resource Manager.**






