---
title: Nepodařilo se spustit Apache HBase Master ve službě Azure HDInsight
description: Službu Apache HBase Master (HMaster) se nepodařilo spustit ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935403"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Službu Apache HBase Master (HMaster) se nepodařilo spustit ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Scénář: Neúspěšná přejmenování pro atomovou

### <a name="issue"></a>Problém

Během procesu spuštění byly zjištěny neočekávané soubory.

### <a name="cause"></a>Příčina

Během procesu spuštění provede HMaster spoustu inicializačních kroků, včetně přesunu dat ze složky (. tmp) do složky data. HMaster také prohlíží složku WALs (zapisovat do protokolů) a zjistí, jestli nejsou žádné neaktivní servery oblastí. Ve všech těchto situacích se jedná o základní `list` příkaz v těchto složkách. Pokud v některé z těchto složek dojde kdykoli k neočekávanému souboru, vyvolá výjimku, a proto se nespustí.

### <a name="resolution"></a>Řešení

V takové situaci zkontrolujte zásobník volání, abyste viděli, která složka může způsobovat problém (například složka WALs nebo. tmp). Potom přes Průzkumníka cloudu nebo pomocí příkazů HDFS Najděte soubor problému. Souborem problému je obvykle `*-renamePending.json` soubor (soubor deníku používaný k implementaci operace atomické přejmenování v ovladači WASB). V důsledku chyb v této implementaci mohou být takové soubory ponechány v případě selhání procesu. Vynutit odstranění tohoto souboru přes Průzkumníka cloudu. Kromě toho může být v tomto umístění dočasný soubor s charakteristikou $. Soubor nejde zobrazit přes Průzkumníka cloudu a jenom pomocí příkazu HDFS `ls` . K odstranění tohoto souboru můžete `hdfs dfs -rm //\$\$\$.\$\$\$` použít příkaz HDFS.

Po odebrání souboru problému by se měl HMaster spustit okamžitě.

---

## <a name="scenario-no-server-address-listed"></a>Scénář: Není uvedená žádná adresa serveru.

### <a name="issue"></a>Problém

Protokol HMaster zobrazí chybovou zprávu podobnou "žádná adresa serveru uvedená v části hbaes: meta pro oblast XXX".

### <a name="cause"></a>Příčina

HMaster nebylo možné inicializovat po restartování HBA.

### <a name="resolution"></a>Řešení

1. V prostředí HBA spusťte následující příkazy (změňte skutečné hodnoty podle potřeby):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. Odstraňte položku HBA: Namespace, protože při kontrole adaptérů HBA: tabulka oboru názvů může být hlášena stejná chyba.

1. Restartujte aktivní HMaster z uživatelského rozhraní Ambari a zobrazte adaptéry HBA ve spuštěném stavu.

1. Spusťte následující příkaz v prostředí HBA a zobrazte všechny offline tabulky:

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scénář: Java. IO. IOException: Vypršel časový limit

### <a name="issue"></a>Problém

HMaster vyprší s závažnou výjimkou jako `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Příčina

Časový limit je známá vada s HMaster. Obecné úlohy při spuštění clusteru můžou trvat dlouhou dobu. HMaster se vypne, pokud ještě není přiřazená tabulka oboru názvů. Úkony po spuštění dochází tam, kde velké množství nevyprázdněných dat existuje a není dostačující časový limit pět minut.

### <a name="resolution"></a>Řešení

1. Přístup k uživatelskému rozhraní Ambari, přejděte do části HBA – > Konfigurace, `hbase-site.xml` v části vlastní přidejte následující nastavení:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Restartujte požadované služby (hlavně HMaster a případně jiné služby HBA).

---

## <a name="scenario-frequent-regionserver-restarts"></a>Scénář: Časté regionserver restartování

### <a name="issue"></a>Problém

Uzly jsou pravidelně restartovány. V protokolech regionserver se můžou zobrazit podobné položky:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Příčina

JVM pozastavení GC na dlouhé regionserver. Pozastavení způsobí, že regionserver přestane reagovat a nebude moct poslat srdce na HMaster v rámci časového limitu relace ZK 40s. HMaster bude přesvědčena, že regionserver je mrtvý a přeruší regionserver a restartuje se.

### <a name="resolution"></a>Řešení

Změňte časový limit relace Zookeeper, ne pouze nastavení `zookeeper.session.timeout` HBA-site-site, ale také Zookeeper nastavení `maxSessionTimeout` . cfg musí být změněno.

1. Přístup k uživatelskému rozhraní Ambari, přejděte na **adaptéry HBA-> konfigurace-> nastavení**, v části časové limity změňte hodnotu časový limit relace Zookeeper.

1. Přístup k uživatelskému rozhraní Ambari, přejděte na **Zookeeper-> config-> Custom v souboru** . cfg, přidejte nebo změňte následující nastavení. Ujistěte se, že hodnota je stejná jako HBA `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Restartujte požadované služby.

---

## <a name="scenario-log-splitting-failure"></a>Scénář: Chyba rozdělování protokolu

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

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
