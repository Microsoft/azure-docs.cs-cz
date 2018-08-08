---
title: Poznámky k verzi archivované pro Azure HDInsight
description: Zpráva k vydání archivované verze a verze Azure HDInsight.
services: hdinsight
editor: jasonwhowell
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: jasonh
ms.openlocfilehash: 34536316fef5f30df4ef1e3c89ddaaafed188c5d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600080"
---
# <a name="archived-release-notes-for-azure-hdinsight"></a>Poznámky k verzi archivované pro Azure HDInsight

Pro **nejnovější** distribuovatelných oprav Azure HDInsight, naleznete v tématu [poznámky k verzi systému HDInsight](hdinsight-release-notes.md).

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [článku Správa verzí HDInsight](hdinsight-component-versioning.md).

## <a name="notes-for-06272018---release-of-new-open-source-versions-adls-gen2-etc-on-hdinsight-36"></a>Poznámky k 27/06/2018 – vydání nové verze opensourcového atd. ADLS Gen2 HDInsight 3.6
Verze z června 2018 HDInsight je významné verze s velkým množstvím nových aktualizací a možnosti pro naše zákazníky. Podrobnosti najdete v tomto [příspěvku](https://azure.microsoft.com/en-us/blog/enterprises-get-deeper-insights-with-hadoop-and-spark-updates-on-azure-hdinsight/) další podrobnosti.

Následující části jsou nejdůležitější funkce obsahuje. Podrobné poznámky k verzi, známé problémy a podobně a opravy chyb najdete [poznámky k verzi pro Azure HDInsight](hdinsight-release-notes.md).

- **Aktualizovat Hadoop a další projekty open source** – kromě 1000 opravy chyb napříč 20 + open source projekty, tato aktualizace obsahuje novou verzi (2.3) Sparkem a systémem Kafka (1.0).
- **Aktualizovat R serveru 9.1 na Machine Learning Services 9.3** – v této verzi poskytujeme datovým vědcům a inženýrům s využitím nejlepší open source vylepšeno díky vylepšením inovace a snadno se operacionalizace, všechny dostupné v jejich Upřednostňovaný jazyk s rychlostí Apache Spark. Tato verze rozšiřují možnosti nabízené v R serveru pomocí přidání podpory pro Python, což vede k změnu názvu clusteru R server pro služby ML. 
- **Podpora pro Azure Data Lake Storage Gen2** – HDInsight bude podporovat verzi Preview služby Azure Data Lake Storage Gen2. Zákazníci budou moct zvolit účet ADLS Gen2 jako úložiště pro svých clusterů HDInsight v oblasti k dispozici.
- **Aktualizace balíčků zabezpečení organizace HDInsight (Preview)** – (Preview) sítě koncové body služeb virtuální podporu objektů blob v Azure Storage, ADLS Gen1, Cosmos DB a Azure DB. 

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Poznámky pro 03/20/2018 – vydání 2.2 Spark v HDInsight 3.6

- Spark 2.2.0 ve Spark Core, SQL, ML zlepšuje stabilitu a přináší strukturované streamování verze GA stav. Spark 2.2.0 je nyní dostupná v HDInsight 3.6.


## <a name="notes-for-08012017-release-of-hdinsight"></a>Poznámky k verzi 08/01/2017 v HDInsight

| Titul | Popis | Ovlivněné oblasti  | Typ clusteru  | 
| --- | --- | --- | --- | --- |
| Verze Microsoft R Server 9.1 na HDInsight |HDInsight teď podporuje zřizování clustery R serveru 9.1 na HDInsight. Další informace o vydání Microsoft R serveru 9.1, naleznete v tématu [tento blog](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Služba |R Server |
| Teď obsahuje novější verze do zásobníku Hadoop HDInsight 3.6|<ul><li>Podrobný seznam aktualizovaných verzí, naleznete v tématu [verzích komponenty systému Hadoop v HDInsight k dispozici](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>Seznam opravených v nejnovějších verzích do zásobníku Hadoop najdete v tématu [informace o opravě Apache](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Seznam rozbíjejících změn mezi HDP 2.6.1 (která je teď dostupná v HDInsight 3.6), najdete v části [ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html ](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Seznam známých problémů v HDP 2.6.1 najdete v tématu [známé problémy](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Služba |Vše |neuvedeno |
| Aktualizace pro clustery s interaktivním Hivem (Preview) |<ul><li><b>Zlepšení funkce.</b> Implementace v mezipaměti metastore, která snižuje zatížení na back-endu SQL díky ukládání do mezipaměti metadat a zvyšuje výkon pro všechny operace s metadaty.  Toto vylepšení je teď výchozí nastavení na všech clusterech Interactive Hive. Další informace najdete na adrese [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Zlepšení funkce.</b> Načítání dynamických oddílů je optimalizovaný. Další informace najdete v tématu [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Zlepšení funkce.</b> Konfigurace optimalizace pro HDInsight v Linuxu.</li><li><b>Oprava chyby.</b> `CredentialProviderFactory$getProviders` není bezpečné pro vlákna. Tato chyba je opravená. Další informace najdete na adrese [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Oprava chyby.</b> Vysoké využití procesoru s ovladačem WASB `liststatus` výsledkem je špatné ATS výkon rozhraní API. Tato chyba je opravená. Další informace najdete na adrese [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Služba |Interaktivní Hive (Preview) |
| Aktualizace pro clustery Hadoop |Templeton úlohy operace spolehlivost. Další informace najdete v tématu [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Služba |Hadoop |
| Aktualizace YARN | HDInsight teď vytvoří databázi Ambari 250 GB (bez zvýšení nákladů), což vede k lepší prostředí pro zákazníky. Tato změna by měl ATS zabránit v získání vyplněné nahoru a pravděpodobně mají lepší výkon. |Služba |Vše |
| Aktualizace Sparku | Verze sparku 2.1.1. Další informace najdete v tématu [verzi Sparku 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Služba | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 - obecnou dostupnost služby HDInsight 3.6

* Tato verze přidává Azure HDInsight verze 3.6, která je založena na HDP 2.6. Zpráva k vydání verze HDP 2.6 jsou k dispozici [tady](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) najdete další informace o verzích HDInsight [tady](hdinsight-component-versioning.md). HDInsight 3.6 je k dispozici pro následující úlohy:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * V2.1.0 interaktivní Hive

* **Podpora pro zobrazení Hive 2.0**. To by měl vylepšit uživatelské prostředí pro interaktivní Hive. Další informace najdete v tématu [Hortonworks dokumentaci](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Vylepšení výkonu pomocí Hive LLAP**. Další informace najdete v tématu [Hortonworks dokumentaci](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nové funkce v podregistru**. Zobrazit [Hortonworks dokumentaci](https://hortonworks.com/apache/hive/#section_4).

* **Zastarání rozhraní příkazového řádku Hive**: Hive rozhraní příkazového řádku je zastaralé a místo toho použít Beeline nepodnikovým zákazníkům. Další informace najdete v tématu [dokumentaci Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Pokyny týkající se použití Beeline s HDInsight naleznete v tématu [použití Beeline s HDInsight Hadoop clusterů](hadoop/apache-hadoop-use-hive-beeline.md).

* **Nové funkce v Apache Phoenix a HBase**.
    * Podpora kvótu úložiště: běžně používá v prostředích s více tenanty umožňující omezené úložný prostor na každou tabulku a na úrovni oboru názvů.
    * Phoenix indexování vylepšení: vytváření přírůstkových indexu nebo opětovné sestavení/obnovení indexování z předchozích chyb.
    * Nástroj pro integritu dat Phoenix: podporuje ověřování schéma, index a další metadata.


* **Problém s HBase**: při spuštění MapReduce sdíleného svazku clusteru hromadné načtení projektu, může dojít k chybě syntaxe.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Místo toho použijte následující syntaxi:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 – verze 2.1 Spark v HDInsight 3.6 (Preview)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) zlepšuje mnoho stability a použitelnosti problémy s předchozími verzemi. Nové funkce přináší také na všechny úlohy Spark, jako je Spark Core, SQL, ML nebo streamování.
* Strukturované streamování získá lepší škálovatelnost s podporou vodoznaky času události a Kafka 0.10 konektoru.
* Dělení Spark SQL je teď provádí pomocí nový mechanismus škálovatelné zpracování oddílu. Další podrobnosti najdete [tady](http://spark.apache.org/releases/spark-release-2-1-0.html) o tom, jak upgradovat.
* Spark 2.1 v Azure HDInsight 3.6 náhled aktuálně nepodporuje nástroje BI připojení pomocí ovladače ODBC.
* Přístup k Azure Data Lake Store z clusterů Spark 2.1 se nepodporuje v této verzi Preview.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016 – vydání 2.0.1 Spark v HDInsight 3.5
Spark 2.0.1 je nyní k dispozici v clusterech Spark (HDInsight verze 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 - verzi R serveru 9.0 na HDInsight 3.5 (Spark 2.0)
*   Clustery s R servery nyní zahrnují možnost pro dvě verze: R Server 9.0 na HDI 3.5 (Spark 2.0) a R Server 8.0 na HDI 3.4 (Spark 1.6).
*   R Server 9.0 na HDI 3.5 (Spark 2.0) je založená na R 3.3.2 a zahrnuje nové zdroje dat scaler názvem RxHiveData a RxParquetData pro načítání dat z Hive a Parquet přímo do datových rámců Spark pro analýzu podle ScaleR. Další informace najdete v tématu vložené nápovědy na tyto funkce v jazyce R pomocí **? RxHiveData** a **? RxParquetData** příkazy.
*   Přihlašovací stránce RStudio serveru community edition je nyní nainstalovaný ve výchozím nastavení (s možností výslovného nesouhlasu) v okně Konfigurace clusteru jako součástí zřizování toku.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 – verzi 2.0 Spark v HDInsight
* Clustery Spark 2.0 na HDInsight 3.5 teď podporují služby Livy a Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10/26/2016 - verzi R serveru v HDInsight
* Identifikátor URI pro přístup k uzlu edge se změnila na **clustername**-ed-ssh.azurehdinsight.net
* Je teď jednodušší a R Server v zřizování clusteru HDInsight.
* R Server v HDInsight je teď dostupná jako regulární HDInsight "R Server" typ clusteru a už není nainstalovaná jako samostatná aplikace HDInsight. Hraniční uzel a binární soubory R serveru jsou nyní zřízené jako součást nasazení clusteru R serveru. To zvyšuje rychlost a spolehlivost zřizování. Cenový model pro R Server se podle nich aktualizuje.
* Cena typ clusteru R serveru nyní podle cenové úrovně Standard plus cena příplatek za R serveru. Tato změna nemá vliv na platné ceny R serveru. změní pouze jak uvedené poplatky na faktuře. Všechny existující clustery s R servery i nadále fungovat a šablon Resource Manageru i nadále fungovat až do vyřazení. **Doporučujeme ale aktualizovat skriptované nasazení použít novou šablonu Resource Manageru.**






