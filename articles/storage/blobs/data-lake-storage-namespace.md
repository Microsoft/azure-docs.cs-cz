---
title: Hierarchický obor názvů Azure Data Lake Storage Gen2
description: Popisuje koncept hierarchického oboru názvů pro Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153073"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Hierarchický obor názvů Azure Data Lake Storage Gen2

Klíčovým mechanismem, který umožňuje Azure Data Lake Storage Gen2 poskytovat výkon systému souborů ve velkém měřítku úložiště objektů a ceny je přidání **hierarchického oboru názvů**. To umožňuje uspořádat kolekci objektů nebo souborů v rámci účtu do hierarchie adresářů a vnořených podadresářů stejným způsobem, jakým je uspořádán systém souborů v počítači. S povoleným hierarchickým oborem názvů je účet úložiště schopen poskytovat škálovatelnost a nákladovou efektivitu ukládání objektů pomocí sémantiky systému souborů, které jsou známé analytickým modulům a architekturám.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Výhody hierarchického oboru názvů

Následující výhody jsou přidruženy k systémům souborů, které implementují hierarchický obor názvů přes data objektů blob:

- **Atomová manipulace s adresářem:** Objekt ukládá aproximovat hierarchii adresářů přijetím konvence vkládání lomítka (/) do názvu objektu k označení segmentů cesty. Zatímco tato konvence funguje pro uspořádání objektů, konvence neposkytuje žádnou pomoc pro akce, jako je přesunutí, přejmenování nebo odstranění adresářů. Bez reálných adresářů musí aplikace zpracovat potenciálně miliony jednotlivých objektů BLOB, aby bylo možné dosáhnout úloh na úrovni adresáře. Naproti tomu hierarchický obor názvů zpracovává tyto úkoly aktualizací jedné položky (nadřazeného adresáře).

    Tato dramatická optimalizace je obzvláště významná pro mnoho architektur pro analýzu velkých objemů dat. Nástroje jako Hive, Spark atd. Bez hierarchického oboru názvů může toto přejmenování často trvat déle než samotný analytický proces. Nižší latence úloh se rovná nižším celkovým nákladům na vlastnictví (TCO) pro analytické úlohy.

- **Známý styl rozhraní:** Souborové systémy jsou dobře pochopeny vývojáři i uživateli. Není třeba se učit nové paradigma úložiště, když se přesunete do cloudu, protože rozhraní systému souborů vystavené úložištěm Dat Lake Gen2 je stejné paradigma, které používají počítače, velké i malé.

Jedním z důvodů, proč úložiště objektů historicky nepodporuje hierarchický obor názvů, je to, že hierarchický obor názvů omezuje měřítko. Hierarchický obor názvů Data Lake Storage Gen2 se však lineárně škáluje a nesnižuje kapacitu dat ani výkon.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Rozhodnutí, zda povolit hierarchický obor názvů

Po povolení hierarchického oboru názvů v účtu jej nelze vrátit zpět do plochého oboru názvů. Proto zvažte, zda má smysl povolit hierarchický obor názvů na základě povahy úloh úložiště objektů.

Některé úlohy nemusí získat žádné výhody povolením hierarchického oboru názvů. Mezi příklady patří zálohování, úložiště bitové kopie a další aplikace, kde je organizace objektů uložena odděleně od samotných objektů (například: v samostatné databázi). 

Zatímco podpora funkcí úložiště objektů Blob a ekosystému služeb Azure stále roste, stále existují některé funkce a služby Azure, které ještě nejsou podporované v účtech, které mají hierarchický obor názvů. Viz [Známé problémy](data-lake-storage-known-issues.md). 

Obecně doporučujeme zapnout hierarchický obor názvů pro úlohy úložiště, které jsou určeny pro systémy souborů, které manipulují s adresáři. To zahrnuje všechny úlohy, které jsou primárně pro zpracování analýzy. Datové sady, které vyžadují vysoký stupeň organizace, budou také těžit z povolení hierarchického oboru názvů.

Důvody pro povolení hierarchického oboru názvů jsou určeny analýzou nákladů na vlastnictví. Obecně řečeno, zlepšení latence úlohy z důvodu akcelerace úložiště bude vyžadovat výpočetní prostředky pro kratší dobu. Latence pro mnoho úloh může být vylepšena z důvodu atomické manipulace s adresářem, která je povolena hierarchickým oborem názvů. V mnoha úlohách představuje výpočetní prostředek > 85 % celkových nákladů, a proto i mírné snížení latence pracovního vytížení odpovídá značnému množství úspor celkových nákladů na věc. I v případech, kdy povolení hierarchického oboru názvů zvyšuje náklady na úložiště, je celkový celkový cena nebo tok stále snížen kvůli nižším nákladům na výpočetní výkon.

Chcete-li analyzovat rozdíly v cenách úložiště dat, cenách transakcí a cenách rezervací kapacity úložiště mezi účty, které mají plochý hierarchický obor názvů, a hierarchickým oborem názvů, přečtěte [si informace o cenách Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Další kroky

- [Vytvoření účtu úložiště](./data-lake-storage-quickstart-create-account.md)
