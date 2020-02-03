---
title: Ukázková data v tabulkách Azure HDInsight Hive - vědecké zpracování týmových dat
description: Nižší data uložená v tabulkách Azure HDInsight Hive pomocí dotazů Hive ke snížení data do více zvládnutelných pro analýzu velikosti.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719989"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Ukázková data v tabulkách Azure HDInsight Hive
Tento článek popisuje, jak na nižší data uložená v tabulkách Azure HDInsight Hive pomocí dotazů Hive ke snížení velikosti lépe zvládnutelné pro analýzu. Pokrývá tři oblíbené metody vzorkování:

* Jednotné náhodných vzorkování
* Náhodný podle skupin
* Vrstveného vzorkování

**Proč vzorkovat data?**
Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné na nižší dat ke snížení velikosti menší, ale reprezentativní a lépe zvládnutelné. Vzorkování dolů usnadňuje pochopení dat, prozkoumávání a vytváření funkcí. Jejich rolí v vědecké zpracování týmových dat je umožnit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

Tento úkol vzorkování je krok v rámci [vědeckého zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Postup odesílání dotazů Hive
Dotazy Hive můžete odeslat z konzoly příkazového řádku Hadoopu hlavního uzlu clusteru Hadoop.  Přihlaste se k hlavnímu uzlu clusteru Hadoop, otevřete konzolu příkazového řádku Hadoop a z ní odešlete dotazy na podregistr. Pokyny k odesílání dotazů na podregistr v konzole příkazového řádku Hadoop najdete v tématu [odeslání dotazů na podregistr](move-hive-tables.md#submit).

## <a name="uniform"></a>Rovnoměrné náhodné vzorkování
Jednotné výběrová znamená, že v datové sadě každý řádek obsahuje stejnou šanci vzorkují. Je možné implementovat přidáním rand() další pole na sadu dat ve vnitřní dotaz "Vyberte" a vnější dotaz "Vyberte" tuto podmínku na náhodných pole.

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

Zde `<sample rate, 0-1>` Určuje poměr záznamů, které uživatelé chtějí vzorkovat.

## <a name="group"></a>Náhodné vzorkování podle skupin
Při vzorkování zařazené do kategorií dat, můžete zahrnout nebo vyloučit všechny instance pro některá z hodnot proměnné zařazené do kategorií. Tento druh vzorkování se nazývá "vzorkování skupina". Například pokud máte kategorií proměnnou "State" (*stav*), která má hodnoty jako NY, MA, CA, NEWARKU a PA, budete chtít záznamy z každého stavu společně bez ohledu na to, zda jsou vzorky nebo nikoli.

Tady je příklad dotazu této ukázky ve skupině:

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

## <a name="stratified"></a>Vzorkování Stratified
Náhodný vzorkování je si s ohledem na proměnnou zařazené do kategorií, pokud ukázky získat hodnoty zařazené do kategorií, které se nacházejí v poměr, jako byly v nadřazené naplnění. Jak je uvedeno výše, pokud použijeme stejný příklad předpokládejme, že data obsahují následující pozorování státy: NJ má 100 pozorování, NY má 60 připomínky a WA má 300 pozorování. Pokud chcete zadat počet vrstveného vzorkování bude 0,5, potom ukázka získali by měl mít přibližně 50, 30 a 150 pozorování NJ, USA a WA v uvedeném pořadí.

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


Informace o pokročilejších metodách vzorkování, které jsou k dispozici v podregistru, najdete v tématu [vzorkování LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

