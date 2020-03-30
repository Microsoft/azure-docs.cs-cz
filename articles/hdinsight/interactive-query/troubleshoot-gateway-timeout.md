---
title: Výjimka při spouštění dotazů z Apache Ambari Hive View v Azure HDInsight
description: Postup řešení potíží při spouštění dotazů Apache Hive prostřednictvím Apache Ambari Hive View v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895039"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Výjimka při spouštění dotazů z Apache Ambari Hive View v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při spuštění dotazu Apache Hive z Apache Ambari Hive View se občas zobrazí následující chybová zpráva:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Příčina

Časový čas brány.

Hodnota časového času brány je 2 minuty. Dotazy z Ambari Hive View `/hive2` jsou odeslány do koncového bodu prostřednictvím brány. Jakmile je dotaz úspěšně zkompilován a přijat, `queryid`server HiveServer vrátí soubor . Klienti pak zachovat dotazování na stav dotazu. Během tohoto procesu pokud HiveServer nevrátí odpověď HTTP do 2 minut, brána HDI vyvolá chybu časového doby brány 502.3 volajícímu. K chybám může dojít při odeslání dotazu ke zpracování (pravděpodobnější) a také ve volání stavu get (méně pravděpodobné). Uživatelé mohli vidět jeden z nich.

Vlákno obslužné rutiny http má být `queryid`rychlé: připravte úlohu a vraťte . Z několika důvodů však mohou být všechna vlákna obslužné rutiny zaneprázdněna, což vede k časovým časům pro nové dotazy a volání stavu get.

### <a name="responsibilities-of-the-http-handler-thread"></a>Odpovědnosti vlákna obslužné rutiny PROTOKOLU HTTP

Když klient odešle dotaz na HiveServer, provádí následující akce ve vlákně popředí:

* Analyzovat požadavek, provést sémantické ověření
* Získat zámek
* Metastore vyhledávání v případě potřeby
* Kompilace dotazu (DDL nebo DML)
* Příprava plánu dotazů
* Provedení autorizace (Spuštění všech příslušných zásad rangerů v zabezpečených clusterech)

## <a name="resolution"></a>Řešení

Některá obecná doporučení ke zlepšení situace:

* Pokud používáte metastore externího podregistru, zkontrolujte metriky DB a ujistěte se, že databáze není přetížená. Zvažte změnu měřítka databázové vrstvy metaúložiště.

* Ujistěte se, že paralelní ops je zapnuta (to umožňuje http obslužná rutina podprocesů spustit paralelně). Chcete-li hodnotu ověřit, spusťte [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) a přejděte na **stránku Hive** > **Configs** > **Advanced** > Custom**hive .** Hodnota pro `hive.server2.parallel.ops.in.session` by `true`měla být .

* Ujistěte se, že skladová položka virtuálního sena clusteru není pro zatížení příliš malá. Zvažte rozdělení práce mezi více clusterů. Další informace naleznete v [tématu Výběr typu clusteru](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Pokud ranger je nainstalován v clusteru, zkontrolujte, zda existuje příliš mnoho zásad ranger, které je třeba vyhodnotit pro každý dotaz. Vyhledejte duplicitní nebo nepotřebné zásady.

* Zkontrolujte hodnotu **velikosti haldy HiveServer2** z Ambari. Přejděte na**možnost Optimalizace****nastavení** > **konfigurace** >  **hive** > . Ujistěte se, že hodnota je větší než 10 GB. Podle potřeby upravte pro optimalizaci výkonu.

* Ujistěte se, že dotaz Hive je dobře vyladěný. Další informace najdete [v tématu Optimalizace dotazů Apache Hive v Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
