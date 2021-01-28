---
title: Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.
description: Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 3301d00dce6feb00edcb70ba9edfedcce2e31ec9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929203"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení problémů souvisejících s uzly Zookeeper v clusterech Azure HDInsight.

## <a name="symptoms"></a>Příznaky

* Oba správci prostředků přejdou do úsporného režimu.
* Namenodes jsou v pohotovostním režimu.
* Spark, podregistr a přízové úlohy nebo dotazy na podregistr selžou kvůli chybám připojení Zookeeper.
* LLAP démony se nepodařilo spustit v zabezpečených clusterech Spark nebo zabezpečených interaktivních podregistrcích.

## <a name="sample-log"></a>Ukázkový protokol

V protokolech příze se může zobrazit chybová zpráva podobná následující v protokolu nitě (/var/log/Hadoop-YARN/YARN/YARN-YARN *. log on hlavních):

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Související problémy

* Služby vysoké dostupnosti, jako je příz, NameNode a Livy, se můžou z mnoha důvodů rozpínat.
* Potvrzení z protokolů, které souvisí s Zookeeper připojeními
* Ujistěte se, že se problém opakuje (nepoužívejte Tato řešení v jednom z případů).
* Úlohy mohou být dočasně neúspěšné kvůli problémům s připojením Zookeeper

## <a name="common-causes-for-zookeeper-failure"></a>Běžné příčiny selhání Zookeeper

* Vysoké využití procesoru na serverech Zookeeper
  * Pokud v uživatelském rozhraní Ambari vidíte téměř 100% využívání CPU na serverech Zookeeper, můžou relace Zookeeper otevřené během této doby vypršet a vypršení časového limitu.
* Zookeeper klienti mají časté časové limity generování sestav.
  * V protokolech pro Správce prostředků Namenode a dalších se zobrazí časté časový limit připojení klientů.
  * To může vést ke ztrátě kvora, častému převzetí služeb při selhání a dalším problémům.

## <a name="check-for-zookeeper-status"></a>Zjistit stav Zookeeper

* Vyhledání serverů Zookeeper ze souboru/etc/hosts nebo z uživatelského rozhraní Ambari
* Spusťte následující příkaz
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (nebo 2182)  
  * Port 2181 je instance Apache Zookeeper
  * Port 2182 se používá v Zookeeper HDInsight (k poskytování HA pro služby, které nejsou nativně HA).
  * Pokud příkaz nezobrazuje žádný výstup, znamená to, že servery Zookeeper nejsou spuštěné.
  * Pokud servery běží, bude výsledek zahrnovat statické připojení klientů a další statistiky.

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>Zatížení procesoru je každé hodiny špičkou.

* Přihlaste se k serveru Zookeeper a podívejte se na/etc/crontab.
* Pokud v tuto chvíli běží nějaké hodinové úlohy, náhodně se spustí v různých Zookeeper serverech.
  
## <a name="purging-old-snapshots"></a>Vyprazdňování starých snímků

* Uzly Zookeeper jsou nakonfigurované tak, aby automaticky vymazaly staré snímky.
* Ve výchozím nastavení se zachovají posledních 30 snímků.
* Počet uchovávaných snímků je řízen konfiguračním klíčem `autopurge.snapRetainCount` . Tato vlastnost je k dispozici v následujících souborech:
  * `/etc/zookeeper/conf/zoo.cfg` pro Hadoop Zookeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` pro HDInsight Zookeeper
* Nastavte `autopurge.snapRetainCount` na hodnotu 3 a restartujte Zookeeper servery.
  * Konfiguraci Hadoop Zookeeper můžete aktualizovat a službu je možné restartovat prostřednictvím Ambari.
  * Ruční zastavení a restartování HDInsight Zookeeper
    * `sudo lsof -i :2182` vám poskytne ID procesu, který se má ukončit.
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Nemazat snímky ručně – ruční odstranění snímků by mohlo způsobit ztrátu dat.

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CancelledKeyException v protokolu serveru Zookeeper nevyžaduje vyčištění snímků.

* Tato výjimka bude vidět na serverech Zookeeper (/var/log/Zookeeper/Zookeeper-Zookeeper-* nebo/var/log/HDInsight-Zookeeper/Zookeeper * soubory).
* Tato výjimka obvykle znamená, že klient již není aktivní a Server nemůže odeslat zprávu.
* Tato výjimka také indikuje, že klient Zookeeper ukončí relace předčasně.
* Vyhledejte další příznaky popsané v tomto dokumentu.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).
- Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.
- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).