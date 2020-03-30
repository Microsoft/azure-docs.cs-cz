---
title: Chyba ui 502 Apache Ambari v Azure HDInsight
description: Při pokusu o přístup k clusteru Azure HDInsight došlo k chybě ui rozhraní Apache Ambari 502
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895764"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scénář: Chyba ui 502 Apache Ambari v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při pokusu o přístup k uživatelskému rozhraní Apache Ambari pro váš cluster HDInsight se zobrazí zpráva podobná: "502 - Webový server obdržel neplatnou odpověď při hraní jako brána nebo proxy server."

## <a name="cause"></a>Příčina

Obecně platí, že stavový kód HTTP 502 znamená, že server Ambari nepracuje správně na aktivním headnode. Existuje několik možných příčin.

## <a name="resolution"></a>Řešení

Ve většině případů, chcete-li zmírnit problém, můžete restartovat aktivní headnode. Nebo zvolte větší velikost virtuálního počítače pro váš headnode.

### <a name="ambari-server-failed-to-start"></a>Server Ambari se nepodařilo spustit.

Můžete zkontrolovat ambari-server protokoly zjistit, proč Ambari server se nepodařilo spustit. Jedním z běžných důvodů je chyba kontroly konzistence databáze. Můžete zjistit v tomto souboru `/var/log/ambari-server/ambari-server-check-database.log`protokolu: .

Pokud jste provedli nějaké změny uzlu clusteru, obraťte je zpět. Vždy používejte ui Ambari k úpravě všech konfigurací souvisejících s Hadoopem a Sparkem.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari server s 100% využití procesoru

Ve vzácných situacích jsme viděli ambari-server proces má téměř 100% využití procesoru neustále. Jako zmírnění, můžete ssh na aktivní headnode a zabít proces serveru Ambari a spustit jej znovu.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari server zabit oom-vrah

V některých scénářích, vaše headnode vyčerpá paměť, a Linux oom-killer začne vybírat procesy zabít. Tuto situaci můžete ověřit vyhledáním ID procesu AmbariServer, které by nemělo být nalezeno. Pak se `/var/log/syslog`podívejte na vaše , a podívejte se na něco takového:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Pak určete, které procesy berou vzpomínky a pokuste se o další příčinu.

### <a name="other-issues-with-ambari-server"></a>Další problémy se serverem Ambari

Zřídka server Ambari nemůže zpracovat příchozí požadavek, můžete najít další informace při pohledu na ambari-server protokoly pro všechny chyby. Jeden takový případ je chyba, jako je tento:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
