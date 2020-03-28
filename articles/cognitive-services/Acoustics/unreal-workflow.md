---
title: Kurz neskutečného návrhu aplikace Project Acoustics
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje pracovní postup návrhu pro akustiku projektu v Unreal a Wwise.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854243"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Kurz návrhu akustiky projektu Unreal/Wwise
Tento kurz popisuje nastavení návrhu a pracovní postup pro akustiku projektu v Unreal a Wwise.

Softwarové předpoklady:
* Projekt Unreal s pluginy Project Acoustics Wwise a Unreal

Chcete-li získat projekt Unreal s projektem Akustika, můžete:
* Postupujte podle pokynů [pro integraci projektu Acoustics Unreal](unreal-integration.md) a přidejte do projektu Unreal aplikaci Akustika projektu
* Nebo použijte [ukázkový projekt Akustika projektu](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Nastavení vlastností Wwise pro celý projekt
Wwise má globální obstrukce a okluze křivky, které ovlivňují, jak modul plug-in Akustika projektu řídí Wwise audio DSP.

### <a name="design-wwise-occlusion-curves"></a>Navrhněte Wwise okluzní křivky
Když je aktivní aplikace Project Acoustics, reaguje na okluze, dolní propustfiltr (LPF) a high-pass filtr (HPF) křivky, které jste nastavili ve Wwise. Doporučujeme nastavit typ křivky objemu na lineární s hodnotou -100 dB pro hodnotu okluze 100.

S tímto nastavením, pokud simulace akustika projektu vypočítá okluzi -18 dB, zadá se do níže uvedené křivky na X = 18 a odpovídající hodnota Y je použita útlum. Chcete-li provést poloviční okluzi, nastavte koncový bod na -50 dB namísto -100 dB nebo na -200 dB, chcete-li zveličit okluzi. Můžete přizpůsobit a doladit libovolnou křivku, která nejlépe vyhovuje vaší hře.
 
![Snímek obrazovky s editorem wwiseokluze](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Zakázat křivky obstrukcí Wwise
Křivky obstrukcí Wwise ovlivňují úroveň sucha v izolaci, ale aplikace Project Acoustics používá ovládací prvky návrhu a simulaci k vynucení poměrů za mokra a za sucha. Doporučujeme zakázat křivku objemu obstrukce. Chcete-li navrhnout vlhkost, použijte ovládací prvek Nastavení vlhkosti popsaný později.
 
Pokud používáte křivky Obstrukce LPF/HPF pro jiné účely, ujistěte se, že jste je nastavili na Y = 0 na X = 0 (to znamená, že neexistuje žádný LPF nebo HPF, pokud není žádná překážka).

![Snímek obrazovky s editorem křivky obstrukce Wwise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Parametry mixážního mixéru Design Project Acoustics
Vlastnosti globálního dozvuku můžete řídit tak, že navštívíte kartu modulu plug-in na kartě Project Acoustics Bus. Poklepáním na "Project Acoustics Mixer (Custom)" otevřete panel nastavení pluginu mixeru.

Můžete také vidět, že mixer plugin má možnost "Provést spatializaci". Pokud byste raději používali vestavěnou prostorovou prostorovou aplikaci projektu Acoustic, zaškrtněte políčko Provést prostorovou orientaci a vyberte si z HRTF nebo Panning. Ujistěte se, že zakázat všechny Dry Aux autobusy, které jste nastavili, jinak uslyšíte přímou cestu dvakrát. Použijte "Wetness Adjust" a "Reverb Time Scale Factor" pro cvičení globální kontroly na kombinaci reverb. Všimněte si, že musíte restartovat Unreal, pak regenerovat soundbanks před zasažením hrát vyzvednout mixer plugin config změny, jako je 'Provést spatialization' zaškrtávací políčko.

![Snímek obrazovky s možnostmi pluginu mixer aplikace Project Acoustics Wwise](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Nastavení ovládacích prvků návrhu aplikace Project Acoustics v hierarchii objektu actor-mixer Wwise
Chcete-li řídit parametry jednotlivých actor-mixer, poklepejte na actor-mixer, pak klikněte na jeho Mixer Plug-in kartu. Zde budete moci změnit všechny parametry na úrovni zvuku. Tyto hodnoty se kombinují s hodnotami nastavenými ze strany Unreal (popsané níže). Například pokud modul plug-in Project Acoustics Unreal nastaví úpravu rozvidennosti na objekt na 0,5 a Wwise jej nastaví na -0,25, výsledná úprava outdooru aplikovaný na tento zvuk je 0,25.

![Snímek obrazovky s nastavením mixážního zařízení zvuku v hierarchii mixážního zařízení wwise](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Ujistěte se, že sběrnice aux má suché odeslání a výstupní sběrnice má mokré
Nezapomeňte, že požadované actor-mixer nastavení protíná obvyklé suché a mokré směrování ve Wwise. Vytváří signál reverb na výstupní sběrnici actor-mixer (nastavenou na aplikaci Project Acoustics Bus) a suchý signál podél uživatelem definované aux bus. Toto směrování je vyžadováno z důvodu funkcí wwise mixer plugin API, které používá plugin Project Acoustics Wwise.

![Snímek obrazovky s editorem Wwise zobrazujícím pokyny pro návrh hlasu pro akustiku projektu](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Nastavení křivek útlumu vzdálenosti
Ujistěte se, že všechny křivky útlumu používané objekty actor-mixery pomocí aplikace Akustika projektu mají uživatelem definované aux odeslat nastavena na "výstupní sběrnice objemu." Wwise to ve výchozím nastavení pro nově vytvořené křivky útlumu. Pokud migrujete existující projekt, zkontrolujte nastavení křivky.

Ve výchozím nastavení má simulace akustiky projektu poloměr 45 metrů kolem místa přehrávače. Obecně doporučujeme nastavit křivku útlumu na -200 dB kolem této vzdálenosti. Tato vzdálenost není tvrdé omezení. Pro některé zvuky jako zbraně budete chtít větší poloměr. V takových případech je upozorněním, že se zúčastní pouze geometrie do 45 m od místa hráče. Pokud je přehrávač v místnosti a zdroj zvuku je mimo místnost a 100 m daleko, bude správně uzavřen. Pokud je zdroj v místnosti a hráč je venku a 100 m daleko, nebude správně uzavřen.

![Snímek obrazovky s křivkami útlumu Wwise](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Vyrovnání míchačky ###
 Jedna další věc, kterou budete chtít udělat, je přidat post mixer ekvalizér. Sběrnici Akustika projektu můžete považovat za typickou sběrnici reverb (ve výchozím režimu reverb) a umístit na ni filtr pro vyrovnání. Ukázku můžete zobrazit v ukázkovém projektu Akustika Wwise.

![Snímek obrazovky s EQ po mixeru Wwise](media/wwise-post-mixer-eq.png)

Například filtr s vysokým průchodem může pomoci zpracovat basy z blízkých nahrávek, které dávají boom, nerealistický reverb. Můžete také dosáhnout větší kontroly po pečení úpravou EQ prostřednictvím RTPC, což vám umožní změnit barvu reverbu v době hry.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Nastavení vlastností akustiky projektu pro celou scénu

Objekt actor Acoustics Space zveřejňuje mnoho ovládacích prvků, které upravují chování systému a jsou užitečné při ladění.

![Snímek obrazovky s ovládacími prvky neskutečného prostoru akustiky](media/acoustics-space-controls.png)

* **Akustika Data:** Tomuto poli musí být přiřazen datový zdroj zapečené akustiky z adresáře Content/Acoustics. Modul plug-in Project Acoustics automaticky přidá adresář Content/Acoustics do sbalených adresářů projektu.
* **Velikost dlaždice:** Rozsahy oblasti kolem naslouchací proces, který chcete akustika data načtena do paměti RAM. Tak dlouho, dokud naslouchací proces sondy bezprostředně kolem přehrávače jsou načteny, výsledky jsou stejné jako načítání akustických dat pro všechny sondy. Větší dlaždice používají více paměti RAM, ale snižují vstupně-blou
* **Automatický datový proud:** Pokud je tato možnost povolena, automaticky se načte do nových dlaždic, jakmile posluchač dosáhne okraje načtené oblasti. Pokud je zakázáno, budete muset načíst nové dlaždice ručně pomocí kódu nebo podrobných plánů
* **Škálování mezipaměti:** řídí velikost mezipaměti používané pro akustické dotazy. Menší mezipaměť používá méně paměti RAM, ale může zvýšit využití procesoru pro každý dotaz.
* **Akustika povolena:** Ladicí ovládací prvek umožňující rychlé přepínání A/B simulace akustiky. Tento ovládací prvek je ignorován v konfiguracích expedice. Ovládací prvek je užitečný pro zjištění, pokud konkrétní zvuková chyba pochází z výpočtů akustiky nebo jiného problému v projektu Wwise.
* **Vzdálenosti aktualizace:** Tuto možnost použijte, pokud chcete pro dotazy na vzdálenost použít předpečené informace o akustice. Tyto dotazy jsou podobné ray přetypovává, ale byly pre-vypočítané tak trvat mnohem méně CPU. Příklad použití je pro diskrétní odrazy z nejbližší povrch naslouchací proces. Chcete-li plně využít, budete muset použít kód nebo podrobné plány pro dotazování vzdálenosti.
* **Statistiky losování:** Zatímco UE `stat Acoustics` může poskytnout informace o procesoru, tento stavový displej zobrazí aktuálně načtený soubor ACE, využití paměti RAM a další informace o stavu v levém horním rohu obrazovky.
* **Kreslit Voxels:** Překrytí voxels v blízkosti naslouchací proces zobrazující mřížku voxel použitou při interpolaci za běhu. Pokud je emitor uvnitř runtime voxel, nezdaří akustické dotazy.
* **Nakreslit sondy:** Ukažte všechny sondy pro tuto scénu. Budou mít různé barvy v závislosti na jejich stavu zatížení.
* **Vzdálenosti kreslení:** Pokud je povolena aktualizace vzdálenosti, zobrazí se pole na nejbližší mantinely naslouchací proces v kvantizovaných směrech kolem posluchače.

## <a name="actor-specific-acoustics-design-controls"></a>Ovládací prvky návrhu akustiky specifické pro objekt actor
Tyto ovládací prvky návrhu jsou vymezeny na jednotlivé zvukové součásti v Unreal.

![Snímek obrazovky s ovládacími prvky neskutečných zvukových komponent](media/audio-component-controls.png)

* **Násobitel okluze:** Řídí efekt okluze. Hodnoty > 1 zesílí okluzi. Hodnoty <1 ji minimalizují.
* **Úprava vlhkosti:** Další reverb dB
* **Násobitel doby rozpadu:** Řídí RT60 multiplicativně, na základě výstupu simulace akustiky
* **Nastavení vytesávosti:** Určuje, jak je dozvuk venku. Hodnoty blíže k 0 jsou více uvnitř, blíže k 1 jsou více venku. Tato úprava je aditivní, takže nastavení na -1 bude vynucovat uvnitř, nastavení na +1 bude vynucovat venku.
* **Vysílací db:** Vykreslete další zvuk přes stěnu s touto hlasitostí v kombinaci s útlumem vzdálenosti na základě zorné čáry.
* **Pokřivení vzdálenosti poměru za mokra:** Upraví dozvuku charakteristiky na zdroji, jako by byl blíže / dále, aniž by to ovlivnilo přímou cestu.
* **Hrát na začátku:** Přepnutím určete, zda se má zvuk automaticky přehrávat při spuštění scény. Ve výchozím nastavení povoleno.
* **Zobrazit akustické parametry:** Zobrazte informace o ladění přímo nad komponentou ve hře. (pouze pro konfigurace bez přepravy)

## <a name="blueprint-functionality"></a>Funkce podrobného plánu
Objekt actor Acoustics Space je přístupný prostřednictvím podrobného plánu, který poskytuje funkce, jako je například načítání mapy nebo úprava nastavení pomocí skriptování úrovně. Uvádíme zde dva příklady.

### <a name="add-finer-grained-control-over-streaming-load"></a>Přidání jemnější kontroly nad zatížením datových proudů
Chcete-li spravovat streamování akustických dat sami namísto automatického streamování na základě pozice přehrávače, můžete použít funkci vynucení dlaždice zatížení:

![Snímek obrazovky s možnostmi streamování podrobného plánu v aplikaci Unreal](media/blueprint-streaming.png)

* **Cíl:** Herec AcousticsSpace
* **Poloha středu:** Střed oblasti, který potřebuje načíst data
* **Uvolnit sondy mimo dlaždici:** Pokud je zaškrtnuto, všechny sondy, které nejsou v nové oblasti, budou uvolněny z paměti RAM. Pokud není zaškrtnuto, nová oblast je načtena do paměti a stávající sondy jsou načteny také do paměti.
* **Blok na dokončení:** Načte dlaždice synchronní operací.

Velikost dlaždice musí být nastavena již před voláním force load tile. Můžete například něco takového provést, abyste načetli soubor ACE, nastavili velikost dlaždice a streamujte v oblasti:

![Snímek obrazovky s možnostmi nastavení streamování v neskutečném](media/streaming-setup.png)

Funkce podrobného plánu load acoustics data použitá v tomto příkladu má následující parametry:

* **Cíl:** Herec AcousticsSpace.
* **Nové Pečeme:** The acoustics data asset to be loaded. Ponecháte-li tuto prázdnou/nakterou nulu, uvolníte aktuální pečení bez načtení nového.

### <a name="optionally-query-for-surface-proximity"></a>Volitelně dotaz na blízkost povrchu
Pokud chcete vidět, jak blízko jsou povrchy v určitém směru kolem posluchače, můžete použít funkci Vzdálenost dotazu. Tato funkce může být užitečná pro řízení směrových zpožděných odrazů nebo pro jiné herní logiky poháněné blízkostí povrchu. Dotaz je levnější než ray-cast, protože výsledky jsou vytaženy z tabulky vyhledávání akustiky.

![Snímek obrazovky s ukázkový dotaz na vzdálenost podrobného plánu](media/distance-query.png)

* **Cíl:** Herec AcousticsSpace
* **Směr pohledu:** Směr dotazu, vystředěný na posluchače
* **Vzdálenost:** Pokud je dotaz úspěšný, vzdálenost k nejbližšímu povrchu
* **Vrácená hodnota:** Logická hodnota - true, pokud byl dotaz úspěšný, jinak false

## <a name="next-steps"></a>Další kroky
* Seznamte se s koncepty [procesu návrhu](design-process.md)
* [Vytvoření účtu Azure pro](create-azure-account.md) pečení vlastní scény


