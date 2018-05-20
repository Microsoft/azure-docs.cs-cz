---
title: Ladění pravidla výkonu MapReduce Azure Data Lake Store | Microsoft Docs
description: Ladění pravidla výkonu MapReduce Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: f5586e7706d4dad7e3c943b2a661fa296b4d30bf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Pokyny pro MapReduce na HDInsight a Azure Data Lake Store optimalizace výkonu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Pokyny o tom, jak vytvořit najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Store. V tématu [vytvoření clusteru HDInsight s Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že povolení vzdálené plochy pro cluster.
* **Použití prostředí MapReduce v HDInsight**.  Další informace najdete v tématu [použití MapReduce v Hadoop v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Ladění pokyny na ADLS výkonu**.  Obecný výkon koncepty, najdete v části [Data Lake Store výkonu ladění pokyny](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Při spuštění úloh MapReduce, zde jsou nejdůležitější parametry, které můžete nakonfigurovat a zvyšuje výkon na ADLS:

* **Mapreduce.map.Memory.MB** – množství paměti pro každý mapper
* **Mapreduce.job.Maps** – počet úloh mapy na úlohu
* **Mapreduce.reduce.Memory.MB** – množství paměti pro každý reduktorem
* **Mapreduce.job.reduces** – počet úloh snižte na úlohu

**Mapreduce.map.Memory / Mapreduce.reduce.memory** toto číslo je třeba upravit v závislosti na tom, kolik paměti je potřeba pro mapu a k omezení počtu úloh.  Výchozí hodnoty mapreduce.map.memory a mapreduce.reduce.memory lze zobrazit v Ambari prostřednictvím konfigurace Yarn.  V Ambari přejděte na YARN a zobrazit kartu konfigurací.  Zobrazí se YARN paměti.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** to bude určit maximální počet mappers nebo přechodky, který se má vytvořit.  Počet rozdělení určí, kolik mappers budou vytvořeny pro úlohu MapReduce.  Proto může získat méně mappers, než jste požadovali, pokud jsou menší rozdělení než počet mappers požadovaný.       

## <a name="guidance"></a>Doprovodné materiály

**Krok 1: Určení počet úloh spuštěných** – ve výchozím nastavení, použije MapReduce celý cluster pro úlohu.  Můžete použít menší clusteru pomocí méně mappers, než je k dispozici kontejnery.  Pokyny v tomto dokumentu se předpokládá, že aplikace je pouze aplikace běžící v clusteru.      

**Krok 2: Nastavení mapreduce.map.memory/mapreduce.reduce.memory** – velikost paměti pro mapu a snížit úlohy budou závislé na určité úlohy.  Pokud chcete zvýšit souběžnost můžete snížit velikost paměti.  Počet současně spuštěných úloh závisí na počet kontejnerů.  Snížením množství paměti na mapper nebo reduktorem víc kontejnerů mohou být vytvořeny, která umožňují více mappers nebo přechodky spuštěny současně.  Příliš mnoho snižují množství paměti může způsobit, že některé procesy spuštění nedostatek paměti.  Pokud dojde k chybě haldy při spuštění vaší úlohy, měli byste zvýšit paměť na mapper nebo reduktorem.  Měli byste zvážit přidání další kontejnerů přidá, velmi starat se pro každý další kontejneru, který může potenciálně snížit výkon.  Další alternativou je získat více paměti pomocí clusteru, který má vyšší objemy paměti nebo zvýšením počtu uzlů v clusteru.  Více paměti umožní víc kontejnerů, které mají být použity, což znamená, že další souběžnosti.  

**Krok 3: Určení celkový YARN paměti** – Pokud chcete ladit mapreduce.job.maps/mapreduce.job.reduces, měli byste zvážit celkové YARN paměti k dispozici pro použití.  Tyto informace jsou k dispozici v Ambari.  Přejděte do YARN a zobrazit kartu konfigurací.  V tomto okně se zobrazí YARN paměti.  Měli vynásobte paměti YARN počtu uzlů v clusteru se získat celkovou velikost paměti YARN.

    Total YARN memory = nodes * YARN memory per node
Pokud používáte prázdný clusteru, paměti, může být celkové paměti YARN pro váš cluster.  Pokud jiné aplikace používají paměť, je možné použít pouze část paměti váš cluster snížením počtu mappers nebo přechodky počet kontejnerů, které chcete použít.  

**Krok 4: Vypočítat počet kontejnerů YARN** – YARN kontejnery stanovují množství souběžnost, které jsou k dispozici pro úlohu.  Trvat celkové paměti YARN, vydělte mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Krok 5: Nastavení mapreduce.job.maps/mapreduce.job.reduces** mapreduce.job.maps/mapreduce.job.reduces nastavit na alespoň počet dostupné kontejnery.  Můžete experimentovat další zvýšením počtu mappers a přechodky zobrazit, pokud chcete získat lepší výkon.  Uvědomte si, že další mappers budou mít dalších zásahů, má příliš mnoho mappers může snížit výkon.  

Plánování CPU a využití procesoru izolace jsou ve výchozím nastavení vypnuté, počet kontejnerů YARN je omezené paměti.

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že aktuálně máte cluster skládá z 8 D14 uzly a chcete spustit úlohu intenzivním vstupně-výstupní operace.  Zde jsou výpočty, které byste měli udělat:

**Krok 1: Určení počet úloh spuštěných** -pro náš příklad předpokládáme, že naše úloha je spuštěna pouze jedna.  

**Krok 2: Nastavení mapreduce.map.memory/mapreduce.reduce.memory** – pro náš příklad běží úlohy náročné vstupně-výstupních operací a rozhodnout, že 3 GB paměti pro mapování úlohy bude dostatečná.

    mapreduce.map.memory = 3GB
**Krok 3: Určení celkový YARN paměti**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Krok 4: Vypočítat počet YARN kontejnery**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Krok 5: Nastavení mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Omezení

**ADLS omezení**

Jako víceklientské služby nastaví ADLS limity účtu úrovně šířky pásma.  Jestli jste nedosáhli tyto limity, začněte zobrazíte selhání úkolů. To lze identifikovat podle sledování omezení chyby v protokolech úloh.  Pokud potřebujete větší šířku pásma pro úlohu, kontaktujte nás.   

Pokud chcete zkontrolovat, pokud jste jsou získávání omezené, musíte povolit ladění na straně klienta protokolování. Zde je, jak můžete to udělat:

1. Uveďte následující vlastnost ve vlastnostech log4j v Ambari > YARN > Konfigurace > Upřesnit yarn log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Restartujte všechny uzly nebo služby, konfigurace se projeví.

3. Pokud jste jsou získávání omezené, se zobrazí kód HTTP 429 chyby v souboru protokolu YARN. Soubor protokolu YARN je v /tmp/&lt;uživatele&gt;/yarn.log

## <a name="examples-to-run"></a>Příklady pro spuštění

K předvedení, jak MapReduce běží na Azure Data Lake Store, zde jsou některé ukázkový kód, která byla spuštěna na cluster s následujícím nastavením:

* 16 uzlu D14v2
* Spuštění HDI 3.6 clusteru Hadoop

Pro počáteční bod tady jsou příklady některých příkazů ke spuštění MapReduce Teragen, Terasort a Teravalidate.  Můžete upravit tyto příkazy podle vašich prostředků.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
