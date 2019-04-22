---
title: Kurz projektu Akustika Unreal návrhu
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje pracovní postup návrhu pro projekt Akustika Unreal a Wwise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 38276757d0472582c3cf5035e1f52d34158a7e38
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784672"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Kurz projektu Akustika Unreal/Wwise návrhu
Tento kurz popisuje nastavení návrhu a pracovní postup pro projekt Akustika Unreal a Wwise.

Požadovaný software:
* Unreal projekt s moduly plug-in Wwise Akustika projektu a Unreal

Chcete-li získat Unreal projekt s Akustika projektu, můžete:
* Postupujte podle [projektu Akustika Unreal integrace](unreal-integration.md) pokynů a přidejte do projektu Unreal Akustika projektu
* Nebo můžete použít [projektu Akustika ukázkový projekt](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Nastavit vlastnosti Wwise celého projektu
Wwise má globální překážky a uzavření křivky, které ovlivňují, jak modul plug-in Akustika projektu jednotky Wwise DSP zvuku.

### <a name="design-wwise-occlusion-curves"></a>Návrh Wwise uzavření křivek
Při aktivním projektu Akustika reaguje na uzavření svazku, nízká pass filtru (LPF) a vysoce pass filtru (HPF) můžete nastavit v Wwise křivky. Doporučujeme nastavit typ křivky vašeho svazku na lineární s hodnotou-100 dB pro uzavření hodnotu 100.

S tímto nastavením simulace projektu Akustika vypočítá uzavření dB-18-li ji zadávat na pod křivkou u X = 18 a odpovídající Y je hodnota zeslabení použít. Poloviční uzavření proveďte nastavte koncový bod do databáze-50 místo dB-100, nebo do dB-200 exaggerate uzavření. Můžete přizpůsobit a optimalizovat všechny křivku, která je nejvhodnější pro vaši hru.
 
![Snímek obrazovky Wwise uzavření křivky editoru](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Zakázat Wwise překážka křivek
Překážka křivky Wwise vliv suchého úroveň izolace, ale Akustika projekt používá k vynucení wet/zkušební poměry ovládací prvky návrhu a simulace. Doporučujeme zakázat křivky překážka svazku. Návrh wetness, pomocí ovládacího prvku upravit Wetness popsané dále.
 
Pokud používáte křivky LPF/HPF překážka pro jiné účely, ujistěte se, že jste je nastavili na Y = 0 X = 0 (to znamená, neexistuje žádný LPF nebo HPF Pokud neexistuje žádná překážka).

![Snímek obrazovky Wwise překážka křivky editoru](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Návrh projektu Akustika mixer parametry
Vlastnosti globální dozvuku můžete řídit návštěvou kartě mixer modulu plug-in Service Bus Akustika projektu. Chcete-li otevřít panel nastavení modulu plug-in mixer dvakrát klikněte na "Mixer Akustika projektu (vlastní)".

Uvidíte také, že modul plug-in mixer má možnost "Provádět zvuk". Pokud byste chtěli raději použít projekt akustický integrované zvuk, zaškrtněte políčko "Provádět zvuk" a vyberte si z HRTF nebo pro posouvání. Ujistěte se, že chcete zakázat všechny suchého Aux sběrnice, které jste nastavili, jinak uslyšíte přímo dvakrát. Použití "Wetness nastavit" a "Dozvuku čas Škálovací faktor" pro globální kontrolu na kombinaci dozvuku využití. Mějte na paměti, je nutné restartovat Unreal a potom znovu vygenerovat soundbanks před tím play, aby přebíral změny konfigurace modulu plug-in mixer například zaškrtávací políčko "provádět zvuk.

![Snímek obrazovky projektu Akustika Wwise mixer modulu plug-in možnosti](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Nastavit ovládací prvky návrhu projektu Akustika v hierarchii Wwise mixer objektu actor
Ovládací prvek parametry, které jednotlivé mixer objektu actor dvakrát klikněte na objekt Actor – Mixer a potom klikněte na kartu modulu Plug-in jeho Mixer. Tady budete moci změnit všechny parametry na úrovni na zvuk. Tyto hodnoty v kombinaci s těmi nastavení na straně Unreal (popsaných níže). Například pokud je projekt Akustika Unreal modul plug-in nastaví Outdoorness úpravy na objekt, na 0,5 a Wwise sady, tak -0.25 Outdoorness úpravy, které u zvukové je 0,25.

![Snímek obrazovky podle nastavení zvukové mixer v hierarchii Wwise mixer objektu actor](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Ujistěte se, aux Service bus má suchého odeslat a Service bus výstup má wet odeslat
Mějte na paměti, že instalační program vyžaduje objekt actor mixer výměn obvykle suchého a vlhkou směrování v Wwise. Vytvoří dozvuku signálu na sběrnici výstup mixeru objektu actor (nastavené na Service Bus Akustika projekt) a suchého signálu podél uživatelem definované aux Service bus. Tato směrování se vyžaduje kvůli funkce modulu plug-in mixer Wwise rozhraní API, které používá modul plug-in Wwise Akustika projektu.

![Editor Wwise snímek obrazovky znázorňující pokyny k návrhu hlasu pro Akustika projektu](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Nastavte vzdálenost zeslabení křivek
Zkontrolujte všechny zeslabení křivky používá objekt actor – míchání pomocí projektu Akustika mít uživatelem definované aux odeslat nastavená na "výstupní Service bus svazek." Wwise to dělá ve výchozím nastavení pro nově vytvořený zeslabení křivky. Pokud migrujete existující projekt, zkontrolujte nastavení křivky.

Simulace Akustika projekt má ve výchozím nastavení protokolu radius 45 měřičů kolem player umístění. Obecně doporučujeme nastavit vaše křivka zeslabení do databáze-200 kolem tohoto vzdálenost. Tato vzdálenost není omezení. Pro některé výslovnost zbraní může být vhodné větší radius. V takových případech výstrahou je, že se bude podílet geometrie pouze v rámci 45 m player umístění. Pokud hráč v místnosti a zdroje zvuku nespadá do místnosti a okamžitě 100 mil., ji bude možné správně occluded. Pokud je zdroj v místnosti a hráč je mimo a okamžitě 100 mil., nebude occluded správně.

![Snímek obrazovky Wwise zeslabení křivek](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Odeslat vyrovnávací Mixer ###
 Jedna věc, kterou můžete chtít provést, je přidání ekvalizér mixer příspěvku. Můžete zpracovávat Akustika projektu Service bus jako typický dozvuku sběrnice (ve výchozím režimu dozvuku) a umístí dělat vyrovnávací filtr. Zobrazí se v projektu Akustika Wwise ukázkový projekt ukázku této.

![Snímek obrazovky Wwise po mixer EQ](media/wwise-post-mixer-eq.png)

Například filtr vysokou pass můžete pomáhaly zvládat basů z bezkontaktní záznamy, které boomy, nereálný dozvuku yield. Můžete také dosáhnout větší kontrolu po která má označení vytvoření úpravou EQ prostřednictvím RTPCs umožňuje změnit barvu dozvuku během hry.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Nastavení vlastností pro projekt Akustika celou scény

Objekt actor Akustika místo poskytuje mnoho ovládacích prvků, které upravují chování systému a jsou užitečné při ladění.

![Snímek obrazovky Unreal Akustika rozmístění ovládacích prvků](media/acoustics-space-controls.png)

* **Akustika Data:** Toto pole musí být přiřazena k assetu dokončené Akustika z obsahu/Akustika adresáře. Modul plug-in Akustika projektu automaticky přidá adresář obsahu/Akustika zabalené adresáře vašeho projektu.
* **Velikost dlaždice:** Rozsahy oblasti kolem naslouchací proces, který chcete Akustika data načtena do paměti RAM. Jako naslouchací proces sondy okamžitě kolem hráč jsou načteny v, výsledky jsou stejné jako načtení akustických dat pro všechny testy. Použijte víc paměti RAM větší dlaždice, ale snížení vstupně-výstupní operace disku
* **Automatické Stream:** Při povolení automaticky načte v nové dlaždice jako naslouchací proces dosáhne edge načíst oblasti. Pokud je zakázán, bude nutné k načtení nové dlaždice ručně prostřednictvím kódu nebo podrobné plány.
* **Škálování mezipaměti:** řídí velikost mezipaměti pro akustický dotazy použít. Menší mezipaměti používá méně paměti RAM, ale může zvýšit využití procesoru pro každý dotaz.
* **Akustika povoleno:** Ladění ovládacího prvku umožňující rychlé A / B přepínání Akustika simulace. Tento ovládací prvek je ignorován v dodání konfigurace. Je užitečné pro vyhledání konkrétní zvuku chyby pocházející Akustika výpočtů nebo jiný problém v projektu Wwise-li ovládací prvek.
* **Aktualizace vzdálenosti:** Tuto možnost použijte, pokud chcete použít předem dokončené Akustika informace pro dotazy vzdálenost. Tyto dotazy se podobají ray přetypování, ale nebyla předvypočítaných proto věnujte mnohem menším procesoru. Příklad použití je pro diskrétní odrazů nejbližší povrchu k naslouchacímu procesu. Plně využít to, budete muset použít kód nebo plány do dotazu vzdálenosti.
* **Statistiky Draw:** Při jeho UE `stat Acoustics` může poskytnout vám informace o procesoru, tento stav zobrazení se zobrazí aktuálně načtené ACE souboru, využití paměti RAM a další informace o stavu v horním levém rohu obrazovky.
* **Draw Voxels:** Překryv voxels zavřít naslouchací proces zobrazující voxel mřížky používané během interpolace modulu runtime. Pokud je vysílače uvnitř voxel modulu runtime, dojde k selhání akustický dotazy.
* **Sondy Draw:** Zobrazit všechny testy pro tento scény. Budou různé barvy v závislosti na stavu jejich zatížení.
* **Nakreslete vzdálenosti:** Pokud je povolená aktualizace vzdálenosti, tím se zobrazí pole na povrchu co nejblíže k naslouchacímu procesu v kvantizované směrech kolem naslouchací proces.

## <a name="actor-specific-acoustics-design-controls"></a>Ovládací prvky návrhu Akustika specifické pro objekt actor
Tyto ovládací prvky návrhu oborem pro jednotlivé zvukové součástí Unreal.

![Snímek obrazovky Unreal zvukové součásti ovládacích prvků](media/audio-component-controls.png)

* **Násobitel uzavření:** Určuje efekt uzavření. Hodnoty > 1 bude dál rozšiřuje vhodné. Hodnoty < 1 budou minimalizovat.
* **Úprava wetness:** Další dozvuku dB
* **Decay – násobitele času:** Ovládací prvky RT60 multiplicatively, na základě výstupu Akustika simulace
* **Úprava outdoorness:** Určuje, jak venku reverberation. Hodnoty blíže 0 jsou více budovách, více venku se blíže k 1. Toto nastavení je sčítání, takže ji nastavíte na hodnotu -1 bude vynucovat budovách, nastavení na + 1 bude vynucovat venku.
* **Přenos Db:** Vykreslení další zvuk prostřednictvím wall s této hlasitost v kombinaci s zeslabení řádku přístup na základě vzdálenosti.
* **Vzdálenost Warp vlhkou poměr:** Upraví reverberation vlastnosti ve zdroji, jako kdyby byly blíže/další okamžitě, aniž by to ovlivnilo přímou cestu.
* **Přehrát v nabídce Start:** Přepnout k určení, zda by měl v nabídce start scény automaticky přehrát zvuk. Ve výchozím nastavení povolené.
* **Zobrazit akustický parametry:** Zobrazit informace o ladění přímo nad komponenty v rámci her. (pouze pro konfigurace bez přesouvání)

## <a name="blueprint-functionality"></a>Funkce podrobného plánu
Objekt actor Akustika místa jsou přístupná přes podrobného plánu, poskytují funkce, třeba načítání mapy nebo změna nastavení prostřednictvím úrovně skriptování. Nabízíme dva příklady v tomto článku.

### <a name="add-finer-grained-control-over-streaming-load"></a>Přidat citlivější kontrolu nad streamování zatížení
Ke správě akustických dat streamování sami místo streamování automaticky na základě player pozice, můžete použít funkci podrobného plánu platnost zatížení dlaždice:

![Snímek obrazovky streamování podrobného plánu možnosti v Unreal](media/blueprint-streaming.png)

* **Cíl:** AcousticsSpace objektu actor
* **Pozice System Center:** System center oblasti, kterou potřebuje načíst data
* **Uvolnění sondy mimo dlaždice:** Pokud je zaškrtnuto, budou všechny testy v nové oblasti není uvolněn z paměti RAM. Pokud není zaškrtnuto, nové oblasti je načten do paměti, při opuštění existující testy také načtených do paměti
* **Blok na dokončení:** Díky synchronní operace načtení dlaždice

Před voláním platnost zatížení dlaždice musí již nastavena velikost dlaždice. Například byste mohli dělat něco jako toto tlačítko Načíst soubor ACE, nastavte velikost dlaždic a streamování v oblasti:

![Snímek obrazovky streamování nastavení možnosti v Unreal](media/streaming-setup.png)

### <a name="optionally-query-for-surface-proximity"></a>Volitelně můžete dotaz na povrchu blízkých výrazů
Pokud chcete zobrazit jak blízko plochy jsou v konkrétní směr kolem naslouchací proces, můžete použít funkci vzdálenost dotazu. Tato funkce může být užitečná pro řízení směrové zpožděné odrazů nebo pro další logika hry využitím surface blízkosti. Dotaz je levnější než ray přetypování, protože výsledky se berou z Akustika vyhledávací tabulky.

![Snímek obrazovky příklad dotazu vzdálenost podrobného plánu](media/distance-query.png)

* **Cíl:** AcousticsSpace objektu actor
* **Směr vypadat:** Směr k dotazování, zarovnání na střed na naslouchací proces
* **Vzdálenost:** Pokud dotaz úspěšný, vzdálenost na nejbližší plochu
* **Návratová hodnota:** Boolean – hodnota true, pokud je dotaz úspěšný, jinak hodnota false

## <a name="next-steps"></a>Další postup
* Seznamte se s koncepty za [návrhu procesu](design-process.md)
* [Vytvoření účtu Azure](create-azure-account.md) k vytvoření vlastní scény


