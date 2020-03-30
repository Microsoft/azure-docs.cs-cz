---
title: Reduktor je v Azure HDInsight pomalý
description: Reduktor je v Azure HDInsight pomalý díky možnému zkosení dat
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895168"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scénář: Reduktor je v Azure HDInsight pomalý

Tento článek popisuje kroky řešení potíží a možná řešení problémů při použití komponent interaktivního dotazu v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při spuštění dotazu, jako `insert into table1 partition(a,b) select a,b,c from table2` je například plán dotazu spustí spoustu reduktorů, ale data z každého oddílu přejde do jednoho reduktoru. To způsobí, že dotaz bude tak pomalý jako čas přijatý reduktorem největšího oddílu.

## <a name="cause"></a>Příčina

Otevřete [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) a ověřte hodnotu sady `hive.optimize.sort.dynamic.partition`.

Hodnota této proměnné má být nastavena na hodnotu true/false na základě povahy dat.

Pokud oddíly ve vstupní tabulce jsou menší (řekněme méně než 10), a tak je `true`počet výstupních oddílů a proměnná je nastavena na , to způsobí, že data globálně seřazeny a zapsány pomocí jednoho reduktoru na oddíl. I v případě, že počet reduktorů k dispozici je větší, několik reduktorů může být zaostává kvůli zkosení dat a maximální paralelismus nelze dosáhnout. Při změně `false`na , více než jeden reduktor může zpracovat jeden oddíl a více menších souborů bude zapsána, což má za následek rychlejší vložení. To může ovlivnit další dotazy i když z důvodu přítomnosti menších souborů.

Hodnota `true` má smysl, když je větší počet oddílů a data nejsou zkosená. V takových případech bude výsledek fáze mapy zapsán tak, že každý oddíl bude zpracován jedním reduktorem, což povede k lepšímu výkonu následného dotazu.

## <a name="resolution"></a>Řešení

1. Pokuste se znovu rozdělit data normalizovat do více oddílů.

1. Pokud #1 není možné, nastavte hodnotu konfigurace na hodnotu false v relaci vřádku a zkuste dotaz znovu. `set hive.optimize.sort.dynamic.partition=false`. Nastavení hodnoty na hodnotu false na úrovni clusteru se nedoporučuje. Hodnota `true` je optimální a podle potřeby nastavte parametr na základě povahy dat a dotazu.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
