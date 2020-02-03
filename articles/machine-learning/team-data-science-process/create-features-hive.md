---
title: Vytváření funkcí pro data v Azure HDInsight Hadoop clusteru – vědecký proces týmových dat
description: Příklady dotazů Hive, které generují funkce v data uložená v clusteru Azure HDInsight Hadoop.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721774"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Vytvoření funkcí pro data v clusteru Hadoop pomocí dotazů Hive
Tento dokument ukazuje, jak vytvoření funkcí pro data uložená v clusteru Azure HDInsight Hadoop pomocí dotazů Hive. Tyto dotazy Hive pomocí vložených Hive User-Defined funkcí (UDF), skriptů, pro které jsou k dispozici.

Operace nutné k vytvoření funkcí může být náročné na paměť. Výkonu dotazů Hive se stane větší význam v takových případech a laděním určitých parametrů se dalo zlepšit. Optimalizace pro tyto parametry jsou popsány v poslední části.

Příklady dotazů, které jsou prezentované, jsou uvedené v [úložišti GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)taky v rámci scénářů [NYC taxislužby Trip data](https://chriswhong.com/open-data/foil_nyc_taxi/) . Tyto dotazy už mají zadáno schéma dat a jsou připravené k odeslání ke spuštění. V poslední části jsou také popsány parametry, které můžou uživatelé naladit tak, aby se dalo zlepšit výkon dotazů Hive.

Tento úkol je krok v rámci [vědeckého zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Předpoklady
Tento článek předpokládá, že máte:

* Vytvoření účtu služby Azure storage. Pokud potřebujete pokyny, přečtěte si téma [Vytvoření účtu Azure Storage](../../storage/common/storage-account-create.md) .
* Zřídit vlastní cluster Hadoop ve službě HDInsight.  Pokud potřebujete pokyny, přečtěte si téma [přizpůsobení Azure HDInsight Hadoop clusterů pro pokročilou analýzu](customize-hadoop-cluster.md).
* Data se odeslal do tabulek Hive v clusterech Azure HDInsight Hadoop. Pokud tomu tak není, dodržujte nejprve [tabulka vytvořit a načíst data do tabulek podregistru](move-hive-tables.md) a nahrajte data do tabulek podregistru.
* Povolit vzdálený přístup ke clusteru. Pokud potřebujete pokyny, přečtěte si téma [přístup k hlavnímu uzlu clusteru Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Generace funkcí
V této části jsou popsány několik příkladů, jak, ve kterém může být funkce generování pomocí dotazů Hive. Po vygenerování další funkce můžete přidat jako sloupce do existující tabulky nebo vytvořit novou tabulku s další funkce a primární klíč, který pak jde připojit k původní tabulky. Tady jsou uvedené příklady:

1. [Generování funkcí založených na četnosti](#hive-frequencyfeature)
2. [Rizika proměnných kategorií v binární klasifikaci](#hive-riskfeature)
3. [Extrahovat funkce z pole DateTime](#hive-datefeatures)
4. [Extrahovat funkce z textového pole](#hive-textfeatures)
5. [Vypočítat vzdálenost mezi souřadnicemi GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Generování funkcí založených na četnosti
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


### <a name="hive-riskfeature"></a>Rizika proměnných kategorií v binární klasifikaci
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

V tomto příkladu jsou proměnné `smooth_param1` a `smooth_param2` nastaveny pro vyhlazení rizikových hodnot vypočítaných z dat. Rizika mít rozsahu od -Inf a Inf. Riziko > 0 označuje, že pravděpodobnost, že cíl je rovno 1 je větší než 0,5.

Po riziko tabulky počítá, uživatelé mohou přiřadit hodnoty rizika na tabulku spojením s tabulkou rizika. V předchozí části poskytla spojovacího dotaz Hive.

### <a name="hive-datefeatures"></a>Extrahovat funkce z polí DateTime
Hive se dodává se sadou funkcí UDF pro zpracování pole data a času. V Hive, je výchozí formát pro datum a čas "rrrr MM-dd 00:00:00" ("1970-01-01 12:21:32" například). Tato část ukazuje příklady, které extrahování dne v měsíci, měsíc od pole Datum a čas a další příklady, které převádějí řetězec data a času ve formátu jiné než výchozí formát na řetězec data a času ve výchozím formátu.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Tento dotaz na podregistr předpokládá, že *\<pole datetime >* je ve výchozím formátu data a času.

Pokud není pole Datum a čas ve formátu výchozí, musíte nejprve převést pole data a času Unix časové razítko a potom převést na řetězec data a času, který je ve výchozím formátu Unix časové razítko. Když je datum a čas ve výchozím formátu, můžou uživatelé používat vložené datum a čas k extrakci funkce UDF.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Pokud v tomto dotazu má *pole\<datetime >* vzor, jako je *03/26/2015 12:04:39*, *\<ho vzorce pole datetime >* by měla být `'MM/dd/yyyy HH:mm:ss'`. K otestování, můžou uživatelé spouštět.

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* v tomto dotazu se ve výchozím nastavení při zřizování clusterů předinstaluje do všech Azure HDInsight Hadoop clusterů.

### <a name="hive-textfeatures"></a>Extrakce funkcí z textových polí
Jakmile tabulka Hive obsahuje textové pole, která obsahuje řetězce slov, které jsou odděleny mezer, následující dotaz extrahuje délku řetězce a počtu slov v řetězci.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Vypočítat vzdálenosti mezi sadami souřadnic GPS
Dotaz zadaný v této části můžete přímo použít pro Data o jízdách NYC taxislužby. Účelem tohoto dotazu je ukazují, jak použít vložený matematické funkce v Hive ke generování funkce.

Pole, která se používají v tomto dotazu, jsou souřadnicemi GPS vyzvednutí a dropoff, pojmenované *pickup\_Zeměpisná délka*, *vyzvednutí\_Zeměpisná šířka*, *dropoff\_zeměpisná délka*a *dropoff\_Zeměpisná šířka*. Dotazy, které s přímým přístupem vzdálenosti mezi vzájemně sbírat míčky a dropoff souřadnice jsou:

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

Matematické rovnice, které vypočítávají vzdálenost mezi dvěma souřadnicemi GPS, najdete na webu <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">typu Pohyblivý text</a> , který vytvořil Petr lapisu. V tomto JavaScriptu je `toRad()` funkce pouze *lat_or_lon*PI/180, která převede stupně na radiány. Zde je *lat_or_lon* Zeměpisná šířka a délka. Vzhledem k tomu, že podregistr neposkytuje funkci `atan2`, ale poskytuje `atan`funkcí, je funkce `atan2` implementovaná `atan` funkcí ve výše uvedeném dotazu na podregistr pomocí definice poskytnuté v <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedii</a>.

![Vytvoření pracovního prostoru](./media/create-features-hive/atan2new.png)

Úplný seznam integrovaných UDF podregistru najdete v části **předdefinované funkce** na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wikiwebu Apache Hive</a>.  

## <a name="tuning"></a>Pokročilá témata: ladění parametrů podregistru za účelem zlepšení rychlosti dotazů
Výchozí nastavení parametrů clusteru Hive nemusí být vhodný pro dotazy Hive a data, která jsou zpracování dotazů. Tato část popisuje některé parametry, které můžou uživatelé naladit ke zlepšení výkonu dotazů Hive. Uživatelé musí přidat parametr dotazy před dotazy zpracování dat ladění.

1. **Místo haldy jazyka Java**: u dotazů, které zahrnují spojování velkých datových sad nebo zpracování dlouhých záznamů, je **nedostatek prostoru haldy** jednou ze běžných chyb. Tato chyba se může vyhnout nastavením parametrů *MapReduce. map. Java. výslovný* a *MapReduce. Task. IO. Sort. MB* na požadované hodnoty. Zde naleznete příklad:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Tento parametr přiděluje 4 GB paměti na místo v haldě Java a také umožňuje řazení efektivněji tím, že pro něj přiděluje více paměti. Je vhodné pohrajte si s těmito přidělení Pokud jsou všechny úlohy chyby související s místo v haldě.

1. **Velikost bloku DFS**: Tento parametr nastavuje nejmenší jednotku dat uložených v systému souborů. Například pokud velikost bloku systému souborů DFS je 128 MB, pak žádná data o velikosti menší a až 128 MB uložený v jeden blok. Data, která je větší než 128 MB je vymezena další bloky. 
2. Volba velikosti malého bloku způsobí, že velká režie v systému Hadoop protože název uzlu musí zpracovat mnoho více požadavků k vyhledání příslušné bloku vztahující se k souboru. Doporučená nastavení, když se zabývají GB (nebo vyšší) data:

        set dfs.block.size=128m;

2. **Optimalizace operace JOIN v podregistru**: zatímco operace JOIN v rozhraní map nebo zmenšení obvykle probíhají ve fázi zmenšení, někdy je možné dosáhnout mimořádných zisků pomocí plánování spojení ve fázi mapy (označované také jako "mapjoins"). Nastavte tuto možnost:
   
       set hive.auto.convert.join=true;

3. **Určení počtu mapovačů k podregistru**: když Hadoop umožňuje uživateli nastavit počet reduktorů, není obvykle počet mapovačů nastaven uživatelem. Zdvih, který umožňuje určitou míru řízení na tomto čísle, je zvolit proměnné Hadoop *mapred. min. Split. Size* a *mapred. max. Split. Size* , protože velikost jednotlivých mapových úloh je určena:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Obvykle výchozí hodnota:
    
   - *mapred. min. Split. Size* je 0, tj.
   - *mapred. max. Split. Size* je **Long. Max** a to pro 
   - *DFS. Block. Size* je 64 MB.

     Jak vidíme, dané velikosti dat ladění tyto parametry "nastavení" je nám umožní optimalizovat počet mapovačů použít.

4. Tady je několik dalších pokročilejších **možností** pro optimalizaci výkonu podregistru. Tyto možnosti umožňují nastavit paměť přidělenou pro mapování a omezení úkolů a můžou být užitečné při vylepšit výkon. Pamatujte, že *MapReduce. zmenšení. Memory. MB* nemůže být větší než velikost fyzické paměti pro každý pracovní uzel v clusteru Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

