---
title: 'Ladění výkonu: Spark, HDInsight & Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft'
description: Azure Data Lake Storage Gen2 Spark Pokyny pro optimalizaci výkonu
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a70b8112af201a49e7eece8b689e75102ec55880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327551"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Výkon ladění: Spark, HDInsight & Azure Data Lake Storage Gen2

Při ladění výkonu na Spark, musíte zvážit počet aplikací, které budou spuštěny na vašem clusteru.  Ve výchozím nastavení můžete v clusteru HDI spouštět 4 aplikace současně (Poznámka: výchozí nastavení se může změnit).  Můžete se rozhodnout používat méně aplikací, abyste mohli přepsat výchozí nastavení a používat pro tyto aplikace více clusteru.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen2**. Pokyny k jeho vytvoření najdete v [tématu Úvodní příručka: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen2. Viz [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Spuštění clusteru Spark na úložišti datových jezer Gen2**.  Další informace najdete [v tématu Použití clusteru HDInsight Spark k analýze dat v zařízení Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Pokyny pro ladění výkonu pro úložiště datových jezer Gen2**.  Obecné koncepty výkonu najdete v [tématu Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametry

Při spuštění úloh Spark, tady jsou nejdůležitější nastavení, které lze vyladit pro zvýšení výkonu na Data Lake Storage Gen2:

* **Num-executors** - Počet souběžných úloh, které mohou být provedeny.

* **Vykonavatel paměti** - množství paměti přidělené každému vykonavatele.

* **Jádro prováděcího modulu** – počet jader přidělených každému vykonavatelu.                     

**Num-vykonavatelé** Num-executors nastaví maximální počet úloh, které lze spustit paralelně.  Skutečný počet úloh, které lze spustit paralelně, je ohraničen prostředky paměti a procesoru, které jsou k dispozici v clusteru.

**Paměť exekutora** Toto je množství paměti, která je přidělena každému vykonavateli.  Paměť potřebná pro každého vykonavatele je závislá na úloze.  Pro složité operace musí být paměť vyšší.  Pro jednoduché operace, jako je čtení a zápis, požadavky na paměť bude nižší.  Velikost paměti pro každého vykonavatele lze zobrazit v Ambari.  V Ambari přejděte na Spark a zobrazte kartu Configs.  

**Exekutor-jádra** Tím se nastaví počet jader použitých na vykonavatele, který určuje počet paralelních vláken, které lze spustit na vykonavatele.  Například pokud vykonavatel jádra = 2, pak každý vykonavatel může spustit 2 paralelní úlohy v prováděcím modulu.  Potřebná jádra vykonavatele budou na úloze závislá.  Vstupně-no těžké úlohy nevyžadují velké množství paměti na úlohu, takže každý vykonavatel může zpracovat více paralelních úloh.

Ve výchozím nastavení jsou dvě virtuální jádra YARN definována pro každé fyzické jádro při spuštění Spark na HDInsight.  Toto číslo poskytuje dobrou rovnováhu souběžnosti a množství přepnutí kontextu z více vláken.  

## <a name="guidance"></a>Doprovodné materiály

Při spuštění analytických úloh Spark pro práci s daty v Programu Porty datových jezer Gen2 doporučujeme použít nejnovější verzi HDInsight, abyste získali nejlepší výkon s Date Lake Storage Gen2. Pokud je vaše úloha náročnější na vstupně-výkon, lze nakonfigurovat určité parametry pro zlepšení výkonu.  Data Lake Storage Gen2 je vysoce škálovatelná platforma úložiště, která zvládne vysokou propustnost.  Pokud úloha se skládá hlavně z čtení nebo zápisu, pak zvýšení souběžnosti pro vstupně-va do a z úložiště datového jezera Gen2 může zvýšit výkon.

Existuje několik obecných způsobů, jak zvýšit souběžnost pro vstupně-to intenzivní úlohy.

**Krok 1: Zjistěte, kolik aplikací běží ve vašem clusteru** – měli byste vědět, kolik aplikací běží v clusteru, včetně té aktuální.  Výchozí hodnoty pro každé nastavení Spark předpokládá, že existují 4 aplikace spuštěné současně.  Proto budete mít k dispozici pouze 25 % clusteru pro každou aplikaci.  Chcete-li získat lepší výkon, můžete přepsat výchozí hodnoty změnou počtu vykonavatelů.  

**Krok 2: Nastavení paměti prováděcího modulu** – První věc, kterou je třeba nastavit, je paměť vykonavatele.  Paměť bude záviset na úloze, kterou budete spouštět.  Souběžnost můžete zvýšit přidělením méně paměti na vykonavatele.  Pokud se při spuštění úlohy zobrazí výjimky z paměti, měli byste zvýšit hodnotu tohoto parametru.  Jednou z možností je získat více paměti pomocí clusteru, který má větší množství paměti nebo zvětšení velikosti clusteru.  Více paměti umožní další prováděcí moduly, které mají být použity, což znamená více souběžnosti.

**Krok 3: Nastavení jader prováděcích modulů** – Pro vstupně-v. intenzivní úlohy, které nemají složité operace, je dobré začít s vysokým počtem jader prováděcího modulu pro zvýšení počtu paralelních úloh na vykonavatele.  Nastavení vykonavatel-jádra na 4 je dobrý začátek.   

    executor-cores = 4
Zvýšení počtu vykonavatel-jádra vám poskytne více paralelismu, takže můžete experimentovat s různými vykonavatel-jádra.  U úloh, které mají složitější operace, byste měli snížit počet jader na vykonavatele.  Pokud je nastavena jádro vykonavatele vyšší než 4, může být uvolňování paměti neefektivní a snížit výkon.

**Krok 4: Určete velikost paměti YARN v clusteru** – Tyto informace jsou k dispozici v Ambari.  Přejděte na YARN a zobrazte kartu Configs.  V tomto okně se zobrazí paměť YARN.  
Poznámka: Když jste v okně, můžete také zobrazit výchozí velikost kontejneru YARN.  Velikost kontejneru YARN je stejná jako paměť na parametr vykonavatele.

    Total YARN memory = nodes * YARN memory per node
**Krok 5: Výpočet num-executors**

**Vypočítat omezení paměti** - parametr num-executors je omezen buď pamětí, nebo procesorem.  Omezení paměti je určeno množstvím dostupné paměti YARN pro vaši aplikaci.  Měli byste vzít celkovou paměť YARN a rozdělit ji pamětí vykonavatele.  Omezení je třeba de-scaled pro počet aplikací, takže jsme se vydělí počtem aplikací.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Vypočítat omezení procesoru** - Omezení procesoru se vypočítá jako celkový virtuální jádra děleno počtem jader na vykonavatele.  Pro každé fyzické jádro jsou k dispozici 2 virtuální jádra.  Podobně jako omezení paměti, musíme vydělit počtem aplikací.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Nastavit num-executors** – num-executors parametr je určen tím, že minimální omezení paměti a omezení procesoru. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Nastavení vyššího počtu num-executors nemusí nutně zvýšit výkon.  Měli byste zvážit, že přidání dalších vykonavatelů přidá další režii pro každý další prováděcí modul, což může potenciálně snížit výkon.  Num-executors je ohraničen prostředky clusteru.    

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že v současné době máte cluster složený z uzlů 8 D4v2, který používá 2 aplikace včetně aplikace, kterou budete spouštět.  

**Krok 1: Určete, kolik aplikací běží ve vašem clusteru** – víte, že máte v clusteru 2 aplikace, včetně aplikace, kterou budete spouštět.  

**Krok 2: Nastavení paměti prováděcího modulu** – v tomto příkladu zjistíme, že 6 GB paměti prováděcího modulu bude stačit pro vstupně-v. náročnou úlohu.  

    executor-memory = 6GB
**Krok 3: Nastavte vykonavatel-jádra** - Vzhledem k tomu, že se jedná o vstupně-v., intenzivní úlohu, můžeme nastavit počet jader pro každého vykonavatele na 4.  Nastavení jader na vykonavatele na větší než 4 může způsobit problémy s uvolňováním paměti.  

    executor-cores = 4
**Krok 4: Určete velikost paměti YARN v clusteru** – přejdeme do Ambari, abychom zjistili, že každý D4v2 má 25 GB paměti YARN.  Vzhledem k tomu, že existuje 8 uzlů, dostupná paměť YARN se vynásobí 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Krok 5: Vypočítat num-executors** – num-executors parametr je určen tím, že minimální omezení paměti a omezení procesoru děleno # aplikací spuštěných na Spark.    

**Vypočítat omezení paměti** – omezení paměti se vypočítá jako celková paměť YARN dělená pamětí na vykonavatele.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Vypočítat omezení procesoru** - Omezení procesoru se vypočítá jako celková jádra příze vydělená počtem jader na vykonavatele.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Nastavit num-executors**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

