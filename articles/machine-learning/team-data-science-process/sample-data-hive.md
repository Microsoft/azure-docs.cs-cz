---
title: Ukázková data v tabulkách Azure HDInsight Hive | Dokumentace Microsoftu
description: Dolů vzorkování dat v tabulkách Hivu Azure HDInsight (Hadopop)
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 793431e6e81712bae5033eff1eecddbf448ce120
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446921"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Ukázková data v tabulkách Azure HDInsight Hive
Tento článek popisuje, jak na nižší data uložená v tabulkách Azure HDInsight Hive pomocí dotazů Hive ke snížení velikosti lépe zvládnutelné pro analýzu. Ho pokrývají tři metody popularly používané vzorkování:

* Jednotné náhodných vzorkování
* Náhodný podle skupin
* Vrstveného vzorkování

**Proč ukázková data?**
Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné na nižší dat ke snížení velikosti menší, ale reprezentativní a lépe zvládnutelné. Vzorkování dolů usnadňuje pochopení dat, prozkoumávání a vytváření funkcí. Jejich rolí v vědecké zpracování týmových dat je umožnit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

Tato úloha vzorkování je krok [vědecké zpracování týmových dat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Postup odesílání dotazů Hive
Dotazy Hive můžete odeslat z konzoly příkazového řádku Hadoopu hlavního uzlu clusteru Hadoop. Chcete-li to provést, přihlaste se k hlavnímu uzlu clusteru Hadoop, otevřete konzolu příkazového řádku pro Hadoop a odesílání dotazů Hive z něj. Pokyny k odesílání dotazů Hive v konzole nástroje příkazového řádku Hadoopu, najdete v článku [způsob odesílání dotazů Hive](move-hive-tables.md#submit).

## <a name="uniform"></a> Jednotné náhodných vzorkování
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

Tady `<sample rate, 0-1>` určuje podíl záznamů, které se má zkusit uživatele.

## <a name="group"></a> Náhodný podle skupin
Při vzorkování zařazené do kategorií dat, můžete zahrnout nebo vyloučit všechny instance pro některá z hodnot proměnné zařazené do kategorií. Tento druh vzorkování se nazývá "vzorkování skupina". Například, pokud máte zařazené do kategorií proměnnou "*stavu*", který obsahuje hodnoty, jako je USA, MA, certifikační Autority, NJ a PA, mají záznamy z každého stavu bude společně, zda jsou vzorkovány nebo ne.

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

## <a name="stratified"></a>Vrstveného vzorkování
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


Informace o Pokročilé metody odběru vzorků, které jsou k dispozici v Hive, najdete v části [LanguageManual vzorkování](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

