---
title: Průvodce laděním výkonu pro použití Powershellu s Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Tipy, jak zlepšit výkon při použití Azure Powershellu s Azure Data Lake Storage Gen1
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
ms.openlocfilehash: 318f2b550e19f4b7f56a7b8cc592d34644dca644
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235598"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Průvodce laděním výkonu pro použití Powershellu s Azure Data Lake Storage Gen1

Tento článek obsahuje seznam vlastností, které můžete ladit pro zajištění lepšího výkonu při použití prostředí PowerShell pro práci s Azure Data Lake Storage Gen1:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Vlastnosti související s výkonem

| Vlastnost            | Výchozí | Popis |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Pomocí tohoto parametru můžete zvolit počet paralelních vláken pro nahrávání nebo stahování jednotlivých souborů. Toto číslo představuje maximální počet vláken, které mohou být přiděleny na soubor, ale můžete dostat méně vláken v závislosti na vašem scénáři (například pokud nahráváte soubor 1 KB, dostanete jedno vlákno i když požadujete třeba 20 vláken).  |
| ConcurrentFileCount | 10      | Tento parametr je určený zejména pro nahrávání nebo stahování složek. Tento parametr určuje počet souborů, které lze souběžně nahrávat nebo stahovat. Toto číslo představuje maximální počet souběžných souborů, které můžou nahrávat nebo stahovat najednou, ale v závislosti na vašem scénáři můžete dostat nižší souběžnost (například pokud nahráváte dva soubory, dostanete dva nahrávání souborů souběžně to i když požadujete pro 15). |

**Příklad**

Tento příkaz stáhne soubory z Data Lake Storage Gen1 uživatele místní disk pomocí 20 vláken na soubor a 100 souběžných souborů.

    Export-AzDataLakeStoreItem -AccountName <Data Lake Storage Gen1 account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Jak zjistím hodnoty těchto vlastností?

Další dotaz, může být je jak určit, jakou hodnotu poskytnout vlastnosti související s výkonem. Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určení celkového počtu vláken** – měli byste začít výpočtem celkového počtu vláken na použít. V rámci obecných pokynů byste měli použít 6 vláken na každé fyzické jádro.

        Total thread count = total physical cores * 6

    **Příklad**

    Předpokládejme, že spouštíte příkazy prostředí PowerShell z virtuálního počítače D14, který má 16 jader.

        Total thread count = 16 cores * 6 = 96 threads


* **Krok 2: Výpočet hodnoty PerFileThreadCount** -výpočtu PerFileThreadCount na základě velikosti souborů. Pro soubory menší než 2,5 GB není nutné tento parametr měnit, protože výchozí hodnota 10 je dostačující. Pro soubory větší než 2,5 GB by měl používat 10 vláken jako základ pro prvních 2,5 GB a přidat 1 vlákno za každý další 256 MB nárůst velikosti souboru. Pokud kopírujete složku se soubory velmi rozdílných velikostí, zvažte jejich seskupení podle podobných velikostí. Velmi rozdílné velikosti souborů mohou způsobit, že výkon nebude optimální. Pokud není možné seskupit soubory podle podobných velikostí, měli byste hodnotu PerFileThreadCount nastavit podle největší velikosti souboru.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Příklad**

    Za předpokladu, že máte 100 souborů od 1 GB až 10 GB, použijeme tedy 10 GB jako největší velikost souboru pro rovnice, který bude číst takto.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Krok 3: Vypočítat ConcurrentFilecount** – pomocí celkového počtu vláken a hodnoty PerFileThreadCount k výpočtu ConcurrentFileCount podle do následující rovnice:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Příklad**

    Podle ukázkových hodnot, které používáme:

        96 = 40 * ConcurrentFileCount

    Takže hodnota **ConcurrentFileCount** je **2.4**, což můžeme zaokrouhlit na **2**.

## <a name="further-tuning"></a>Další ladění

Možná budete vyžadovat další ladění, protože existuje velká škála velikostí souborů, se kterými můžete pracovat. Předchozí výpočty dobře fungují v případě všech nebo většiny souborů se velikost blíží rozsah 10 GB. Pokud ale bude existovat mnoho různých velikostí souborů a mnoho jich bude menších, mohli byste hodnotu PerFileThreadCount snížit. Díky snížení hodnoty PerFileThreadCount můžeme zvýšit hodnotu ConcurrentFileCount. Takže pokud předpokládáme, že většina našich souborů je menších a rozsah 5 GB, můžeme znovu provést výpočet:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Ano **ConcurrentFileCount** bude 96/20, což je 4,8 a po zaokrouhlení **4**.

Tato nastavení můžete dále ladit zvýšením nebo snížením hodnoty **PerFileThreadCount** v závislosti na rozložení velikostí souborů.

### <a name="limitation"></a>Omezení

* **Počet souborů, které je menší než hodnota ConcurrentFileCount**: Pokud je menší než počet souborů, které nahráváte **ConcurrentFileCount** , že jste vypočítali, měli byste snížit **ConcurrentFileCount** musí rovnat počtu souborů. Zbývající vlákna můžete použít ke zvýšení hodnoty **PerFileThreadCount**.

* **Příliš mnoho vláken**: Pokud zvýšíte počet vláken příliš mnoho bez nezvětšíte velikost clusteru, riskujete tím snížení výkonu. Může docházet ke kolizím při přepínání kontextu na procesoru.

* **Nedostatečná souběžnost**: Pokud souběžnost není dostatečná, může být příliš malá vašeho clusteru. Můžete zvýšit počet uzlů v clusteru, která poskytuje větší souběžnost.

* **Chybám omezování**: Vám může docházet k chybám omezování, pokud je souběžnost příliš vysoká. Pokud dochází k chybám omezování, měli byste buď snížit souběžnost, nebo nás kontaktovat.

## <a name="next-steps"></a>Další postup
* [Použití Azure Data Lake Storage Gen1 pro potřeby velkého objemu dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

