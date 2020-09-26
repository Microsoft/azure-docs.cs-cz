---
title: Protokol změn Azure Media Player
description: Azure Media Player protokolu změn.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: c63a8efc4bbcf6e5a124d439e6f9f91397e2fa53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315078"
---
# <a name="changelog"></a>Protokol změn

## <a name="236-official-update-september-21-2020"></a>2.3.6 (oficiální aktualizace září 21 2020)

### <a name="features-236"></a>Funkce 2.3.6

Přidání podpory jenom pro zvuk pro azureHtml5JS Tech (POMLČKa) podpora v reálném čase se změnou jazyka pro podporu v reálném čase

### <a name="bug-fixes-236"></a>Opravy chyb 2.3.6

Při použití příkazu "playsinline" v přehrávání HLS na zařízeních Apple se kliknutím na tlačítko "živé" způsobí, že video restartuje obrázek AMP. v některých případech dojde k chybě, při přehrávání HLS FairPlay [usnadnění], které nejsou definovány pro tlačítka, když se klávesnice používá [usnadnění], je poměr světelnosti menší než 1.3:1 pro indikátor průběhu [přístupnost] fokus klávesnice se někdy nevrátí na tlačítko Kvalita videa [usnadnění]. na videu se nezobrazuje ovládací prvky. obrazovky zabraňující programu Narrator v jejich hledání

### <a name="changes-236"></a>2.3.6 změny

Při volání aplikací vracet smysluplné chyby doručení

## <a name="235-official-update-june-1-2020"></a>2.3.5 (oficiální aktualizace Červen 1 2020)

### <a name="bug-fixes-235"></a>Opravy chyb 2.3.5

- Přístup Naslouchací proces kláves ESC v podokně možnosti je připojen k dokumentu
- Přístup Zabránit zobrazení uživatelského rozhraní přehrávače, pokud ovládací panel nebo nabídka Možnosti obsahuje fokus
- Na řídicím panelu se zobrazuje nesprávná doba chodu, když je povolené nastavení displeje na zdi.

### <a name="changes-235"></a>Změny 2.3.5

- Přidání chybové zprávy pro kód chyby 0x00400005 a zdokumentované

## <a name="234-official-update-march-4-2020"></a>2.3.4 (oficiální aktualizace, březen 4 2020)

### <a name="bug-fixes-234"></a>Opravy chyb 2.3.4

- Nejde nastavit PlayReady overrideLicenseAcquistionUrl
- Nejde přehrát nějaký obsah s nekontinuitou.
- Přístup Hodnota atributu ID pro výstrahu čtečky obrazovky musí být jedinečná.
- Přístup Při navigaci v dialogovém okně Nastavení skrytých titulků se fokus zaměřuje mimo dialogové okno.

### <a name="changes-234"></a>Změny 2.3.4

- Obsah protokolu po úspěšném stažení, aby pomohl analyzovat chyby dešifrování 2.3.3 (oficiální aktualizace, listopad 12 2019)

### <a name="features-234"></a>Funkce 2.3.4

- Přidání podpory pro zobrazení času na zdi jako překryvu a na ovládacím panelu

### <a name="bug-fixes-234"></a>Opravy chyb 2.3.4

- Přepínač zvukové stopy funguje, ale výstup chyby v objektu IE11 a motiv nepodporuje vlastnost nebo metodu Enabled.
- Pokud je vyrovnávací paměť plně načtená, přepínač zvukové stopy se nezdařil.
- Přepínač zvukové stopy se nezdařil, když uživatel pozastaví video a přepne mezi zvukovým stopami velmi rychle.
- Přístup Pro ovládací prvek video v přehrávači videa nejsou definovány popisy tlačítek
- Chybějící tlačítka hlasitosti v Html5 v závislosti na tom, kdy se přijímá ' loadstart '
- Přístup Neexistuje způsob, jak nastavit alternativní text pro plakátový obrázek.
- Přístup Po výběru možnosti Hotovo v dialogovém okně Nastavení popisků došlo ke ztrátě fokusu aplikace
- Přístup V části segmenty Preview jsou pro ' video ' definovány nesprávné atributy ARIA

### <a name="changes-234"></a>Změny 2.3.4

- Při přehrávání HLSu v iOS a macOS Safari se odstranil prázdný popisek/záznam titulků.
- Snížil se počet 412s pro popisky IMSC1.
- Upozornění na výstup v konzole pro 10 po sobě jdoucích prázdných odpovědí na IMSC1 titulků, abychom usnadnili živé ladění

## <a name="232-official-update-october-9-2019"></a>2.3.2 (oficiální aktualizace říjen 9 2019)

### <a name="features-232"></a>Funkce 2.3.2

– Přidala se podpora PlayReady pro PŘERUŠOVANé přehrávání pro prohlížeč chrom Edge.

### <a name="bug-fixes-232"></a>Opravy chyb 2.3.2

- Aktuální rychlost přehrávání se v nabídce rychlost přehrávání vizuálně nezobrazuje, pokud ji uživatel ručně nenastaví.
- Přístup Podokno nastavení se nebalí s klíčem "ESC".
- Přístup Klávesová zkratka AMP nefunguje, když je zapnutý Narrator

### <a name="changes-232"></a>Změny 2.3.2

- V případě prohlížečů, které nepodporují zvukový kodek E-AC3, jsou zvukové stopy E-AC3 v nabídce zvukové stopy skryté.
- Pro prohlížeče, které podporují zvukový kodek E-AC3, je ve výchozím nastavení vybráno zvukové stopy E-AC3.
- V případě prohlížečů, které nepodporují přepínání zvukového kodeku, jsou zvukové stopy s jiným kodekem z vybrané stopy v nabídce zvukové stopy skryté.

## <a name="231-official-update-august-12-2019"></a>2.3.1 (oficiální aktualizace srpen 12 2019)

### <a name="features-231"></a>Funkce 2.3.1

- Signalizace události, když jsou EMSG boxy přijímány v podobě přerušovaného přehrávání – přidání podpory k zobrazení zvukových stop EC-3 v nabídce zvuk v prohlížečích podporujících ES-3 a umožnění přepínání zvukové stopy z AAC na EC3 a naopak v prohlížeči Edge na bázi Chromimum

### <a name="bug-fixes-231"></a>Opravy chyb 2.3.1

- Nabídka zvukové stopy je po odebrání běhů EC-3 poškozená.
- Aktuální čas může být skvělý než doba trvání videa.
- Nastavení rychlosti přehrávání prostřednictvím initialSpeed nefunguje
- Někdy se může stát, že hráč po hledání vypadá zablokované.
- Na okraji a v IE na dotykové obrazovce po přiblížení na stránku se stisknutí nebo najetí myší přes okno SeekBar nezíská přesně správný segment videa.
- Přístup Označení standardu ARIA pro Play/Pause není popisný pro chybu v programu Video Player map Live segment Nenalezeno, aby se mohl správně připojit k chybě amp.
- Přístup Role Aria používané pro Play/Pause musí odpovídat platným hodnotám (. VJS-text-Track-Display).
- Přístup Některé role ARIA musí být obsažené v konkrétních nadřazených prvkůch.
- Přístup K dispozici není žádný popis pro tlačítko Přehrát/pozastavit IMSC1 titulků videa. titulky se můžou po hledání v aktuální video/zvukové vyrovnávací paměti zmizet.

### <a name="changes-231"></a>Změny 2.3.1

- Po získání segmentDecryptError a přehrávači už je na živém okraji, Player teď aktualizuje manifest, místo abyste vyzkoušeli další segment.
- Přidání dalšího protokolování pro diagnostiku
- Aktualizovaná dokumentace, která zahrnuje podporu FairPlay pro iOS Safari
- Přidal se příklad pro možnost srclang možnosti IMSC1.
- Přidání odsazení, textPadding, přepsání boxShadow pro textové stopy
- Přidání ErrorCode (0x0020025B) pro odlišení toho, že se stahování segmentu nepovedlo kvůli nejenom vyvolání 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (oficiální verze vydaná v dubnu 30 2019)

### <a name="features-230"></a>Funkce 2.3.0

- Přidání podpory pro titulky IMSC1 pro POMLČKu
- Přidaná podpora pro prostředky pouze pro video pro POMLČKu
- Přidání rozhraní API presentationTimeOffsetInSec

### <a name="bug-fixes-230"></a>Opravy chyb 2.3.0

- Profil heuristiky AMP LowLatency je v konfliktu s přehráváním zvukového videa v iOS a zrušením ztlumení pro některé jazyky mají špatné překlady.
- Hodnota Aria-ValueNow posuvníku indikátoru průběhu je někdy nesprávná.
- Hodnota role Aria zobrazovaného textu stopy není správná.

### <a name="changes-230"></a>2.3.0 změny

- Protokoly teď zahrnují velikost stažených fragmentů multimédií.
- Odebrána podpora IE 9 a IE 10
- Aktualizovala se ukázka CEA708, aby se zobrazovaly titulky zarovnané vlevo.
- Zahrnout MediaError. Message do protokolů pro selhání přehrávání

## <a name="224-official-update-february-22-2019"></a>2.2.4 (oficiální aktualizace z února 22 2019) ##

### <a name="bug-fixes-224"></a>Opravy chyb 2.2.4 ###

- [Oprava chyby] WEBOVÉ Přístup Odebrala se dostupná karta fiktivní, když se zobrazí chybová obrazovka.
- [Oprava chyby] WEBOVÉ Opravili klávesovou zkratku pro IE11 a Edge.
- [Oprava chyby] WEBOVÉ Opravila se výjimka pro popisky CEA708.
- [Oprava chyby] WEBOVÉ Opravili jsme problém se zamrznutím videa pro prohlížeč Edge.

### <a name="changes-224"></a>Změny 2.2.4 ###

- Mění WEBOVÉ Pokud dojde k chybě dešifrování fragmentů, přehrávač se pokusí přehrání aktuální a různé fragmenty.
- Mění WEBOVÉ Pružnější překrývající se fragmenty videa nebo zvuku

## <a name="223-official-update-january-9-2019"></a>2.2.3 (oficiální aktualizace lednu 9 2019) ##

### <a name="features-223"></a>Funkce 2.2.3 ###

- Zapnut HLS Přidání nabídky zvukové stopy pro přehrávání Safari HLS

### <a name="bug-fixes-223"></a>Opravy chyb 2.2.3 ###

- [Oprava chyby] WEBOVÉ Přístup Během živého vysílání se tlačítko "Live" nedá vybrat pomocí klávesnice.
- [Oprava chyby] WEBOVÉ Pevná falešná falešná 0x0400003 chyby z důvodu neúspěšného testu MSE
- [Oprava chyby] WEBOVÉ Opravili jsme problém, kdy se může video při spuštění živého streamu ukotvit.

### <a name="changes-223"></a>Změny 2.2.3 ###

- Mění WEBOVÉ Přidání dalších informací do protokolu pro povolení lepší diagnostiky
- Mění WEBOVÉ Pokud je k dispozici více než jedna přenosová rychlost na stejném rozlišení obrazovky, jsou pro výběr dostupné všechny přenosové rychlosti.

## <a name="222-official-update"></a>2.2.2 (oficiální aktualizace) ##

### <a name="bug-fixes-222"></a>Opravy chyb 2.2.2 ###

- [Oprava chyby] WEBOVÉ Když hráč zaznamená přechodný výpadek sítě, zastaví přehrávání okamžitě.
- [Oprava chyby] WEBOVÉ Přístup Chybové dialogové okno není dostupné na klávesnici.
- [Oprava chyby] WEBOVÉ Při přehrávání datového prostředku bez nepodporované chyby se zobrazuje nekonečný číselník

### <a name="changes-222"></a>Změny 2.2.2 ###

- Mění [AMP] přidání lokalizovaných řetězců pro uživatelské rozhraní inzerce

## <a name="221-official-update"></a>2.2.1 (oficiální aktualizace) ##

### <a name="features-221"></a>Funkce 2.2.1 ###

- Zapnut [CMAF] Přidání podpory pro HLS CMAF

### <a name="bug-fixes"></a>Opravy chyb ###

- [Oprava chyby] [AMP] nejasné časovače v logice opakování při vyhodnocování chyb přehrávání
- [Oprava chyby] WEBOVÉ [Firefox] při zastavení živého programu není v prohlížeči Firefox a Chrome vyvolána událost.
- [Oprava chyby] WEBOVÉ Ovládací prvky zobrazené po SetSource –, i když jsou ovládací prvky nastavené na hodnotu false v možnostech přehrávače

### <a name="changes"></a>Změny ###

- Mění [Živé titulky] Změnili jste název rozhraní API pro popisky CEA z 608 na 708. Další informace najdete v tématu [Nastavení popisků CEA708](/javascript/api/azuremediaplayer/amp.player.cea708captionssettings) .-->

## <a name="220-official-release"></a>2.2.0 (oficiální verze) ##

### <a name="features-220"></a>Funkce 2.2.0 ###

- Zapnut [Azurehtml5JS] Blikající [LiveCaptions] Podpora titulků CEA 708 v Azurehtml5JS a flash tech pro vymazání obsahu a AES.

### <a name="bug-fixes-220"></a>Opravy chyb 2.2.0 ###

- [Oprava chyby] Detekce verze Flash nefunguje v Chrome nebo Edge

### <a name="changes-220"></a>2.2.0 změny ###

- Mění WEBOVÉ Heuristiky Změnil se název heuristické profilu z rychlého startu na LowLatency
- Mění Blikající Změna v přehrávači Flash pro detekci verzí, aby se povolilo přehrávání obsahu AES s novou aktualizací Adobe Flash

## <a name="219-official-hotfix"></a>2.1.9 (oficiální oprava hotfix) ##

### <a name="bug-fixes-219"></a>Opravy chyb 2.1.9 ###

- [Oprava chyby] Bydlí Došlo k výjimce při přechodu živých streamů do videa na vyžádání nebo živé archivy.

### <a name="changes-219"></a>2.1.9 změny ###

- Mění Blikající AES Upravená logika flash tech, aby nepoužívala sharedbytearrays pro dešifrování AES, protože společnost Adobe zablokovala použití na flash 30. Mějte prosím na paměti, že přehrávání bude fungovat, jenom když Adobe nasadí novou verzi Flash kvůli chybě v V30. Další podrobnosti najdete v části [známé problémy](azure-media-player-known-issues.md) .

## <a name="218-official-update"></a>2.1.8 (oficiální aktualizace) ##

### <a name="bug-fixes-218"></a>Opravy chyb 2.1.8 ###

- [Oprava chyby] Číselníkem občas není zobrazený příkaz post a před přehráním.
- [Oprava chyby] Když je povolená možnost muted, přehrávač se nespustí ztlumený.
- [Oprava chyby] Posuvník hlasitosti se zobrazí, pokud jsou ovládací prvky nastaveny na hodnotu false.
- [Oprava chyby] Přehrávání se občas opakuje, když uživatel přeskočí na živou hranu.
- [Oprava chyby] Firefox Přehrávač občas vyvolá výjimku JavaScriptu při načtení.
- [Oprava chyby] Přístup Tlačítko Přehrát/pozastavit/hlasitost při výběru pomocí ovládacích prvků klávesnice ztratí osnovu fokus
- [Oprava chyby] Uvolnění úniku paměti v přehrávači je zrušeno.
- [Oprava chyby] Volání metody src () po chybě přehrávače neobnoví původní tovární nastavení.
- [Oprava chyby] Bydlí Když uživatel po ukončení vysílání klikne na živé tlačítko, AMP je ve stavu při načítání konstantní.
- [Oprava chyby] Chrome Přehrávač přestane reagovat a přehrávání se při minimalizaci prohlížeče na pozadí nezdařilo.

### <a name="changes-218"></a>2.1.8 změny ###

- Mění Aktualizovaná chyba 0x0600001, která se zobrazí, když se obsah AES přehrává s použitím Flash 30, protože není v tuto chvíli podporovaný. Další podrobnosti najdete v části [známé problémy](azure-media-player-known-issues.md) .
- Mění Přidání dalších opakování pro živé scénáře, když manifest vyžádá 404 nebo vrátí prázdné manifesty.

## <a name="217-official-update"></a>2.1.7 (oficiální aktualizace) ##

### <a name="features-217"></a>Funkce 2.1.7 ###

- Zapnut [AzureHtml5JS] Přidání možnosti konfigurace pro vyprázdnění zastaralých dat ve zdrojové vyrovnávací paměti médií

### <a name="bug-fixes-217"></a>Opravy chyb 2.1.7 ###

- [Oprava chyby] Přístup [Čtečka obrazovky] Odstranila se prázdná hlavička, která je zahrnutá v případě, že název není nastavený.
- [Oprava chyby] [UWA] AMP vyvolá výjimku při přehrávání v univerzální aplikaci pro Windows
- [Oprava chyby] [OSX] setActiveTextTrack () nefunguje v Safari v OSx
- [Oprava chyby] Bydlí Kliknutím na živý okraj po odstranění a opětovném spuštění programu Player vrátí výjimku.
- [Oprava chyby] Pokožk Aktuální čas pro určité prostředky se zkrátí.
- [Oprava chyby] [DRM] oprava podporovaná pro přehrávání v prohlížečích, které podporují víc CENC DRM

### <a name="changes-217"></a>2.1.7 změny ###

- Mění Vzory Přístup Přidání značky jazyka ke všem ukázkám

## <a name="216-official-update"></a>2.1.6 (oficiální aktualizace) ##

### <a name="bug-fixes-216"></a>Opravy chyb 2.1.6 ###

- [Oprava chyby] AMP se zobrazením nesprávné doby trvání konkrétního prostředku
- [Oprava chyby] [FairPlay-HLS] Fairplay chyby nešířené do uživatelského rozhraní
- [Oprava chyby] Vlastní heuristické vlastnosti se ignorují v AMP 2.1.5.

### <a name="changes-216"></a>2.1.6 změny ###

- Mění [FairPlayDRM] Odebral se časový limit pro žádost o certifikát a žádost o licenci pro FairPlay, aby se zajistila parita s implementacemi PlayReady a Widevine.
- Mění Různá heuristická vylepšení pro boj s rozmazaným obsahem

### <a name="features-216"></a>Funkce 2.1.6 ###

- Zapnut Přidání podpory MPD-Time-CMAF Format

## <a name="215-official-hotfix"></a>2.1.5 (oficiální oprava hotfix) ##

### <a name="bug-fixes-215"></a>Opravy chyb 2.1.5 ###

- [Oprava chyby] Titulky VTT stylu není správně vykreslený přehrávačem
- [Oprava chyby] Přístup Živé tlačítko neobsahuje popisek Aria.

## <a name="214-official-update"></a>2.1.4 (oficiální aktualizace) ##

### <a name="bug-fixes-214"></a>Opravy chyb 2.1.4 ###

- [Oprava chyby] Přístup Vybrána Uživatelé se nemohou soustředit na vlastní tlačítka přidaná na pravé straně tlačítka celá obrazovka na ovládacím panelu.
- [Oprava chyby] IE11 [Panel hlasitosti] Automaticky otevírané okno s kartami na více svazcích zajistí, že se celá obrazovka videa v IE11 v režimu celé obrazovky.
- [Oprava chyby] [Skin | Flush] zobrazuje se mezera mezi řídicím panelem a automaticky otevíraných oken panelu svazků.
- [Oprava chyby] WEBOVÉ Titulky Pokud se ve stávajícím přehrávači změní zdroj, staré vložené stopy se nevymažou.
- [Oprava chyby] Přístup Předčítání Čtečka obrazovky čte nesprávně řízení hlasitosti
- [Oprava chyby] [Flash] Událost přehrání se občas neaktivuje z flash tech
- [Oprava chyby] WEBOVÉ Vybrána Pokud má hráč fokus a je v režimu celé obrazovky, vyžaduje možnost Přehrát/pozastavit dvě kliknutí.
- [Oprava chyby] WEBOVÉ Pokožk Na indikátoru průběhu konkrétního prostředku se zobrazuje nesprávná doba trvání
- [Oprava chyby] Okna [AD Butler] Nepřeberný analyzátor nezpracovává nepřeberný soubor, který nemá událost průběhu.
- [Oprava chyby] Sdn [AMP 2.1.1] Opravený problém pro podporu modulu plug-in SDN pro podregistr SDN
- [Oprava chyby] Přístup Když má uživatel fokus na tlačítko hlasitosti, Narrator přečte "tlačítko pro půlnoc ztlumení".

### <a name="changes-214"></a>Změny 2.1.4 ###

- Mění Přístup [Asistenční technologie] Tlačítka teď mají vlastnost Aria-Live pro zlepšení prostředí pomocí technologie pro usnadnění
- Mění Přístup [Tlačítko hlasitosti | Narrator] vylepšený přístup k tlačítku hlasitosti změnou funkcí procházení a chováním posuvníku. Tyto změny usnadňují uživatelům klávesnice měnit hlasitost přehrávače.
- Mění Překročení časového limitu kontextové nabídky nečinnosti od 3 do 5 sekund
- Mění Přístup Jasu Zvýšení poměru kontrastu jasu u rozevíracích nabídek v nastavení titulků

## <a name="213-official-update"></a>2.1.3 (oficiální aktualizace) ##

### <a name="bug-fixes-213"></a>Opravy chyb 2.1.3 ###

- [Oprava chyby] [Moduly plug-in | Překrytí názvu] modul plug-in nadpisu vyvolá výjimky JS s AMP v2. X +
- [Oprava chyby] Událost zdrojové sady se posílá do konzoly JavaScriptu i v případě, že je protokolování vypnuté.
- [Oprava chyby] Pokožk Tipy pro čas přehrávače se vykreslují mimo kontext přehrávače při najetí myší na časový pruh trvání ukončení.
- [Oprava chyby] Přístup [Čtečka obrazovky] Program Narrator čte "orientační region" nebo "orientační oblast přehrávače videa", když se v prohlížeči soustředí na přehrávač
- [Oprava chyby] WEBOVÉ Nelze zakázat osnovu přehrávače prostřednictvím šablon stylů CSS.
- [Oprava chyby] Přístup Pokud je uživatel v režimu zobrazení na celé obrazovce, nelze se soustředit na celý hráč.
- [Oprava chyby] Pokožk Bydlí Vzhled neodpovídá lokalizovanému ŽIVÉmu textu v japonštině.
- [Oprava chyby] Pokožk Doba trvání a aktuální čas se odříznout, když datový proud > 60 min-[Oprava chyby] [iPhone | Live] přehrávač zobrazuje text pro aktuální čas a dobu trvání v ovládacím panelu.
- [Oprava chyby] WEBOVÉ Volání rozhraní API heuristiky přehrávači poskytuje výjimky jazyka JavaScript
- [Oprava chyby] [Nativní Html5 | iOS] Videotag vlastnost "playsinline", která nešíří do přehrávače
- [Oprava chyby] [iOS | IFRAME] Hráč nemůže vstoupit do celoobrazovkového režimu na iPhonu, pokud je přehrávač načtený v iframe.
- [Oprava chyby] WEBOVÉ Heuristiky AMP vždy funguje s hybridním profilem bez ohledu na možnosti přehrávače
- [Oprava chyby] [AMP | Win 8.1] vyvolá se při hostování aplikace Win 8.1 pomocí webviewu.

### <a name="changes-213"></a>Změny 2.1.3 ###

- Mění WEBOVÉ Přidání informací o koncovém bodu CDN v události FragmentDownloadComplete
- Mění WEBOVÉ Bydlí Vylepšená a optimalizovaná latence živého streamování

## <a name="212-official-hotfix"></a>2.1.2 (oficiální oprava hotfix) ##

### <a name="bug-fixes-212"></a>Opravy chyb 2.1.2 ####

- [Oprava chyby] Přístup [Narrator systému Windows] Program Narrator přečte "průběh půlnoci", když má uživatel kontext indikátoru průběhu a aktuální čas je 0:00
- [Oprava chyby] [Skin] velikost loga je pevně zakódována v kódu jazyka JavaScript.
- Přístup Klávesové zkratky Klávesová zkratka není povolena při kliknutí na přehrávač.

### <a name="changes-212"></a>Změny 2.1.2 ####

- Mění Protokolu Adresa URL manifestu protokolu, když se přehrávač nepovede načíst manifest

### <a name="features-212"></a>Funkce 2.1.2 ###

- Mění Předepsané Vybrané Vylepšené zatížení a časy spuštění přehrávače

## <a name="211-official-update"></a>2.1.1 (oficiální aktualizace) ##

### <a name="bug-fixes-211"></a>Opravy chyb 2.1.1 ####

- [Oprava chyby] iOS Nastavení automatického přehrávání na false vrátí nekonečný číselník v prohlížeči Safari pro iOS.
- [Oprava chyby] Hledání v čase větším než doba trvání obsahu neposkytuje nekonečné číselník
- [Oprava chyby] Klávesové zkratky pro získání kontextu přehrávače vyžadují více karet.
- [Oprava chyby] Video se zablokuje po dobu několika sekund od změny velikosti přehrávače v určitých prostředcích.
- [Oprava chyby] Nekonečné číselník (po dokončení hledání), když uživatel provede více hledání rychleji
- [Oprava chyby] Ovládací panel není během neaktivity skrytý.
- [Oprava chyby] Otevřením WebApp, který hostuje AMP, může způsobit, že se webová stránka načte dvakrát.
- [Oprava chyby] Neomezeně při přehrávání obsahu určitých prostředků přes flash tech
- [Oprava chyby] Nabídka další možnosti se nezobrazuje u modulů plug-in třetích stran
- [Oprava chyby] [Skin | Trubice] [Live] Pokud je hráč na živém okraji programu, zobrazí se dvě živé ikony.
- [Oprava chyby] Pokožk Logo nejde zakázat.
- [Oprava chyby] [DD + obsah] Nepřetržitý číselník ukazuje prostředky, které obsahují zvukovou stopu Dolby Digital Audio
- [Oprava chyby] Při přepínání zvukového jazyka během živě se zablokuje nejnovější AMP.
- [Chyba oprava] pevná nezobrazení pozadí pro číselník
- [Oprava chyby] Neomezený číselník v opravách chyb statických ukázek pro AES Flash tokeny

### <a name="changes-211"></a>Změny 2.1.1 ####

- Mění Byl přidán chybový kód pro požadavek Widevine https: od v58 pro Chrome musí být obsah Widevine načten nebo přehrán prostřednictvím protokolu, `https://` jinak se přehrávání nezdaří.
- Mění Přidána jmenovka ARIA pro číselník načítání, takže technologie usnadnění může při načítání obsahu v mluveném komentáři popsat "načítání videa"  

## <a name="210-official-release"></a>2.1.0 (oficiální verze) ##

### <a name="features-210"></a>Funkce 2.1.0 ###

- Zapnut [AzureHtml5JS] Podpora VOD AD pro předběžnou část příspěvku
- Zapnut Testování [AzureHtml5JS] Živá podpora ad pro předběžnou část příspěvku
- Zapnut Přidání nové možnosti vzhledu – AMP-flush
- Zapnut Přidání vylepšených popisků ARIA pro lepší integraci s čtečkami obrazovky/asistenční technologie
- Zapnut Pokožk Vzhled teď zobrazuje všechny ikony a tlačítka v režimu vysokého kontrastu.

### <a name="bug-fixes-210"></a>Opravy chyb 2.1.0 ###

- [Oprava chyby] Počet problémů s přístupností a uživatelským rozhraním
- [Oprava chyby] V IE9 se nesprávně načítá AMP.

### <a name="changes-210"></a>2.1.0 změny ###

- Mění Přestrukturované elementy modelu DOM v přehrávači pro přizpůsobení reklamy
- Mění Přepnuto z CSS na SCSS pro vývoj skinů
- Mění Vzory Přidání ukázky pro reklamy VOD
- Mění Vzory Přidání vzorku pro rychlost přehrávání
- Mění Vzory Přidání ukázky pro vyprázdnit vzhled

## <a name="200-beta-release"></a>2.0.0 (beta verze) ##

- [Change] Aktualizováno na VJS5
- zapnut Přidání nového rozhraní API pro tekutiny pro kapalinu odezvy přehrávače
- Zapnut Rychlost přehrávání
- Mění Přepnuto z CSS na SCSS pro vzhled

## <a name="183-official-hotfix-update"></a>1.8.3 (oficiální aktualizace hotfix) ##

### <a name="bug-fixes-183"></a>Opravy chyb 1.8.3 ###

- [Oprava chyby] [AzureHtml5JS] Některé prostředky s negativním DTS neumožňují přehrávání v Chrome.

## <a name="182-official-hotfix-update"></a>1.8.2 (oficiální aktualizace hotfix) ##

### <a name="bug-fixes-182"></a>Opravy chyb 1.8.2 ###

- [Oprava chyby] [AzureHtml5JS] Rychlejší zvukové přenosové rychlosti se nepřehrávají přes AzureHtml5JS.

## <a name="181-official-update"></a>1.8.1 (oficiální aktualizace) ##

### <a name="bug-fixes-181"></a>Opravy chyb 1.8.1 ###

- [Oprava chyby] iOS Titulky a titulky se nezobrazují v nativním přehrávači
- [Oprava chyby] WEBOVÉ Adresy URL streamování zálohovaných přes CDN připojené k tokenům ověřování se nehrají
- [Oprava chyby] Fairplay Kód chyby FairPlay chybí tech ID (bity [31-28] kódu chyby). Další informace naleznete v části kódy chyb.
- [Oprava chyby] Prohlížeče Modul Obsah PlayReady v Safari – vracení nekonečné nekonečného číselníku

### <a name="changes-181"></a>1.8.1 změny ###

- Mění Html5 Změna nativních protokolů Html5 tech verbose, aby obsahovaly události z VideoTag

## <a name="180-official-update"></a>1.8.0 (oficiální aktualizace) ##

### <a name="features-180"></a>Funkce 1.8.0 ###

- Funkce DIGITÁLNÍCH Přidání podpory FairPlay (Další informace najdete v [chráněném obsahu](azure-media-player-protected-content.md) )

### <a name="bug-fixes-180"></a>Opravy chyb 1.8.0 ###

- [Oprava chyby] WEBOVÉ Hledání uživatelů neaktivuje událost čekání při omezení sítě.
- [Oprava chyby] [Flash] Výběr kvality v programu Flash tech vyvolá výjimku
- [Oprava chyby] WEBOVÉ Možnost dynamického výběru kvality zobrazit v místní nabídce
- [Oprava chyby] Pokožk Je obtížné vybrat poslední položku nabídky kontextových nabídek

### <a name="changes-180"></a>1.8.0 změny ###

- Mění Aktualizace přehrávače na aktuální požadavky EME pro Chrome
- Mění Výchozí techOrder se změnily tak, aby vyhovovaly novému technickému html5FairPlayHLS (Další informace najdete v části [chráněný obsah](azure-media-player-protected-content.md) ).
- Mění [AzureHtml5JS] Zapnuté přehrávání MPEG-spojovníku v Safari
- Mění Vzory Změna vzorků s více technologiemi DRM pro přizpůsobení FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (oficiální aktualizace hotfix) ##

### <a name="bug-fixes-174"></a>Opravy chyb 1.7.4 ###

- [Oprava chyby] Chrome V případě, že má uživatel kontext přehrávače, se zobrazí modrý obrys kolem popisovače hledání.
- [Oprava chyby] IE9 Při načítání přehrávače v IE9 došlo k výjimce JavaScriptu.

## <a name="173-official-hotfix-update"></a>1.7.3 (oficiální aktualizace hotfix) ##

### <a name="bug-fixes-173"></a>Opravy chyb 1.7.3 ###

- [Oprava chyby] [AzureHtml5JS] Časování přehrávače v omezených sítích

### <a name="changes-173"></a>1.7.3 změny ###

- Mění Povoluje se webšifra na Edge pro dešifrování obsahu AES.
- Mění Optimalizace heuristiky AMP na účet pro bloky uložené v mezipaměti
- Mění [AzureHtml5JS] Optimalizace heuristiky snížením latence při odhadu šířky pásma

## <a name="172-official-hotfix-update"></a>1.7.2 (oficiální aktualizace hotfix) ##

### <a name="features-172"></a>Funkce 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- Zapnut [AzureHtml5JS | Firefox] povolení přehrávání Widevine pomocí EME pro Firefox 47 +
- Zapnut Přidat událost pro odstraňování hráčů
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Opravy chyb 1.7.2 ###

- [Oprava chyby] Kódované parametry dotazu adresy URL pro CDN Akamai nejsou správně dekódované.
- [Oprava chyby] Vyvolaná výjimka v manifestPlayableWindowLength ()
- [Oprava chyby] Po skončení přehrávání videa nemůže prohlížeč na videu vždycky kliknout na Přehrát.
- [Oprava chyby] Reakce na velikost, která není v souladu s rychlými změnami velikosti okna
- [Oprava chyby] [Edge | IE] reagující na změnu velikosti, která nezohledňuje zatížení stránky pro šířku = x, výšku = auto
- [Oprava chyby] [Android | Chrome] Chrome, který žádá o oprávnění k přehrání obsahu DRM, pokud není obsah zašifrovaný
- [Oprava chyby] Přístup Vstupního Ovládací prvky klávesnice nepoužívají správně výběr položek místní nabídky
- [Oprava chyby] Přístup Chybějící zobrazené ohraničení v režimu vysokého kontrastu
- [Oprava chyby] [Flash] Po uvolnění přehrávače dojde k výjimce, protože se neodebere naslouchací proces události myši.
- [Oprava chyby] [Flash] Problém s analýzou adresy URL manifestu se zakódovanými prostory
- [Oprava chyby] iOS Chyba typu při vyhodnocování tech. featuresVolumeControl

### <a name="changes-172"></a>1.7.2 změny ###

- Mění DIGITÁLNÍCH Přesunuté kontroly DRM po nastavení zdroje jenom ke kontrole, když je obsah zašifrovaný
- Mění AES Z požadavku na doručení klíče se odebral nedefinovaný text typu nebo prostého textu.
- Mění Přístup Systém Windows Narrator nyní čte "Media Player", pokud je kontext v přehrávači místo vlastností

## <a name="171-official-hotfix-update"></a>1.7.1 (oficiální aktualizace hotfix) ##

### <a name="features-171"></a>Funkce 1.7.1 ###

- Zapnut Přidaná možnost pro hybridní heuristický profil (ve výchozím nastavení je nastavený tento profil)

### <a name="bug-fixes-171"></a>Opravy chyb 1.7.1 ###

- [Oprava chyby] Návrh s odezvou nefunguje jako na standardu HTML5 (šířka = 100%, Výška = auto).
- [Oprava chyby] Procentuální hodnoty pro šířku a výšku se nechovají podle očekávání v v 1.7.0

## <a name="170-official-update"></a>1.7.0 (oficiální aktualizace) ##

### <a name="features-170"></a>Funkce 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- Zapnut [AzureHtml5JS] [Flash] Přidání currentMediaTime () pro získání kodéru čas kodéru aktuální doby v sekundách
- Zapnut [Flash] Implementovaná rozhraní API telemetrie pro stažení s videoBufferData () a audioBufferData ()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- Zapnut [Flash] Přidání události ' downloadbitratechanged '
- Zapnut Zvýšení času načítání v porovnání se staršími verzemi přehrávače
- Zapnut Chyby se protokolují do konzoly JavaScriptu.

### <a name="bug-fixes-170"></a>Opravy chyb 1.7.0 ###

- [Oprava chyby] Kódovaná adresa URL titulku s parametry řetězce dotazu, které se nezobrazují v přehrávači
- [Oprava chyby] Výjimka, která se vyvolala v případě, že není načtena technologie API amp Hráč. plakát () se nazývá.
- [Oprava chyby] Výjimka vyvolaná, když se funkce pokusí získat přístup ke Playeru po vyřazení
- [Oprava chyby] Přístup Chybějící osnova při zaměření na indikátor průběhu – hlavní pozice
- [Oprava chyby] Přístup Místní nabídky mají stín v režimu vysokého kontrastu.
- [Oprava chyby] [iOS] nativní přehrávání titulků přehrávače WebVTT nefunguje
- [Oprava chyby] [AzureHtml5JS] Při přehrávání streamu HTTP na serveru HTTPS by se měla zobrazit chyba 0x0100002, která místo toho jako výsledek smíšeného obsahu vydává nekonečnou číselník.
- [Oprava chyby] [AzureHtml5JS] Chybějící Koncový segment, který způsobuje chybu kontroly stavu smyček při zobrazení navnímaného nekonečného stavu ukládání do vyrovnávací paměti
- [Oprava chyby] [AzureHtml5JS] Nesprávný název zvukové stopy v nabídce, pokud se používají kódy useManifestForLabel = false a tři písmena
- [Oprava chyby] [AzureHtml5JS | Chrome] vnímaný nekonečný stav vyrovnávací paměti na konci obsahu způsobeného nepřesností s plovoucí desetinnou čárkou v době trvání pomocí JavaScriptu v Chrome
- [Oprava chyby] [Flash] Při vytvoření přehrávače Flash se nezávažná chyba nezávažně zobrazuje.
- [Oprava chyby] [Flash] Přehrávání selhává, když video a zvukové streamy používají různá časová měřítka, protože došlo k selhání nepřesnosti při zaokrouhlování s "adresou URL fragmentu (...). Nepovedlo se vygenerovat FLVTags.
- [Oprava chyby] [Flash] Problémy s analýzou adres URL manifestu pomocí zakódovaných prostorů
- [Oprava chyby] [Flash] Chybí zaškrtnutí k určení, jestli verze Flash Playeru >= 11,4, která způsobuje chybu v přehrávání, a ne návrat k dalšímu Techu v techOrder.
- [Oprava chyby] [Flash] AES Problémy, které přijímají tokeny AES s podtržítky
- [Oprava chyby] [Silverlight | OSX] "//" předpona manifestu namísto protokolu (HTTP nebo HTTPS) je rozpoznána jako neomezený číselník pro získání místního souboru

### <a name="changes-170"></a>1.7.0 změny ###

- Mění [Flash] Sloučené skripty SWF ("MSAdaptiveStreamingPlugin-osmf 2.0. swf" a "StrobeMediaPlayback. 2.0. swf") do jediného souboru SWF s názvem "StrobeMediaPlayback. 2.0. swf"
- Mění [Flash] Bylo aktualizováno šíření kódu chyby, abyste získali přesnější kódy chyb (např. 404s nyní má za následek 0x30200194 namísto obecné chyby 0x30200000).

## <a name="163-official-hotfix-update"></a>1.6.3 (oficiální aktualizace hotfix) ##

### <a name="bug-fixes-163"></a>Opravy chyb 1.6.3 ###

- [Oprava chyby] Výjimka běhového prostředí JavaScript při spuštění obslužné rutiny události klávesových zkratek po likvidaci přehrávače
- [Oprava chyby] Svém [AzureHtml5JS] Bez přehrávání na mobilním zařízení s použitím mobilní sítě
- [Oprava chyby] Aktualizace zfalšovat pro spuštění jako webový pracovní proces pro uvolnění uživatelského rozhraní

## <a name="162-official-hotfix-update"></a>1.6.2 (oficiální aktualizace hotfix) ##

### <a name="features-162"></a>Funkce 1.6.2 ###

- Zapnut Přidání dalších jazyků k lokalizaci (Další informace najdete v dokumentaci)

### <a name="bug-fixes-162"></a>Opravy chyb 1.6.2 ###

- [Oprava chyby] [IE9-10] Kliknutí na oblasti v minimalizovaném okně prohlížeče z důvodu chyby IE9/IE10, která se minimalizuje na Window. rozostření ()
- [Oprava chyby] [Flash] Nepřijímat tokeny AES s podtržítky

## <a name="161-official-hotfix-update"></a>1.6.1 (oficiální aktualizace hotfix) ##

### <a name="bug-fixes-161"></a>Opravy chyb 1.6.1 ###

- [Oprava chyby] [Bliknutí | Edge, IE] [Silverlight | IE] nejde získat fokus na jiné prvky uživatelského rozhraní pro vstupy nebo jiné v IE/Edge.
- [Oprava chyby] Selhání přehrávání AES při nedefinovaném zfalšovat
- [Oprava chyby] Svém [AzureHtml5JS | Chrome] nepřetržitý číselník nepřehrávání obsahu, když je ve smyčce kontroly stavu
- [Oprava chyby] [IE9] konzola. log () není podporována službou IE 9, která způsobila výjimku.

## <a name="160-official-update"></a>1.6.0 (oficiální aktualizace) ##

### <a name="features-160"></a>Funkce 1.6.0 ###

- [Funkce] 33% omezení velikosti azuremediaplayer.min.js
- Zapnut [AzureHtml5JS | Edge] [netestovaný] Podpora pro DD + audio streamy na okraji (po počátečním výběru se nemění žádný kodek). Aplikace musí v tuto chvíli vybrat správný zvukový stream.
- Zapnut Ovládací prvky pro klávesové zkratky (další podrobnosti najdete v dokumentaci)
- Zapnut Tip času průběhu najetí myší na přesný čas hledání
- Zapnut Umožňuje asynchronní detekci modulů plug-in, pokud v modulu plug-in existuje metoda setupDone.

### <a name="bug-fixes-160"></a>Opravy chyb 1.6.0 ###

- [Oprava chyby] Nevyprazdňování protokolu paměti v getMemoryLog (true)
- [Oprava chyby] Přemístění výběru přenosové rychlosti při přesunu myši způsobuje problém s výběrem nižších přenosových rychlostí prostřednictvím ovládacího prvku myš
- [Oprava chyby] Při provádění kontroly DRM dojde v systému Mac k chybě aplikace.
- [Oprava chyby] Třídy CSS jsou snadno nechtěně přepsány
- [Oprava chyby] Chrome Aktualizace identifikace z prohlížeče řetězce uživatelského agenta je hraniční
- [Oprava chyby] [AzureHtml5JS] Tlačítko s titulky se nezobrazují na panelu nástrojů na okraji (Win10) nebo Chrome (Mac)
- [Oprava chyby] Svém [AzureHtml5JS | Chrome] výjimka InvalidStateError při volání endOfStream () na krátká videa
- [Oprava chyby] Firefox Odebrání upozornění DRM, které vyvolala Firefox při kontrole možností prohlížeče
- [Oprava chyby] Html5 Titulky a popisky nejsou zobrazené s progresivním obsahem MP4.
- [Oprava chyby] [Flash] Zprávy s porovnávacími časovými razítky byly přihlášeny v opačném pořadí.
- [Oprava chyby] Přístup [Chrome | Firefox] TAB a vybrat ovládací prvky automaticky vybrat první položku nabídky
- [Oprava chyby] Přístup Karta pro řízení hlasitosti – tlačítko

### <a name="changes-160"></a>1.6.0 změny ###

- Mění Pro výběr na úrovni kvality použít čas dešifrování AES
- Mění Aktualizace přepisu adresy URL pro použití HLS v4 před HLS v3 pro streamování s více zvuky
- Mění Nastavit nativeControlsForTouch na hodnotu false jako výchozí (aby fungovala správně, musí být false)

## <a name="150-official-update"></a>1.5.0 (oficiální aktualizace) ##

### <a name="features-150"></a>Funkce 1.5.0 ###

- Zapnut Vylepšení obecného webového zabezpečení (prevence injektáže, XSS atd.)
- Zapnut Moduly pro integraci modulu plug-in SDN pro událost a možnosti sourceset. Sdn
- Zapnut Zpracování robustních chyb 5XX a 4XX během přehrávání

### <a name="bug-fixes-150"></a>Opravy chyb 1.5.0 ###

- [Oprava chyby] Aktualizace šablon stylů CSS minifikace pro použití kódů písma entity HTML pro tlačítka místo Unicode
- [Oprava chyby] [AzureHtml5JS] Obsah s více DRM vždy vybere token prvního prvku z protectionInfo, což způsobí selhání druhé DRM.
- [Oprava chyby] [AzureHtml5JS] Hledání se nikdy nedokončuje při hledání v oblasti s chybějícími segmenty.
- [Oprava chyby] [AzureHtml5JS | Edge] povolit EME v aktualizaci Edge pro přehrávání PlayReady
- [Oprava chyby] [AzureHtml5JS | Firefox] aktualizace EME povolte, pokud chcete, aby aplikace Firefox V42 + (s programem MSE) byla chráněná proti Silverlightu pro chráněný obsah
- [Oprava chyby] [Flash] Chyba aktualizace. zpráva z čísla na podrobný řetězec

### <a name="changes-150"></a>1.5.0 změny ###

- Mění Plakáty aktuálně fungují jenom jako absolutní adresy URL.

## <a name="140-official-update"></a>1.4.0 (oficiální aktualizace) ##

### <a name="features-140"></a>Funkce 1.4.0 ###

- Zapnut [AzureHtml5JS | Chrome] jednoduchá podpora Widevine DRM
- Zapnut [AzureHtml5JS] Zpracování robustních chyb 404/412 během přehrávání

### <a name="bug-fixes-140"></a>Opravy chyb 1.4.0 ###

- [Oprava chyby] [Flash] Vylepšení ověřování parametrů

## <a name="130-official-update"></a>1.3.0 (oficiální aktualizace) ##

### <a name="features-130"></a>Funkce 1.3.0 ###

- Zapnut [AzureHtml5JS] [Flash] Přepínání zvukového obsahu s více zvukovými kodeky

### <a name="bug-fixes-130"></a>Opravy chyb 1.3.0 ###

- [Oprava chyby] [AzureHtml5JS | Chrome] občasné nekonečné číselníky
- [Oprava chyby] [AzureHtml5JS | IE] [Windows Phone] výjimka způsobující, že Windows Phone mají potíže s přehráváním
- [Oprava chyby] [Flash] U dalších instancí se nezdařila hodnota AutoPlay nastavená na hodnotu false.
- [Oprava chyby] Problémy s velikostí v nabídce uživatelského rozhraní

## <a name="120-official-update"></a>1.2.0 (oficiální aktualizace) ##

### <a name="features-120"></a>Funkce 1.2.0 ###

- Zapnut [AzureHtml5JS | Firefox] podpora, když je povolený MSE
- Zapnut Už nepotřebujete, aby aplikace poskytovala cesty k záložním technickým binárním souborům (SWF, XAP). Cesta je relativní ke skriptu Azure Media Player.

### <a name="bug-fixes-120"></a>Opravy chyb 1.2.0 ###

- [Oprava chyby] [AzureHtml5JS | Chrome] přehrávač na pozadí se posune za živou hranou.
- [Oprava chyby] [AzureHtml5JS | Edge] celá obrazovka nepracuje
- [Oprava chyby] [AzureHtml5JS] Při nastavení v možnostech není protokolování správně zapnuté.
- [Oprava chyby] Blikající Při čekání na událost se zobrazuje jak ukládání do vyrovnávací paměti, tak i ikona vyrovnávací paměti
- [Oprava chyby] V případě, že požadavek na počáteční šířku pásma neproběhne, umožní přehrávání pokračovat.
- [Oprava chyby] Nepovedlo se načíst přehrávač při inicializaci s nedefinovanými možnostmi.
- [Oprava chyby] Při pokusu o uvolnění přehrávače poté, co je již odstraněn, dojde k výjimce vdata
- [Oprava chyby] Nesprávně namapované ikony panelu kvality

## <a name="111-official-hotfix-update"></a>1.1.1 (oficiální aktualizace hotfix) ##

### <a name="bug-fixes-111"></a>Opravy chyb 1.1.1 ###

- [Oprava chyby] Problém s aplikací Internet Explorer na celé obrazovce
- [Oprava chyby] Moduly plug-in již nejsou přepsány

## <a name="110-official-update"></a>1.1.0 (oficiální aktualizace) ##

### <a name="features-110"></a>Funkce 1.1.0 ###

- Zapnut Aktualizovat řetězce lokalizace uživatelského rozhraní

### <a name="bug-fixes-110"></a>Opravy chyb 1.1.0 ###

- [Oprava chyby] Tlačítko pro velký počet přehrání nemá dostatečný kontrast.
- [Oprava chyby] Indikátor fokusu na kartě vizuálu
- [Oprava chyby] Nabídka pro výběr přenosové rychlosti teď používá správné informace o rozlišení
- [Oprava chyby] Další nabídka možností je teď dynamicky velikost
- [Oprava chyby] Různé problémy uživatelského rozhraní

## <a name="100-official-release"></a>1.0.0 (oficiální verze) ##

### <a name="features-100"></a>Funkce 1.0.0 ###

- Zapnut Základní testování dostupnosti pro ovládací prvek karta, ovládací prvek pro výběr, čtečku obrazovky, uživatelské rozhraní s vysokým kontrastem
- Zapnut Aktualizované uživatelské rozhraní
- Zapnut Protokolování vývoje
- Zapnut Rozhraní API pro dynamické nastavení stop titulků a titulků
- Zapnut Základní funkce lokalizace
- Zapnut Konsolidace kódu chyby napříč technickými pracovníky
- Zapnut Nový kód chyby pro, když nejsou nainstalované moduly plug-in (jako flash nebo Silverlight)
- Zapnut [AzureHtml5JS] Implementované základní diagnostické události

### <a name="bug-fixes-100"></a>Opravy chyb 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Oprava chyby] [AzureHtml5JS] Při živém přehrávání se zamrznutí na MPD aktualizace, když jsou v časovém razítku malé nepřesnosti.
- [Oprava chyby] [AzureHtml5JS] Zmírnění některých problémů s živým přehráváním
- [Oprava chyby] [AzureHtml5JS] Vyprázdnit vyrovnávací paměť při zapnuté heuristické velikosti okna a přejít na obrazovku s vyšším rozlišením
- [Oprava chyby] [AzureHtml5JS] Chrome teď správně zobrazuje ukončenou událost. *Při použití AzureHtml5JS se při použití odkazu na předchozí známý problém Chromu nepošle správně událost ukončení. V podkladovém prohlížeči se vyskytl problém.*
- [Oprava chyby] [AzureHtml5JS] Pro tento techer byl zakázán Safari pro řešení *potíží s přehráváním pomocí OSX Yosemite s AzureHtml5JS tech. K dispozici jsou problémy s implementací programu MSE. Dočasné zmírnění: vynutí aplikaci Flash, Silverlight jako technickou objednávku pro tyto uživatelské agenty.*
- [Oprava chyby] [Flash] loadstart aktivována po chybě

## <a name="020-beta"></a>0.2.0 (beta verze) ##

### <a name="features-020"></a>Funkce 0.2.0 ###

- Zapnut Dokončené testování PlayReady a AES pro na vyžádání a živé – viz tabulka kompatibility
- Zapnut Manipulace s nekontinuitou
- Zapnut Podpora pro časová razítka větší než 2 ^ 53
- Zapnut Parametr dotazu adresy URL uchovává požadavek manifestu.
- Zapnut Netestovanými Podpora pro `QuickStart` `HighQuality` profily a heuristické služby
- Zapnut Netestovanými Vystavení informací streamu videa pro přenosové rychlosti, šířku a výšku na AzureHtml5JS a na blesku
- Zapnut Netestovanými Výběr přenosové rychlosti na AzureHtml5JS a přehrávači (viz dokumentace k rozhraní API)

### <a name="bug-fixes-020"></a>Opravy chyb 0.2.0 ###

- [Chyba oprava] velké tlačítko Přehrát teď zobrazitelné v WP 8.1
- [Oprava chyby] Opravili jsme několik problémů s přehráváním za provozu.
- [Chyba oprava] zrušit ztlumení – tlačítko teď funguje na uživatelském rozhraní.
- [Chyba oprava] aktualizované prostředí načítání uživatelského rozhraní pro režim automatického přehrávání
- [Oprava chyby] Problém zavaděče AMD a definování konfliktů metod
- [Oprava chyby] Problém načítání aplikace WP 8,1 Cordova
- [Oprava chyby] Chráněný obsah dotazování na platformu/technickou podporu typ ochrany replik k výběru vhodného Techu pro přehrávání.  Opravuje předchozí známý problém_obsahu PlayReady z aplikace Chrome (Desktop)/Safari 8 (na OSX Yosemite) v současné době neumožňuje použití aplikace Silverlight Player_.
- [Chyba Fix] nezachycená výjimka v WinServer 2012 R2 z důvodu Media Foundation není ve výchozím nastavení nainstalovaná na tomto počítači.  Došlo k pokusu o použití rozhraní API značek HTML, která nejsou implementována, čímž dojde k chybě. Současné zmírnění je zachytit tuto chybu a vrátit hodnotu false namísto vyvolání chyby.
- [Chyba oprava] vždy získá segment init po hledání nebo selhání protokolu HTTP, které brání histogramu během přehrávání.
- [Chyba oprava] vypnout sledování simulovaného průběhu a timeupdates, když došlo k chybě.
- [Chyba oprava] odebrat nabídku kliknutím pravým tlačítkem
- [Oprava chyby] [AzureHtml5JS] chybová zpráva, která se nezobrazuje, když je sada tokenů pro obsah PlayReady neplatná
- [Oprava chyby] [AzureHtml5JS] při živém přehrávání se neberou v úvahu heuristické velikosti oken.
- [Oprava chyby] [Flash] Odstranění impulzu Media Player zobrazené zprávy, aby se zobrazily jenom Azure Media Player zprávy
- [Oprava chyby] [Silverlight] při hledání po dobu trvání nebo menší než 0 nejde získat událost Seek

## <a name="010-beta-release"></a>0.1.0 (beta verze) ##

Počáteční verze předběžného vydání

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)
