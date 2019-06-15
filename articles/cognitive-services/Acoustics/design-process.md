---
title: Koncepce návrhu s využitím akustické simulace
titlesuffix: Azure Cognitive Services
description: Tento přehled vysvětluje, jak Akustika projektu zahrnuje akustický simulace do procesu návrhu zvuku.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 4a1a0b15da091a1c020eb132f6b14b9ee14d334c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335391"
---
# <a name="project-acoustics-design-process-concepts"></a>Koncepty proces návrhu Akustika projektu

Tento přehled vysvětluje, jak Akustika projektu zahrnuje fyzické simulace akustický do procesu návrhu zvuku.

## <a name="sound-design-with-audio-dsp-parameters"></a>Zvukové návrhu s parametry, DSP zvuku

3D interaktivní názvy dosažení jejich konkrétní zvuk pomocí zvukový signál digitálního zpracování bloky (DSP) konání modul pro zvuk. Tyto bloky rozsahu od jednoduché kombinování reverberation, odezvu, zpoždění, vyrovnávací, komprese a omezení a další efekty. Výběr, uspořádání a nastavení parametrů na těchto důsledcích zodpovídá za zvukové návrháře, který vytváří zvuku graf, který dosahuje cílů aesthetic a hraní her prostředí.

V nadpisech interaktivní zvuky a naslouchacího procesu přesunu v rámci 3D prostoru, jak tyto parametry se přizpůsobit měnícím podmínky? Návrhář zvukové často uspořádá svazky v celém oboru, které jsou naprogramovány na aktivaci změny parametrů k dosažení změnami reverberation účinky, například nebo duck zvuků v kombinaci jako naslouchací proces přesune z jedné části scény do jiného. Akustika systémy jsou také k dispozici, můžete automatizovat některé z těchto účinky.

3D názvy pomocí osvětlení a kinematiky fyzika systémy, které jsou opodstatněny fyzika ale Návrhář upravena k dosažení kombinaci cílů podrobných informací a hraní her. Vizuálního návrháře nenastavil jednotlivých obrazových bodů, ale místo toho upravuje 3D modelů, materiály a světla přenosu systémy, které jsou všechny fyzicky založené na obchodu visual estetiku a náklady na využití procesoru. Co by ekvivalentní proces pro zvuk? Projekt Akustika je prvním krokem při zkoumání tento dotaz. Nejprve jsme budete touch na co to znamená přenést akustické energie prostřednictvím mezerou.

![Snímek obrazovky AltSpace scény překrývající se zónami dozvuku](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulsů odpovědi: Dva body v prostoru akusticky sítě

Pokud jste obeznámeni s návrhem zvuku, může být obeznámeni s akustický impulsů odpovědi. Odpověď akustický impulsů modely přenos zvuk ze zdroje do naslouchacího procesu. Odpověď impulsů proto můžete zachytit všechny zajímavé efekt Akustika místnosti, jako je vhodné a reverberation. Impulsů odpovědi mají také některé výkonné vlastnosti, které umožňují zvukové efekty DSP škálování. Přidání dvou zvukového signálu společně a zpracování impulsů odpovědí poskytuje stejný výsledek jako použití odpovědi impulsů samostatně pro každý signál a sečtením výsledků. Akustický šíření a impulsů odpovědi také nejsou závislé na zvuk zpracovává pouze na scénu modelovaných a umístění zdroje a naslouchacího procesu. Stručně řečeno odpověď impulsů distills na zvukové šíření vliv na scéně.

Odpověď impulsů zachytí každou zajímavé místnosti akustický efekt a můžeme použít i pro zvuk efektivně pomocí filtru a získáme impulsů odpovědi z měření nebo simulace. Ale co když jsme není úplně vhodné Akustika tak, aby přesně odpovídaly fyziky, ale místo toho ji tak, aby odpovídaly citové požadavky scény tvarovat? Ale mnohem jako hodnoty pixel, je při reakci na impulsů jen seznam tisíce čísla, jak jsme pravděpodobně nastavit tak, aby vyhovovala potřebám aesthetic? A co když Chceme mít uzavření/překážka, která se liší plynule při předání prostřednictvím dveří, nebo za překážky, kolik impulsů odpovědi je potřeba získat smooth efekt? Co když zdroj přesune rychle? Jak jsme interpolovat?

Vypadá obtížně použitelnou simulace a impulsů odpovědi pro některé aspekty Akustika v interaktivní názvy. Ale jsme i přesto vytvořit systém zvuku přenosu, který podporuje návrháře nastavení, pokud jsme naše reakce impulsů připojit z simulace se naše zkušenosti zvuku parametry efekt DSP.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Simulace propojíte DSP zvuku s parametry

Odpověď impulsů obsahuje každou zajímavé (a každý nezajímavé) akustické vliv. Bloky DSP zvuku, když své parametry jsou nastaveny správně, může mít za následek zajímavé akustické vliv. Použití akustické simulace Centrum umožňující prosazovat blok DSP zvuku k automatizaci přenos zvuku ve 3D scéně je jenom pár měření parametrech DSP zvuku z impulsů odpovědi. Toto měření je dobře pochopitelné i pro některé běžné a důležité akustické efekty včetně uzavření, překážka, portalling a reverberation.

Ale pokud simulace je připojený přímo k parametrům DSP zvuku, kde je návrháře úpravy? Co nám získat? Dobře abychom získali značné množství paměti zpět tak, že impulsů odpovědi se zahodí a zachování několika DSP parametrů. A poskytnout návrháře určitou energii za konečný výsledek, budeme potřebovat pouze hledal způsob, jak vložit návrháře mezi simulace a DSP zvuku.

![Graf s odpovědí stylizované impulsů s parametry překrývající](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Zvuk: design by transformace zvuku DSP parametry z simulace

Zvažte, jaký vliv, které mají vaše prodejci slunečních brýlí zobrazení na světě. Jasně dne můžete snížit brýlí nám na něco lépe seznámíte. V tmavě místnosti nemusí být vidět nic vůbec. Brýlí nenastavuje určitou úroveň jas ve všech situacích; využívají jenom všechno, co tmavší.

Pokud používáme simulace Centrum umožňující prosazovat naše zvuku DSP pomocí vhodné a reverberation parametrů, můžeme přidat filtr po simulátor upravit parametry, které DSP "vidí". Filtr nebude vynucovat určitou úroveň uzavření nebo dozvuku tail délka mnohem jako prodejci slunečních brýlí neprovádějte každé místnosti stejné jas. Filtr může mít jenom každý occluder occlude menší. Nebo occlude informace. Přidávání a úprava jeden filtr "ztmavení" parametr uzavření, velké, otevřete místnosti by ještě malý vliv na žádné vhodné při dveří zvýší z média pro silné uzavření účinek, při zachování plynulost platit přechody že poskytuje simulace.

V toto paradigma úlohy návrháři změní z výběru akustický parametry pro každé situaci, výběru a úpravu filtry, které platí pro nejdůležitější DSP parametry z simulace. Z úzké nemuseli dělat starosti nastavení smooth přechody vyšší nemuseli dělat starosti intenzitu zrcadlových uzavření a reverberation efekty a přítomnost zdroje v kombinaci se zvýší oprávnění návrháře aktivit. Samozřejmě požadavky na situaci, vždy k dispozici jeden filtr je jednoduše přejděte zpět do výběru DSP parametry konkrétního zdroje v konkrétní situaci.

## <a name="sound-design-in-project-acoustics"></a>Zvukové návrhu v projektu Akustika

Balíček projektu Akustika integruje všech komponent popsaných výše: simulátoru, encoder, který extrahuje parametry a vytvoří prostředek Akustika DSP zvuku a výběr filtrů. Zvukové návrh s použitím Akustika projektu zahrnuje výběr parametrů pro filtry, které upravit parametry uzavření a reverberation odvozený od simulace a použity na DSP zvuku, pomocí dynamických ovládacích prvků, které jsou zveřejněné v editoru her a modul zvuku.

## <a name="next-steps"></a>Další postup
* Vyzkoušejte si pomocí paradigma návrhu [Akustika projekt rychlý start pro Unity](unity-quickstart.md) nebo [Akustika projekt rychlý start pro Unreal](unreal-quickstart.md)
* Prozkoumejte [projektu Akustika návrh ovládacích prvků pro Unity](unity-workflow.md) nebo [projektu Akustika návrh ovládacích prvků pro Unreal](unreal-workflow.md)

