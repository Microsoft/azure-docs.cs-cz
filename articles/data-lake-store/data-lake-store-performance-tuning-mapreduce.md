---
title: Ladění výkonu Azure Data Lake Storage Gen1 – MapReduce
description: Přečtěte si o ladění výkonu pro MapReduce v Azure Data Lake Storage Gen1, včetně parametrů, pokynů, ukázkového výpočtu a omezení.
author: stewu
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 03c35d0af97cf24d1683d0ff21f10a0371391616
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88190205"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Pokyny k ladění výkonu pro MapReduce ve službě HDInsight a Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny, jak ho vytvořit, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) .
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Další informace najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Použití MapReduce ve službě HDInsight**. Další informace najdete v tématu [použití MapReduce v Hadoop ve službě HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce) .
* **Přečtěte si pokyny pro ladění výkonu pro data Lake Storage Gen1**. Obecné koncepty výkonu najdete v tématu [Data Lake Storage Gen1 pokyny k ladění výkonu](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) .

## <a name="parameters"></a>Parametry

Při spouštění úloh MapReduce jsou tady nejdůležitější parametry, které můžete nakonfigurovat, aby se zvýšil výkon Data Lake Storage Gen1:

|Parametr      | Popis  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Velikost paměti, která se má přidělit každému mapovači.  |
|`Mapreduce.job.maps`     |  Počet úloh mapy na úlohu.  |
|`Mapreduce.reduce.memory.mb`     |  Velikost paměti, která se má přidělit každému snížení.  |
|`Mapreduce.job.reduces`    |   Počet úloh snižujících náklady na úlohu.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>MapReduce. map. Memory/MapReduce. zmenšení. Memory

Upravte toto číslo na základě toho, kolik paměti je potřeba pro mapu nebo zmenšení úlohy. Můžete zobrazit výchozí hodnoty `mapreduce.map.memory` a `mapreduce.reduce.memory` v Ambari prostřednictvím konfigurace příze. V Ambari přejděte do PŘÍZe a zobrazte kartu **Konfigurace** . Zobrazí se paměť PŘÍZe.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>MapReduce. job. map/MapReduce. job. redukuje

Určuje maximální počet mapovačů nebo reduktorůů, které se mají vytvořit. Počet rozdělení určuje, kolik mapovačů se má pro úlohu MapReduce vytvořit. Proto můžete získat méně mapovačů, než kolik jich požadujete, pokud je menší počet rozdělení, než je počet požadovaných mapovačů.

## <a name="guidance"></a>Pokyny

### <a name="step-1-determine-number-of-jobs-running"></a>Krok 1: určení počtu spuštěných úloh

Ve výchozím nastavení bude MapReduce používat celý cluster pro vaši úlohu. Méně než tento cluster můžete použít k menšímu počtu mapovačů, než je dostupných kontejnerů. Pokyny v tomto dokumentu předpokládají, že vaše aplikace je jediná aplikace spuštěná v clusteru.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Krok 2: nastavte MapReduce. map. Memory/MapReduce. zmenšení. Memory

Velikost paměti pro mapu a omezení úloh bude závislá na konkrétní úloze. Velikost paměti můžete snížit, pokud chcete zvýšit souběžnost. Počet souběžně spuštěných úloh závisí na počtu kontejnerů. Tím, že se zmenší množství paměti na jedno nebo více kontejnerů, je možné vytvořit více kontejnerů, které umožní souběžně běžet více mapovačů nebo reduktorů. Zmenšení množství paměti může způsobit nedostatek paměti z některých procesů. Pokud při spuštění úlohy dojde k chybě haldy, zvyšte velikost paměti na Mapovač nebo zmenšení. Zvažte, že přidání dalších kontejnerů přináší další režii pro každý další kontejner, což může způsobit snížení výkonu. Další možností je získat více paměti pomocí clusteru s větším množstvím paměti nebo zvýšením počtu uzlů v clusteru. Více paměti umožní použít více kontejnerů, což znamená více souběžnosti.

### <a name="step-3-determine-total-yarn-memory"></a>Krok 3: určení celkové paměti PŘÍZe

Pro účely ladění MapReduce. job. map/MapReduce. job. redukuje zvažte množství celkové dostupné paměti PŘÍZe k použití. Tyto informace jsou k dispozici v Ambari. Přejděte na PŘÍZe a zobrazte kartu **Konfigurace** . V tomto okně se zobrazí paměť PŘÍZe. K získání celkové paměti PŘÍZe vynásobte paměť PŘÍZe počtem uzlů v clusteru.

`Total YARN memory = nodes * YARN memory per node`

Pokud používáte prázdný cluster, může být paměť celkovou paměti PŘÍZí pro váš cluster. Pokud jiné aplikace používají paměť, můžete zvolit, že se má použít jenom část paměti clusteru, a to snížením počtu mapovačů nebo reduktorů počtu kontejnerů, které chcete použít.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Krok 4: výpočet počtu kontejnerů PŘÍZe

Kontejnery PŘÍZe určují množství souběžnosti, které je k dispozici pro úlohu. Využijte celkovou paměť PŘÍZe a rozdělte ji pomocí MapReduce. map. Memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Krok 5: nastavte MapReduce. job. Maps/MapReduce. job. redukujes

Nastavte MapReduce. job. Maps/MapReduce. job. zmenšuje alespoň na počet dostupných kontejnerů. Můžete ještě experimentovat zvýšením počtu mapovačů a reduktorů, abyste viděli, jestli získáte lepší výkon. Mějte na paměti, že další mapovače budou mít další režii, takže příliš mnoho mapovačů může snížit výkon.

Plánování procesoru a izolace procesoru jsou ve výchozím nastavení vypnuté, takže počet kontejnerů PŘÍZ je omezený pamětí.

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že aktuálně máte cluster tvořený 8 D14 uzly a chcete spustit úlohu náročné na vstupně-výstupní operace. Tady jsou výpočty, které byste měli provést:

### <a name="step-1-determine-number-of-jobs-running"></a>Krok 1: určení počtu spuštěných úloh

V našem příkladu předpokládáme, že naše úloha je právě spuštěná.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Krok 2: nastavte MapReduce. map. Memory/MapReduce. zmenšení. Memory

V našem příkladu máte spuštěnou úlohu náročné na vstupně-výstupní operace a rozhodnete se, že 3 GB paměti pro úlohy mapy jsou dostatečné.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Krok 3: určení celkové paměti PŘÍZe

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Krok 4: výpočet počtu kontejnerů PŘÍZe

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Krok 5: nastavte MapReduce. job. Maps/MapReduce. job. redukujes

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Omezení

**Omezení Data Lake Storage Gen1**

V případě víceklientské služby Data Lake Storage Gen1 nastaví limity šířky pásma na úrovni účtu. Pokud jste dosáhli těchto limitů, začnete zobrazovat selhání úloh. To je možné identifikovat pozorováním chyb omezení v protokolech úloh. Pokud potřebujete pro svoji úlohu větší šířku pásma, kontaktujte nás prosím.

Chcete-li zjistit, zda se vám omezilo omezení, je nutné povolit protokolování ladění na straně klienta. Můžete to udělat takto:

1. Do vlastností log4j v Ambari > PŘÍZ > config vložte následující vlastnost > rozšířené příze-log4j: log4j. protokolovací. com. Microsoft. Azure. datalake. Store = DEBUG

2. Aby se konfigurace projevila, restartujte všechny uzly/služby.

3. Pokud se vám omezí omezení, zobrazí se v souboru protokolu PŘÍZ kód chyby HTTP 429. Soubor protokolu PŘÍZe je v/tmp/ &lt; User &gt; /YARN.log

## <a name="examples-to-run"></a>Příklady spuštění

Chcete-li předvést, jak MapReduce běží na Data Lake Storage Gen1, následuje ukázkový kód, který byl spuštěn v clusteru s následujícím nastavením:

* D14v2 se 16 uzly
* Cluster Hadoop se systémem HDI 3,6

Tady je několik ukázkových příkazů pro výchozí bod, které spouští MapReduce Teragen, Terasort a Teravalidate. Tyto příkazy můžete upravit v závislosti na svých prostředcích.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
