---
title: Seznámení s Počítačové zpracování obrazu prostorovou analýzou
titleSuffix: Azure Cognitive Services
description: Tento dokument vysvětluje základní koncepty a funkce Počítačové zpracování obrazuho kontejneru prostorových analýz.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f90e4e5e187977f0ee77a565ff9143902ea3a10d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736831"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Seznámení s Počítačové zpracování obrazu prostorovou analýzou

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

## <a name="example-use-cases-for-spatial-analysis"></a>Příklady případů použití pro prostorovou analýzu

Níže jsou uvedené příklady případů použití, které jsme měli na mysli, protože jsme navrhli a otestovali prostorovou analýzu.

**Dodržování předpisů pro sociální Distancing** – prostor kanceláře má několik kamer, které používají prostorovou analýzu k monitorování dodržování předpisů sociálními Distancingy měřením vzdálenosti mezi lidmi. Správce zařízení může v průběhu času využít Heat mapy znázorňující agregované statistiky dodržování předpisů sociální distancing a zajistit tak sociální distancing.

**Analýza nakupujících** – nákup v prodejnách používá kamery zaměřené na produktového displeje k měření dopadu na změny v provozu v obchodě. Systém správce úložiště umožňuje určit, které nové produkty mají na zapojení největší změny.

**Správa front** – kamery, na které se odkazuje ve frontách pro rezervaci, poskytují výstrahy správcům, když doba čekání trvá příliš dlouho, takže jim umožní otevřít další řádky. Historické údaje o opouštění frontě poskytují přehled o chování zákazníků.

**Dodržování předpisů při maskování obličeje** – prodejny můžou používat kamery, které ukazují na obchody ze Storu, abyste zkontrolovali, jestli se zákazníci, kteří načtou do obchodu, používají pro zajištění dodržování bezpečnostních předpisů a analýzu agregovaných statistik a získali přehled o trendech využití masek. 

**Sestavování &ch analýz** – kancelářský budova používá kamery zaměřené na vchody do klíčových prostorů k měření Footfall a způsobu, jakým uživatelé používají pracoviště. Insights umožňují, aby manažer pro vytváření upravil službu a rozložení, aby lépe poskytoval uživatelům.

**Minimální zjišťování zaměstnanců** – v datovém centru kamery monitorují aktivity kolem serverů. Když zaměstnanci fyzicky opravuje citlivá zařízení, v případě bezpečnostních důvodů se vždy vyžaduje, aby byly v rámci opravy přítomny dvě osoby. Kamery slouží k ověření, že jsou dodržovány tyto zásady.

**Optimalizace pracoviště** – v rychlé příležitostné restauraci se kamery v kuchyni používají ke generování agregačních informací o pracovním postupu zaměstnanců. Tuto možnost používají manažeři ke zlepšení procesů a školení týmu.

## <a name="considerations-when-choosing-a-use-case"></a>Důvody pro výběr případu použití

**Vyhněte se kritickým bezpečnostním výstrahám** – analýza prostorových dat nebyla navržena pro kritické bezpečnostní výstrahy v reálném čase. Nemělo by se spoléhat na scénáře, kdy se k aktivaci zásahu v reálném čase potřebují k tomu, aby se zabránilo újmě, jako je vypnutí velkého strojového stroje, když se osoba nachází. Dá se použít pro snížení rizika pomocí statistiky a zásahu ke snížení rizikového chování, jako jsou lidé, kteří vstupují do oblasti s omezeným nebo zakázaným přístupem.

**Nepoužívejte pro rozhodování o zaměstnanosti** – územní analýza poskytuje pravděpodobnostní metriky týkající se umístění a pohybu lidí v prostoru. I když můžou být tato data užitečná pro agregované zlepšení procesů, data nejsou dobrým indikátorem výkonu jednotlivých pracovních procesů a neměly by se používat k rozhodování o zaměstnanosti.

**Nepoužívejte u rozhodnutí týkajících se zdravotní péče o zdravotní péči** pravděpodobnostní a částečná data související s pohybem lidí. Data nejsou vhodná pro rozhodování týkající se stavu.

**Vyhněte se použití v chráněných prostorech** – Chraňte soukromí jednotlivců vyhodnocením umístění a pozic kamery, úpravou úhlů a oblastí zájmů, aby se přehlédnout chráněné oblasti, jako je Restrooms.

**Pečlivě zvažte použití ve školách nebo starších zařízeních péče** – prostorová analýza není intenzivně testována s daty, která obsahují podverze 18 nebo dospělého do stáří 65. Doporučujeme, aby zákazníci důkladně vyhodnotili chybové sazby pro svůj scénář v prostředích, kde se tyto věkové predominate.

**Pečlivě zvažte použití ve veřejných prostorech** – vyhodnoťte umístění a pozice kamery, úpravu úhlů a oblastí zájmů, abyste minimalizovali shromažďování ze veřejných prostorů. Osvětlení a počasí ve veřejných prostorech, jako jsou ulic a parky, významně ovlivní výkon systému prostorových analýz a je velmi obtížné zajistit efektivní zveřejnění ve veřejných prostorech.

## <a name="spatial-analysis-gating-for-public-preview"></a>Uzavírání prostorových analýz pro verzi Public Preview

Abychom zajistili, že se prostorová analýza používá pro scénáře, pro které byla navržena, zpřístupňujeme tuto technologii zákazníkům prostřednictvím procesu aplikace. Pokud chcete získat přístup k prostorovým analýzám, budete muset začít vyplňovat online formulář pro příjem dat. [Začněte svou aplikaci tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Přístup k prostorové analýze Public Preview se může vztahovat jenom na základě našich kritérií způsobilosti, dozvíte ČSFD procesu a dostupnosti pro podporu omezeného počtu zákazníků v rámci této ověřované verze Preview. Ve verzi Public Preview hledáme zákazníky, kteří mají významnou relaci s Microsoftem, zájem o spolupráci v doporučených případech použití a další scénáře, které jsou v souladu s našimi zodpovědnými závazky AI.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Charakteristiky a omezení pro prostorovou analýzu](/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)