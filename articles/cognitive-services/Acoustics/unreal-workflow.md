---
title: Výukový kurz pro návrh Unrealí projektů
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje pracovní postup návrhu pro akustické projekty v Unreal a Wwise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5061370f43947341bb05bc30fa596604bc27ce74
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706578"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Kurz pro návrh projektových Unreal/Wwise
Tento kurz popisuje nastavení návrhu a pracovní postup pro akustické projekty v Unreal a Wwise.

Požadavky na software:
* Projekt Unreal s akustickými moduly plug-in Wwise a Unreal

Chcete-li získat projekt Unreal s akustickými možnostmi projektu, můžete:
* Podle pokynů pro [integraci projektu Unreal](unreal-integration.md) pokyny k přidání akustického projektu do projektu Unreal
* Nebo použijte [ukázkový projekt akustické a projekt](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Nastavení vlastností Wwise na úrovni projektu
Wwise má globální překážku a překrytí křivky, které ovlivňují způsob, jakým modul plug-in nastavil v projektu procesor Wwise zvuku.

### <a name="design-wwise-occlusion-curves"></a>Návrh překrytí křivky Wwise
Pokud je akustické množství projektu aktivní, reaguje na překrytí na svazek, na křivky LPF (Low-Pass Filter) a HPF (High-Pass Filter), které jste nastavili v Wwise. Doporučujeme nastavit typ křivky hlasitosti na lineární s hodnotou-100 dB pro hodnotu překrytí 100.

Pokud toto nastavení způsobí, že simulace akustického projektu vypočítá překrytí of-18 dB, zapíše se k níže uvedené křivce na X = 18 a odpovídající hodnota Y se projeví. Chcete-li provést poloviční překrytí, nastavte ke službě Endpoint 50 dB místo-100 dB nebo 200 na exaggerate překrytí dB. Můžete přizpůsobit a vyladit jakoukoli křivku, která pro vaši hru vyhovuje nejlépe.
 
![Snímek obrazovky s editorem křivky Wwise překrytí](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Zakázat křivky překážek Wwise
Wwiseé křivky mají vliv na suchou úroveň v izolaci, ale v případě akustického projektu používá ovládací prvky návrhu a simulaci k vymáhání vlhkého a suchého poměru. Doporučujeme zakázat křivku pro překážku. Pro návrh wetness použijte ovládací prvek Wetness pro úpravy popsaný níže.
 
Pokud používáte pro jiné účely překážku LPF/HPF, ujistěte se, že jste je nastavili na Y = 0 v X = 0 (to znamená, že pokud nedochází k žádnému překážkám, neexistují žádné LPF ani HPF).

![Snímek obrazovky s editorem křivky překážek Wwise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Parametry směšovače v projektech návrhu
Globální vlastnosti reverb můžete řídit návštěvou karty Plug-in směšovače na zvukové službě projektu. Dvojitým kliknutím na položku "směšovač akustického projektu (vlastní)" otevřete panel nastavení modulu plug-in směšovače.

Můžete také vidět, že modul plug-in směšovače má možnost provést rozprostorování. Pokud místo toho chcete použít vestavěnou prostorovou práci projektu, zaškrtněte políčko provést rozklad a vyberte jednu z HRTF nebo posouvání. Ujistěte se, že jste zakázali všechny suché bussesé AUX, které jste nastavili. v opačném případě bude Přímá cesta slyšet dvakrát. K cvičení globálního řízení v kombinaci reverb použijte Wetness úpravy a faktor reverb Time Scale. Všimněte si, že musíte restartovat Unreal a pak znovu vygenerovat soundbanks, abyste mohli začít hrát změny konfigurace modulu plug-in mixer, jako je zaškrtávací políčko provést rozložení.

![Snímek obrazovky s možnostmi modulu plug-in Wwise mixer v projektu](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Nastavení ovládacích prvků pro návrh akustického projektu v hierarchii Wwise actor-mixer
Pokud chcete ovládat parametry jednotlivého směšovače Actor, poklikejte na objekt actor-mixer a pak klikněte na kartu plugin plug-in. Tady budete moct změnit všechny parametry na úrovni zvuku. Tyto hodnoty se kombinují s těmi, které jsou nastavené na Unreal straně (popsané níže). Například pokud projekt Akustickě Unreal modul plug-in vystaví úpravu tónů u objektu na 0,5 a Wwise ho nastaví na-0,25, Výsledná úprava u tohoto zvuku se projeví na 0,25.

![Snímek obrazovky s nastavením zvukového směšovače v hierarchii Wwise actor-mixer](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Ujistěte se, že posílaná sběrnice má suché odeslání, a výstupní sběrnice posílá
Mějte na paměti, že požadovaná instalace objektu actor-mixer promění v Wwise normální a suché směrování. Vytváří signál reverb na výstupní sběrnici objektu actor (nastavenou na akustickou hodnotu v projektech) a suchý signál podél uživatelem definované pomocné sběrnice. Toto směrování se vyžaduje v důsledku funkcí rozhraní API pro modul plug-in Wwise mixer, které projekt využívá modulem plug-in Wwise.

![Snímek obrazovky s editorem Wwise zobrazující pokyny pro návrh hlasu pro akustické projekty](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Nastavení křivek pro zeslabení vzdálenosti
Zajistěte, aby všechny křivky odstavování používané pomocí směšovačů actor pomocí akustických prostředí projektu měly na svazku výstupní sběrnice uživatelsky definované pomocné možnosti odeslání. Wwise to ve výchozím nastavení dělá pro nově vytvořené křivky zeslabení. Pokud migrujete existující projekt, ověřte nastavení křivky.

Ve výchozím nastavení má simulace akustického projektu poloměr 45 měřičů kolem umístění přehrávače. Obecně doporučujeme nastavit v této vzdálenosti křivku odstavování na dB-200 dB. Tato vzdálenost není pevným omezením. U některých zvuků, jako jsou zbraně, můžete chtít větší poloměr. V takových případech je k dispozici pouze geometrie v 45 m umístění přehrávače. Pokud je hráč v místnosti a zdroj zvuku je mimo místnost a 100 milionů se, bude správně zastíněna. Pokud je zdroj v místnosti a hráč je mimo a 100 metrů, nebude správně zastíněna.

![Snímek obrazovky s křivkami na zeslabení Wwise](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Sestavování po vymíchání ###
 Další věc, kterou můžete chtít udělat, je přidat ekvalizér po vymíchání. Můžete nakládat se sběrnicí projektu jako typickou reverb sběrnici (ve výchozím režimu reverb) a vložit do ní filtr, aby se vyrovnala. Ukázku můžete zobrazit v projektu Ukázka akustické Wwise projektu.

![Snímek obrazovky Wwise po obmíchání EQ](media/wwise-post-mixer-eq.png)

Například filtr vysokého úspěšnosti vám může pomáhat zvládnout silné nahrávání téměř pořízených záznamů, které boomy, nereálné reverb. Další ovládací prvky po zanesli můžete také dosáhnout úpravou EQ až RTPCs, což vám umožní měnit barvu reverb v době hry.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Nastavení vlastností akustického projektu na úrovni scény

Objekt actor akustického prostoru zpřístupňuje mnoho ovládacích prvků, které mění chování systému a jsou užitečné při ladění.

![Snímek obrazovky s ovládacími prvky místa Unreal akustického prostoru](media/acoustics-space-controls.png)

* **Akustická data:** V tomto poli musí být přiřazen prostředek akustického materiálu vloženými z adresáře Content/akustické. Modul plug-in akustického projektu automaticky přidá do sbalených adresářů vašeho projektu adresář Content/akustického obsahu.
* **Velikost dlaždice:** Rozsahy oblasti kolem naslouchacího procesu, u kterých chcete data akustického zatížení načíst do paměti RAM. Pokud se sondy naslouchacího procesu hned po načtení přehrávače nacházejí v, jsou výsledky stejné jako načítání akustických dat pro všechny sondy. Větší dlaždice využívají více paměti RAM, ale omezují I/O disku.
* **Automatický Stream:** Pokud je povoleno, automaticky načte do nových dlaždic, protože naslouchací proces dosáhne okraje načtené oblasti. Pokud je tato zakázaná, bude nutné načíst nové dlaždice ručně prostřednictvím kódu nebo modrotisky.
* **Škálování mezipaměti:** určuje velikost mezipaměti používané pro zvukové dotazy. Menší mezipaměť používá méně paměti RAM, ale může zvýšit využití procesoru u každého dotazu.
* **Povolené akustické:** Ovládací prvek pro ladění umožňující rychlé A/B přepínání akustických simulací. Tento ovládací prvek se v konfiguracích expedice ignoruje. Tento ovládací prvek je užitečný pro hledání, zda konkrétní zvuková chyba vznikla v výpočtech akustického přenosu nebo v případě jiného problému v projektu Wwise.
* **Aktualizovat vzdálenosti:** Tuto možnost použijte, pokud chcete použít vloženýmielné informace pro dotazy na vzdálenost. Tyto dotazy jsou podobné přetypování v Ray, ale byly předem vypočítány, takže Využijte mnohem méně procesoru. Příkladem použití je samostatná odraza z nejbližšího povrchu pro naslouchací proces. Abyste to mohli plně využít, budete muset pro dotazování vzdálenosti použít kód nebo plány.
* **Statistika vykreslování:** I když vám může poskytnout informace o procesoru, zobrazí se v tomto zobrazení stav aktuálně načtený soubor ACE, využití paměti RAM a další informace o stavu v levém horním rohu obrazovky. `stat Acoustics`
* **Voxels vykreslování:** Překrytí voxels blízko k naslouchacímu procesu ukazující Voxel mřížku používanou během interpolace za běhu. Pokud je modul pro vystavení v běhovém prostředí Voxel, dojde k selhání akustického dotazu.
* **Vykreslit sondy:** Zobrazit všechny sondy pro tuto scénu. Budou se lišit barvy v závislosti na stavu zatížení.
* **Kreslit vzdálenosti:** Pokud je zapnutá možnost aktualizovat vzdálenosti, zobrazí se v quantized směrech kolem naslouchacího procesu pole na nejbližším povrchu naslouchacího procesu.

## <a name="actor-specific-acoustics-design-controls"></a>Ovládací prvky návrhu pro určité akustické úrovni actor
Tyto ovládací prvky návrhu jsou vymezeny na jednotlivé komponenty zvuku v Unreal.

![Snímek obrazovky s ovládacími prvky zvukové komponenty Unreal](media/audio-component-controls.png)

* **Multiplikátor překrytí:** Ovládá efekt překrytí. Hodnoty > 1 budou doplnit překrytí. Hodnoty < 1 budou minimalizovány.
* **Wetness úpravy:** Další reverb dB
* **Multiplikátor Decay času:** Ovládá RT60 multiplicatively na základě výstupu simulace akustického množství.
* **Korekce mezi dveřmi:** Určuje, jak je reverberation venku. Hodnoty blíž k 0 jsou více než jedna dvířka, blíž k 1 je více venku. Tato úprava je doplňková, takže když ji nastavíte na hodnotu-1, vynutila se dvířka a její nastavení na + 1 bude vymáhat venku.
* **Přenosová databáze:** Vykreslete další zvuk přes zeď s touto nahlasem v kombinaci s použitím útlumu vzdálenosti na základě pohledu.
* **Osnova vlhkého poměru na dálku:** Upraví charakteristiky reverberation ve zdroji, jako kdyby byl blíž nebo ještě více, aniž by to ovlivnilo přímou cestu.
* **Přehrát při spuštění:** Přepínač pro určení, zda se má zvuk automaticky přehrávat při spuštění scény. Ve výchozím nastavení povoleno.
* **Zobrazit akustické parametry:** Zobrazit informace o ladění přímo nad součástí hry (jenom pro nepřenosové konfigurace)

## <a name="blueprint-functionality"></a>Funkce podrobného plánu
Přístup k prostoru akustické paměti je přístupný prostřednictvím podrobného plánu, který poskytuje funkce, jako je načítání mapy nebo úprava nastavení prostřednictvím skriptování na úrovni. Zde uvádíme dva příklady.

### <a name="add-finer-grained-control-over-streaming-load"></a>Přidat jemnější kontrolu nad zatížením streamování
Chcete-li spravovat akustickou datovou streamování sami, místo streamování automaticky na základě pozice přehrávače, můžete použít funkci podrobného plánu dlaždice vynutit zatížení:

![Snímek obrazovky s možnostmi streamování podrobného plánu v Unreal](media/blueprint-streaming.png)

* **Cílové** Objekt actor AcousticsSpace
* **Pozice středu:** Střed oblasti, která vyžaduje načtená data
* **Uvolnit sondy mimo dlaždici:** Pokud je zaškrtnuto, všechny sondy v nové oblasti nebudou uvolněny z paměti RAM. Pokud není zaškrtnuto, nová oblast je načtena do paměti a zároveň opouští existující sondy, které jsou načteny do paměti.
* **Zablokování při dokončování:** Provede načtení dlaždice synchronní operaci.

Velikost dlaždice musí být nastavená před voláním dlaždice vynutit načtení. Například můžete provést něco podobného načtení souboru ACE, nastavit velikost dlaždice a Stream v oblasti:

![Snímek obrazovky s možnostmi nastavení streamování v Unreal](media/streaming-setup.png)

Funkce podrobného plánu dat zatížení použitá v tomto příkladu má následující parametry:

* **Cílové** Objekt actor AcousticsSpace
* **Nové zanesli:** Datový Asset, který se má načíst. Když tento parametr necháte prázdné nebo nastavíte na hodnotu null, uvolní se aktuální zanesli bez načtení nového.

### <a name="optionally-query-for-surface-proximity"></a>Volitelně dotaz na okolí Surface
Pokud chcete zjistit, jak se mají v rámci naslouchacího procesu v určitém směru pohybovat povrchy, můžete použít funkci vzdálenost dotazu. Tato funkce může být užitečná pro řízení zpožděných odrazů nebo pro jiné herní logiky, které jsou založené na blízkosti povrchu. Dotaz je levnější než přetypování na Ray, protože výsledky jsou načítány z vyhledávací tabulky s akustickými výsledky.

![Snímek obrazovky s ukázkovým dotazem na vzdálenost](media/distance-query.png)

* **Cílové** Objekt actor AcousticsSpace
* **Směr hledání:** Směr pro dotazování v, na střed naslouchacího procesu
* **Délku** Pokud je dotaz úspěšný, vzdálenost k nejbližšímu povrchu
* **Návratová hodnota:** Boolean – true, pokud je dotaz úspěšný, jinak false

## <a name="next-steps"></a>Další postup
* Prozkoumejte koncepty za [proces návrhu](design-process.md)
* [Vytvoření účtu Azure](create-azure-account.md) pro zaneslií vlastní scény


