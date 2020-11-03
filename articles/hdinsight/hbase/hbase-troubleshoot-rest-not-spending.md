---
title: Apache HBA nereagují na žádosti ve službě Azure HDInsight
description: Řešení potíží s Apache HBA nereaguje na žádosti ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: c4a0ef82b951fa43eb4c58050d3148fd2d695026
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286970"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scénář: Apache HBA nereaguje na žádosti ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Služba REST HBA nereaguje na požadavky v Azure HDInsight.

## <a name="cause"></a>Příčina

Možnou příčinou může být to, že služba REST HBA služby REST nevrací sokety, což je obzvláště běžné, když je služba spuštěná po dlouhou dobu (například měsíce). Z klientské sady SDK se může zobrazit chybová zpráva podobná této:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Řešení

Po SSHing k hostiteli restartujte adaptéry HBA pomocí následujícího příkazu. Můžete také použít akce skriptů k restartování této služby na všech pracovních uzlech:

```bash
sudo service hdinsight-hbrest restart
```

Tento příkaz zastaví na stejném hostiteli HBA serveru oblastí. Můžete buď ručně spustit HBA v rámci serveru oblastí prostřednictvím Ambari, nebo nechat Ambari funkce automatického restartování obnovit HBA automaticky místní server.

Pokud problém přetrvává, můžete jako úlohu CRON, která se spouští každých 5 minut na všech pracovních uzlech, nainstalovat následující skript pro zmírnění rizika. Tento skript pro zmírnění rizika otestuje službu REST a restartuje ji v případě, že služba REST nereaguje.

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]