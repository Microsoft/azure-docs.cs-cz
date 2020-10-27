---
title: Snížení úrovně zpomalení v Azure HDInsight
description: V Azure HDInsight je zpomalení z možných zešikmení dat pomalé.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 2fbe68c5dfe9b0d5b60e1e3eeabc95b201b878c6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532812"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scénář: v Azure HDInsight je zpomalení pomalé

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při spuštění dotazu, jako `insert into table1 partition(a,b) select a,b,c from table2` je například plán dotazu, se spustí svazek reduktorů, ale data z každého oddílu přecházejí do jednoho zmenšení. Tím dojde k tomu, že dotaz bude pomalý jako čas, který zmenší největší oddíl.

## <a name="cause"></a>Příčina

Otevřete [Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) a ověřte hodnotu Set `hive.optimize.sort.dynamic.partition` .

Hodnota této proměnné má být nastavena na hodnotu true nebo false na základě povaze dat.

Pokud jsou oddíly ve vstupní tabulce menší (tzn. méně než 10), a proto je počet výstupních oddílů a proměnná je nastavená na `true` , způsobí to, že se data globálně seřadí a napíší pomocí jednoho zmenšení na oddíl. I v případě, že počet dostupných reduktorů je větší, může být několik reduktorů zpožděných na začátku z důvodu zešikmení dat a maximální paralelismus nelze dosáhnout. Při změně na `false` je možné zpracovat více než jedno zmenšení jednoho oddílu a zapíše se více menších souborů, což bude mít za následek rychlejší vložení. To může mít vliv na další dotazy, i když z důvodu přítomnosti menších souborů.

Hodnota `true` dává smysl, pokud je počet oddílů větší a data nejsou zkosená. V takových případech se výsledek fáze mapy zapíše tak, že každý oddíl bude zpracován jedním snížením, což vedlo k lepšímu výkonu dotazů.

## <a name="resolution"></a>Řešení

1. Pokuste se znovu rozdělit data pro normalizaci do několika oddílů.

1. Pokud #1 není možné, nastavte hodnotu konfigurace na false v relaci Beeline a zkuste dotaz znovu. `set hive.optimize.sort.dynamic.partition=false`. Nastavení hodnoty na hodnotu false na úrovni clusteru se nedoporučuje. Hodnota `true` je optimální a v závislosti na povaze dat a dotazu nastavte parametr podle potřeby.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).