---
title: Ukázková data v tabulkách Azure HDInsight Hive – proces vědecké analýzy týmových dat
description: Ukázková data uložená v tabulkách Azure HDInsight Hive pomocí dotazů Hive, aby se data zmenšila na velikost lépe spravovatelnou pro analýzu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df85edc3de00e2b0342bc3102fe9e85564a9835b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719989"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Ukázková data v tabulkách Azure HDInsight Hive
Tento článek popisuje, jak snížit vzorkování dat uložených v tabulkách Azure HDInsight Hive pomocí dotazů Hive, abyste je zmenšili na velikost lépe zvládnutelnou pro analýzu. Zahrnuje tři populárně používané metody odběru vzorků:

* Jednotný náhodný odběr vzorků
* Náhodný odběr vzorků podle skupin
* Stratifikovaný odběr vzorků

**Proč vzorkovat data?**
Pokud je datová sada, kterou chcete analyzovat, velká, je obvykle vhodné data snížit na menší, ale reprezentativní a lépe zvládnutelnou velikost. Vzorkování dolů usnadňuje pochopení dat, zkoumání a technické zpracování prvků. Jeho úlohou v procesu teamových dat je umožnit rychlé vytváření prototypů funkcí zpracování dat a modelů strojového učení.

Tato úloha vzorkování je krokem v [procesu vědecké vědy o týmových datech (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="how-to-submit-hive-queries"></a>Jak odeslat dotazy Hive
Dotazy hive lze odeslat z konzoly příkazového řádku Hadoop na hlavním uzlu clusteru Hadoop.  Přihlaste se do hlavního uzlu clusteru Hadoop, otevřete konzolu příkazového řádku Hadoop a odešlete odtud dotazy Hive. Pokyny k odesílání dotazů Hive v konzole příkazového řádku Hadoop naleznete v tématu [Jak odeslat dotazy hive](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a>Jednotný náhodný odběr vzorků
Jednotný náhodný odběr vzorků znamená, že každý řádek v sadě dat má stejnou šanci na odběr vzorků. Může být implementována přidáním další pole rand() do sady dat ve vnitřním "select" dotazu a ve vnějším "select" dotazu, který podmínku na toto náhodné pole.

Zde je příklad dotazu:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Zde `<sample rate, 0-1>` určuje podíl záznamů, které uživatelé chtějí vzorkovat.

## <a name="random-sampling-by-groups"></a><a name="group"></a>Náhodný odběr vzorků podle skupin
Při vzorkování kategorických dat můžete zahrnout nebo vyloučit všechny instance pro určitou hodnotu kategorické proměnné. Tento druh odběru vzorků se nazývá "vzorkování podle skupin". Máte-li například kategorickou proměnnou "*State*", která má hodnoty jako NY, MA, CA, NJ a PA, chcete, aby záznamy z každého stavu byly společně, ať už jsou vzorkovány nebo ne.

Zde je příklad dotazu, který vzorky podle skupin:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified-sampling"></a><a name="stratified"></a>Stratifikovaný odběr vzorků
Náhodný odběr vzorků je stratifikovaný s ohledem na kategorickou proměnnou, pokud získané vzorky mají kategorické hodnoty, které jsou přítomny ve stejném poměru jako v nadřazeném obyvatelstvu. Pomocí stejného příkladu jako výše, předpokládejme, že vaše data mají následující pozorování podle stavů: NJ má 100 pozorování, NY má 60 pozorování a WA má 300 pozorování. Pokud zadáte rychlost stratifikovaného odběru vzorků na 0,5, měl by získaný vzorek mít přibližně 50, 30 a 150 pozorování NJ, NY a WA.

Zde je příklad dotazu:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Informace o pokročilejších metodách vzorkování, které jsou k dispozici v Hive, naleznete v [tématu LanguageManual Sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

