---
title: Ladění pravidla výkonu Spark Azure Data Lake Store | Microsoft Docs
description: Ladění pravidla výkonu Spark Azure Data Lake Store
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
ms.openlocfilehash: a807bea13063d2a0b3c1c71ddb6c98aa2d2568d3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197065"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>Pokyny pro Spark v HDInsight a Azure Data Lake Store optimalizace výkonu

Při ladění výkonu na Spark, budete muset vzít v úvahu počet aplikací, které budou spuštěny v clusteru.  Ve výchozím nastavení, můžete spustit 4 aplikace souběžně na clusteru HDI (Poznámka: ve výchozím nastavení se může změnit).  Můžete rozhodnout pro použití méně aplikací, abyste mohli přepíší výchozí nastavení a použít více clusteru pro tyto aplikace.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Pokyny o tom, jak vytvořit najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Store. V tématu [vytvoření clusteru HDInsight s Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že povolení vzdálené plochy pro cluster.
* **Spuštění clusteru Spark v Azure Data Lake Store**.  Další informace najdete v tématu [clusteru používejte HDInsight Spark k analýze dat v Data Lake Store](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Ladění pokyny na ADLS výkonu**.  Obecný výkon koncepty, najdete v části [Data Lake Store výkonu ladění pokyny](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Parametry

Při spuštění úlohy Spark, zde jsou nejdůležitější nastavení, která lze ladit a zvyšuje výkon na ADLS:

* **Poče vykonavatelů** -počet souběžných úloh, které mohou být provedeny.

* **Paměť vykonavatele** -množství paměti přidělené pro každý prováděcího modulu.

* **Vykonavatele jader** -počet jader přidělené každý prováděcího modulu.                     

**Poče vykonavatelů** Num vykonavatelů Nastaví maximální počet úkolů, které můžou běžet souběžně.  Skutečný počet úloh, které může běžet paralelně ohraničená paměti a prostředky procesoru, které jsou k dispozici v clusteru.

**Paměť vykonavatele** Toto je množství paměti přidělené ke každé prováděcího modulu.  Paměti potřebné pro každé vykonavatele je závislá na úlohu.  Paměť pro komplexní operace, musí být vyšší.  Pro jednoduché operace, jako je ke čtení a zápisu bude nižší požadavky na paměť.  Velikost paměti pro každý vykonavatele lze zobrazit v Ambari.  V Ambari přejděte na Spark a zobrazit kartu konfigurací.  

**Vykonavatele jader** to Nastaví množství jader použitou na prováděcího modulu, která určuje počet paralelních vláken, která lze spustit na prováděcího modulu.  Například pokud prováděcí modul jader = 2, pak každý vykonavatele můžete spustit 2 paralelní úlohy v prováděcí modul.  Jádrech vykonavatele potřeby budou závislé na úlohu.  Úlohy náročnými vstupně-výstupní operace nevyžadují velké množství paměti na jeden úkol, každý vykonavatele může zpracovat více paralelních úloh.

Ve výchozím nastavení jsou definovány dvě virtuální YARN jádra pro každou počet fyzických jader při spuštění Spark v HDInsight.  Toto číslo poskytuje vhodné rovnováhy concurrecy a množství kontext přepínání z více vláken.  

## <a name="guidance"></a>Doprovodné materiály

Při spouštění Spark analytické úlohy pro práci s daty v Data Lake Store, doporučujeme pomocí nejnovější verze HDInsight získáte nejlepší výkon s Data Lake Store. Když vaše úlohy více vstupně-výstupní operace náročné na prostředky, můžete určité parametry nakonfigurované ke zlepšení výkonu.  Azure Data Lake Store je platforma vysoce škálovatelné úložiště, která dokáže zpracovat vysoké propustnosti.  Pokud úloha obsahuje především pro čtení nebo zápisu, pak zvýšení souběžnosti pro vstupy/výstupy do a z Azure Data Lake Store může zvýšit výkon.

Chcete-li zvýšit souběžnost úloh intenzivním vstupně-výstupních operací několik obecné způsoby.

**Krok 1: Určení, kolik aplikace běží v clusteru** – byste měli vědět, kolik aplikací jsou spuštěné v clusteru, včetně aktuální.  Výchozí hodnoty pro každý Spark předpokládá nastavení, které jsou 4 současné spuštění aplikace.  Proto bude mít pouze 25 % clusteru k dispozici pro každou aplikaci.  Chcete-li získat lepší výkon, můžete přepsat výchozí hodnoty tak, že změníte počet vykonavatelů.  

**Krok 2: Nastavení vykonavatele paměti** – první věc, kterou chcete nastavit je vykonavatele paměti.  Paměť budou závislé na úlohy, které chcete spustit.  Přidělování paměti prováděcího modulu, může zvýšit souběžnost.  Pokud se zobrazí mimo výjimky paměti při spuštění vaší úlohy, měli byste zvýšit hodnotu tohoto parametru.  Jeden alternativou je získat více paměti pomocí clusteru, který má vyšší objemy paměti nebo zvýšení velikosti vašeho clusteru.  Více paměti povolí další vykonavatelů má používat, což znamená, že další souběžnosti.

**Krok 3: Nastavte vykonavatele jader** – pro vstupně-výstupních operací náročné úlohy, které nemají komplexních operací, je vhodné začínat velký počet vykonavatele jader a zvýšit počet paralelních úloh za prováděcího modulu.  Nastavení vykonavatele jader na 4 je dobré spustit.   

    executor-cores = 4
Zvýšení počtu jader vykonavatele vám poskytne další paralelismus tak můžete experimentovat s jinou vykonavatele jader.  Pro úlohy, které mají složitějších operací by měly snížit počet jader na prováděcího modulu.  Pokud vykonavatele jader je nastavena na hodnotu vyšší než 4, pak uvolňování paměti může stát neefektivní a snížit výkon.

**Krok 4: Určení množství paměti YARN v clusteru** – tyto informace jsou k dispozici v Ambari.  Přejděte do YARN a zobrazit kartu konfigurací.  V tomto okně se zobrazí YARN paměti.  
Poznámka: když jste v okně, uvidíte také výchozí velikost YARN kontejneru.  Velikost kontejneru YARN je stejný jako paměť na vykonavatele parametru.

    Total YARN memory = nodes * YARN memory per node
**Krok 5: Vypočítat num vykonavatelů**

**Vypočítat omezení paměti** -parametr num vykonavatelů je omezené paměti nebo procesoru.  Omezení paměti je určen podle množství dostupné paměti YARN pro vaši aplikaci.  Měli byste trvat celkové paměti YARN a vydělte vykonavatele paměti.  Omezení musí být zrušte škálovat pro počet aplikací, takže jsme rozdělit podle počtu aplikací.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Výpočet procesoru omezení** – počítá se jako celkový virtuální jader dělený počet jader na vykonavatele omezení procesoru.  Existují 2 virtuální jádra pro každou počet fyzických jader.  Podobně jako omezení paměti, máme dělení podle počtu aplikací.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Nastavit počet vykonavatelů** – je určen parametr num vykonavatelů provedením minimum omezení paměti a procesoru omezení. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Nastavení vyššího počtu num vykonavatelů nemusí se nutně zvýšit výkon.  Měli byste zvážit přidání další vykonavatelů přidá, velmi starat se pro každý další vykonavatele, který může potenciálně snížit výkon.  Poče vykonavatelů ohraničená prostředků clusteru.    

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že aktuálně máte cluster skládá z 8 D4v2 uzly se systémem 2 aplikace, včetně toho, který chcete spustit.  

**Krok 1: Určení, kolik aplikace běží v clusteru** – víte, že máte 2 aplikace v clusteru, včetně toho, který chcete spustit.  

**Krok 2: Nastavení vykonavatele paměti** – v tomto příkladu jsme určit, že bude 6 GB paměti vykonavatele dostatečná pro úlohy náročné vstupně-výstupních operací.  

    executor-memory = 6GB
**Krok 3: Nastavte vykonavatele jader** – vzhledem k tomu, že je to úlohy náročné vstupně-výstupních operací, jsme můžete nastavit počet jader pro každý vykonavatele na 4.  Nastavení jader na vykonavatele na větší než 4 může způsobit problémy s kolekce paměti.  

    executor-cores = 4
**Krok 4: Určení množství paměti YARN v clusteru** – jsme přejděte na Ambari a zjistěte, zda má každý D4v2 25 GB paměti YARN.  Vzhledem k tomu, že je 8 uzlů, dostupná paměť YARN se násobí hodnotou 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Krok 5: Vypočítat num vykonavatelů** – je určen parametr num vykonavatelů provedením minimální omezení paměti a procesoru omezení dělený počet aplikací běžících na Spark.    

**Vypočítat omezení paměti** – omezení paměti se počítá jako celkovou velikost paměti YARN dělený paměť na prováděcího modulu.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Výpočet procesoru omezení** – počítá se jako jádrech celkový yarn dělený počet jader na vykonavatele omezení procesoru.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Sada num vykonavatelů**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

