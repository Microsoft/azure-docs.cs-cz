---
title: Koncepce návrhu s využitím akustické simulace
titlesuffix: Azure Cognitive Services
description: Tento koncepční přehled vysvětluje, jak aplikace Project Acoustics zahrnuje akustickou simulaci do procesu návrhu zvuku.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854298"
---
# <a name="project-acoustics-design-process-concepts"></a>Koncepty návrhu návrhu akustiky projektu

Tento koncepční přehled vysvětluje, jak aplikace Project Acoustics začleňuje fyzickou akustickou simulaci do procesu návrhu zvuku.

## <a name="sound-design-with-audio-dsp-parameters"></a>Zvukový design s parametry audio DSP

3D interaktivní tituly dosahují svého konkrétního zvuku pomocí zvukových digitálních bloků zpracování signálu (DSP) hostovaných v audio motoru. Tyto bloky se pohybují ve složitosti od jednoduchého míchání, dozvuku, echo, zpoždění, vyrovnání, komprese a omezení a další chod. Výběr, uspořádání a nastavení parametrů na tyto efekty je odpovědností zvukového designéra, který staví zvukový graf, který dosahuje estetických a herních cílů zážitku.

Jak se tyto parametry přizpůsobují měnícím se podmínkám, jak se tyto parametry přizpůsobují měnícím se podmínkám, jak se tyto parametry přizpůsobují měnícím se podmínkám? Zvukový designér často uspořádá svazky v celém prostoru, které jsou naprogramovány tak, aby spouštěly změny parametrů, aby se dosáhlo změn v efektech dozvuku, například, nebo aby se vyhnul zvukům v mixu, když se posluchač pohybuje z jedné části scény do druhé. K dispozici jsou také akustické systémy, které mohou některé z těchto efektů automatizovat.

3D tituly používají systémy osvětlení a kinematické fyziky, které jsou fyzikicky motivované, ale designově upravené tak, aby dosáhly kombinace cílů ponoření a hraní. Vizuální návrhář nenastavuje jednotlivé hodnoty pixelů, ale spíše upravuje 3D modely, materiály a lehké transportní systémy, které jsou fyzicky založeny na vypnutí vizuální estetiky a nákladů na procesor. Jaký by byl ekvivalentní proces pro zvuk? Projekt Akustika je prvním krokem při zkoumání této otázky. Nejprve se dotkneme toho, co to znamená přepravovat akustickou energii prostorem.

![Snímek obrazovky altspace scény překryté zónami dozvuku](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulsní odezvy: Akusticky spojující dva body v prostoru

Pokud jste obeznámeni se zvukovým designem, můžete být obeznámeni s akustickými impulsními odezvami. Akustická impulsní odezva modeluje přenos zvuku ze zdroje na posluchače. Proto impulsní odezva může zachytit každý zajímavý efekt akustiky místnosti, jako je okluze a dozvuk. Impulsní odezvy mají také určité výkonné vlastnosti, které umožňují škálování zvukových efektů DSP. Sečtenídvou zvukových signálů dohromady a zpracování s impulsní odezvou dává stejný výsledek jako použití impulsní odezvy zvlášť na každý signál a přidání výsledků. Akustické šíření a impulsní odezvy také nezávisí na zpracovávaném zvuku, pouze na modelované scéně a na umístění zdroje a posluchače. Stručně řečeno, impulsní odezva destiluje vliv scény na šíření zvuku.

Impulsní odezva zachycuje každý zajímavý akustický efekt místnosti a můžeme jej efektivně aplikovat na zvuk pomocí filtru a můžeme získat impulsní odezvy z měření nebo simulace. Ale co když nechceme, aby akustika přesně odpovídala fyzice, ale spíše ji formovat tak, aby odpovídala emocionálním požadavkům scény? Ale stejně jako hodnoty pixelů, impulsní odezva je jen seznam tisíců čísel, jak ji můžeme případně upravit tak, aby vyhovovala estetickým potřebám? A co když chceme mít okluzi / obstrukce, která se mění hladce při průchodu dveřmi nebo za překážkami, kolik impulsních reakcí potřebujeme, abychom získali hladký efekt? Co když se zdroj pohybuje rychle? Jak to, že interpolujeme?

Pro některé aspekty akustiky v interaktivních titulech se zdá obtížné používat simulační a impulsní odezvy. Ale stále můžeme vytvořit audio transportní systém, který podporuje úpravy designérů, pokud můžeme spojit naše impulsní reakce ze simulace s našimi známými parametry zvukového DSP efektu.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Připojení simulace ke zvukovému DSP s parametry

Impulsní odezva obsahuje každý zajímavý (a každý nezajímavý) akustický efekt. Audio DSP bloky, když jsou jejich parametry správně nastaveny, může vykreslit zajímavý akustický efekt. Použití akustické simulace k pohonu zvukového bloku DSP pro automatizaci přenosu zvuku ve 3D scéně je pouze otázkou měření parametrů audio DSP z impulsní odezvy. Toto měření je dobře pochopeno pro některé běžné a důležité akustické účinky, včetně okluze, obstrukce, portálování a dozvuku.

Ale pokud je simulace připojena přímo k parametrům audio DSP, kde je nastavení návrháře? Co jsme získali? No, získáme značné množství paměti zpět tím, že vyřadíimpulsní odpovědi a udržet několik DSP parametrů. A dát návrháři určitou moc nad konečným výsledkem, musíme jen najít způsob, jak vložit návrháře mezi simulaci a audio DSP.

![Graf se stylizovanou impulsní odezvou s překrytými parametry](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Zvukový design transformací parametrů zvukového DSP ze simulace

Zamyslete se nad tím, jaký vliv mají vaše sluneční brýle na váš pohled na svět. Za jasného dne mohou brýle snížit lesk na něco pohodlnějšího. V temné místnosti možná nic neuvidíte. Brýle nenastavují určitou úroveň jasu ve všech situacích; Dělají všechno temnější.

Pokud použijeme simulaci k pohonu našeho audio DSP pomocí parametrů okluze a dozvuku, můžeme přidat filtr po simulátoru pro úpravu parametrů, které DSP "vidí". Filtr by nevynutil určitou úroveň okluze nebo dozvuku ocasní délky, podobně jako sluneční brýle nedělají v každé místnosti stejný jas. Filtr může jen dělat každý okluzní okluze méně. Nebo více okluze. Přidáním a úpravou jednoho "ztmavujícího" parametru okluze by velké otevřené místnosti stále měly malý nebo žádný efekt okluze, zatímco dveře by se zvýšily ze středního na silný efekt okluze při zachování hladkosti ve efektních přechodech které simulace poskytuje.

V tomto paradigmatu se úloha návrháře mění od výběru akustických parametrů pro každou situaci až po výběr a úpravu filtrů, které se použijí na nejdůležitější parametry DSP pocházející ze simulace. Povyšuje činnost projektanta z úzkých obav o nastavení hladkých přechodů na vyšší obavy z intenzity okluze a dozvuku a přítomnosti zdrojů v mixu. Samozřejmě, když situace vyžaduje, jeden filtr vždy k dispozici je jednoduše vrátit k výběru parametrů DSP pro konkrétní zdroj v konkrétní situaci.

## <a name="sound-design-in-project-acoustics"></a>Zvukový design v aplikaci Project Acoustics

Balíček Akustika projektu integruje každou z výše popsaných součástí: simulátor, kodér, který extrahuje parametry a vytváří akustiku, zvukový DSP a výběr filtrů. Zvukový design s project acoustics zahrnuje výběr parametrů pro filtry, které upravují parametry okluze a dozvuku odvozené ze simulace a aplikované na audio DSP, s dynamickými ovládacími prvky vystavenými uvnitř herního editoru a zvukového enginu.

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte paradigma návrhu pomocí [rychlého startu aplikace Project Acoustics for Unity](unity-quickstart.md) nebo [rychlého startu aplikace Akustika projektu pro unreal](unreal-quickstart.md)
* Prozkoumejte [ovládací prvky návrhu akustiky projektu pro Unity](unity-workflow.md) nebo ovládací [prvky návrhu akustiky projektu pro unreal](unreal-workflow.md)

