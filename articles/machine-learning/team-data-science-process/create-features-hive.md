---
title: Vytváření funkcí pro data v Azure HDInsight Hadoop clusteru – vědecký proces týmových dat
description: Příklady dotazů na podregistr, které generují funkce v datech uložených v clusteru Azure HDInsight Hadoop.
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
ms.openlocfilehash: 30c0a02c2cbc11002f8e0bf0295dab91de5d0365
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323666"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Vytváření funkcí pro data v clusteru Hadoop pomocí dotazů na podregistry
V tomto dokumentu se dozvíte, jak vytvářet funkce pro data uložená v clusteru Azure HDInsight Hadoop pomocí dotazů na podregistry. Tyto dotazy na podregistr používají vložené funkce User-Defined podregistru (UDF), které jsou k dispozici.

Operace potřebné k vytvoření funkcí můžou být náročné na paměť. Výkon dotazů na podregistr v takových případech bude v takovém případě méně důležitý a lze ho zlepšit optimalizací určitých parametrů. Ladění těchto parametrů je popsáno v poslední části.

Příklady dotazů, které jsou prezentované, jsou uvedené v [úložišti GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)taky v rámci scénářů [NYC taxislužby Trip data](https://chriswhong.com/open-data/foil_nyc_taxi/) . Tyto dotazy již mají zadané schéma dat a jsou připraveny k odeslání ke spuštění. V poslední části jsou popsány také parametry, které mohou uživatelé ladit, aby bylo možné zlepšit výkon dotazů na podregistry.

Tento úkol je krok v rámci [vědeckého zpracování týmových dat (TDSP)](./index.yml).

## <a name="prerequisites"></a>Předpoklady
V tomto článku se předpokládá, že máte následující:

* Vytvořili jste účet úložiště Azure. Pokud potřebujete pokyny, přečtěte si téma [Vytvoření účtu Azure Storage](../../storage/common/storage-account-create.md) .
* Byl zřízen přizpůsobený cluster Hadoop se službou HDInsight.  Pokud potřebujete pokyny, přečtěte si téma [přizpůsobení Azure HDInsight Hadoop clusterů pro pokročilou analýzu](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Data byla nahrána do tabulek podregistru v clusterech Azure HDInsight Hadoop. Pokud tomu tak není, dodržujte nejprve [tabulka vytvořit a načíst data do tabulek podregistru](move-hive-tables.md) a nahrajte data do tabulek podregistru.
* Povolen vzdálený přístup ke clusteru. Pokud potřebujete pokyny, přečtěte si téma [přístup k hlavnímu uzlu clusteru Hadoop](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Generace funkcí
V této části jsou popsány několik příkladů způsobů, jak lze vygenerovat funkce pomocí dotazů na podregistr. Jakmile vygenerujete další funkce, můžete je buď přidat jako sloupce do existující tabulky, nebo vytvořit novou tabulku s dalšími funkcemi a primárními klíči, které pak můžete propojit s původní tabulkou. Zde jsou uvedeny příklady:

1. [Generování funkcí založených na četnosti](#hive-frequencyfeature)
2. [Rizika proměnných kategorií v binární klasifikaci](#hive-riskfeature)
3. [Extrahovat funkce z pole DateTime](#hive-datefeatures)
4. [Extrahovat funkce z textového pole](#hive-textfeatures)
5. [Vypočítat vzdálenost mezi souřadnicemi GPS](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Generování funkcí založených na četnosti
Často je vhodné vypočítat frekvence úrovní kategorií proměnné nebo frekvence určitých kombinací úrovní od více proměnných kategorií. Uživatelé mohou pomocí následujícího skriptu vypočítat tyto frekvence:

```hiveql
select
    a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
from
(
    select
        <column_name1>,<column_name2>, count(*) as sub_count
    from <databasename>.<tablename> group by <column_name1>, <column_name2>
)a
order by frequency desc;
```


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Rizika proměnných kategorií v binární klasifikaci
V binární klasifikaci musí být nečíselné proměnné kategorií převedeny na číselné funkce, pokud používané modely berou pouze numerické funkce. Tento převod se provádí nahrazením každé jiné než číselné úrovně číslem rizika. Tato část obsahuje některé obecné dotazy na podregistry, které počítají hodnoty rizik (log lichá) proměnné kategorií.

```hiveql
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
```

V tomto příkladu jsou proměnné `smooth_param1` a `smooth_param2` nastavené na vyhlazení rizikových hodnot vypočítaných z dat. Rizika mají rozsah mezi-inf a inf. Riziková > 0 značí, že pravděpodobnost, že je cíl roven 1, je větší než 0,5.

Po vypočítání tabulky rizik můžou uživatelé k tabulce přiřadit rizikové hodnoty tím, že se spojí s tabulkou rizik. Dotaz, který se připojuje k podregistru, byl uveden v předchozí části.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Extrahovat funkce z polí DateTime
Podregistr se dodává se sadou UDF pro zpracování polí DateTime. V podregistru je výchozí formát data a času "yyyy-MM-DD 00:00:00" (například "1970-01-01 12:21:32"). V této části jsou uvedeny příklady, které extrahují den v měsíci, měsíc z pole DateTime a další příklady, které převádějí řetězec DateTime v jiném formátu než výchozí formát na řetězec DateTime ve výchozím formátu.

```hiveql
select day(<datetime field>), month(<datetime field>)
from <databasename>.<tablename>;
```

Tento dotaz na podregistr předpokládá, že *\<datetime field>* je ve výchozím formátu data a času.

Pokud pole DateTime není ve výchozím formátu, je třeba nejprve převést pole DateTime na časové razítko systému UNIX a pak převést časové razítko systému UNIX na řetězec DateTime, který je ve výchozím formátu. Pokud je hodnota DateTime ve výchozím formátu, uživatelé mohou použít vložené hodnoty DateTime UDF k extrakci funkcí.

```hiveql
select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
from <databasename>.<tablename>;
```

V tomto dotazu, pokud *\<datetime field>* má vzorec jako *03/26/2015 12:04:39* , by měl být *\<pattern of the datetime field>* `'MM/dd/yyyy HH:mm:ss'` . K otestování můžou uživatelé spustit

```hiveql
select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
from hivesampletable limit 1;
```

*Hivesampletable* v tomto dotazu se ve výchozím nastavení při zřizování clusterů předinstaluje do všech Azure HDInsight Hadoop clusterů.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Extrakce funkcí z textových polí
Pokud má tabulka podregistru textové pole obsahující řetězec slov, který je oddělen mezerami, následující dotaz vyextrahuje délku řetězce a počet slov v řetězci.

```hiveql
select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
from <databasename>.<tablename>;
```

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Vypočítat vzdálenosti mezi sadami souřadnic GPS
Dotaz uvedený v této části se dá přímo použít pro data NYC taxislužby na cestách. Účelem tohoto dotazu je Ukázat, jak použít vloženou matematickou funkci v podregistru pro generování funkcí.

Pole, která se používají v tomto dotazu, jsou souřadnicemi GPS pro vyzvednutí a dropoff, s názvem *výstupní \_ Zeměpisná délka* , *vyzvednutí \_ zeměpisné šířky* , *dropoff \_ Zeměpisná délka* a *dropoff \_ Zeměpisná šířka*. Dotazy, které počítají přímou vzdálenost mezi vyzvednutím a dropoff souřadnicemi, jsou:

```hiveql
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
```

Matematické rovnice, které vypočítávají vzdálenost mezi dvěma souřadnicemi GPS, najdete na webu <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">typu Pohyblivý text</a> , který vytvořil Petr lapisu. V tomto JavaScriptu `toRad()` je funkce jenom *lat_or_lon* PI/180, která převede stupně na radiány. Zde je *lat_or_lon* Zeměpisná šířka a délka. Vzhledem k tomu, že podregistr funkci neposkytuje `atan2` , ale poskytuje funkci `atan` , `atan2` je funkce implementovaná `atan` funkcí ve výše uvedeném dotazu na podregistr pomocí definice poskytované v <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedii</a>.

![Vytvoření pracovního prostoru](./media/create-features-hive/atan2new.png)

Úplný seznam integrovaných UDF podregistru najdete v části **předdefinované funkce** na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wikiwebu Apache Hive</a>.  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a> Pokročilá témata: ladění parametrů podregistru za účelem zlepšení rychlosti dotazů
Výchozí nastavení parametrů clusteru podregistru nemusí být vhodné pro dotazy na podregistr a data, která jsou zpracovávána dotazy. V této části jsou popsány některé parametry, které mohou uživatelé ladit pro zlepšení výkonu dotazů na podregistry. Uživatelé musí před dotazy na zpracování dat přidat dotazy ladění parametrů.

1. **Místo haldy jazyka Java** : u dotazů, které zahrnují spojování velkých datových sad nebo zpracování dlouhých záznamů, je **nedostatek prostoru haldy** jednou ze běžných chyb. Tato chyba se může vyhnout nastavením parametrů *MapReduce. map. Java. výslovný* a *MapReduce. Task. IO. Sort. MB* na požadované hodnoty. Tady je příklad:
   
    ```hiveql
    set mapreduce.map.java.opts=-Xmx4096m;
    set mapreduce.task.io.sort.mb=-Xmx1024m;
    ```

    Tento parametr přiděluje 4 GB paměti na místo v haldě Java a také umožňuje řazení efektivněji tím, že pro něj přiděluje více paměti. Pokud dojde k chybám při selhání úloh souvisejících s místem v haldě, je vhodné se s těmito alokacemi pohrát.

1. **Velikost bloku DFS** : Tento parametr nastavuje nejmenší jednotku dat uložených v systému souborů. Pokud je například velikost bloku DFS 128 MB, uloží se v jednom bloku data o velikosti menší než a až 128 MB. Pro data, která jsou větší než 128 MB, se přiděluje další bloky. 
2. Volba velikosti malého bloku způsobí velké režijní náklady v Hadoop, protože uzel Name musí zpracovat mnoho dalších požadavků, aby bylo možné najít příslušný blok, který se vztahuje k souboru. Doporučené nastavení při práci s gigabajty (nebo většími) daty:

    ```hiveql
    set dfs.block.size=128m;
    ```

2. **Optimalizace operace JOIN v podregistru** : zatímco operace JOIN v rozhraní map nebo zmenšení obvykle probíhají ve fázi zmenšení, někdy je možné dosáhnout mimořádných zisků pomocí plánování spojení ve fázi mapy (označované také jako "mapjoins"). Nastavte tuto možnost:
   
    ```hiveql
    set hive.auto.convert.join=true;
    ```

3. **Určení počtu mapovačů k podregistru** : když Hadoop umožňuje uživateli nastavit počet reduktorů, není obvykle počet mapovačů nastaven uživatelem. Zdvih, který umožňuje určitou míru řízení na tomto čísle, je zvolit proměnné Hadoop *mapred. min. Split. Size* a *mapred. max. Split. Size* , protože velikost jednotlivých mapových úloh je určena:
   
    ```hiveql
    num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
    ```
   
    Obvykle výchozí hodnota:
    
   - *mapred. min. Split. Size* je 0, tj.
   - *mapred. max. Split. Size* je **Long. Max** a to pro 
   - *DFS. Block. Size* je 64 MB.

     Jak vidíte, s ohledem na velikost dat, vyladění těchto parametrů pomocí "nastavení" nám umožňuje vyladit počet používaných mapovačů.

4. Tady je několik dalších pokročilejších **možností** pro optimalizaci výkonu podregistru. Tyto možnosti umožňují nastavit paměť přidělenou pro mapování a omezení úkolů a můžou být užitečné při vylepšit výkon. Pamatujte, že *MapReduce. zmenšení. Memory. MB* nemůže být větší než velikost fyzické paměti pro každý pracovní uzel v clusteru Hadoop.
   
    ```hiveql
    set mapreduce.map.memory.mb = 2048;
    set mapreduce.reduce.memory.mb=6144;
    set mapreduce.reduce.java.opts=-Xmx8192m;
    set mapred.reduce.tasks=128;
    set mapred.tasktracker.reduce.tasks.maximum=128;
    ```