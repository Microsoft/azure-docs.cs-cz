---
title: Snížení úrovně zpomalení v Azure HDInsight
description: V Azure HDInsight je zpomalení z možných zešikmení dat pomalé.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: f9b6e0d862d17badb1caa672852214cdd86abb49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930812"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]