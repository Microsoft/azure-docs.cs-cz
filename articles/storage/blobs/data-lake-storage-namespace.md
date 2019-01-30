---
title: Azure Data Lake Storage Gen2 ve verzi Preview hierarchické Namespace
description: Popisuje pojem hierarchického oboru názvů pro verzi Preview služby Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 967e24ae6e004fe6ce2b1c0aa6c039f46be2598c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244500"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 Preview hierarchické – obor názvů

Klíče mechanismus, který umožňuje Azure Data Lake Storage Gen2 Preview k zajištění výkonu systému souborů v objektu škálování úložiště a ceny, je přidání **hierarchického oboru názvů**. To umožňuje kolekci objektů nebo souborů v rámci účet, který chcete být uspořádány do hierarchie adresářů a podadresářů vnořené stejným způsobem, že je uspořádaný systému souborů ve vašem počítači. Účet úložiště se stane s hierarchického oboru názvů povolené schopný poskytnout škálovatelnost a efektivitu nákladů pro úložiště objektů s sémantiku systému souborů, které jsou známé analytics modulů a architektur.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Výhody hierarchického oboru názvů

Systémy souborů, které implementují hierarchického oboru názvů přes data objektů blob přidružených následující výhody:

- **Manipulace s Atomic adresáře:** Úložiště objektů přibližný hierarchii adresářů přijetím konvence vkládání lomítka (/) v názvu objektu k označení segmenty cesty. Zatímco tato konvence funguje pro uspořádání objektů, představuje tato konvence žádné pomoc v případě akce, jako je přesunutí, přejmenování nebo odstranění adresáře. Bez skutečné adresářů musí aplikace potenciálně zpracovat miliony jednotlivých objektů BLOB k dosažení úrovně adresář úlohy. Naopak hierarchického oboru názvů zpracovává tyto úlohy aktualizací jednu položku (nadřazený adresář).

    Je významný zejména pro mnoho architektur analýzy velkých objemů dat. Tento výrazné optimalizaci. Nástroje, jako je Spark, Hive, atd. často zapisovat výstup do dočasného umístění a potom přejmenujte umístění na závěr úlohy. Bez hierarchického oboru názvů přejmenujte může obvykle trvat déle, než se zpracování analýzy samotný. Nižší latenci a úlohy se rovná snížení celkových nákladů na vlastnictví (TCO) pro úlohy související s analýzou.

- **Styl známému rozhraní služby:** Systémy souborů jsou dobře pochopitelné vývojářům a uživatelům. Není nutné další nové úložiště paradigma při přesunu do cloudu jako rozhraní systému souborů, který je zveřejněn prostřednictvím Data Lake Storage Gen2 je stejný paradigma používají počítače, velkých i malých.

Jedním z důvodů, že úložiště objektů nejsou podporované v minulosti hierarchické obory názvů je, že hierarchické obory názvů omezené škálování. Data Lake Storage Gen2 hierarchického oboru názvů však škáluje se lineárně a mít nežádoucí vliv datovou kapacitou nebo výkonem.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Pokud chcete povolit hierarchického oboru názvů

Zapnutí hierarchického oboru názvů se doporučuje pro úlohy úložiště, které jsou určené pro systémy souborů, které pracují s adresáře. To zahrnuje všechny úlohy, které jsou určené primárně pro zpracování analýzy. Datové sady, které vyžadují vysokou míru organizace bude přínosem také tím, že hierarchického oboru názvů.

Důvody pro povolení hierarchického oboru názvů se určují analýzou celkových nákladů na vlastnictví. Obecně řečeno vylepšení v úloze latenci kvůli zrychlení úložiště bude vyžadovat výpočetní prostředky pro méně času. Z důvodu manipulaci s atomic adresář, který je povolený podle hierarchického oboru názvů se dá vylepšit latence pro mnoho úloh. V mnoha úloh výpočetních prostředků představuje > 85 % celkových nákladů a tak i mírné snížení latence zatížení odpovídá značné úspory celkových nákladů na vlastnictví. Dokonce i v případech, kde povolení hierarchického oboru názvů zvyšuje náklady na úložiště celkových nákladů na vlastnictví stále snížit z důvodu snížení výpočetní náklady.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Pokud chcete zakázat hierarchického oboru názvů

Některé úlohy úložiště objektu nelze získat žádnou výhodu povolením hierarchického oboru názvů. Tyto úlohy příklady zálohování, úložiště obrázků a dalších aplikací, kde je objekt organizace ukládají odděleně od samotných objektech (*třeba*, v samostatné databáze).


## <a name="next-steps"></a>Další postup

- [Vytvoření účtu úložiště](./data-lake-storage-quickstart-create-account.md)
