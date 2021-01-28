---
title: Nepodařilo se spustit Apache HBase Master ve službě Azure HDInsight
description: Službu Apache HBase Master (HMaster) se nepodařilo spustit ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: c30077d0d8f359e93745b53755f9dae998073d4d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936896"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Službu Apache HBase Master (HMaster) se nepodařilo spustit ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Scénář: selhání při přejmenování Atomie

### <a name="issue"></a>Problém

Během procesu spuštění byly zjištěny neočekávané soubory.

### <a name="cause"></a>Příčina

Během procesu spuštění provede HMaster spoustu inicializačních kroků, včetně přesunu dat ze složky (. tmp) do složky data. HMaster také zjistí, jestli neexistují žádné nereagující servery oblastí (WAL).

HMaster provede základní příkaz seznamu ve složkách WAL. Pokud HMaster zobrazí neočekávaný soubor v některé z těchto složek, vyvolá výjimku a nespustí se.

### <a name="resolution"></a>Řešení

Zkontrolujte zásobník volání a pokuste se určit, která složka může způsobovat problém (například může se jednat o složku WAL nebo složku. tmp). Potom v Průzkumníku cloudu nebo pomocí příkazů HDFS zkuste najít soubor problému. Obvykle se jedná o `*-renamePending.json` soubor. ( `*-renamePending.json` Soubor je soubor deníku, který se používá k implementaci operace pro atomické přejmenování v ovladači WASB. Z důvodu chyb v této implementaci mohou být tyto soubory ponechány po selhání procesu a tak dále.) Vynutit – odstraňte tento soubor buď v Průzkumníku cloudu, nebo pomocí příkazů HDFS.

V tomto umístění může být někdy také dočasný soubor s názvem něco podobného `$$$.$$$` . `ls`K zobrazení tohoto souboru je nutné použít příkaz HDFS. soubor nelze v Průzkumníkovi cloudu zobrazit. K odstranění tohoto souboru použijte příkaz HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` .

Po spuštění těchto příkazů by se měl HMaster spustit hned.

---

## <a name="scenario-no-server-address-listed"></a>Scénář: není uvedená žádná adresa serveru.

### <a name="issue"></a>Problém

Může se zobrazit zpráva s oznámením, že `hbase: meta` tabulka není online. Spuštění `hbck` může hlásit, že `hbase: meta table replicaId 0 is not found on any region.` v protokolech HMaster se může zobrazit zpráva: `No server address listed in hbase: meta for region hbase: backup <region name>` .  

### <a name="cause"></a>Příčina

HMaster nebylo možné inicializovat po restartování HBA.

### <a name="resolution"></a>Řešení

1. V prostředí HBA zadejte následující příkazy (podle potřeby změňte skutečné hodnoty):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Odstraňte `hbase: namespace` položku. Tato položka může být stejná jako chyba, která je hlášena při `hbase: namespace` prohledávání tabulky.

1. Restartujte aktivní HMaster z uživatelského rozhraní Ambari a zobrazte adaptéry HBA ve spuštěném stavu.

1. V prostředí HBA spusťte následující příkaz a zobrazte tak všechny offline tabulky:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scénář: v jazyce Java. IO. IOException: vypršel časový limit

### <a name="issue"></a>Problém

HMaster vyprší s závažnou výjimkou podobnou této: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` .

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud máte spoustu tabulek a oblastí, které se při restartování služeb HMaster nevyprázdnily. Časový limit je známá vada s HMaster. Obecné úlohy při spuštění clusteru můžou trvat dlouhou dobu. HMaster se vypne, pokud ještě není přiřazená tabulka oboru názvů. Úkony po spuštění dochází tam, kde velké množství nevyprázdněných dat existuje a není dostačující časový limit pět minut.

### <a name="resolution"></a>Řešení

1. Z uživatelského rozhraní Apache Ambari přejdete do části **HBA**  >  **Konfigurace**. Do vlastního `hbase-site.xml` souboru přidejte následující nastavení:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Restartujte požadované služby (HMaster a případně jiné služby HBA).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scénář: častý restart serveru oblasti

### <a name="issue"></a>Problém

Uzly jsou pravidelně restartovány. V protokolech serveru oblastí se můžou zobrazit podobné položky:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Příčina

`regionserver`Pozastavení JVM GC. Pozastavení bude `regionserver` mít za následek nereagovat a nebude moct odeslat srdcový signál do HMaster v rámci časového limitu ZK relace 40s. HMaster se bude domnívat `regionserver` , že bude nedoručená a `regionserver` restartuje se.

### <a name="resolution"></a>Řešení

Změňte časový limit relace Zookeeper, ne pouze `hbase-site` nastavení, `zookeeper.session.timeout` ale také `zoo.cfg` `maxSessionTimeout` je třeba změnit nastavení Zookeeper.

1. Přístup k uživatelskému rozhraní Ambari, přejděte na **adaptéry HBA-> konfigurace-> nastavení**, v části časové limity změňte hodnotu časový limit relace Zookeeper.

1. Přístup k uživatelskému rozhraní Ambari, přejděte na **Zookeeper-> config-> Custom** `zoo.cfg` , přidejte nebo změňte následující nastavení. Ujistěte se, že hodnota je stejná jako HBA `zookeeper.session.timeout` .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Restartujte požadované služby.

---

## <a name="scenario-log-splitting-failure"></a>Scénář: Chyba při rozdělování protokolu

### <a name="issue"></a>Problém

HMasters se nepodařilo provést v clusteru HBA.

### <a name="cause"></a>Příčina

Nesprávně nakonfigurované nastavení HDFS a HBA pro sekundární účet úložiště

### <a name="resolution"></a>Řešení

Nastavte adaptéry HBA. RootDir: wasb://@.blob.core.windows.net/hbase a restartujte služby na Ambari.

---

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).