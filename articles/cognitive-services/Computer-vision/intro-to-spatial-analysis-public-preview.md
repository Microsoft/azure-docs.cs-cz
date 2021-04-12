---
title: Co je prostorová analýza?
titleSuffix: Azure Cognitive Services
description: Tento dokument vysvětluje základní koncepty a funkce Počítačové zpracování obrazuho kontejneru prostorových analýz.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284538"
---
# <a name="what-is-spatial-analysis"></a>Co je prostorová analýza?

Služba prostorové analýzy pomáhá organizacím maximalizovat jejich hodnoty fyzických prostorů tím, že bude rozumět pohybům a přítomnosti uživatelů v dané oblasti. Umožňuje ingestování videa z CCTV nebo sledovacích kamer, spouštění operací AI k extrakci poznatků z datových proudů videa a generování událostí, které budou používat jiné systémy. Pomocí vstupu z datového proudu kamery může operace AI provádět například počet uživatelů, kteří zadávají nějaké místo nebo měří dodržování předpisů s maskou obličeje a sociálními distancing pokyny.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Co dělá

Základní operace analýzy prostorových analýz jsou postavené na kanálu, který ingestuje video, detekuje lidi ve videu, sleduje lidi při jejich přesouvání v průběhu času a generuje události v případě, že lidé komunikují s oblastmi zájmu.

## <a name="spatial-analysis-features"></a>Funkce prostorové analýzy

| Funkce | Definice |
|------|------------|
| **Detekce lidí** | Tato součást odpoví na otázku "kde jsou lidé z této image?" Vyhledá lidi v imagi a předá ohraničovací rámeček, který indikuje umístění jednotlivých osob do komponenty sledování osob. |
| **Sledování lidí** | Tato součást spojuje detekci osob v průběhu času, protože se lidé pohybují před fotoaparátem. Používá k tomu časovou logiku, jak lidé obvykle přesunou a základní informace o celkovém vzhledu lidí. Nesleduje lidi v různých fotoaparátech. Pokud osoba ukončí pole zobrazení po dobu delší než minutu a pak znovu přejde do zobrazení kamery, systém tuto hodnotu vnímat jako novou osobu. Sledování lidí nejedinečným způsobem neidentifikuje jednotlivce napříč fotoaparáty. Nepoužívá rozpoznávání obličeje ani sledování Gait. |
| **Detekce masky obličeje** | Tato součást detekuje umístění plochy osoby v poli kamery fotoaparátu a identifikuje přítomnost masky obličeje. Operace AI prohledává obrázky z videa. tam, kde je zjištěna ploška, služba poskytuje ohraničující rámeček kolem plochy. Pomocí schopností detekce objektů identifikuje přítomnost masek obličeje v ohraničujícím rámečku. Detekce masky obličeje nezahrnuje odlišení jedné plošky od jiné plošky, předvídání nebo klasifikaci atributů obličeje nebo provádění rozpoznávání obličeje. |
| **Oblast zájmu** | Toto je uživatelem definovaná zóna nebo řádek ve vstupním snímku videa. Když osoba komunikuje s touto oblastí na videu, systém vygeneruje událost. Například pro operaci PersonCrossingLine je ve videu definován řádek. Když osoba přeškrtne, vygeneruje se událost. |
| **Událost** | Událost je primární výstup pro prostorovou analýzu. Každá operace vysílá konkrétní událost buď pravidelně (například jednou za minutu), nebo při každém výskytu konkrétního triggeru. Událost obsahuje informace o tom, co se stalo ve vstupním videu, ale neobsahuje žádné obrázky ani video. Například operace PeopleCount může generovat událost obsahující aktualizovaný počet pokaždé, když se počet osob změní (trigger) nebo jednou za minutu (pravidelně). |

## <a name="get-started"></a>Začínáme

### <a name="public-preview-gating"></a>Verze Public Preview

Abychom zajistili, že se prostorová analýza používá pro scénáře, pro které byla navržena, zpřístupňujeme tuto technologii zákazníkům prostřednictvím procesu aplikace. Pokud chcete získat přístup k prostorovým analýzám, musíte začít vyplněním formuláře online příjem. [Začněte svou aplikaci tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Přístup k prostorové analýze Public Preview se může vztahovat jenom na základě našich kritérií způsobilosti, dozvíte ČSFD procesu a dostupnosti pro podporu omezeného počtu zákazníků v rámci této ověřované verze Preview. Ve verzi Public Preview hledáme zákazníky, kteří mají významnou relaci s Microsoftem, zájem o spolupráci na doporučených případech použití a dalších scénářích, které se týkají našich zodpovědných závazků AI.

### <a name="follow-a-quickstart"></a>Postup při rychlém startu

Až budete mít přístup k prostorové analýze, pomocí [rychlého](spatial-analysis-container.md) startu nastavte kontejner a začněte analyzovat video.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Zodpovědné využívání technologie prostorových analýz

Pokud se chcete dozvědět, jak používat zodpovědnou technologie pro prostorovou analýzu, přečtěte [si poznámku o transparentnosti](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Poznámky transparentnosti Microsoftu mají za úkol porozumět tomu, jak naše technologie AI funguje, takže vlastníci systému můžou mít vliv na výkon a chování systému a také na důležité úvahy o celém systému, včetně technologie, lidí a prostředí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: kontejner prostorové analýzy](spatial-analysis-container.md)– ' ' ' ' ' ' ' ' ' ' ' ' '