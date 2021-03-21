---
title: Výjimka při spouštění dotazů ze zobrazení podregistru Apache Ambari ve službě Azure HDInsight
description: Postup řešení potíží při spouštění Apache Hive dotazů prostřednictvím zobrazení podregistru Apache Ambari ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 40ef380cd2bd4743b92daf44a0a5b70ade1cbb35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933009"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Výjimka při spouštění dotazů ze zobrazení podregistru Apache Ambari ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Když spustíte dotaz Apache Hive ze zobrazení podregistru Apache Ambari, obdržíte následující chybovou zprávu:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Příčina

Vypršel časový limit brány.

Hodnota časového limitu brány je 2 minuty. Dotazy ze zobrazení podregistru Ambari se odesílají do `/hive2` koncového bodu prostřednictvím brány. Jakmile je dotaz úspěšně zkompilován a přijat, vrátí HiveServer `queryid` . Klienti si pak pomohou dotazovat se na stav dotazu. Pokud v průběhu tohoto procesu HiveServer nevrátí odpověď HTTP do 2 minut, vyvolá brána HDI pro volající chybu vypršení časového limitu brány 502,3. K chybám může dojít při odeslání dotazu ke zpracování (pravděpodobnější) a také ve volání get status (pravděpodobně méně pravděpodobný). Uživatelé uvidí jednu z nich.

Vlákno obslužné rutiny protokolu HTTP by mělo být rychlé: Připravte úlohu a vraťte `queryid` . Z několika důvodů ale může dojít k zaneprázdnění všech vláken obslužných rutin, což má za následek vypršení časových limitů pro nové dotazy a volání get status.

### <a name="responsibilities-of-the-http-handler-thread"></a>Odpovědnosti vlákna obslužné rutiny HTTP

Když klient odešle dotaz do HiveServer, provede následující ve vlákně na popředí:

* Analyzovat požadavek, provést sémantické ověřování
* Získat zámek
* Metastore vyhledávání v případě potřeby
* Kompilovat dotaz (DDL nebo DML)
* Příprava plánu dotazů
* Provést autorizaci (spouštět všechny použitelné zásady Ranger v zabezpečených clusterech)

## <a name="resolution"></a>Řešení

Některá obecná doporučení pro zlepšení situace:

* Pokud používáte externí podregistr metastore, zkontrolujte metriky databáze a ujistěte se, že databáze není přetížená. Zvažte možnost škálování vrstvy databáze metastore.

* Ujistěte se, že je paralelní operace zapnutá (to umožňuje paralelní spouštění vláken obslužných rutin HTTP). Pokud chcete ověřit hodnotu, spusťte [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) a **přejděte k části Konfigurace podregistru**  >    >  **Upřesnit**  >  **vlastní podregistr – lokalita**. Hodnota `hive.server2.parallel.ops.in.session` by měla být `true` .

* Ujistěte se, že SKU virtuálního počítače clusteru není pro zatížení příliš malá. Zvažte rozdělení práce mezi několik clusterů. Další informace najdete v tématu [Výběr typu clusteru](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Pokud je v clusteru nainstalovaný Ranger, zkontrolujte prosím, jestli je pro každý dotaz moc velký počet Ranger zásad, které je potřeba vyhodnotit. Vyhledejte duplicitní nebo nepotřebné zásady.

* Ověřte hodnotu **velikosti haldy HiveServer2** z Ambari. Přejděte k   >    >  **optimalizaci nastavení** konfigurace podregistru  >  . Ujistěte se, že hodnota je větší než 10 GB. Upravte podle potřeby pro optimalizaci výkonu.

* Ujistěte se, že dotaz na podregistr je dobře laděný. Další informace najdete v tématu věnovaném [optimalizaci Apache Hive dotazů ve službě Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).