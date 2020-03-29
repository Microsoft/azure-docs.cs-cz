---
title: Vytváření funkcí pro data v clusteru Azure HDInsight Hadoop – proces vědecké analýzy týmových dat
description: Příklady dotazů Hive, které generují funkce v datech uložených v clusteru Azure HDInsight Hadoop.
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
ms.openlocfilehash: c926aac3ea4360793ff52b616a55dc6198357c8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721774"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Vytváření funkcí pro data v clusteru Hadoop pomocí dotazů Hive
Tento dokument ukazuje, jak vytvořit funkce pro data uložená v clusteru Azure HDInsight Hadoop pomocí dotazů Hive. Tyto dotazy Hive používají vložené hive uživatelem definované funkce (UDFs), skripty, pro které jsou k dispozici.

Operace potřebné k vytvoření funkcí mohou být náročné na paměť. Výkon dotazů Hive se v takových případech stává kritičtějším a lze jej zlepšit laděním určitých parametrů. Ladění těchto parametrů je popsáno v poslední části.

Příklady dotazů, které jsou prezentovány, jsou specifické pro scénáře [NYC Taxi Trip Data](https://chriswhong.com/open-data/foil_nyc_taxi/) jsou také k dispozici v [úložišti GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tyto dotazy již mají schéma dat zadané a jsou připraveny ke spuštění. V poslední části jsou také popsány parametry, které mohou uživatelé vyladit tak, aby bylo možné zlepšit výkon dotazů Hive.

Tento úkol je krokem v [procesu vědecké vědy o týmových datech (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořil účet úložiště Azure. Pokud potřebujete pokyny, přečtěte [si tématy Vytvoření účtu Azure Storage.](../../storage/common/storage-account-create.md)
* Zřídit vlastní cluster Hadoop se službou HDInsight.  Pokud potřebujete pokyny, [přečtěte si informace o přizpůsobení clusterů Azure HDInsight Hadoop pro pokročilou analýzu](customize-hadoop-cluster.md).
* Data byla nahrána do tabulek Hive v clusterech Azure HDInsight Hadoop. Pokud tomu tak není, postupujte [podle vytvořit a načíst data do tabulek Hive](move-hive-tables.md) nahrát data do tabulek Hive jako první.
* Povolený vzdálený přístup ke clusteru. Pokud potřebujete pokyny, přečtěte si [část Přístup k hlavnímu uzlu clusteru Hadoop](customize-hadoop-cluster.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Generování funkcí
V této části je popsáno několik příkladů způsobů, jakými mohou být funkce generovány pomocí dotazů Hive. Jakmile vygenerujete další funkce, můžete je buď přidat jako sloupce do existující tabulky, nebo vytvořit novou tabulku s dalšími funkcemi a primárním klíčem, které lze pak spojit s původní tabulkou. Zde jsou uvedeny příklady:

1. [Generování funkcí založených na frekvenci](#hive-frequencyfeature)
2. [Rizika kategorických proměnných v binární klasifikaci](#hive-riskfeature)
3. [Extrahování prvků z pole Datetime](#hive-datefeatures)
4. [Extrahování prvků z textového pole](#hive-textfeatures)
5. [Výpočet vzdálenosti mezi souřadnicemi GPS](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Generování funkcí založených na frekvenci
Často je užitečné vypočítat frekvence úrovní kategorické proměnné nebo frekvence určitých kombinací úrovní z více kategorických proměnných. Uživatelé mohou k výpočtu těchto frekvencí použít následující skript:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Rizika kategorických proměnných v binární klasifikaci
V binární klasifikaci musí být nečíselné kategorické proměnné převedeny na číselné funkce, pokud používané modely přebírají pouze číselné funkce. Tento převod se provádí nahrazením každé nečíselné úrovně číselným rizikem. Tato část zobrazuje některé obecné dotazy Hive, které vypočítávají rizikové hodnoty (kurzy protokolu) kategorické proměnné.

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

V tomto příkladu `smooth_param1` `smooth_param2` proměnné a jsou nastaveny na vyhlazení hodnoty rizika vypočtené z dat. Rizika mají rozsah mezi -Inf a Inf. Riziko > 0 označuje, že pravděpodobnost, že cíl se rovná 1 je větší než 0,5.

Po výpočtu tabulky rizik mohou uživatelé přiřadit hodnotám rizik k tabulce spojením s tabulkou rizik. Dotaz spojení Hive byl uveden v předchozí části.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Extrahování prvků z polí datetime
Hive je dodáván se sadou UDFpro zpracování datetime pole. V Hive je výchozí formát datetime 'yyyy-MM-dd 00:00:00' ('1970-01-01 12:21:32' například). Tato část ukazuje příklady, které extrahují den v měsíci, měsíc z pole datetime a další příklady, které převádějí řetězec datetime ve formátu jiném než ve výchozím formátu na řetězec datetime ve výchozím formátu.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Tento dotaz Hive předpokládá, že * \<pole datetime>* je ve výchozím formátu datetime.

Pokud pole datetime není ve výchozím formátu, je třeba nejprve převést časové razítko datetime na časové razítko Unixu a poté převést časové razítko Unixu na řetězec datetime, který je ve výchozím formátu. Pokud datetime je ve výchozím formátu, uživatelé mohou použít vložené datetime UDFs extrahovat funkce.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

V `'MM/dd/yyyy HH:mm:ss'`tomto dotazu * \<* pokud datumčasové pole>má vzor jako *03/26/2015 12:04:39*, * \<vzor pole datetime>'* by měl být . Chcete-li jej otestovat, mohou uživatelé

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Podkovené tabulky* v tomto dotazu je předinstalován na všech clusterech Azure HDInsight Hadoop ve výchozím nastavení při clustery jsou zřízeny.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Extrahování prvků z textových polí
Pokud má tabulka Hive textové pole, které obsahuje řetězec slov, která jsou oddělena mezerami, následující dotaz extrahuje délku řetězce a počet slov v řetězci.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Výpočet vzdáleností mezi sadami souřadnic GPS
Dotaz uvedený v této části lze použít přímo na NYC Taxi Data. Účelem tohoto dotazu je ukázat, jak použít vložené matematické funkce v Hive generovat funkce.

Pole, která se používají v tomto dotazu, jsou GPS souřadnice míst vyzvednutí a odtažení, s názvem *délka\_vyzvednutí*, *šířka vyzvednutí\_*, zeměpisná *délka dropoff\_* a *zeměpisná šířka vysazení\_*. Dotazy, které počítají přímou vzdálenost mezi souřadnicemi vyzvednutí a předání, jsou:

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

Matematické rovnice, které počítají vzdálenost mezi dvěma GPS souřadnicemi lze nalézt na <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">movité typ skripty</a> stránky, jehož autorem Je Peter Lapisu. V tomto Javascriptu `toRad()` je funkce jen *lat_or_lon*pi/180, která převádí stupně na radiány. Zde *je lat_or_lon* zeměpisná šířka nebo délka. Vzhledem k tomu, `atan2`že Hive `atan`neposkytuje `atan2` funkci , `atan` ale poskytuje funkci , je funkce implementována funkcí ve výše uvedeném dotazu Hive pomocí definice uvedené ve <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedii</a>.

![Vytvoření pracovního prostoru](./media/create-features-hive/atan2new.png)

Úplný seznam integrovaných UDv úlu najdete v sekci **Vestavěné funkce** na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki Apache Hive</a>).  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a>Pokročilá témata: Optimalizace parametrů Hive pro zlepšení rychlosti dotazu
Výchozí nastavení parametrů clusteru Hive nemusí být vhodné pro dotazy Hive a data, která dotazy zpracovávají. Tato část popisuje některé parametry, které mohou uživatelé vyladit ke zlepšení výkonu dotazů Hive. Uživatelé musí přidat dotazy optimalizace parametrů před dotazy zpracování dat.

1. **Java haldy prostor**: Pro dotazy zahrnující spojování velkých datových sad nebo zpracování dlouhých záznamů, **vyčerpání místa haldy** je jednou z běžných chyb. Této chybě se lze vyhnout nastavením parametrů *mapreduce.map.java.opts* a *mapreduce.task.io.sort.mb* na požadované hodnoty. Zde naleznete příklad:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Tento parametr přiděluje 4 GB paměti do místa haldy Jazyka Java a také umožňuje řazení efektivnější přidělením více paměti pro něj. Je vhodné hrát s těmito přidělení, pokud existují chyby selhání úlohy související s haldy prostoru.

1. **Velikost bloku DFS**: Tento parametr nastaví nejmenší jednotku dat, kterou systém souborů ukládá. Například pokud je velikost bloku DFS 128 MB, jsou všechna data o velikosti menší než 128 MB uložena v jednom bloku. Data, která je větší než 128 MB je přidělena další bloky. 
2. Výběr velikosti malého bloku způsobí velké režijní náklady v Hadoopu, protože uzel názvu musí zpracovat mnoho dalších požadavků k nalezení příslušného bloku týkajícího se souboru. Doporučené nastavení při práci s gigabajty (nebo většími) daty je:

        set dfs.block.size=128m;

2. **Optimalizace operace spojení v Hive**: Zatímco operace spojení v rámci map/reduce se obvykle odehrávají ve fázi snižování, někdy lze dosáhnout obrovských zisků plánováním spojení ve fázi mapy (také nazývané "mapjoins"). Nastavit tuto možnost:
   
       set hive.auto.convert.join=true;

3. **Určení počtu mapovačů na Hive**: Zatímco Hadoop umožňuje uživateli nastavit počet reduktorů, počet mapovačů obvykle není nastaven uživatelem. Trik, který umožňuje určitý stupeň kontroly na toto číslo je zvolit Hadoop proměnné *mapred.min.split.size* a *mapred.max.split.size* jako velikost každého úkolu mapy je určena:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Výchozí hodnota obvykle:
    
   - *mapred.min.split.size* je 0, velikost
   - *mapred.max.split.size* je **Long.MAX** a velikost 
   - *dfs.block.size* je 64 MB.

     Jak můžeme vidět, vzhledem k velikosti dat, ladění těchto parametrů "nastavením" jim umožňuje vyladit počet mapperů používaných.

4. Zde je několik dalších **pokročilejších možností** pro optimalizaci výkonu Hive. Tyto možnosti umožňují nastavit paměť přidělenou mapování a snížení úkolů a mohou být užitečné při ladění výkonu. Mějte na paměti, že *mapreduce.reduce.memory.mb* nemůže být větší než velikost fyzické paměti každého pracovního uzlu v clusteru Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

