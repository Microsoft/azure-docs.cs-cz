---
title: Dodaný procesor v Apache HBA clusteru – Azure HDInsight
description: Řešení potíží s dodaným PROCESORem na serveru oblastí v clusteru Apache HBA ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: ab9aa6d7be83faa0c26951b2c45092bbcb063d79
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540206"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scénář: doložení procesoru na serveru oblasti v clusteru Apache HBA v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Proces serveru oblasti Apache HBA začíná pracovat blízko až 200% CPU, což způsobuje, že se výstrahy HBase Master procesu a clusteru nefungují při plné kapacitě.

## <a name="cause"></a>Příčina

Pokud spouštíte clustery HBA v 3.4, možná jste dosáhli potenciální chyby způsobené upgradem JDK na verzi 1.7.0 _151. Příznak uvidíme, že proces serveru oblastí se začne používat blízko až 200% CPU (aby se ověřilo, že jste tento `top` příkaz spustili. Pokud se v procesu zabírají blízko 200% CPU získat své PID a ověříte, že je proces serveru oblastí spuštěný `ps -aux | grep`

## <a name="resolution"></a>Řešení

1. Nainstalujte JDK 1,8 na všechny uzly clusteru následujícím způsobem:

    * Spusťte akci skriptu `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Nezapomeňte vybrat možnost, která se má spustit na všech uzlech.

    * Alternativně se můžete přihlásit do každého jednotlivého uzlu a spustit příkaz `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Přejít na uživatelské rozhraní Ambari – `https://<clusterdnsname>.azurehdinsight.net` .

1. Přejděte na **adaptéry HBA->konfigurace – >pokročilé – >Upřesnit** `hbase-env configs` a změňte proměnnou `JAVA_HOME` na `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Uložte změnu konfigurace.

1. [Volitelné, ale Doporučené] [Vyprázdnit všechny tabulky v clusteru](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. V uživatelském rozhraní Ambari restartujte všechny HBA služby, které vyžadují restart.

1. V závislosti na datech v clusteru může trvat několik minut až hodinu, než cluster dosáhne stabilního stavu. Způsob, jakým ověříte, že cluster dosáhne stabilního stavu, je buď zaškrtnutím HMaster uživatelského rozhraní (všechny servery oblastí, které by měly být aktivní) z Ambari (Refresh), nebo z prostředí hlavnímu uzlu Run HBA a potom spusťte příkaz status.

Chcete-li ověřit, zda byl upgrade úspěšný, zkontrolujte, zda jsou příslušné procesy HBA spouštěny pomocí vhodné verze jazyka Java – například pro kontrolu serveru v oblasti:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).