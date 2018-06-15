---
title: Spuštění příkladů MapReduce s Hadoop v HDInsight - Azure | Microsoft Docs
description: Začněte používat MapReduce ukázky v jar souborů zahrnutých v HDInsight. Připojte se ke clusteru pomocí SSH a pak použít příkaz Hadoop ke spuštění ukázkové úlohy.
keywords: Příklad jar hadoop, příklady jar hadoop, příklady mapreduce hadoop, mapreduce příklady
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 14f860d64c482ac7ef74512aea4850821d30132c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202301"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Spuštění příkladů MapReduce zahrnuté v HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Informace o spouštění příklady MapReduce součástí Hadoop v HDInsight.

## <a name="prerequisites"></a>Požadavky

* **Cluster služby HDInsight**: najdete v části [Začínáme pomocí Hadoop Hive v HDInsight v Linuxu](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]
    > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Klientem SSH**: Další informace najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Příklady MapReduce

**Umístění**: jsou ukázky umístěny v clusteru HDInsight na `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Obsah**: následující ukázky jsou obsaženy v archivu:

* `aggregatewordcount`: Agregace na základě mapreduce program, který udává slova ve vstupní soubory.
* `aggregatewordhist`: Agregace na základě mapreduce program, který vypočítá histogramu slova ve vstupní soubory.
* `bbp`: Mapreduce program, který používá Baileyův. Borwein Plouffe k výpočtu přesný číslic čísla pí.
* `dbcount`: Příklad úlohu, která udává Stránkové zobrazení protokolů uložených v databázi.
* `distbbp`: Mapreduce program, který používá typ BBP vzorec k výpočtu přesný bits pí.
* `grep`: Mapreduce program, který počty shod regex ve vstupu.
* `join`: Úlohu, která provede spojení přes seřadit, rovnoměrně rozděleny do oddílů datových sad.
* `multifilewc`: Úlohu, která počty slov z několika souborů.
* `pentomino`: Mapreduce dlaždice, kterým program k řešení problémů pentomino najít.
* `pi`: Mapreduce program, který odhadne pí pomocí jako Monte Carlo metoda.
* `randomtextwriter`: Mapreduce program, který zapíše 10 GB náhodné textový dat na jeden uzel.
* `randomwriter`: Mapreduce program, který zapíše 10 GB náhodná data na jeden uzel.
* `secondarysort`: Příklad definování sekundární řazení do fáze snižte.
* `sort`: Mapreduce program, který seřadí data zapsaná náhodný zápis.
* `sudoku`: Sudoku solver.
* `teragen`: Vygenerování dat pro terasort.
* `terasort`: Spusťte terasort.
* `teravalidate`: Kontrola výsledků terasort.
* `wordcount`: Mapreduce program, který udává slova ve vstupní soubory.
* `wordmean`: Mapreduce program, který udává průměrnou délku slova ve vstupní soubory.
* `wordmedian`: Mapreduce program, který udává Střední délka slova ve vstupní soubory.
* `wordstandarddeviation`: Mapreduce program, který udává směrodatnou odchylku Délka slova ve vstupní soubory.

**Zdrojový kód**: zdrojový kód pro tyto ukázky je zahrnuta v clusteru HDInsight na `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Spustit příklad wordcount

1. Připojení k HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Z `username@#######:~$` řádku, použijte následující příkaz k zobrazení seznamu ukázky:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Tento příkaz vytvoří seznam ukázka z předchozí části tohoto dokumentu.

3. Použijte následující příkaz k získání nápovědy na konkrétní vzorku. V takovém případě **wordcount** ukázka:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Zobrazí se následující zpráva:

        Usage: wordcount <in> [<in>...] <out>

    Tato zpráva znamená, můžete zadat několik cest vstupní zdroj dokumenty. Konečné cesty je, se uloží výstupní (počet slova v dokumentech zdroje).

4. Počítat všechny slova v poznámkových bloků z Leonardo Da Vinci, které jsou k dispozici jako ukázková data k vašemu clusteru použijte následující:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Zadejte pro tuto úlohu je pro čtení z `/example/data/gutenberg/davinci.txt`. Výstup pro tento příklad je uložené v `/example/data/davinciwordcount`. Obě cesty jsou umístěny na výchozí úložiště pro cluster, není místním systému souborů.

   > [!NOTE]
   > Jak jsme uvedli v nápovědě pro ukázku wordcount, můžete také zadat více vstupní soubory. Například `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` by počet slova v davinci.txt a ulysses.txt.

5. Po dokončení úlohy pro zobrazení výstupu použijte následující příkaz:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Tento příkaz připojí všechny výstupní soubory vytvořené úlohou. Zobrazuje výstup do konzoly. Výstup se bude podobat následujícímu:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Každý řádek představuje slova a kolikrát došlo v vstupní data.

## <a name="the-sudoku-example"></a>Příklad Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) je logiku stavebnice, tvoří devět 3 x 3 mřížky. Některé buněk v mřížce mít čísel, zatímco jiné jsou prázdné a cílem je řešení pro prázdné buňky. Předchozí odkaz obsahuje další informace o stavebnice, ale tato ukázka má řešení pro prázdné buňky. Naše vstup, takže by měl být soubor, který je v následujícím formátu:

* Devět řádků devět sloupců
* Každý sloupec může obsahovat buď v podobě čísla nebo `?` (což znamená prázdné buňky)
* Buňky jsou oddělené mezerou

Tento problém je možné vytvořit hádanky vám Sudoku; nelze opakovat číslo sloupce nebo řádku. Je třeba na clusteru HDInsight, který je správně strukturován. Je umístěný na adrese `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` a obsahuje následující text:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Pokud chcete spustit tento příklad problém prostřednictvím příklad Sudoku, použijte následující příkaz:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Výsledky se zobrazí podobná následující text:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Příklad platformy (pí)

Ukázka pí používá statistické (jako Monte Carlo) metoda odhadnout hodnotu čísla pí. Body jsou umístěny v náhodných v čtverce jednotky. Druhou mocninu také obsahuje kruh. Pravděpodobnost, že body spadal do kruhu jsou stejné pro oblast na kruh pí/4. Z hodnoty 4R se dá odhadnout hodnotu čísla pí. R je poměr počtu bodů, které jsou uvnitř kruhu celkového počtu bodů, které jsou v rámci druhou mocninu. Větší ukázkové bodů použít, tím lepší odhad je.

Pokud chcete tuto ukázku spustit, použijte následující příkaz. Tento příkaz používá 16 mapy s 10 000 000 ukázky odhadnout hodnotu čísla pí:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Hodnoty vrácené tento příkaz je podobná **3.14159155000000000000**. Pro odkazy jsou prvních 10 desetinných míst pí 3.1415926535.

## <a name="10-gb-greysort-example"></a>Příklad Greysort 10 GB

GraySort je řazení srovnávacího testu. Metrika je míra řazení (TB za minutu), které se dosáhne při řazení velkých objemů dat, obvykle 100 TB minimální.

Tato ukázka používá mírné 10 GB dat tak, aby mohl být program spuštěn poměrně rychle. Používá MapReduce aplikace vyvinuté tak, že Owen O'Malley a Arun Murthy. Tyto aplikace won roční srovnávacího testu řazení terabajt pro obecné účely ("daytona") v 2009, míru 0.578 TB za minutu (100 TB 173 minut). Další informace o tomto a dalších řazení srovnávacích testů najdete v tématu [Sortbenchmark](http://sortbenchmark.org/) lokality.

Tato ukázka používá tři sady MapReduce programů:

* **TeraGen**: A MapReduce program, který generuje řádky dat. Chcete-li seřadit

* **TeraSort**: ukázky vstupní data a používá MapReduce k řazení dat. do celkové pořadí

    TeraSort je standardní řazení MapReduce, s výjimkou vlastní dělicí metody. Dělicí metody používá seřazený seznam klíčů N-1 vzorků, které definují rozsah klíče pro každý snižte. Konkrétně, všechny klíče takové které ukázkové [i-1] < = klíč < ukázka [i] odešlou ke snížení i. Tato dělicí metody zaručuje, že výstupy snížit i jsou všechny menší než výstup snížit i + 1.

* **TeraValidate**: A MapReduce program, který ověří, že výstup globálně seřazen

    Vytvoří jedna mapa každý soubor v adresáři výstup a každé mapování zajišťuje, že každý klíč je menší než nebo rovna předchozí. Mapování funkce generuje záznamy první a poslední klíčů každého souboru. Snižte funkce zajišťuje, že první klíč souboru i vyšší než poslední klíč i-1 souboru. Jako výstup fázi snižte hlásit problémy s klíči, které jsou mimo pořadí.

Použijte následující kroky pro vygenerování dat, řazení a pak ověříte výstup:

1. Generovat 10 GB dat, který je uložený na výchozí úložiště clusteru HDInsight na `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` Informuje Hadoop kolik mapy úlohy použít pro tuto úlohu. Poslední dva parametry pokyn úlohu vytvoření 10 GB dat a uložit ho v `/example/data/10GB-sort-input`.

2. Použijte následující příkaz k řazení dat.:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` Informuje Hadoop, kolik snížit úlohy pro úlohu. Poslední dva parametry jsou jenom vstupní a výstupní umístění pro data.

3. Použijte následující postupy pro ověření dat generované řazení:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili ke spuštění ukázky, které jsou součástí clustery HDInsight se systémem Linux. Kurzech k používání Pig, Hive a MapReduce s HDInsight naleznete v následujících tématech:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

