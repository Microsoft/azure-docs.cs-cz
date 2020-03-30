---
title: Optimalizace výkonu Úložiště datového jezera Azure – PowerShell
description: Tipy, jak zlepšit výkon při používání Azure PowerShellu s Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: c975af1799d427651b76bb9fde5ff765afed3f86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904568"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Pokyny pro ladění výkonu pro použití PowerShellu s Azure Data Lake Storage Gen1

Tento článek popisuje vlastnosti, které můžete vyladit získat lepší výkon při použití Prostředí PowerShell pro práci s Data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Vlastnosti související s výkonem

| Vlastnost            | Výchozí | Popis |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Pomocí tohoto parametru můžete zvolit počet paralelních vláken pro nahrávání nebo stahování jednotlivých souborů. Toto číslo představuje maximální podprocesy, které mohou být přiděleny na soubor, ale může získat méně podprocesů v závislosti na scénáři (například pokud nahráváte soubor 1 KB, získáte jedno vlákno, i když požádáte o 20 vláken).  |
| ConcurrentFileCount | 10      | Tento parametr je určený zejména pro nahrávání nebo stahování složek. Tento parametr určuje počet souborů, které lze souběžně nahrávat nebo stahovat. Toto číslo představuje maximální počet souběžných souborů, které lze nahrát nebo stáhnout najednou, ale v závislosti na vašem scénáři může dojít k menší souběžnosti (například pokud nahráváte dva soubory, získáte dva souběžné soubory, i když se zeptáte 15). |

**Příklad:**

Tento příkaz stahuje soubory z data lake storage gen1 na místní jednotku uživatele pomocí 20 vláken na soubor a 100 souběžných souborů.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Jak určit hodnoty vlastností

Další otázka, kterou můžete mít, je, jak určit, jakou hodnotu poskytnout pro vlastnosti související s výkonem. Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určete celkový počet vláken** - Začněte výpočtem celkového počtu vláken, který chcete použít. Jako obecné vodítko byste měli použít šest vláken pro každé fyzické jádro.

    `Total thread count = total physical cores * 6`

    **Příklad:**

    Předpokládejme, že spouštíte příkazy prostředí PowerShell z virtuálního počítače D14, který má 16 jader.

    `Total thread count = 16 cores * 6 = 96 threads`

* **Krok 2: Vypočítat PerFileThreadCount** - Vypočítáme naše PerFileThreadCount na základě velikosti souborů. U souborů menších než 2,5 GB není nutné tento parametr měnit, protože výchozí hodnota 10 je dostatečná. U souborů větších než 2,5 GB byste měli jako základ pro prvních 2,5 GB použít 1 vlákna a přidat 1 podproces pro každé další zvýšení velikosti souboru o 256 MB. Pokud kopírujete složku se soubory velmi rozdílných velikostí, zvažte jejich seskupení podle podobných velikostí. Velmi rozdílné velikosti souborů mohou způsobit, že výkon nebude optimální. Pokud není možné seskupit soubory podle podobných velikostí, měli byste hodnotu PerFileThreadCount nastavit podle největší velikosti souboru.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Příklad:**

    Za předpokladu, že máte 100 souborů v rozmezí od 1 GB do 10 GB, používáme 10 GB jako největší velikost souboru pro rovnici, která by četla jako následující.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Krok 3: Výpočet concurrentFilecount** - Použití celkového počtu vláken a PerFileThreadCount pro výpočet ConcurrentFileCount na základě následující rovnice:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Příklad:**

    Podle ukázkových hodnot, které používáme:

    `96 = 40 * ConcurrentFileCount`

    Takže hodnota **ConcurrentFileCount** je **2.4**, což můžeme zaokrouhlit na **2**.

## <a name="further-tuning"></a>Další ladění

Možná budete vyžadovat další ladění, protože existuje velká škála velikostí souborů, se kterými můžete pracovat. Předchozí výpočet funguje dobře, pokud jsou všechny nebo většina souborů větší a blíže k rozsahu 10 GB. Pokud ale bude existovat mnoho různých velikostí souborů a mnoho jich bude menších, mohli byste hodnotu PerFileThreadCount snížit. Díky snížení hodnoty PerFileThreadCount můžeme zvýšit hodnotu ConcurrentFileCount. Takže pokud budeme předpokládat, že většina našich souborů jsou menší v rozmezí 5-GB, můžeme předělat náš výpočet:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Takže **ConcurrentFileCount** se změní na 96/20, což je 4.8, zaokrouhleno na **4**.

Tato nastavení můžete dále ladit zvýšením nebo snížením hodnoty **PerFileThreadCount** v závislosti na rozložení velikostí souborů.

### <a name="limitation"></a>Omezení

* **Počet souborů je menší než hodnota ConcurrentFileCount**: Pokud je počet souborů, které nahráváte, menší než hodnota **ConcurrentFileCount**, kterou jste vypočítali, měli byste snížit hodnotu **ConcurrentFileCount** tak, aby se rovnala počtu souborů. Zbývající vlákna můžete použít ke zvýšení hodnoty **PerFileThreadCount**.

* **Příliš mnoho vláken**: Pokud příliš zvýšíte počet vláken a nezvětšíte velikost clusteru, riskujete tím snížení výkonu. Může docházet ke kolizím při přepínání kontextu na procesoru.

* **Nedostatečná souběžnost**: Pokud souběžnost není dostatečná, může to být způsobeno malou velikostí clusteru. Můžete zvýšit počet uzlů v clusteru, což vám dává větší souběžnost.

* **Chyby omezování**: Pokud je souběžnost příliš vysoká, může docházet k chybám omezování. Pokud dochází k chybám omezování, měli byste buď snížit souběžnost, nebo nás kontaktovat.

## <a name="next-steps"></a>Další kroky

* [Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

