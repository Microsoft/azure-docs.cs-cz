---
title: Azure Data Lake Storage Gen2 hierarchický obor názvů
description: Popisuje koncept hierarchického oboru názvů pro Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 0b98892bd31b097e3dc217d54f52f12550599d32
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847148"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 hierarchický obor názvů

Klíčový mechanismus, který umožňuje Azure Data Lake Storage Gen2 poskytovat výkon systému souborů při škálování úložiště objektů a ceny jsou přidání **hierarchického oboru názvů**. Díky tomu může být kolekce objektů nebo souborů v rámci účtu uspořádána do hierarchie adresářů a vnořených podadresářů stejným způsobem, jakým je systém souborů v počítači uspořádán. S povoleným hierarchickým oborem názvů je účet úložiště schopný zajistit škálovatelnost a cenovou efektivitu úložiště objektů s sémantikou systému souborů, které jsou známé pro analytické stroje a architektury.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Výhody hierarchického oboru názvů

K systémům souborů, které implementují hierarchický obor názvů přes data objektů blob, jsou přidružené následující výhody:

- **Manipulace s atomovoum adresářem:** Objekt ukládá přibližnou hierarchii adresářů tím, že přijímá v názvu objektu konvenci vkládání lomítka (/) a označuje segmenty cest. I když tato konvence funguje pro organizování objektů, neposkytuje Tato konvence žádnou pomoc pro akce, jako je přesunutí, přejmenování nebo odstranění adresáře. Bez reálných adresářů musí aplikace zpracovat potenciálně miliony individuálních objektů blob, aby dosáhli úloh na úrovni adresáře. Naproti tomu hierarchické obor názvů zpracovává tyto úlohy aktualizací jediné položky (nadřazený adresář).

    Tato výrazné optimalizace je zvláště důležitá pro mnoho analytických rozhraní pro velké objemy dat. Nástroje, jako je například podregistr, Spark atd., často zapisují výstup do dočasných umístění a přejmenují umístění na závěr úlohy. Bez hierarchického oboru názvů může tento přejmenování často trvat déle než samotný proces analýzy. Nižší latence úlohy se rovná nižší celkové náklady na vlastnictví (celkových nákladů na vlastnictví) pro úlohy analýzy.

- **Známý styl rozhraní:** Pro vývojáře a uživatele se dobře rozumí systémy souborů. Při přechodu do cloudu se nemusíte učit nové paradigma úložiště, protože rozhraní systému souborů vystavené nástrojem Data Lake Storage Gen2 je stejné paradigma používané počítači, velkými a malými.

Jeden z důvodů, proč úložiště objektů nehistoricky podporovaly hierarchický obor názvů, je, že hierarchické obor názvů omezuje škálování. Data Lake Storage Gen2 hierarchické obor názvů se ale škáluje lineárně a nesnižuje ani datovou kapacitu ani výkon.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Kdy povolit hierarchický obor názvů

Doporučujeme, abyste zapnuli hierarchický obor názvů pro úlohy úložiště, které jsou navržené pro souborové systémy, které pracují s adresáři. To zahrnuje všechny úlohy, které jsou primárně určené pro zpracování analýz. Datové sady, které vyžadují vysoký stupeň organizace, budou také přínosné povolením hierarchického oboru názvů.

Důvody pro povolení hierarchického oboru názvů jsou určeny analýzou nákladů na vlastnictví. Obecně řečeno, vylepšení latence úloh v důsledku akcelerace úložiště bude vyžadovat pro kratší dobu výpočetní prostředky. Kvůli manipulaci s atomovou službou, která je povolená hierarchickým oborem názvů, se dá zlepšit latence pro mnoho úloh. V mnoha úlohách výpočetní prostředky představuje > 85% celkových nákladů, takže i mírné snížení latence úloh se rovná významnému množství úspor nákladů na celkové náklady. I v případech, kdy povolení hierarchického oboru názvů zvyšuje náklady na úložiště, je celkové náklady na vlastnictví stále snížené z důvodu snížených výpočetních nákladů.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Kdy se má zablokovat hierarchický obor názvů

Některé úlohy úložiště objektů nemusí získat žádné výhody tím, že umožňují hierarchický obor názvů. Mezi příklady patří zálohy, úložiště obrázků a další aplikace, ve kterých je organizace objektů uložená odděleně od samotných objektů (například v samostatné databázi).

## <a name="next-steps"></a>Další postup

- [Vytvoření účtu úložiště](./data-lake-storage-quickstart-create-account.md)
