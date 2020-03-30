---
title: Optimalizace výkonu Azure Data Lake Storage Gen1 – MapReduce
description: Azure Data Lake Storage Gen1 MapReduce Performance Tuning Guidelines
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904590"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Pokyny k ladění výkonu pro MapReduce na HDInsight a Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1.](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Viz [Vytvoření clusteru HDInsight s gen1 úložiště datového jezera](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Pomocí MapReduce na HDInsight**. Další informace naleznete v tématu [Use MapReduce in Hadoop on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Projděte si pokyny pro optimalizaci výkonu pro úložiště datových jezer Gen1**. Obecné koncepty výkonu najdete v [tématu Data Lake Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Při spuštění úloh MapReduce, zde jsou nejdůležitější parametry, které můžete nakonfigurovat pro zvýšení výkonu na data Lake Storage Gen1:

|Parametr      | Popis  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Velikost paměti, kterou chcete přidělit každému mapovači.  |
|`Mapreduce.job.maps`     |  Počet úloh mapy na úlohu.  |
|`Mapreduce.reduce.memory.mb`     |  Množství paměti přidělit každý reduktor.  |
|`Mapreduce.job.reduces`    |   Počet snížení úkolů na úlohu.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Upravte toto číslo na základě toho, kolik paměti je potřeba pro mapu a/nebo snížit úkol. Můžete zobrazit výchozí hodnoty `mapreduce.map.memory` `mapreduce.reduce.memory` a v Ambari pomocí konfigurace příze. V Ambari přejděte na YARN a zobrazte kartu **Configs.** Zobrazí se paměť YARN.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

To určuje maximální počet mapovačů nebo reduktorů k vytvoření. Počet rozdělení určuje, kolik mapovačů jsou vytvořeny pro úlohu MapReduce. Proto můžete získat méně mapovačů, než jste požadovali, pokud existuje méně rozdělení než požadovaný počet mapovačů.

## <a name="guidance"></a>Doprovodné materiály

### <a name="step-1-determine-number-of-jobs-running"></a>Krok 1: Určení počtu spuštěných úloh

Ve výchozím nastavení mapreduce bude používat celý cluster pro vaši úlohu. Můžete použít méně clusteru pomocí méně mapovačů, než jsou k dispozici kontejnery. Pokyny v tomto dokumentu předpokládá, že vaše aplikace je jediná aplikace spuštěná v clusteru.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Krok 2: Nastavení mapreduce.map.memory/mapreduce.reduce.memory

Velikost paměti pro mapu a snížení úkolů bude záviset na konkrétní úloze. Velikost paměti můžete zmenšit, pokud chcete zvýšit souběžnost. Počet souběžně spuštěných úloh závisí na počtu kontejnerů. Snížením množství paměti na mapovač nebo reduktor lze vytvořit více kontejnerů, které umožňují souběžné spuštění více mapovačů nebo reduktorů. Snížení množství paměti příliš mnoho může způsobit, že některé procesy dojdou paměti. Pokud se při spuštění úlohy zobrazí chyba haldy, zvětšete paměť na mapovač nebo reduktor. Zvažte, že přidání další kontejnery přidá další režii pro každý další kontejner, což může potenciálně snížit výkon. Další alternativou je získat více paměti pomocí clusteru, který má vyšší množství paměti nebo zvýšení počtu uzlů v clusteru. Více paměti umožní více kontejnerů, které mají být použity, což znamená více souběžnosti.

### <a name="step-3-determine-total-yarn-memory"></a>Krok 3: Určení celkové paměti YARN

Chcete-li optimalizovat mapreduce.job.maps/mapreduce.job.reduces, zvažte množství celkové paměti YARN, která je k dispozici pro použití. Tyto informace jsou k dispozici v Ambari. Přejděte na YARN a zobrazte kartu **Configs.** V tomto okně se zobrazí paměť YARN. Vynásobte paměť YARN počtem uzlů v clusteru, abyste získali celkovou paměť YARN.

`Total YARN memory = nodes * YARN memory per node`

Pokud používáte prázdný cluster, může být paměť celkovou pamětí YARN pro váš cluster. Pokud paměť používají jiné aplikace, můžete použít pouze část paměti clusteru snížením počtu mapovačů nebo reduktorů na počet kontejnerů, které chcete použít.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Krok 4: Výpočet počtu kontejnerů YARN

Kontejnery YARN určují množství souběžnosti, které jsou k dispozici pro úlohu. Vezměte celkovou paměť YARN a vydělte ji mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Krok 5: Nastavení mapreduce.job.maps/mapreduce.job.reduces

Nastavte mapreduce.job.maps/mapreduce.job.reduzuje alespoň počet dostupných kontejnerů. Můžete dále experimentovat zvýšením počtu mapovačů a reduktorů, abyste zjistili, zda získáte lepší výkon. Mějte na paměti, že další mapovače budou mít další režii, takže příliš mnoho mapovačů může snížit výkon.

Plánování procesoru a izolace procesoru jsou ve výchozím nastavení vypnuty, takže počet kontejnerů YARN je omezen pamětí.

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že aktuálně máte cluster složený z uzlů 8 D14 a chcete spustit úlohu intenzivní vstupně-operace. Zde jsou výpočty, které byste měli udělat:

### <a name="step-1-determine-number-of-jobs-running"></a>Krok 1: Určení počtu spuštěných úloh

Pro náš příklad předpokládáme, že naše práce je jediná, která běží.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Krok 2: Nastavení mapreduce.map.memory/mapreduce.reduce.memory

V našem příkladu spouštějíte úlohu náročnou vstupně-up a rozhodujete, že 3 GB paměti pro úlohy mapy je dostatečná.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Krok 3: Určení celkové paměti YARN

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Krok 4: Výpočet počtu kontejnerů YARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Krok 5: Nastavení mapreduce.job.maps/mapreduce.job.reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Omezení

**Omezení úložiště datového jezera Gen1**

Jako služba pro více klientů nastavuje Data Lake Storage Gen1 limity šířky pásma na úrovni účtu. Pokud dosáhnete těchto limitů, začnete vidět selhání úloh. To lze identifikovat sledováním chyb omezení v protokolech úloh. Pokud potřebujete větší šířku pásma pro svou práci, kontaktujte nás.

Chcete-li zkontrolovat, zda jste stále omezení, je třeba povolit protokolování ladění na straně klienta. Zde je návod, jak to můžete udělat:

1. Vložte následující vlastnost do vlastností log4j v Ambari > YARN > Config > Advanced yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Restartujte všechny uzly nebo službu, aby se konfigurační konfigurace projevila.

3. Pokud jste stále omezen, uvidíte kód chyby HTTP 429 v souboru protokolu YARN. Soubor protokolu YARN je v&lt;/tmp/ user&gt;/yarn.log

## <a name="examples-to-run"></a>Příklady spuštění

Chcete-li ukázat, jak MapReduce běží na Data Lake Storage Gen1, následuje některé ukázkový kód, který byl spuštěn v clusteru s následujícím nastavením:

* 16 uzlů D14v2
* Cluster Hadoop se systémem HDI 3.6

Pro výchozí bod zde jsou některé příklady příkazů pro spuštění MapReduce Teragen, Terasort a Teravalidate. Tyto příkazy můžete upravit na základě vašich prostředků.

### <a name="teragen"></a>Teragen (Teragen)

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort (Terasort)

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
