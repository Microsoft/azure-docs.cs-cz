---
title: Přehled prostorové analýzy
titleSuffix: Azure Cognitive Services
description: Tento dokument vysvětluje základní koncepty a funkce Počítačové zpracování obrazuho kontejneru prostorových analýz.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100575363"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Přehled Počítačové zpracování obrazu prostorové analýzy

Počítačové zpracování obrazu prostorová analýza je nová funkce služby Azure Cognitive Services Počítačové zpracování obrazu, která organizacím pomáhá maximalizovat hodnotu jejich fyzických prostorů díky porozumění pohybům a přítomnosti uživatelů v dané oblasti. Umožňuje ingestování videa z CCTV nebo sledovacích kamer, spouštění operací AI k extrakci poznatků z datových proudů videa a generování událostí, které budou používat jiné systémy. Pomocí vstupu z datového proudu kamery může operace AI provádět například počet uživatelů, kteří zadávají nějaké místo nebo měří dodržování předpisů s maskou obličeje a sociálními distancing pokyny.

## <a name="the-basics-of-spatial-analysis"></a>Základy prostorové analýzy

V současné době se základní operace analýzy prostorových analýz vytvářejí na kanálu, který ingestuje video, detekuje lidi ve videu, sleduje lidi při jejich přesouvání v průběhu času a generuje události v případě, že lidé komunikují s oblastmi, které vás zajímají.

## <a name="spatial-analysis-terms"></a>Výrazy prostorové analýzy

| Označení | Definice |
|------|------------|
| Detekce lidí | Tato součást odpoví na otázku "kde jsou lidé z tohoto obrázku"? Vyhledá lidi v imagi a předá ohraničovací rámeček, který indikuje umístění jednotlivých osob do komponenty sledování osob. |
| Sledování lidí | Tato součást spojuje detekci osob v průběhu času, protože se lidé pohybují před fotoaparátem. Používá k tomu časovou logiku, jak lidé obvykle přesunou a základní informace o celkovém vzhledu lidí. Nesleduje lidi v různých fotoaparátech. Pokud uživatel existuje pole zobrazení z fotoaparátu po delší dobu než minutu a pak znovu přejde do zobrazení kamery, systém tuto hodnotu vnímat jako novou osobu. Sledování lidí nejedinečným způsobem neidentifikuje jednotlivce napříč fotoaparáty. Nepoužívá rozpoznávání obličeje ani sledování Gait. |
| Detekce masky obličeje | Tato součást detekuje umístění plochy osoby v poli kamery fotoaparátu a identifikuje přítomnost masky obličeje. K tomu je potřeba, aby operace AI prověřování imagí z videa. tam, kde je zjištěna ploška, služba poskytuje ohraničující rámeček kolem plochy. Pomocí schopností detekce objektů identifikuje přítomnost masek obličeje v ohraničujícím rámečku. Detekce masky obličeje nezahrnuje odlišení jedné plošky od jiné plošky, předvídání nebo klasifikaci atributů obličeje nebo provádění rozpoznávání obličeje. |
| Oblast zájmu | Toto je zóna nebo řádek definovaný ve vstupním videu jako součást konfigurace. Když osoba komunikuje s oblastí videa, vygeneruje systém událost. Například pro operaci PersonCrossingLine je ve videu definován řádek. Když osoba přeškrtne, vygeneruje se událost. |
| Událost | Událost je primární výstup pro prostorovou analýzu. Každá operace vysílá konkrétní událost buď pravidelně (např. jednou za minutu) nebo při výskytu konkrétního triggeru. Událost obsahuje informace o tom, co se stalo ve vstupním videu, ale neobsahuje žádné obrázky ani video. Například operace PeopleCount může generovat událost obsahující aktualizovaný počet pokaždé, když se počet osob změní (trigger) nebo jednou za minutu (pravidelně). |

## <a name="responsible-use-of-spatial-analysis-technology"></a>Zodpovědné využívání technologie prostorových analýz

Pokud se chcete dozvědět, jak používat zodpovědnou technologie pro prostorovou analýzu, přečtěte [si poznámku o transparentnosti](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Poznámky transparentnosti Microsoftu mají za úkol porozumět tomu, jak naše technologie AI funguje, takže vlastníci systému můžou mít vliv na výkon a chování systému a také na důležité úvahy o celém systému, včetně technologie, lidí a prostředí.

## <a name="spatial-analysis-gating-for-public-preview"></a>Uzavírání prostorových analýz pro verzi Public Preview

Abychom zajistili, že se prostorová analýza používá pro scénáře, pro které byla navržena, zpřístupňujeme tuto technologii zákazníkům prostřednictvím procesu aplikace. Pokud chcete získat přístup k prostorovým analýzám, budete muset začít vyplňovat online formulář pro příjem dat. [Začněte svou aplikaci tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Přístup k prostorové analýze Public Preview se může vztahovat jenom na základě našich kritérií způsobilosti, dozvíte ČSFD procesu a dostupnosti pro podporu omezeného počtu zákazníků v rámci této ověřované verze Preview. Ve verzi Public Preview hledáme zákazníky, kteří mají významnou relaci s Microsoftem, zájem o spolupráci v doporučených případech použití a další scénáře, které jsou v souladu s našimi zodpovědnými závazky AI.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s kontejnerem prostorových analýz](spatial-analysis-container.md)