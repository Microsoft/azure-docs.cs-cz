---
title: Ladění výkonu Azure Data Lake Storage Gen1 – PowerShell
description: Tipy, jak zvýšit výkon při použití Azure PowerShell s Azure Data Lake Storage Gen1
author: stewu
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: f5e6f6601a563a387476e4e2eaf353c8bef384ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85504691"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Pokyny k ladění výkonu pro použití PowerShellu s Azure Data Lake Storage Gen1

Tento článek popisuje vlastnosti, které můžete ladit, abyste získali lepší výkon a při práci s Data Lake Storage Gen1 používáte PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Vlastnosti související s výkonem

| Vlastnost            | Výchozí | Description |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Pomocí tohoto parametru můžete zvolit počet paralelních vláken pro nahrávání nebo stahování jednotlivých souborů. Toto číslo představuje maximální počet vláken, která se dají přidělit na jeden soubor, ale v závislosti na vašem scénáři můžete dostat méně vláken (například pokud nahráváte soubor o velikosti 1 KB, dostanete jedno vlákno i v případě, že požádáte o 20 vláken).  |
| ConcurrentFileCount | 10      | Tento parametr je určený zejména pro nahrávání nebo stahování složek. Tento parametr určuje počet souborů, které lze souběžně nahrávat nebo stahovat. Toto číslo představuje maximální počet souběžných souborů, které je možné nahrávat nebo stahovat najednou, ale v závislosti na vašem scénáři můžete dosáhnout nižší souběžnosti (například pokud nahráváte dva soubory, získáte dvě souběžná nahrávání souborů i v případě, že požádáte o 15). |

**Příklad:**

Tento příkaz stáhne soubory z Data Lake Storage Gen1 na místní disk uživatele pomocí 20 vláken na soubor a 100 souběžných souborů.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Určení hodnot vlastností

Další otázkou, kterou můžete mít, je určit, jakou hodnotu má poskytnout vlastnost související s výkonem. Tady je několik rad, kterými se můžete řídit.

* **Krok 1: určení celkového počtu vláken** – Začněte tak, že vypočítáte celkový počet vláken, který se má použít. V rámci obecných pokynů byste měli pro každý fyzický jádro použít šest vláken.

    `Total thread count = total physical cores * 6`

    **Příklad:**

    Předpokládejme, že spouštíte příkazy prostředí PowerShell z virtuálního počítače D14, který má 16 jader.

    `Total thread count = 16 cores * 6 = 96 threads`

* **Krok 2: výpočet hodnoty perfilethreadcount** – vypočítáme naše hodnoty perfilethreadcount na základě velikosti souborů. Pro soubory menší než 2,5 GB není nutné tento parametr měnit, protože výchozí hodnota 10 je dostačující. Pro soubory větší než 2,5 GB byste měli použít 10 vláken jako základ pro prvních 2,5 GB a přidat 1 vlákno pro každé další zvýšení velikosti souboru 256-MB. Pokud kopírujete složku se soubory velmi rozdílných velikostí, zvažte jejich seskupení podle podobných velikostí. Velmi rozdílné velikosti souborů mohou způsobit, že výkon nebude optimální. Pokud není možné seskupit soubory podle podobných velikostí, měli byste hodnotu PerFileThreadCount nastavit podle největší velikosti souboru.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Příklad:**

    Za předpokladu, že máte 100 souborů v rozmezí od 1 do 10 GB, používáme jako největší velikost souboru pro rovnici 10 GB, což by bylo načteno jako následující.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Krok 3: výpočet hodnota concurrentfilecount** -použijte celkový počet vláken a hodnoty perfilethreadcount pro výpočet hodnota concurrentfilecount na základě následující rovnice:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Příklad:**

    Podle ukázkových hodnot, které používáme:

    `96 = 40 * ConcurrentFileCount`

    Takže hodnota **ConcurrentFileCount** je **2.4**, což můžeme zaokrouhlit na **2**.

## <a name="further-tuning"></a>Další ladění

Možná budete vyžadovat další ladění, protože existuje velká škála velikostí souborů, se kterými můžete pracovat. Předchozí výpočet funguje i v případě, že jsou všechny nebo nejvíc souborů větší a blíží se k rozsahu 10 GB. Pokud ale bude existovat mnoho různých velikostí souborů a mnoho jich bude menších, mohli byste hodnotu PerFileThreadCount snížit. Díky snížení hodnoty PerFileThreadCount můžeme zvýšit hodnotu ConcurrentFileCount. Pokud tedy předpokládáme, že většina našich souborů je v rozsahu 5 GB menší, můžeme opakovat náš výpočet:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

**Hodnota concurrentfilecount** se tedy bude 96/20, což je 4,8, zaokrouhleno na **4**.

Tato nastavení můžete dále ladit zvýšením nebo snížením hodnoty **PerFileThreadCount** v závislosti na rozložení velikostí souborů.

### <a name="limitation"></a>Omezení

* **Počet souborů je menší než hodnota ConcurrentFileCount**: Pokud je počet souborů, které nahráváte, menší než hodnota **ConcurrentFileCount**, kterou jste vypočítali, měli byste snížit hodnotu **ConcurrentFileCount** tak, aby se rovnala počtu souborů. Zbývající vlákna můžete použít ke zvýšení hodnoty **PerFileThreadCount**.

* **Příliš mnoho vláken**: Pokud příliš zvýšíte počet vláken a nezvětšíte velikost clusteru, riskujete tím snížení výkonu. Může docházet ke kolizím při přepínání kontextu na procesoru.

* **Nedostatečná souběžnost**: Pokud souběžnost není dostatečná, může to být způsobeno malou velikostí clusteru. Můžete zvýšit počet uzlů v clusteru, což vám poskytne víc souběžnosti.

* **Chyby omezování**: Pokud je souběžnost příliš vysoká, může docházet k chybám omezování. Pokud dochází k chybám omezování, měli byste buď snížit souběžnost, nebo nás kontaktovat.

## <a name="next-steps"></a>Další kroky

* [Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

