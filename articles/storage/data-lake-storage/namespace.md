---
title: Azure Data Lake Storage Gen2 ve verzi Preview hierarchické Namespace
description: Popisuje pojem hierarchického oboru názvů pro verzi Preview služby Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: b5d3a735bd490468e989ac29c9f082475cc7eab3
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283359"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 Preview hierarchické – obor názvů

Klíče mechanismus, který umožňuje Azure Data Lake Storage Gen2 Preview k zajištění výkonu systému souborů v objektu škálování úložiště a ceny, je přidání **hierarchického oboru názvů**. To umožňuje kolekci objektů nebo souborů v rámci účet, který chcete být uspořádány do hierarchie adresářů a podadresářů vnořené stejným způsobem, že je uspořádaný systému souborů ve vašem počítači. Účet úložiště se stane s hierarchického oboru názvů povolené schopný poskytnout škálovatelnost a efektivitu nákladů pro úložiště objektů s sémantiku systému souborů, které jsou známé analytics modulů a architektur.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Výhody hierarchického oboru názvů

> [!NOTE]
> Ve verzi public preview služby Azure Data Lake Storage Gen2 některé funkce uvedené níže mohou lišit v jejich dostupnost. Nové funkce a oblasti vydané během do programu preview, tyto informace se budou prostřednictvím vývěsky prostřednictvím naší vyhrazené skupiny Yammeru.  

Systémy souborů, které implementují hierarchického oboru názvů přes data objektů blob přidružených následující výhody:

- **Atomic directory manipulace:** úložiště objektů přibližný hierarchii adresářů přijetím konvence vkládání lomítka (/) v názvu objektu k označení segmenty cesty. Zatímco tato konvence funguje pro uspořádání objektů, představuje tato konvence žádné pomoc v případě akce, jako je přesunutí, přejmenování nebo odstranění adresáře. Bez skutečné adresářů musí aplikace potenciálně zpracovat miliony jednotlivých objektů BLOB k dosažení úrovně adresář úlohy. Naopak hierarchického oboru názvů zpracovává tyto úlohy aktualizací jednu položku (nadřazený adresář).

    Je významný zejména pro mnoho architektur analýzy velkých objemů dat. Tento výrazné optimalizaci. Nástroje, jako je Spark, Hive, atd. často zapisovat výstup do dočasného umístění a potom přejmenujte umístění na závěr úlohy. Bez hierarchického oboru názvů přejmenujte může obvykle trvat déle, než se zpracování analýzy samotný. Nižší latenci a úlohy se rovná snížení celkových nákladů na vlastnictví (TCO) pro úlohy související s analýzou.

- **Styl známých rozhraní:** systémy souborů jsou dobře pochopitelné i vývojářům a uživatelům. Není nutné další nové úložiště paradigma při přesunu do cloudu jako rozhraní systému souborů, který je zveřejněn prostřednictvím Data Lake Storage Gen2 je stejný paradigma používají počítače, velkých i malých.

Jedním z důvodů, že úložiště objektů nejsou podporované v minulosti hierarchické obory názvů je, že hierarchické obory názvů omezené škálování. Data Lake Storage Gen2 hierarchického oboru názvů však škáluje se lineárně a mít nežádoucí vliv datovou kapacitou nebo výkonem.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Pokud chcete povolit hierarchického oboru názvů

Zapnutí hierarchického oboru názvů se doporučuje pro úlohy úložiště, které jsou určené pro systémy souborů, které pracují s adresáře. To zahrnuje všechny úlohy, které jsou určené primárně pro zpracování analýzy. Datové sady, které vyžadují vysokou míru organizace bude přínosem také tím, že hierarchického oboru názvů.

Důvody pro povolení hierarchického oboru názvů se určují analýzou celkových nákladů na vlastnictví. Obecně řečeno vylepšení v úloze latenci kvůli zrychlení úložiště bude vyžadovat výpočetní prostředky pro méně času. Z důvodu manipulaci s atomic adresář, který je povolený podle hierarchického oboru názvů se dá vylepšit latence pro mnoho úloh. V mnoha úloh výpočetních prostředků představuje > 85 % celkových nákladů a tak i mírné snížení latence zatížení odpovídá značné úspory celkových nákladů na vlastnictví. Dokonce i v případech, kde povolení hierarchického oboru názvů zvyšuje náklady na úložiště celkových nákladů na vlastnictví stále snížit z důvodu snížení výpočetní náklady.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Pokud chcete zakázat hierarchického oboru názvů

Některé úlohy úložiště objektu nelze získat žádnou výhodu povolením hierarchického oboru názvů. Tyto úlohy příklady zálohování, úložiště obrázků a dalších aplikací, kde je objekt organizace ukládají odděleně od samotných objektech (*třeba*, v samostatné databáze).

> [!NOTE]
> S verzí preview Pokud povolíte hierarchického oboru názvů, neexistuje žádná interoperability dat nebo operace mezi objektem Blob a REST API služby Data Lake Storage Gen2. Tato funkce bude přidána během období preview.

## <a name="next-steps"></a>Další postup

- [Vytvoření účtu úložiště](./quickstart-create-account.md)