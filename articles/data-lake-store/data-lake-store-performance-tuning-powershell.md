---
title: Pokyny pro používání prostředí Powershell s Data Lake Store ladění výkonu | Microsoft Docs
description: Tipy pro zlepšení výkonu při použití Azure PowerShell s Data Lake Store
services: data-lake-store
documentationcenter: ''
author: stewu
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 7b19972ed4a75ac899a4b78b28ab36ba305a5a64
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Pokyny pro používání prostředí PowerShell s Azure Data Lake Store optimalizace výkonu

Tento článek obsahuje seznam vlastností, které lze ladit získat lepší výkon při použití prostředí PowerShell pro práci s Data Lake Store:

## <a name="performance-related-properties"></a>Vlastnosti související s výkonem

| Vlastnost            | Výchozí | Popis |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Pomocí tohoto parametru můžete zvolit počet paralelních vláken pro nahrávání nebo stahování jednotlivých souborů. Toto číslo představuje maximální počet vláken, které mohou být přiděleny na soubor, ale může získat méně vláken v závislosti na váš scénář (například pokud nahráváte soubor 1 KB, můžete získat jedno vlákno i v případě, že je požádat o 20 podprocesů).  |
| ConcurrentFileCount | 10      | Tento parametr je určený zejména pro nahrávání nebo stahování složek. Tento parametr určuje počet souborů, které lze souběžně nahrávat nebo stahovat. Toto číslo představuje maximální počet souběžných souborů, které je možné nahrát nebo stáhnout najednou, ale může získat méně souběžnosti v závislosti na váš scénář (například pokud nahráváte dva soubory, získáte dvě nahrávání souběžných soubory i v případě, že se požádat pro 15). |

**Příklad**

Tento příkaz stáhne soubory z Azure Data Lake Store na místní jednotku a použije k tomu 20 vláken na soubor a 100 souběžných souborů.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Jak určit hodnoty pro tyto vlastnosti?

Další otázky, které byste mohli je tom, jak zjistit, co hodnota zajistit pro vlastnosti související s výkonem. Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určení celkového počtu vláken** – Měli byste začít výpočtem celkového počtu vláken, která se mají použít. V rámci obecných pokynů měli byste použít šesti vláken pro každý počet fyzických jader.

        Total thread count = total physical cores * 6

    **Příklad**

    Předpokládejme, že spouštíte příkazy prostředí PowerShell z virtuálního počítače D14, který má 16 jader.

        Total thread count = 16 cores * 6 = 96 threads


* **Krok 2: Výpočet hodnoty PerFileThreadCount** – Hodnotu PerFileThreadCount vypočítáme na základě velikosti souborů. Pro soubory je menší než 2,5 GB není třeba měnit tento parametr, protože stačí výchozí hodnotu 10. Pro soubory větší než 2,5 GB by měl použít 10 vláken jako základ pro první 2,5 GB a přidejte 1 vlákno pro každý další 256 MB nárůst velikosti souboru. Pokud kopírujete složku se soubory velmi rozdílných velikostí, zvažte jejich seskupení podle podobných velikostí. Velmi rozdílné velikosti souborů mohou způsobit, že výkon nebude optimální. Pokud není možné seskupit soubory podle podobných velikostí, měli byste hodnotu PerFileThreadCount nastavit podle největší velikosti souboru.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Příklad**

    Za předpokladu, že máte 100 souborů od 1 GB až 10 GB, použijeme 10 GB jako největší velikost souboru pro rovnice, který by pro čtení, podobně jako tento.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Krok 3: Vypočítat ConcurrentFilecount** – použít počet celkový počet vláken a PerFileThreadCount k výpočtu ConcurrentFileCount podle následujícího vzorce:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Příklad**

    Podle ukázkových hodnot, které používáme:

        96 = 40 * ConcurrentFileCount

    Takže hodnota **ConcurrentFileCount** je **2.4**, což můžeme zaokrouhlit na **2**.

## <a name="further-tuning"></a>Další ladění

Možná budete vyžadovat další ladění, protože existuje velká škála velikostí souborů, se kterými můžete pracovat. Předchozí výpočtu funguje dobře v případě, že všechny nebo většinu soubory jsou větší a blíže rozsah 10 GB. Pokud ale bude existovat mnoho různých velikostí souborů a mnoho jich bude menších, mohli byste hodnotu PerFileThreadCount snížit. Díky snížení hodnoty PerFileThreadCount můžeme zvýšit hodnotu ConcurrentFileCount. Ano Pokud předpokládáme, že většina našich soubory je menší v rozsahu 5 GB, jsme vrátit naše výpočet:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Ano **ConcurrentFileCount** 96/20, což je 4.8, se stane zaokrouhlen **4**.

Tato nastavení můžete dále ladit zvýšením nebo snížením hodnoty **PerFileThreadCount** v závislosti na rozložení velikostí souborů.

### <a name="limitation"></a>Omezení

* **Počet souborů je menší než hodnota ConcurrentFileCount**: Pokud je počet souborů, které nahráváte, menší než hodnota **ConcurrentFileCount**, kterou jste vypočítali, měli byste snížit hodnotu **ConcurrentFileCount** tak, aby se rovnala počtu souborů. Zbývající vlákna můžete použít ke zvýšení hodnoty **PerFileThreadCount**.

* **Příliš mnoho vláken**: Pokud příliš zvýšíte počet vláken a nezvětšíte velikost clusteru, riskujete tím snížení výkonu. Může docházet ke kolizím při přepínání kontextu na procesoru.

* **Nedostatečná souběžnost**: Pokud souběžnost není dostatečná, může to být způsobeno malou velikostí clusteru. Můžete zvýšit počet uzlů v clusteru, která umožňuje více souběžnosti.

* **Chyby omezování**: Pokud je souběžnost příliš vysoká, může docházet k chybám omezování. Pokud dochází k chybám omezování, měli byste buď snížit souběžnost, nebo nás kontaktovat.

## <a name="next-steps"></a>Další postup
* [Použití Azure Data Lake Store pro potřeby velkého objemu dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

