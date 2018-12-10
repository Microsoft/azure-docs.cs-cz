---
title: Vytvoření funkcí pro data v clusteru Hadoop – vědecké zpracování týmových dat
description: Příklady dotazů Hive, které generují funkce v data uložená v clusteru Azure HDInsight Hadoop.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0ade4ac054f345084cf0bc0a6dc7885329eb8b9c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141879"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Vytvoření funkcí pro data v clusteru Hadoop pomocí dotazů Hive
Tento dokument ukazuje, jak vytvoření funkcí pro data uložená v clusteru Azure HDInsight Hadoop pomocí dotazů Hive. Tyto dotazy Hive pomocí vložených Hive User-Defined funkcí (UDF), skriptů, pro které jsou k dispozici.

Operace nutné k vytvoření funkcí může být náročné na paměť. Výkonu dotazů Hive se stane větší význam v takových případech a laděním určitých parametrů se dalo zlepšit. Optimalizace pro tyto parametry jsou popsány v poslední části.

Dotazy, které jsou uvedeny příklady specifické pro [Data o jízdách taxislužby NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) scénáře jsou také uvedeny ve [úložiště GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tyto dotazy už mají zadáno schéma dat a jsou připravené k odeslání ke spuštění. V poslední části jsou také popsány parametry, které můžou uživatelé naladit tak, aby se dalo zlepšit výkon dotazů Hive.

Tato úloha je nějaký krok [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvoření účtu služby Azure storage. Pokud potřebujete pokyny, přečtěte si [vytvoření účtu služby Azure Storage](../../storage/common/storage-quickstart-create-account.md)
* Zřídit vlastní cluster Hadoop ve službě HDInsight.  Pokud potřebujete získat pokyny, přečtěte si téma [přizpůsobení clusterů Azure HDInsight pro pokročilé analýzy Hadoop](customize-hadoop-cluster.md).
* Data se odeslal do tabulek Hive v clusterech Azure HDInsight Hadoop. Pokud ne, postupujte podle [vytvoření a načtení dat do tabulek Hive](move-hive-tables.md) nejdřív odesílat data do tabulek Hive.
* Povolit vzdálený přístup ke clusteru. Pokud potřebujete získat pokyny, přečtěte si téma [přístup k hlavní uzel z clusteru Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Funkce generování
V této části jsou popsány několik příkladů, jak, ve kterém může být funkce generování pomocí dotazů Hive. Po vygenerování další funkce můžete přidat jako sloupce do existující tabulky nebo vytvořit novou tabulku s další funkce a primární klíč, který pak jde připojit k původní tabulky. Tady jsou uvedené příklady:

1. [Funkce na základě frekvence generování](#hive-frequencyfeature)
2. [Rizika zařazené do kategorií proměnným binární klasifikace](#hive-riskfeature)
3. [Extrakce funkce z pole Datum a čas](#hive-datefeatures)
4. [Extrakce funkce z textového pole](#hive-textfeatures)
5. [Vypočítá vzdálenost mezi souřadnice GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Funkce na základě frekvence generování
Často je užitečné k výpočtu frekvence úrovních kategorií proměnné nebo frekvence určité kombinace úrovní z více kategorií proměnných. Uživatele můžete použít následující skript k výpočtu těchto frekvencí:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Rizika zařazené do kategorií proměnným binární klasifikace
Binární klasifikace musí být jiné než číselné proměnné zařazené do kategorií převeden na číselné funkce při modelů používá pouze trvá číselné funkce. Tento převod se provádí nahrazení jednotlivých úrovních nečíselné číselné rizika. Tato část uvádí některé obecné dotazy Hive, které výpočet rizika hodnot (protokolu číslům umocněným na druhou) zařazené do kategorií proměnné.

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

V tomto příkladu proměnné `smooth_param1` a `smooth_param2` plynulé riziko hodnoty vypočítané z dat jsou nastavené. Rizika mít rozsahu od -Inf a Inf. Riziko > 0 označuje, že pravděpodobnost, že cíl je rovno 1 je větší než 0,5.

Po riziko tabulky počítá, uživatelé mohou přiřadit hodnoty rizika na tabulku spojením s tabulkou rizika. V předchozí části poskytla spojovacího dotaz Hive.

### <a name="hive-datefeatures"></a>Extrakce funkce z pole data a času
Hive se dodává se sadou funkcí UDF pro zpracování pole data a času. V Hive, je výchozí formát pro datum a čas "rrrr MM-dd 00:00:00" ("1970-01-01 12:21:32" například). Tato část ukazuje příklady, které extrahování dne v měsíci, měsíc od pole Datum a čas a další příklady, které převádějí řetězec data a času ve formátu jiné než výchozí formát na řetězec data a času ve výchozím formátu.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Tento dotaz Hive předpokládá, *<datetime field>* je ve výchozím formátu data a času.

Pokud není pole Datum a čas ve formátu výchozí, musíte nejprve převést pole data a času Unix časové razítko a potom převést na řetězec data a času, který je ve výchozím formátu Unix časové razítko. Když je datum a čas ve výchozím formátu, můžou uživatelé používat vložené datum a čas k extrakci funkce UDF.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

V tomto dotazu Pokud *<datetime field>* ve tvaru jako *03/26/2015 12:04:39*,  *<pattern of the datetime field>"* by měl být `'MM/dd/yyyy HH:mm:ss'`. K otestování, můžou uživatelé spouštět.

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* v tomto dotazu je předinstalovaný na všech clusterech Azure HDInsight Hadoop ve výchozím nastavení při zřizování clusterů.

### <a name="hive-textfeatures"></a>Extrakce funkce z textových polí
Jakmile tabulka Hive obsahuje textové pole, která obsahuje řetězce slov, které jsou odděleny mezer, následující dotaz extrahuje délku řetězce a počtu slov v řetězci.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Vypočítá vzdálenost mezi sadami souřadnice GPS
Dotaz zadaný v této části můžete přímo použít pro Data o jízdách NYC taxislužby. Účelem tohoto dotazu je ukazují, jak použít vložený matematické funkce v Hive ke generování funkce.

Pole, které se používají v tomto dotazu jsou souřadnice GPS sbírat míčky a dropoff umístění s názvem *vyzvednutí\_délky*, *vyzvednutí\_zeměpisná šířka*,  *dropoff\_délky*, a *dropoff\_zeměpisná šířka*. Dotazy, které s přímým přístupem vzdálenosti mezi vzájemně sbírat míčky a dropoff souřadnice jsou:

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

Matematické rovnice, které vypočítá vzdálenost mezi dvěma souřadnice GPS můžete najít na <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Přesouvatelných typ skripty</a> lokality autorem Peter Lapisu. V tomto jazyce Javascript, funkce `toRad()` je právě *lat_or_lon*pí/180 *, který převádí stupně na radiány. Tady *lat_or_lon* je zeměpisné šířky a délky. Protože Hive neposkytuje funkce `atan2`, ale poskytuje funkci `atan`, `atan2` funkce je implementovaná pomocí `atan` funkce ve výše uvedeném dotazu Hive pomocí definice součástí <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Vytvoření pracovního prostoru](./media/create-features-hive/atan2new.png)

Úplný seznam Hive UDF embedded najdete v **předdefinované funkce** části na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a> Pokročilá témata: parametry ladění Hive ke zlepšení rychlosti dotazu
Výchozí nastavení parametrů clusteru Hive nemusí být vhodný pro dotazy Hive a data, která jsou zpracování dotazů. Tato část popisuje některé parametry, které můžou uživatelé naladit ke zlepšení výkonu dotazů Hive. Uživatelé musí přidat parametr dotazy před dotazy zpracování dat ladění.

1. **Místo v haldě Java**: pro dotazy týkající se připojení k velké datové sady nebo zpracování dlouhé záznamy **nemá dostatek místa v haldě** je jedním z běžných chyb. Tato chyba se lze vyvarovat nastavením parametrů *mapreduce.map.java.opts* a *mapreduce.task.io.sort.mb* na požadované hodnoty. Zde naleznete příklad:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Tento parametr přiděluje 4GB paměti na místo v haldě Java a také umožňuje řazení zefektivnit přidělením paměti pro něj. Je vhodné pohrajte si s těmito přidělení Pokud jsou všechny úlohy chyby související s místo v haldě.

1. **Velikost bloku DFS**: Tento parametr nastavuje nejmenší jednotka dat, které jsou uloženy v systému souborů. Například pokud velikost bloku systému souborů DFS je 128 MB, pak žádná data o velikosti menší a až 128 MB uložený v jeden blok. Data, která je větší než 128 MB je vymezena další bloky. 
2. Volba velikosti malého bloku způsobí, že velká režie v systému Hadoop protože název uzlu musí zpracovat mnoho více požadavků k vyhledání příslušné bloku vztahující se k souboru. Doporučená nastavení, když se zabývají GB (nebo vyšší) data:

        set dfs.block.size=128m;

2. **Optimalizace operace spojení v podregistru**: během operace spojení v rámci mapování/zmenšování obvykle můžou probíhat ve fázi snížit, v některých případech enormní nárůst se dá dosáhnout plánování spojení ve fázi mapy (také nazývané "mapjoins"). Chcete-li přímo Hive k tomu, kdykoli je to možné, nastavte:
   
       set hive.auto.convert.join=true;

3. **Určení počtu mapovačů Hive k**: Přestože Hadoop umožňuje uživateli nastavit počet reduktorů, počet mapovačů obvykle nesmí být nastavený uživatelem. Trik, který umožňuje určitý stupeň ovládací prvek na toto číslo je zvolit Hadoop proměnné *mapred.min.split.size* a *mapred.max.split.size* jako velikost každé mapování úkolu se určuje podle:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Obvykle výchozí hodnota:
    
    - *mapred.min.Split.size* je 0, u
    - *mapred.Max.Split.size* je **Long.MAX** a 
    - *DFS.Block.size* 64 MB.

    Jak vidíme, dané velikosti dat ladění tyto parametry "nastavení" je nám umožní optimalizovat počet mapovačů použít.

4. Tady je několik dalších dalších **pokročilé možnosti** optimalizace výkonu Hive. Tyto umožňují nastavit je paměť přidělená pro mapovací a redukční úkoly a může být užitečné při úpravách výkonu. Mějte na paměti, *mapreduce.reduce.memory.mb* nemůže být větší než velikost fyzické paměti každý pracovní uzel v clusteru Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

