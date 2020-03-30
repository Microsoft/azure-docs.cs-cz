---
title: Apache HBase REST nereaguje na požadavky v Azure HDInsight
description: Vyřešte problém s Apache HBase REST nereaguje na požadavky v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887168"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scénář: Apache HBase REST nereaguje na požadavky v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Služba Apache HBase REST nereaguje na požadavky v Azure HDInsight.

## <a name="cause"></a>Příčina

Možnou příčinou může být služba Apache HBase REST je nevracení soketů, což je obzvláště běžné, když služba byla spuštěna po dlouhou dobu (například měsíce). Z klientské sady SDK se může zobrazit chybová zpráva podobná:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Řešení

Restartujte HBase REST pomocí níže uvedeného příkazu po sshing u hostitele. Akce skriptu můžete také použít k restartování této služby ve všech pracovních uzlech:

```bash
sudo service hdinsight-hbrest restart
```

Tento příkaz zastaví hbase region server na stejném hostiteli. Můžete buď ručně spustit HBase Region Server přes Ambari, nebo nechat Ambari automatické restartování funkce obnovit HBase Region Server automaticky.

Pokud problém přetrvává, můžete nainstalovat následující skript zmírnění jako úlohu CRON, která se spouští každých 5 minut na každém pracovním uzlu. Tento skript zmírnění ping služby REST a restartuje ji v případě, že služba REST nereaguje.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
