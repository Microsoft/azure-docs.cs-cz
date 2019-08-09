---
title: Koncepce návrhu s využitím akustické simulace
titlesuffix: Azure Cognitive Services
description: Tento koncepční přehled vysvětluje, jak by akustické množství projektu v podniku bylo akustické simulace do procesu návrhu zvuku.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854298"
---
# <a name="project-acoustics-design-process-concepts"></a>Koncepty procesu návrhu projektových akustických procesů

Tento koncepční přehled vysvětluje, jak se akustické z projektu dotýkají fyzické akustické simulace do procesu návrhu zvuku.

## <a name="sound-design-with-audio-dsp-parameters"></a>Návrh zvuku pomocí parametrů pro DSP zvuku

Trojrozměrné interaktivní tituly dosahují svého konkrétního zvuku pomocí bloků protokolu DSP (audio Digital Signal Processing) hostovaných ve zvukovém stroji. Tyto bloky mají složitost od jednoduchého míchání až po reverberation, ozvěny, zpoždění, rovnost, kompresi a omezení a další účinky. Výběr, uspořádání a nastavení parametrů těchto efektů je zodpovědný za návrháře zvuku, který sestavuje zvukový graf, který dosahuje cílů prostředí estetického a hraní.

V interaktivním názvu, když se zvuky a naslouchací proces pohybují po celém 3D prostoru, jak se tyto parametry přizpůsobí měnícím se podmínkám? Návrhář zvuku často uspořádá svazky v prostoru, které jsou naprogramovány na změny parametrů, aby bylo možné dosáhnout změn v reverberationch efektech, například nebo pro ztracené zvuky v kombinaci při přesunu naslouchacího procesu z jedné části scény do jiné. K dispozici jsou také zvukové systémy, které mohou automatizovat některé z těchto efektů.

Trojrozměrné tituly používají osvětlovací a kinematické fyziky, které jsou kvalitativně motivované, ale návrháři jsou upravené, aby dosáhli kombinace jejich cílů. Vizuální Návrhář nenastavuje jednotlivé hodnoty pixelů, ale místo toho upravuje 3D modely, materiály a systémy lehkých přenosů, které jsou fyzicky založené na obchodování na vizuálních prostředcích a náklady na procesor. Jaký je stejný postup pro zvuk? Akustické množství projektu je prvním krokem při průzkumu této otázky. Nejdřív se podíváme na to, co znamená pro přenos akustického energie přes prostor.

![Snímek obrazovky AltSpace scény překrytý reverb zónami](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulse odpovědi: Akustické propojení dvou bodů v prostoru

Pokud jste obeznámeni s návrhem zvukového obsahu, možná budete obeznámeni s akustickými Impulse odpověďmi. Akustická odezva Impulse modeluje přenos zvuku ze zdroje do naslouchacího procesu. Proto může odpověď Impulse zachytit každý zajímavý účinek akustické místnosti, jako je například překrytí a reverberation. Odpovědi na Impulse mají také určité výkonné vlastnosti, které umožňují škálování zvukových PROCESORů. Přidání dvou zvukových signálů společně a zpracování pomocí odpovědi Impulse má stejný výsledek jako použití odpovědi Impulse odděleně u každého signálu a přidání výsledků. Akustické šíření a odezvy Impulse také nezávisí na zpracovávaných zvukech, pouze na scéně, která je modelována, a na zdrojových a naslouchacích místech. V krátké době Impulse odpověď přechází vlivem scény na šíření zvuku.

Impulse odpověď zachycuje každý zajímavý zvukový efekt a můžeme ho použít pro efektivní zvuk s filtrem a můžeme získat Impulse odpovědi z měření nebo simulace. Ale co dělat v případě, že nechcete, aby akustické objemy přesně odpovídaly fyzikě, ale místo toho je tvarovat, aby odpovídaly požadavkům scény emocionálních? Ale podobně jako u hodnot v pixelech je Impulse odpověď jenom na seznam tisíců čísel, jak ho můžeme upravit tak, aby splňovalo estetické potřeby? A co když chceme mít překrytí/překážku, který se při předávání přes dveře nebo za překážkou mění bez problémů, kolik odpovědí Impulse potřebujeme k hladkému účinku? Co když se zdroj pohybuje rychle? Jak se interpoluje?

Zvuky v IT obtížně využívají simulaci a Impulse odezvy pro některé aspekty akustických funkcí v interaktivních titulcích. Přesto ale můžeme vytvořit systém zvukové dopravy, který podporuje úpravy návrháře, pokud můžeme připojit Naše odpovědi Impulse z simulace s našimi známými parametry efektu DSP pro záznam.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Propojení simulace s použitím parametrů

Impulse odpověď obsahuje každý zajímavý (a každý nezajímavý) Akustický účinek. Zvukové bloky DSP v případě správného nastavení jejich parametrů můžou vykreslovat zajímavý akustický efekt. Použití akustické simulace k řízení zvukového bloku DSP pro automatizaci zvukového přenosu ve 3D scéně je jen pro měření parametrů zvukového protokolu DSP z Impulse odpovědi. Toto měření se dobře rozumí pro určité běžné a důležité akustické účinky, včetně překrytí, překážky, portálu a reverberation.

Pokud je však simulace připojena přímo k parametrům DSP zvukového zařízení, kde se jedná o úpravu návrháře? Co jsme získali? Díky zahození odpovědí na Impulse a uchování několika parametrů DSP je dobré získat velkou velikost paměti. A pokud chcete, aby Návrhář před konečným výsledkem vyplynul, musíme najít pouze způsob, jak vložit návrháře mezi simulací a DSP zvukového zařízení.

![Graf se stylizovanou Impulse odpovědí s překrývajícími se parametry](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Návrh zvuku tím, že transformují zvukové parametry DSP z simulace

Vezměte v úvahu účinek, který vaše Sunglasses má na pohled světa. V jasném dni mohou skleněné brýle omezit září na pohodlnější. V tmavé místnosti možná nebudete moci vůbec zobrazit cokoli. Brýle nenastavuje určitou úroveň jasu ve všech situacích. jenom to vše zjednodušuje.

Pokud používáme simulaci k vyřízení našeho zvukového DSP pomocí parametrů překrytí a reverberation, můžeme po simulátoru přidat filtr pro úpravu parametrů, které se v zařízení DSP zobrazuje. Filtr by vynutil určitou úroveň překrytí nebo reverb zakončení, podobně jako Sunglasses v každé místnosti stejnou hodnotu jasu. Filtr může pouze učinit všechny occluder occlude méně. Nebo occlude více. Přidáním a úpravou filtru parametrů ztmavení překrytí, velkých a otevřených místností by stále neměl žádný překrytí efekt, zatímco dveře by se zvýšily na silný překrytí efekt a zároveň zachovávají plynulé přechody v důsledku. které simulace poskytuje.

V tomto paradigma se úlohy návrháře mění z výběru akustických parametrů pro každou a každou situaci, k výběru a úpravě filtrů, které se použijí na nejdůležitější parametry DSP přicházející z simulace. Zvyšuje vliv na aktivity návrháře z úzkých důvodů nastavení hladkého přechodu na vyšší aspekty intenzity překrytí a reverberation a přítomnosti zdrojů v kombinaci. V případě, že se situace vyžaduje, je vždy k dispozici jeden filtr, který umožňuje jednoduše přejít zpět a vybrat parametry DSP pro konkrétní zdroj v konkrétní situaci.

## <a name="sound-design-in-project-acoustics"></a>Návrh zvuku v případě akustického projektu

Balíček akustického projektu integruje každou z komponent popsaných výše: simulátor, kodér, který extrahuje parametry a sestavuje prostředky akustického přenosu, DSP pro přenos zvuku a výběr filtrů. Návrh zvuku s akustickými vlivy na projekt má za následek výběr parametrů pro filtry, které upraví parametry překrytí a reverberation odvozené z simulace a aplikované na zvukový procesor, s dynamickými ovládacími prvky zveřejněnými v editoru her a zvukovým modulem.

## <a name="next-steps"></a>Další postup
* Vyzkoušejte si paradigmata návrhu pomocí [projektů pro rychlý Start akustického projektu pro Unity](unity-quickstart.md) nebo [rychlý Start akustického projektu pro Unreal](unreal-quickstart.md)
* Prozkoumejte ovládací [prvky pro návrh akustického projektu pro Unity](unity-workflow.md) nebo [ovládací prvky návrhu pro Unreal](unreal-workflow.md) , které jsou v projektu.

