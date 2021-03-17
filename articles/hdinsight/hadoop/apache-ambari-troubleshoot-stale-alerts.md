---
title: Zastaralé výstrahy Apache Ambari v Azure HDInsight
description: Diskuze a analýza možných důvodů a řešení pro zastaralá upozornění Apache Ambari v HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: e31e5d5a5b27f4dcb267905eae6329666fc3292c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946715"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scénář: zastaralá upozornění Apache Ambari v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

V uživatelském rozhraní Apache Ambari se může zobrazit výstraha podobná této:

![Příklad zastaralá výstraha Apache Ambari](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Příčina

Ambari agenti nepřetržitě monitorují stav mnoha prostředků. *Výstrahy* je možné nakonfigurovat tak, aby vás upozornily na to, jestli konkrétní vlastnosti clusteru jsou v rámci předem stanovených prahových hodnot. Pokud je splněna podmínka upozornění, Ambari agenti po každém spuštění kontroly prostředků stav vrátí zpět na server Ambari a aktivuje výstrahu. Pokud se výstraha nekontroluje podle intervalu v profilu výstrahy, server aktivuje výstrahu *zastaralá upozornění Ambari serveru* .

Existují různé důvody, proč není možné spustit kontrolu stavu ve stanoveném intervalu:

* Hostitelé mají těžké použití (vysoké využití procesoru), takže agent Ambari nemůže získat dostatek systémových prostředků, aby bylo možné výstrahy spustit včas.

* Cluster je zaneprázdněn prováděním mnoha úloh nebo služeb během období vysokého zatížení.

* Malý počet hostitelů v clusteru je hostitelem mnoha komponent, takže je potřeba, abyste mohli spouštět mnoho výstrah. Pokud je počet komponent velký, můžou úlohy výstrahy vyčkat na naplánované intervaly.

## <a name="resolution"></a>Řešení

Vyzkoušejte následující metody, abyste vyřešili problémy se zastaralými výstrahami Ambari.

### <a name="increase-the-alert-interval-time"></a>Zvýšit čas intervalu výstrahy

Hodnotu jednotlivého intervalu výstrah můžete zvýšit na základě doby odezvy a zatížení clusteru:

1. V uživatelském rozhraní Apache Ambari vyberte kartu **výstrahy** .
1. Vyberte název definice výstrahy, který chcete.
1. Z definice vyberte **Upravit**.
1. Zvyšte hodnotu **intervalu kontroly** a pak vyberte **Uložit**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Zvyšte čas časového intervalu výstrahy pro Ambari výstrahy serveru.

1. V uživatelském rozhraní Apache Ambari vyberte kartu **výstrahy** .
1. V rozevíracím seznamu **skupiny** vyberte **AMBARI výchozí**.
1. Vyberte výstrahu **výstrahy serveru Ambari** .
1. Z definice vyberte **Upravit**.
1. Zvyšte hodnotu **intervalu kontroly** .
1. Zvyšte hodnotu **násobitele intervalu** a pak vyberte **Save (Uložit**).

### <a name="disable-and-reenable-the-alert"></a>Zakázat a znovu povolit upozornění

Pokud chcete zahodit zastaralou výstrahu, zakažte ji a pak ji znovu povolte:

1. V uživatelském rozhraní Apache Ambari vyberte kartu **výstrahy** .
1. Vyberte název definice výstrahy, který chcete.
1. Z definice vyberte možnost **povoleno** v pravém rohu uživatelského rozhraní.
1. V automaticky otevíraném okně pro **potvrzení** vyberte **Potvrdit zakázat**.
1. Počkejte několik sekund na vymazání všech výstrah "instance" zobrazených na stránce.
1. Z definice vyberte v pravém dolním rohu uživatelského rozhraní možnost **zakázáno** .
1. V automaticky otevíraném okně pro **potvrzení** vyberte **Potvrdit povolit**.

### <a name="increase-the-alert-grace-period"></a>Prodloužit dobu odkladu výstrahy

Před tím, než agent Ambari oznámí, že nakonfigurované upozornění nenalezlo svůj plán, je období odkladu. Pokud výstraha nepřinesla svůj plánovaný čas, ale běžela v období odkladu, negeneruje se zastaralá výstraha.

Výchozí `alert_grace_period` hodnota je 5 sekund. Toto nastavení můžete nakonfigurovat v/etc/Ambari-agent/conf/ambari-agent.ini. Pro hostitele, na kterých se v pravidelných intervalech vyskytují zastaralé výstrahy, zkuste zvýšit hodnotu na 10. Pak restartujte agenta Ambari.

## <a name="next-steps"></a>Další kroky

Pokud se tady problém nezmiňuje nebo ho nemůžete vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure v [komunitní podpoře pro Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) na Twitteru. Jedná se o oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojuje komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, odešlete žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Tam si můžete vybrat nápovědu (**?**) z nabídky portál nebo otevřít podokno Nápověda a **Podpora** . Další informace najdete v tématu [Postup vytvoření žádosti o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

  Součástí předplatného Microsoft Azure je podpora správy předplatných a fakturace. Technická podpora je dostupná prostřednictvím [plánů podpory Azure](https://azure.microsoft.com/support/plans/).