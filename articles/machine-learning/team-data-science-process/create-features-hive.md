---
title: Vytvoření funkce pro data v clusteru služby Hadoop pomocí dotazů Hive | Microsoft Docs
description: Příklady dotazů Hive, které generují funkce v dat uložených v clusteru Azure HDInsight Hadoop.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: a22ce30833102767c5652ddb29c8ea4b4ac83624
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Vytvoření funkce pro data v clusteru Hadoop pomocí dotazů Hive
Tento dokument ukazuje, jak vytvořit funkcí pro data uložená v clusteru Azure HDInsight Hadoop pomocí dotazů Hive. Tyto dotazy Hive pomocí vložených Hive User-Defined funkcí (UDF), skripty, pro které jsou k dispozici.

Operace, které jsou nutné k vytvoření funkce může být náročná na paměť. Výkon dotazů Hive se stane více důležitých v takových případech a lze vylepšit optimalizace určitých parametrů. Ladění z těchto parametrů je popsané v poslední části.

Příklady dotazů, které jsou uvedené jsou specifické pro [NYC taxíkem cestě Data](http://chriswhong.com/open-data/foil_nyc_taxi/) scénáře jsou také uvedeny v [úložiště GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tyto dotazy už máte zadané schéma data a jsou připravené k odeslání do spustit. V poslední části jsou také popsány parametry, které uživatelé můžete vyladit tak, aby je možné zlepšit výkon dotazů Hive.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

To **nabídky** odkazy na témata, které popisují, jak vytvořit funkce pro data v různých prostředích. Tato úloha je krok v [tým datové vědy procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořit účet úložiště Azure. Pokud budete potřebovat pokyny, najdete v části [vytvoření účtu úložiště Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Zřizuje přizpůsobené clusteru Hadoop se službou HDInsight.  Pokud budete potřebovat pokyny, najdete v části [přizpůsobit Azure HDInsight Hadoop clusterů pro pokročilé analýzy](customize-hadoop-cluster.md).
* Data byla uložena do tabulek Hive v Azure HDInsight Hadoop clusterů. Pokud ne, postupujte podle [vytvoření a načtení dat do tabulek Hive](move-hive-tables.md) nejprve nahrát data do tabulek Hive.
* Povolit pro vzdálený přístup ke clusteru. Pokud budete potřebovat pokyny, najdete v části [přístup hlavního uzlu Hadoop clusteru](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Funkce generování
V této části jsou popsány několik příkladů, způsoby, ve kterém může být funkce generování pomocí dotazů Hive. Po vygenerování můžete další funkce, můžete je přidat jako sloupce do existující tabulky nebo vytvořit novou tabulku s další funkce a primární klíč, který lze potom spojit s původní tabulky. Zde jsou uvedené příklady:

1. [Funkci na základě frekvence generování](#hive-frequencyfeature)
2. [Rizika kategorií proměnných v binární klasifikace](#hive-riskfeature)
3. [Extrahování funkce z pole data a času](#hive-datefeatures)
4. [Extrahování funkce z textové pole.](#hive-textfeatures)
5. [Vypočítat vzdálenost mezi GPS souřadnice](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Funkci na základě frekvence generování
Často je užitečné pro výpočet frekvence úrovní záznamu do kategorií proměnné nebo frekvencí určitých kombinací úrovně z několika kategorií proměnných. Uživatele můžete použít následující skript k výpočtu těchto frekvencí:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Rizika kategorií proměnných v binární klasifikace
V binární klasifikace musí být kategorií proměnné jiné než číselné převedena na číselné funkce při modely používá pouze číselné funkce. Tento převod je potřeba nahraďte každou úroveň jiné než číselné číselné riziko. Tato část uvádí některé obecné dotazy Hive, které výpočet hodnot riziko (pravděpodobnost protokolu) kategorií proměnné.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

V tomto příkladu proměnné `smooth_param1` a `smooth_param2` jsou nastaveny na funkce smooth hodnoty rizika vypočítanou ze data. Rizika mít rozsahu -Inf a Inf. Riziko > 0 označuje, že je větší než 0,5 pravděpodobnost, že cíl je rovno 1.

Po riziko se počítá tabulky uživatele lze přiřadit hodnoty rizika do tabulky pomocí připojení s tabulkou riziko. Dotaz Hive spojující zadaná v předchozí části.

### <a name="hive-datefeatures"></a>Extrahování funkce z pole data a času
Hive obsahuje sadu UDF pro zpracování pole data a času. V Hive, je výchozí formát data a času, rrrr MM-dd 00:00:00 ' (' pod hodnotou 1970-01-01 12:21:32, třeba). Tato část uvádí příklady, které extrahování dne v měsíci, měsíc z pole data a času a další příklady, které převést řetězec ve formátu data a času, jiné než výchozí formát řetězce data a času ve výchozím formátu.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Tento dotaz Hive předpokládá, že *<datetime field>* je ve výchozím formátu data a času.

Pokud je pole data a času není ve formátu výchozí, budete muset nejdřív převést pole data a času na časové razítko systému Unix a pak převést na řetězec data a času, který je ve výchozím formátu Unix časové razítko. Pokud hodnota datetime je ve výchozím formátu, uživatelé mohou nainstalovat vložená data a času k extrakci funkce UDF.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

V tomto dotazu Pokud *<datetime field>* má vzor jako *03/26/2015 12:04:39*,  *<pattern of the datetime field>'* by měla být `'MM/dd/yyyy HH:mm:ss'`. Chcete-li otestovat ji, můžou uživatelé spouštět.

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* v tomto dotazu předinstalovaný na všech clusterech Azure HDInsight Hadoop ve výchozím nastavení při zřizování clusterů.

### <a name="hive-textfeatures"></a>Extrahování funkce z textová pole
Pokud tabulka Hive obsahuje textové pole, která obsahuje řetězec slova, která jsou oddělené mezerami, následující dotaz extrahuje délka řetězce a počet slova v řetězci.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Vypočítat vzdálenosti mezi sady souřadnic, GPS
Dotaz zadaný v této části je přímo použít pro Data NYC taxíkem cesty. Účelem tohoto dotazu je ukazují, jak použít embedded matematické funkce v podregistru ke generování funkce.

Pole, které se používají v tomto dotazu jsou souřadnice GPS vyzvednutí a dropoff umístění s názvem *vyzvednutí\_zeměpisné délky*, *vyzvednutí\_zeměpisnou šířku*,  *dropoff\_zeměpisné délky*, a *dropoff\_zeměpisnou šířku*. Dotazy, které vypočítat přímé vzdálenost mezi souřadnice vyzvednutí a dropoff jsou:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Matematické vzorce, které vypočítat vzdálenost mezi dvě souřadnice GPS naleznete na <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type skripty</a> lokality, autorem Petr Lapisu. V této Javascript, funkce `toRad()` je právě *lat_or_lon*pí/180 *, která převede radiánech stupňů. Zde *lat_or_lon* zeměpisné šířky nebo délky. Vzhledem k tomu, že Hive neposkytuje funkce `atan2`, ale poskytuje funkce `atan`, `atan2` funkce je implementováno modulem `atan` funkce ve výše uvedeném dotazu Hive pomocí definice součástí <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Vytvořit pracovní prostor](./media/create-features-hive/atan2new.png)

Úplný seznam Hive embedded těchto funkcích naleznete v **integrované funkce** části na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a> Rozšířené témata: parametry Tune Hive ke zlepšení rychlost dotazu
Výchozí nastavení parametrů clusteru Hive nemusí být vhodný pro dotazy Hive a data, která jsou zpracování dotazů. Tato část popisuje některé parametry, které uživatelé mohli vyladit ke zlepšení výkonu dotazů Hive. Uživatelé musí přidat parametr ladění dotazy před dotazy zpracování data.

1. **Místo haldy Java**: pro dotazy týkající se propojení rozsáhlých datových sad, nebo zpracování dlouho záznamů, **volné místo haldy** je jednou z běžných chyb. Tato chyba se vyhnout nastavením parametry *mapreduce.map.java.opts* a *mapreduce.task.io.sort.mb* požadované hodnoty. Zde naleznete příklad:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Tento parametr přiděluje 4GB paměti do prostoru haldy Java a také umožňuje řazení zefektivnit přidělením více paměti pro ni. Je vhodné můžete experimentovat s tyto přidělení, pokud jsou všechny úlohy chyby související s haldy místa.

1. **Velikost bloku systému souborů DFS**: Tento parametr nastaví nejmenší jednotka data, která ukládá systému souborů. Jako příklad Pokud velikost bloku systému souborů DFS je 128 MB, pak žádná data o velikosti menší a až do 128 MB uložený v jeden blok. Data, která je větší než 128 MB je vymezena navíc bloky. 
2. Volba velikosti malé bloku způsobí, že velké režie v Hadoop vzhledem k tomu, že název uzlu má zpracovat mnoho více žádostí o najít relevantní bloku, která se týkají do souboru. Doporučená nastavení, když zabývají gigabajtů (nebo vyšší) data:

        set dfs.block.size=128m;

2. **Optimalizace operace spojení v podregistru**: během operace spojení v rámci mapy nebo snižte obvykle provést ve fázi snižte se v některých případech lze dosáhnout značné zvýšení plánování spojení ve fázi mapy (také nazývané "mapjoins"). Chcete-li přímé Hive k tomu, kdykoli je to možné, nastavte hodnotu:
   
       set hive.auto.convert.join=true;

3. **Určující počet mappers k Hive**: při Hadoop umožňuje uživatelům nastavit počet přechodky, počet mappers obvykle nesmí být nastavený uživatelem. Základem, který umožňuje určitý stupeň řízení na toto číslo je volba proměnné Hadoop *mapred.min.split.size* a *mapred.max.split.size* jako velikost každé mapy je dáno úloh:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Obvykle se výchozí hodnota:
    
    - *mapred.min.Split.size* 0, s
    - *mapred.Max.Split.size* je **Long.MAX** a 
    - *DFS.Block.size* 64 MB.

    Jak jsme můžete vidět, danou velikost dat ladění tyto parametry "nastavení" je umožňuje optimalizovat počet mappers použít.

4. Tady je několik dalších dalších **rozšířené možnosti** pro optimalizaci výkonu Hive. Ty vám umožní nastavit je paměť přidělená pro mapování a snížit úlohy a mohou být užitečné při postupně je upravujte výkonu. Mějte na paměti, že *mapreduce.reduce.memory.mb* nemůže být větší než velikost fyzické paměti každý pracovní uzel v clusteru Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

