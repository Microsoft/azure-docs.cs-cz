---
title: Apache HBase Master se nepodařilo spustit v Azure HDInsight
description: Apache HBase Master (HMaster) se nepodařilo spustit v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887202"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) se nepodařilo spustit v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Scénář: Atomické přejmenování selhání

### <a name="issue"></a>Problém

Během procesu spouštění byly zjištěny neočekávané soubory.

### <a name="cause"></a>Příčina

Během procesu spuštění hmaster provádí mnoho kroků inicializace, včetně přesunutí dat od začátku (.tmp) složky do datové složky. HMaster se také dívá na složku protokolů wal (write-ahead" a zobrazí, pokud existují nějaké servery oblasti, které nereagují.

HMaster dělá základní seznam příkaz u wal složek. Pokud kdykoli HMaster vidí neočekávaný soubor v některé z těchto složek, vyvolá výjimku a nespustí.

### <a name="resolution"></a>Řešení

Zkontrolujte zásobník volání a pokuste se zjistit, která složka může být příčinou problému (například může to být složka WAL nebo složka TMP). Potom v Cloud Exploreru nebo pomocí příkazů HDFS zkuste najít problémový soubor. Obvykle se jedná `*-renamePending.json` o soubor. (Soubor `*-renamePending.json` je soubor deníku, který se používá k implementaci operace atomického přejmenování v ovladači WASB. Kvůli chybám v této implementaci mohou tyto soubory zůstat po selhání procesu a tak dále.) Vynucené odstranění tohoto souboru buď v Cloud Exploreru, nebo pomocí příkazů HDFS.

Někdy může být také dočasný soubor `$$$.$$$` s názvem něco jako v tomto umístění. Chcete-li zobrazit tento `ls` soubor, musíte použít příkaz HDFS. soubor v Aplikaci Cloud Explorer nevidíte. Chcete-li tento soubor odstranit, `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`použijte příkaz HDFS .

Po spuštění těchto příkazů by měl HMaster začít okamžitě.

---

## <a name="scenario-no-server-address-listed"></a>Scénář: Není uvedena žádná adresa serveru.

### <a name="issue"></a>Problém

Může se zobrazit zpráva, `hbase: meta` že tabulka není online. Spuštění `hbck` může `hbase: meta table replicaId 0 is not found on any region.` hlásit, že v protokolech HMaster `No server address listed in hbase: meta for region hbase: backup <region name>`se může zobrazit zpráva: .  

### <a name="cause"></a>Příčina

HMaster nelze inicializovat po restartování HBase.

### <a name="resolution"></a>Řešení

1. V prostředí HBase zadejte následující příkazy (podle potřeby změňte skutečné hodnoty):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Odstraňte `hbase: namespace` položku. Tato položka může být stejná chyba, `hbase: namespace` která je hlášena při skenování tabulky.

1. Restartujte aktivní hmaster z ambarského uznatého počítače HBase ve spuštěném stavu.

1. Chcete-li vyvolat všechny offline tabulky, spusťte v prostředí HBase následující příkaz:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scénář: java.io.IOException: Timedout

### <a name="issue"></a>Problém

HMaster out s fatální výjimku podobné: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud máte mnoho tabulek a oblastí, které nebyly vyprázdněny při restartování služeb HMaster. Time-out je známá vada s HMaster. Obecné úlohy spuštění clusteru může trvat dlouhou dobu. HMaster se vypne, pokud tabulka oboru názvů ještě není přiřazena. Zdlouhavé úlohy při spuštění dojít, kde existuje velké množství nevyprázdněných dat a časový čas pět minut není dostačující.

### <a name="resolution"></a>Řešení

1. Z ui Apache Ambari přejděte na **HBase** > **Configs**. Do vlastního `hbase-site.xml` souboru přidejte následující nastavení:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Restartujte požadované služby (HMaster a případně další služby HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scénář: Server frequent region se restartuje

### <a name="issue"></a>Problém

Uzly se pravidelně restartují. Z protokolů serveru oblasti se mohou zobrazit položky podobné:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Příčina

Dlouhá `regionserver` pauza JVM GC. Pauza způsobí, `regionserver` že přestane reagovat a není schopen poslat tlukot srdce na HMaster v rámci časového režimu relace zk 40s. HMaster bude `regionserver` věřit, že `regionserver` je mrtvý a přeruší a restartovat.

### <a name="resolution"></a>Řešení

Změnit časový čas relace Zookeeper, `zookeeper.session.timeout` a to `zoo.cfg` `maxSessionTimeout` nejen `hbase-site` nastavení, ale také Zookeeper nastavení je třeba změnit.

1. Přístup k uzlu Ambari, přejděte na **HBase -> Configs -> Nastavení**, v části Časové tovy, změňte hodnotu časového omezení relace Zookeeper.

1. Přístup k uzlu Ambari, přejděte na **Zookeeper -> Configs -> Custom** `zoo.cfg`, přidejte nebo změňte následující nastavení. Ujistěte se, že hodnota `zookeeper.session.timeout`je stejná jako HBase .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Restartujte požadované služby.

---

## <a name="scenario-log-splitting-failure"></a>Scénář: Selhání rozdělení protokolu

### <a name="issue"></a>Problém

HMasters se nepodařilo přijít na clusteru HBase.

### <a name="cause"></a>Příčina

Chybně nakonfigurovaná nastavení HDFS a HBase pro účet sekundárního úložiště.

### <a name="resolution"></a>Řešení

nastavit hbase.rootdir: wasb://@.blob.core.windows.net/hbase a restartovat služby na Ambari.

---

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
