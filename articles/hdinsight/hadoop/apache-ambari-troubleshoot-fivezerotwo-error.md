---
title: Chyba uživatelského rozhraní Apache Ambari 502 ve službě Azure HDInsight
description: Chyba uživatelského rozhraní Apache Ambari 502 při pokusu o přístup ke clusteru Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 463e1cbcc397ae5112b863cbf2e509f08964f214
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546632"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scénář: Chyba rozhraní Apache Ambari UI 502 ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při pokusu o přístup k uživatelskému rozhraní Apache Ambari pro váš cluster HDInsight se zobrazí zpráva podobná této: "502-webový server obdržel neplatnou odpověď v době, kdy se jednalo o bránu nebo proxy server."

## <a name="cause"></a>Příčina

Obecně platí, že stavový kód HTTP 502 znamená, že server Ambari neběží správně na aktivním hlavnímu uzlu. Existuje několik možných hlavních příčin.

## <a name="resolution"></a>Řešení

Ve většině případů můžete pro zmírnění problému restartovat aktivní hlavnímu uzlu. Nebo pro hlavnímu uzlu vyberte větší velikost virtuálního počítače.

### <a name="ambari-server-failed-to-start"></a>Ambari Server se nepovedlo spustit.

V protokolech Ambari-Server můžete zjistit, proč se Ambari Server nepovedlo spustit. Jednou z běžných příčin je chyba kontroly konzistence databáze. Můžete to zjistit v tomto souboru protokolu: `/var/log/ambari-server/ambari-server-check-database.log` .

Pokud jste provedli jakékoli úpravy uzlu clusteru, vraťte je prosím. Vždy používejte uživatelské rozhraní Ambari k úpravě všech konfigurací spojených s Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Server Ambari, který přijímá 100% využití CPU

V některých situacích jsme viděli, že proces Ambari-Server má nepřetržitě až 100% využití procesoru. Jako zmírnění rizika můžete protokol SSH na aktivní hlavnímu uzlu a ukončit proces serveru Ambari a znovu ho spustit.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Server Ambari ukončil OOM-Killer

V některých scénářích vaše hlavnímu uzlu vyčerpá paměť a Linux OOM-Killer začne vybírat procesy, které se mají ukončit. Tuto situaci můžete ověřit tak, že vyhledáte ID procesu AmbariServer, které by se nemělo najít. Pak se podívejte na své `/var/log/syslog` a hledejte něco podobného:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Pak určete, které procesy berou v paměti, a pokuste se o další hlavní příčinu.

### <a name="other-issues-with-ambari-server"></a>Další problémy se serverem Ambari

Nejenom zřídka Server Ambari nemůže zpracovat příchozí požadavek. Další informace najdete v protokolech Ambari-Server pro případné chyby. Příkladem takového případu je chyba:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).