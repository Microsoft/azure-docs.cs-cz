---
title: 'Ladění výkonu: Spark, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Seznamte se s pokyny pro optimalizaci výkonu Sparku pomocí Azure HDInsight a Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 8ae9f96b42c0eb36a9380589780d141711c7ae4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88034727"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Ladění výkonu: Spark, HDInsight & Azure Data Lake Storage Gen2

Při ladění výkonu Sparku je potřeba vzít v úvahu počet aplikací, které budou v clusteru spuštěny.  Ve výchozím nastavení můžete spouštět 4 aplikace souběžně na vašem clusteru HDI (Poznámka: výchozí nastavení se může změnit).  Můžete se rozhodnout použít méně aplikací, abyste mohli přepsat výchozí nastavení a používat pro tyto aplikace více clusterů.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen2**. Pokyny, jak ho vytvořit, najdete v tématu [rychlý Start: vytvoření účtu úložiště Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen2. Viz [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Spuštění clusteru Spark na data Lake Storage Gen2**.  Další informace najdete v tématu [použití clusteru HDInsight Spark k analýze dat v Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Pokyny k ladění výkonu na data Lake Storage Gen2**.  Obecné koncepty výkonu najdete v tématu [Data Lake Storage Gen2 pokyny k ladění výkonu](data-lake-storage-performance-tuning-guidance.md) . 

## <a name="parameters"></a>Parametry

Když spouštíte úlohy Sparku, tady jsou nejdůležitější nastavení, která je možné vyladit, aby se zvýšil výkon Data Lake Storage Gen2:

* Number **-prováděčes** – počet souběžných úloh, které mohou být provedeny.

* **Vykonavatel – paměť** – množství paměti přidělené každému vykonavateli.

* **Vykonavatel – jader** – počet jader přidělených každému vykonavateli.                     

**Počet – vykonavatelé** Počet – vykonavatelé nastaví maximální počet úloh, které mohou běžet paralelně.  Skutečný počet úloh, které lze spustit paralelně, je svázán s prostředky paměti a CPU dostupnými ve vašem clusteru.

**Prováděcí modul – paměť** Toto je množství paměti, které je přiděleno každému vykonavateli.  Paměť potřebná pro každý prováděcí modul závisí na úloze.  U složitých operací musí být paměť vyšší.  U jednoduchých operací, jako je čtení a zápis, budou požadavky na paměť nižší.  Velikost paměti pro každý prováděcí modul se dá zobrazit v Ambari.  V Ambari přejděte na Spark a zobrazte kartu konfigurace.  

**Prováděcí modul – jádra** Tím se nastaví počet jader používaných na prováděcí modul, který určuje počet paralelních vláken, která se dají spustit na vykonavatele.  Například pokud exekutor-cores = 2, pak každý prováděcí modul může v vykonavateli spustit 2 Paralelní úlohy.  Potřebná jádra vykonavatele budou závislá na úloze.  Vstupně-výstupní úlohy v/v nevyžadují velké množství paměti na každý úkol, takže každý prováděcí modul může zpracovávat více paralelních úkolů.

Ve výchozím nastavení jsou pro každý fyzický jádro při spuštění Sparku ve službě HDInsight definovány dva jádra virtuálních PŘÍZe.  Toto číslo poskytuje dobré vyvážení souběžnosti a množství kontextu přepínání z více vláken.  

## <a name="guidance"></a>Pokyny

Při spouštění analytických úloh Sparku pro práci s daty v Data Lake Storage Gen2 doporučujeme použít nejnovější verzi HDInsight, abyste získali nejlepší výkon pomocí Data Lake Storage Gen2. Když je úloha náročnější na vstupně-výstupní operace, můžete nakonfigurovat některé parametry, aby se zlepšil výkon.  Data Lake Storage Gen2 je vysoce škálovatelná úložná platforma, která dokáže zvládnout vysokou propustnost.  Pokud se úloha skládá hlavně z čtení nebo zápisů, zvýšení souběžnosti vstupu/výstupu do a z Data Lake Storage Gen2 může zvýšit výkon.

Existuje několik obecných způsobů, jak zvýšit souběžnost pro úlohy náročné na vstupně-výstupní operace.

**Krok 1: určení, kolik aplikací je v clusteru spuštěno** – měli byste zjistit, kolik aplikací běží na clusteru, včetně aktuálního.  Výchozí hodnoty pro každé nastavení Sparku předpokládá, že je souběžně spuštěných 4 aplikací.  Proto budete mít k dispozici pouze 25% clusteru pro každou aplikaci.  Chcete-li získat lepší výkon, můžete přepsat výchozí hodnoty změnou počtu prováděcích modulů.  

**Krok 2: nastavení prováděcího modulu-paměti** – první věc, kterou je třeba nastavit, je vykonavatelská paměť.  Paměť bude závislá na úloze, kterou budete spouštět.  Souběžnost můžete zvýšit souběžnost přidělením méně paměti na vykonavatel.  Pokud se vám při spuštění úlohy zobrazí nedostatek paměti, měli byste zvýšit hodnotu tohoto parametru.  Jednou z možností je získat více paměti pomocí clusteru s větším množstvím paměti nebo zvýšením velikosti clusteru.  Víc paměti umožní použít víc prováděcích modulů, což znamená víc souběžnosti.

**Krok 3: nastavení prováděcích jader** – pro úlohy náročné na vstupně-výstupní operace, které nemají složitou operaci, je dobré začít s vysokým počtem jader vykonavatelů, aby se zvýšil počet paralelních úloh na vykonavatele.  Nastavování prováděcích jader na 4 je dobrým startem.   

prováděcí modul – jádra = 4

Zvýšení počtu prováděcích jader vám poskytne více paralelismu, abyste mohli experimentovat s různými prováděcími jádry.  Pro úlohy, které mají složitější operace, byste měli snížit počet jader na vykonavatele.  Pokud je jádro prováděče nastavené na vyšší než 4, může být uvolňování paměti neefektivní a snižuje výkon.

**Krok 4: určení množství paměti příze v clusteru** – tyto informace jsou k dispozici v Ambari.  Přejděte na PŘÍZe a zobrazte kartu konfigurace.  V tomto okně se zobrazí paměť PŘÍZe.  
Všimněte si, že když jste v okně, můžete také zobrazit výchozí velikost kontejneru PŘÍZ.  Velikost kontejneru PŘÍZe je stejná jako hodnota parametru paměti na vykonavatele.

Celková paměť PŘÍZ = uzly * PŘÍZe paměť na uzel

**Krok 5: výpočet počtu prováděcích modulů**

**Vypočítat omezení paměti** – parametr počet-prováděcích modulů je omezený buď pamětí, nebo procesorem.  Omezení paměti je určeno množstvím dostupné paměti PŘÍZe vaší aplikace.  Měli byste využít celkovou paměť PŘÍZe a rozdělit ji pomocí prováděcího modulu-paměť.  Omezení musí být pro počet aplikací ve větším měřítku, takže rozdělujeme podle počtu aplikací.

Omezení paměti = (celková paměť PŘÍZe/paměti prováděcího modulu)/počet aplikací

**Vypočítat omezení procesoru** – omezení CPU se vypočítává jako celkový počet virtuálních jader dělený počtem jader na vykonavatele.  Pro každý fyzický jádro je k dispozici 2 virtuální jádra.  Podobně jako u omezení paměti je nutné rozdělit počet aplikací.

- virtuální jádra = (uzly v clusteru * # fyzických jader v uzlu * 2)
- Omezení procesoru = (celkový počet virtuálních jader/počet jader na vykonavatele)/počet aplikací

**Nastavit počet prováděcích** modulů – parametr NUM-prováděče se určí tak, že se vyberou minimálně omezení paměti a omezení CPU. 

NUM-exekutors = min (celkový počet virtuálních jader/počet jader na vykonavatele, dostupná paměť PŘÍZ/prováděcí modul – paměť)

Nastavení většího počtu prováděcích modulů nemusí nutně zvyšovat výkon.  Měli byste zvážit, že přidáním dalších prováděcích modulů přidáte další režii pro každý další prováděcí modul, což může způsobit snížení výkonu.  Počet – vykonavatelé jsou vázány prostředky clusteru.    

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že aktuálně máte cluster tvořený 8 D4v2 uzly, na kterých běží 2 aplikace, včetně toho, který budete spouštět.  

**Krok 1: určení, kolik aplikací běží ve vašem clusteru** – víte, že máte ve svém clusteru 2 aplikace, včetně toho, který budete spouštět.  

**Krok 2: nastavení prováděcího modulu-paměti** – pro účely tohoto příkladu určíme, že 6 GB prováděcích paměti bude stačit pro úlohy náročné na vstupně-výstupní operace.  

prováděcí modul – paměť = 6 GB

**Krok 3: nastavení prováděcích jader** – vzhledem k tomu, že se jedná o náročnou vstupně-výstupní úlohu, můžeme nastavit počet jader pro každý prováděcí modul na 4.  Nastavení jader na jeden prováděcí modul na větší než 4 může způsobit problémy uvolňování paměti.  

prováděcí modul – jádra = 4

**Krok 4: určení množství paměti příze v clusteru** – navigujte na Ambari a zjistěte, že každý D4V2 má 25 GB paměti příze.  Vzhledem k tomu, že existují 8 uzlů, je dostupná paměť PŘÍZe vynásobena 8.

- Celková paměť PŘÍZ = uzly * PŘÍZ paměť * na uzel
- Celková paměť PŘÍZ = 8 uzlů * 25 GB = 200 GB

**Krok 5: výpočet počtu prováděcích** modulů – parametr počet-prováděcích modulů se určuje tak, že se vyberou minimálně omezení paměti a omezení procesoru dělené počtem aplikací spuštěných ve Sparku.    

**Vypočítat omezení paměti** – omezení paměti je vypočítáno jako celková paměť příze dělená pamětí na vykonavatele.

- Omezení paměti = (celková paměť PŘÍZe/paměti prováděcího modulu)/počet aplikací
- Omezení paměti = (200 GB/6 GB)/2
- Omezení paměti = 16 (zaokrouhleno)

**Vypočítat omezení procesoru** – omezení CPU se vypočítá jako celkový počet jader příze dělený počtem jader na vykonavatele.

- PŘÍZe jader = uzly v clusteru * počet jader na uzel * 2
- Jader PŘÍZe = 8 uzlů × 8 jader na D14 × 2 = 128
- Omezení procesoru = (celkový počet jader na jeden prováděcí modul/počet jader na vykonavatele)/počet aplikací
- Omezení procesoru = (128/4)/2
- Omezení procesoru = 16

**Nastavit moduly pro číslování**

- NUM-prováděče = min (omezení paměti, omezení procesoru)
- NUM-prováděče = min (16, 16)
- počet – vykonavatelé = 16

