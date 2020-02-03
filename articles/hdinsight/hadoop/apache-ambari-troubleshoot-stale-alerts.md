---
title: Zastaralé výstrahy Apache Ambari v Azure HDInsight
description: Diskuze a analýza možných důvodů a řešení pro zastaralé výstrahy Apache Ambari v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722808"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scénář: zastaralá upozornění Apache Ambari v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

V uživatelském rozhraní Apache Ambari se může zobrazit výstraha podobná následujícímu obrázku:

![Příklad zastaralá výstraha Apache Ambari](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Příčina

Ambari agenti nepřetržitě spouštějí kontroly stavu za účelem monitorování stavu mnoha prostředků. Každá výstraha je nakonfigurovaná tak, aby se spouštěla v předem definovaných časových intervalech. Po spuštění každého upozornění agenti Ambari oznámí stav zpět na server Ambari. V tomto okamžiku, pokud server Ambari zjistí, že některé výstrahy nebyly včas spuštěny, spustí "výstrahy serveru Ambari". Existují různé důvody, proč není možné provést kontrolu stavu ve stanoveném intervalu:

* Pokud jsou hostitelé s velkým využitím (vysoký procesor), existuje možnost, že agent Ambari nemohl získat dostatek systémových prostředků, aby se výstrahy včas spustily.

* Cluster je zaneprázdněn prováděním mnoha úloh nebo služeb během velkého zatížení.

* Několik hostitelů v clusteru může hostovat mnoho komponent, takže bude nutné, aby spouštěla mnoho výstrah. Pokud je počet komponent velký, je možné, že úlohy výstrahy můžou přijít na naplánované intervaly.

## <a name="resolution"></a>Řešení

### <a name="increase-alert-interval-time"></a>Prodloužit dobu trvání intervalu upozornění

Můžete zvolit, že se má zvýšit hodnota jednotlivého intervalu výstrahy na základě doby odezvy clusteru a jeho zatížení.

1. V uživatelském rozhraní Apache Ambari vyberte kartu **výstrahy** .
1. Vyberte požadovaný název definice výstrahy.
1. Z definice vyberte **Upravit**.
1. Upravte hodnotu **interval kontroly** podle potřeby a potom vyberte **Uložit**.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Prodloužit dobu časového intervalu upozornění pro Ambari výstrahy serveru

1. V uživatelském rozhraní Apache Ambari vyberte kartu **výstrahy** .
1. V rozevíracím seznamu **skupiny** vyberte **AMBARI výchozí**.
1. Vyberte **výstrahy serveru Ambari**výstrahy.
1. Z definice vyberte **Upravit**.
1. Upravte hodnotu **interval kontroly** podle potřeby.
1. Upravte hodnotu **násobitele intervalu** podle potřeby a pak vyberte **Uložit**.

### <a name="disable-and-enable-the-alert"></a>Zakázat a povolit výstrahu

Výstrahu můžete zakázat a pak znovu povolit, aby se zahození zastaralých výstrah vypnulo.

1. V uživatelském rozhraní Apache Ambari vyberte kartu **výstrahy** .
1. Vyberte požadovaný název definice výstrahy.
1. Z definice vyberte možnost **povoleno** umístěné na pravé straně.
1. V automaticky otevíraném okně **potvrzení** vyberte **Potvrdit zakázat**.
1. Počkejte několik sekund, než se vymaže všechny výstrahy "instance" zobrazené na stránce.
1. Z definice vyberte možnost **zakázáno** v pravém dolním rohu.
1. V automaticky otevíraném okně **potvrzení** vyberte **Potvrdit povolení**.

### <a name="increase-alert-grace-time"></a>Prodloužit dobu odkladu výstrahy

Před tím, než agent Ambari oznámí, že nakonfigurované upozornění nenalezlo svůj plán, je k dispozici čas odkladu. I v případě, že výstraha nevrátila svůj naplánovaný čas, ale aktivovala se v čase odkladu výstrahy, není aktivována zastaralá výstraha

Výchozí hodnota `alert_grace_period` je 5 sekund. Toto nastavení `alert_grace_period` lze konfigurovat v `/etc/ambari-agent/conf/ambari-agent.ini`. Pro ty hostitele, ze kterých jsou v pravidelných intervalech aktivovány zastaralé výstrahy, se pokuste zvýšit na hodnotu 10. Pak restartujte agenta Ambari

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
