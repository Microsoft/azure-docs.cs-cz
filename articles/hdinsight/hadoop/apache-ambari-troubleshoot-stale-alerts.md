---
title: Zastaralé výstrahy Apache Ambari v Azure HDInsight
description: Diskuse a analýza možných důvodů a řešení pro upozornění Apache Ambari zatuchlých v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539106"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scénář: Apache Ambari zastaralé výstrahy v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

V ui Apache Ambari se může zobrazit výstraha, jako je tato:

![Příklad výstrahy Apache Ambari stale](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Příčina

Ambari agenti neustále sledovat stav mnoha zdrojů. *Výstrahy* lze nakonfigurovat tak, aby vás upozorňovaly, zda jsou určité vlastnosti clusteru v rámci předem určených prahových hodnot. Po spuštění každé kontroly prostředků, pokud je splněna podmínka výstrahy, agenti Ambari hlásí stav zpět na server Ambari a aktivují výstrahu. Pokud výstraha není zkontrolována podle intervalu v profilu výstrah, server spustí výstrahu *ambari server stale alerts.*

Existují různé důvody, proč se kontrola stavu nemusí spustit v definovaném intervalu:

* Hostitelé jsou pod velkým použitím (vysoké využití procesoru), takže agent Ambari nemůže získat dostatek systémových prostředků pro spuštění výstrah včas.

* Cluster je zaneprázdněn prováděním mnoha úloh nebo služeb během období vysokého zatížení.

* Malý počet hostitelů v clusteru jsou hostování mnoho součástí, a proto jsou nutné spustit mnoho výstrah. Pokud je počet součástí velký, úlohy výstrah mohou zmeškat naplánované intervaly.

## <a name="resolution"></a>Řešení

Vyzkoušejte následující metody k vyřešení problémů se zastaralými výstrahami Ambari.

### <a name="increase-the-alert-interval-time"></a>Prodloužení intervalu výstrah

Můžete zvýšit hodnotu jednotlivých intervalů výstrah na základě doby odezvy a zatížení clusteru:

1. V uzu Apache Ambari vyberte kartu **Výstrahy.**
1. Vyberte požadovaný název definice výstrahy.
1. V definici vyberte **Upravit**.
1. Zvyšte hodnotu **Zkontrolovat interval** a pak vyberte **Uložit**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Prodloužení intervalu výstrah pro výstrahy serveru Ambari

1. V uzu Apache Ambari vyberte kartu **Výstrahy.**
1. V rozevíracím seznamu **Skupiny** vyberte **možnost AMBARI Default**.
1. Vyberte výstrahu **výstrahy serveru Ambari.**
1. V definici vyberte **Upravit**.
1. Zvyšte hodnotu **Interval kontroly.**
1. Zvyšte hodnotu **Násobitel intervalu** a pak vyberte **Uložit**.

### <a name="disable-and-reenable-the-alert"></a>Zakázání a opětovné povolení výstrahy

Chcete-li zahodit zatuchlou výstrahu, zakažte ji a znovu ji povolte:

1. V uzu Apache Ambari vyberte kartu **Výstrahy.**
1. Vyberte požadovaný název definice výstrahy.
1. V definici vyberte **Povoleno** v úplně pravé části ui.
1. V rozbalovacím okně **Potvrzení** vyberte **Potvrdit zakázat**.
1. Počkejte několik sekund, než budou vymazány všechny instance výstrahy zobrazené na stránce.
1. V definici vyberte **Zakázáno** v úplně pravé části ui.
1. V rozbalovacím okně **Potvrzení** vyberte **Potvrdit povolení**.

### <a name="increase-the-alert-grace-period"></a>Prodloužení výstražné lhůty

Před oznámením agenta Ambari, že nakonfigurovaná výstraha zmeškala svůj plán, je období odkladu. Pokud výstraha zmeškala naplánovaný čas, ale běžela v období odkladu, nevytvoří se zastaralá výstraha.

Výchozí `alert_grace_period` hodnota je 5 sekund. Toto nastavení můžete nakonfigurovat v souboru /etc/ambari-agent/conf/ambari-agent.ini. U hostitelů, u kterých dochází k zastaralým výstrahám v pravidelných intervalech, zkuste zvýšit hodnotu na 10. Potom restartujte agenta Ambari.

## <a name="next-steps"></a>Další kroky

Pokud zde váš problém nebyl zmíněn nebo jej nemůžete vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Odpovědi od odborníků na Azure najdete na [komunitní podpoře Azure](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s na Twitteru. Toto je oficiální účet Microsoft Azure pro zlepšení zákaznického prostředí. Propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, odešlete žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Chcete-li se tam dostat, vyberte v nabídce portálu nápovědu (**?**) nebo otevřete podokno **Nápověda + podpora.** Další informace najdete v [tématu Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). 

  Podpora pro správu předplatného a fakturaci je součástí vašeho předplatného Microsoft Azure. Technická podpora je dostupná prostřednictvím [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
