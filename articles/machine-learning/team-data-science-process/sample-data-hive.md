---
title: Ukázková data v tabulkách podregistru Azure HDInsight – proces týmu pro datové vědy
description: Vypnutí ukázkových dat uložených v tabulkách Azure HDInsight s použitím dotazů na podregistry k omezení velikosti dat na více spravovatelných pro účely analýzy.
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
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321879"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Ukázková data v tabulkách Azure HDInsight Hive
Tento článek popisuje, jak pomocí dotazů na podregistru snížit data uložená v tabulkách podregistru Azure HDInsight a zmenšit tak jejich lepší správu pro účely analýzy. Pokrývá tři oblíbené metody vzorkování:

* Rovnoměrné náhodné vzorkování
* Náhodné vzorkování podle skupin
* Vzorkování Stratified

**Proč vzorkovat data?**
Pokud je datová sada, kterou plánujete analyzovat, rozsáhlá, je obvykle vhodné ji vyvzorkovat, aby se snížila na menší, ale jenom se zástupcem a více spravovatelnými velikostmi. Vypínání vzorků usnadňuje porozumění datům, průzkumům a inženýrům funkcí. Jejím úkolem v rámci vědeckého zpracování týmových dat je povolit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

Tento úkol vzorkování je krok v rámci [vědeckého zpracování týmových dat (TDSP)](./index.yml).

## <a name="how-to-submit-hive-queries"></a>Odeslání dotazů na podregistr
Dotazy na podregistr je možné odeslat z konzoly Hadoop Command-Line v hlavním uzlu clusteru Hadoop.  Přihlaste se k hlavnímu uzlu clusteru Hadoop, otevřete konzolu Hadoop Command-Line a z ní odešlete dotazy na podregistr. Pokyny k odesílání dotazů na podregistr v konzole Command-Line Hadoop najdete v tématu [odeslání dotazů na podregistr](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Rovnoměrné náhodné vzorkování
Jednotný náhodný odběr znamená, že každý řádek v datové sadě má stejnou pravděpodobnost vzorkování. Může být implementováno přidáním dalšího pole Rand () do datové sady v rámci vnitřního dotazu Select a v vnějším dotazu Select, který je podmínkou pro toto náhodné pole.

Zde je příklad dotazu:

```python
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
```

Zde `<sample rate, 0-1>` Určuje poměr záznamů, které uživatelé chtějí vzorkovat.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Náhodné vzorkování podle skupin
Při vzorkování dat kategorií můžete chtít zahrnout nebo vyloučit všechny instance pro určitou hodnotu proměnné kategorií. Tento druh vzorkování se nazývá "vzorkování podle skupin". Například pokud máte kategorií proměnnou "State" (*stav*), která má hodnoty jako NY, MA, CA, NEWARKU a PA, budete chtít záznamy z každého stavu společně bez ohledu na to, zda jsou vzorky nebo nikoli.

Tady je příklad dotazu, který ukázky seskupují:

```python
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
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Vzorkování Stratified
Náhodný odběr vzorkování je stratified s ohledem na proměnnou kategorií, když se získané vzorky nacházejí v kategorií hodnotách, které jsou přítomny ve stejném poměru, jako kdyby byly v nadřazeném souboru. Pomocí výše uvedeného příkladu Předpokládejme, že vaše data obsahují následující pozorování podle států: NEWARKU má 100 pozorování, NY má 60 pozorování a WA 300 obsahuje poznámky. Pokud zadáte rychlost vzorkování stratified, která bude 0,5, pak získaný vzorek by měl mít přibližně 50, 30 a 150 pozorování NEWARKU, NY a WA.

Zde je příklad dotazu:

```hiveql
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
```

Informace o pokročilejších metodách vzorkování, které jsou k dispozici v podregistru, najdete v tématu [vzorkování LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).