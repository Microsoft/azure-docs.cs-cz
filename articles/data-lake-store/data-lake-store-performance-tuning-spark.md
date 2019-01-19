---
title: Azure Data Lake Storage Gen1 výkonu Sparku pokyny k ladění | Dokumentace Microsoftu
description: Azure Data Lake Storage Gen1 výkonu Sparku pokyny k ladění
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
ms.openlocfilehash: dc92e7d2fcc911aeb6d92b91dd2d430af3c502ad
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401703"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Průvodce laděním výkonu pro Spark v HDInsight a Azure Data Lake Storage Gen1

Při ladění výkonu ve Sparku, je potřeba zvážit počet aplikací, které budou spuštěny v clusteru.  Ve výchozím nastavení, můžete spustit 4 aplikací souběžně v clusteru HDI (Poznámka: ve výchozím nastavení se může změnit).  Můžete se rozhodnout použít menší počet aplikací, takže můžete přepsat výchozí nastavení a informace clusteru použijte pro tyto aplikace.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage Gen1. Zobrazit [vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že se že povolení vzdálené plochy pro cluster.
* **Spuštění clusteru Spark v Data Lake Storage Gen1**.  Další informace najdete v tématu [clusteru HDInsight Spark použít k analýze dat v Data Lake Storage Gen1](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Pokyny pro Data Lake Storage Gen1 pro optimalizaci výkonu**.  Obecné informace o výkonu koncepty, najdete v části [Data Lake Storage Gen1 ladění Průvodce výkonem](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Parametry

Při spuštění úlohy Spark, tady jsou nejdůležitější nastavení, které můžete ladit pro zvýšení výkonu v Data Lake Storage Gen1:

* **Počet prováděcích modulů** – počet souběžných úloh, které mohou být provedeny.

* **Prováděcí modul paměti** -množství paměti přidělené na každý prováděcího modulu.

* **Prováděcí modul jader** – počet jader přidělené na každý prováděcího modulu.                     

**Počet prováděcích modulů** počet prováděcích modulů Nastaví maximální počet úloh, které můžou běžet paralelně.  Skutečný počet úloh, které můžou běžet paralelně ohraničená paměťových a Procesorových prostředků k dispozici ve vašem clusteru.

**Prováděcí modul paměti** Toto je množství paměti přidělené na každý prováděcího modulu.  Paměti potřebné pro každé prováděcí modul je závislá na úlohu.  Paměť pro složité operace musí být vyšší.  Jednoduché operací, jako je čtení a zápis bude nižší požadavky na paměť.  Velikost paměti pro každou prováděcí modul lze zobrazit v Ambari.  V Ambari přejděte na Spark a zobrazit na kartě konfigurace.  

**Prováděcí modul jader** tím se nastaví množství jader využívaných za prováděcího modulu, která určuje počet paralelních vláken, které můžou běžet na prováděcího modulu.  Například pokud prováděcí modul jádra = 2, pak každý prováděcí modul můžete spustit 2 paralelní úlohy v prováděcí modul.  Prováděcí modul jádra potřeby budou závislé na úlohu.  Vstupně-výstupní operace náročné úlohy nevyžadují velké množství paměti na jeden úkol, takže každý prováděcí modul dokáže zpracovat další paralelní úlohy.

Ve výchozím nastavení jsou definovány dvě virtuální jádra YARN pro každé fyzické jádro při spouštění Sparku v HDInsight.  Toto číslo obsahuje dobrou rovnováhu mezi souběžnosti a množství kontextu přepínání z více vláken.  

## <a name="guidance"></a>Doprovodné materiály

Při spouštění analytických úloh pro práci s daty v Data Lake Storage Gen1 Spark, doporučujeme pomocí nejnovější verze HDInsight s Data Lake Storage Gen1 co nejlepšího výkonu. Po další náročných na vstupně-výstupních operací vaší úlohy se může určité parametry nakonfigurovány ke zlepšení výkonu.  Data Lake Storage Gen1 je vysoce škálovatelné úložiště platforma, která dokáže zpracovat vysokou propustnost.  Pokud úloha obsahuje především pro čtení nebo zápisu, pak zvýšit souběžnost pro vstup a výstup do a z Data Lake Storage Gen1 může zvýšit výkon.

Pro zlepšení souběžnosti pro úlohy náročné na vstupně-výstupních operací několik obecné způsoby.

**Krok 1: Určení, kolik aplikace běží ve vašem clusteru** – měli byste vědět, kolik aplikace běží na clusteru, včetně aktuální.  Výchozí hodnoty pro každý Spark nastavení předpokládá, které jsou 4 aplikace, které běží souběžně.  Proto bude mít jenom 25 % clusteru k dispozici pro každou aplikaci.  Pokud chcete získat lepší výkon, můžete přepsat výchozí hodnoty změnou počtu prováděcích procesů.  

**Krok 2: Nastavení paměti prováděcí modul** – první věc, kterou chcete nastavit je paměť prováděcího modulu.  Paměť budou závislé na úlohu, která se má spustit.  Zlepšení souběžnosti přidělením méně paměti za prováděcího modulu.  Pokud se zobrazí nedostatek paměti výjimky při spuštění úlohy, měli byste zvýšit hodnotu tohoto parametru.  Jeden alternativou je načíst větší množství paměti pomocí funkce clusteru, který má větší objem paměti a zvyšuje velikost vašeho clusteru.  Větší množství paměti umožní další moduly provádění, kterou chcete použít, což znamená, že větší souběžnost.

**Krok 3: Nastavte prováděcího modulu jádra** – pro vstupně-výstupních operací úloh náročných na, které nemají komplexních operací, je dobré začít s vysokým počtem prováděcího modulu jádra na zvýšení počtu paralelních úkolů na prováděcí modul.  Nastavení prováděcího modulu jádra na 4 je dobrý začátek.   

    executor-cores = 4
Zvýšení počtu jader prováděcí modul vám poskytne další paralelismu tak můžete experimentovat s jinou prováděcího modulu jádra.  Pro úlohy, které mají složitějších operací by měla snížit počet jader na prováděcího modulu.  Pokud prováděcí modul jader je nastavená na vyšší hodnotu než 4, pak uvolňování paměti může být neefektivní a snížit výkon.

**Krok 4: Určit množství paměti YARN v clusteru** – tyto informace jsou k dispozici v Ambari.  Přejděte na YARN a zobrazit na kartě konfigurace.  V tomto okně se zobrazí paměti YARN.  
Poznámka: když jste v okně se zobrazí také výchozí velikost kontejneru YARN.  Velikost kontejneru YARN je stejný jako paměti na jeden parametr prováděcího modulu.

    Total YARN memory = nodes * YARN memory per node
**Krok 5: Vypočítat počet prováděcích modulů**

**Vypočítat omezení paměti** – parametr Počet prováděcích modulů je omezena paměti nebo procesoru.  Omezení paměti určuje množství dostupné paměti YARN pro vaši aplikaci.  By měla trvat celkové paměti YARN a dělení, který paměti prováděcího modulu.  Omezení musí být zrušit škálován pro počet aplikací, které tak jsme rozdělit podle počtu aplikací.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Výpočet procesoru omezení** – omezení procesor se vypočte jako celkový počet virtuálních jader, které jsou rozdělené podle počtu jader na prováděcího modulu.  Existují 2 virtuální jádra pro každé fyzické jádro.  Podobná omezení paměti, máme dělení podle počtu aplikací.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Nastavte počet prováděcích modulů** – počet prováděcích modulů parametru se určuje podle trvá minimálně omezení paměti a procesoru omezení. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Nastaví větší číslo počet prováděcích modulů nemusí se nutně zvýšit výkon.  Měli byste zvážit přidání další moduly provádění, přidá další režie pro každý další prováděcí modul, který může potenciálně dojít ke snížení výkonu.  Počet prováděcích modulů ohraničená prostředky clusteru.    

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že máte aktuálně cluster skládá z 8 D4v2 uzly, na kterém běží 2 aplikace, včetně toho, který chcete spustit.  

**Krok 1: Určení, kolik aplikace běží ve vašem clusteru** – víte, že máte 2 aplikací v clusteru, včetně toho, který chcete spustit.  

**Krok 2: Nastavení paměti prováděcí modul** – v tomto příkladu ověříme, že bude 6 GB paměti prováděcí modul dostatečná pro úlohy náročné na vstupně-výstupních operací.  

    executor-memory = 6GB
**Krok 3: Nastavte prováděcího modulu jádra** – protože úlohu náročné na vstupně-výstupních operací, jsme nastavili na počet jader pro každý prováděcí modul na 4.  Nastavování počet jader na prováděcí modul na větší než 4 může způsobit problémy kolekce uvolnění paměti.  

    executor-cores = 4
**Krok 4: Určit množství paměti YARN v clusteru** – přejdeme na Ambari a zjistěte, zda má každý D4v2 25 GB paměti YARN.  Protože je 8 uzlů, dostupné paměti YARN se násobí hodnotou 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Krok 5: Vypočítat počet prováděcích modulů** – počet prováděcích modulů parametru se určuje podle trvá minimálně omezení paměti a procesoru omezení dělený # aplikace běžící na Sparku.    

**Vypočítat omezení paměti** – omezení paměti se počítá jako celkové paměti YARN dělený paměti za prováděcího modulu.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Výpočet procesoru omezení** – The procesoru omezení se počítá jako yarn celkový počet jader, vydělí počtem jader na prováděcího modulu.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Počet prováděcích modulů set**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

