---
title: Azure Media Player Changelog
description: Azure Media Player changelog.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 15f8a3ac8c2777b3a878de92db495e559f64ad20
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726547"
---
# <a name="changelog"></a>Changelog #

## <a name="224-official-update-february-22-2019"></a>2.2.4 (Oficiální aktualizace 22. února 2019) ##

### <a name="bug-fixes-224"></a>Opravy chyb 2.2.4 ###

- [Oprava chyby] [AMP] [Přístupnost] Po zobrazení chybové obrazovky byla odebrána dosažitelná karta Fantom
- [Oprava chyby] [AMP] Opravena klávesová zkratka "M" pro IE11 a Edge
- [Oprava chyby] [AMP] Opravena výjimka pro titulky CEA708
- [Oprava chyby] [AMP] Opraven problém se zmrazením videa v prohlížeči Edge

### <a name="changes-224"></a>Změny 2.2.4 ###

- To je v pořádku. [AMP] Když dojde k chybě dešifrování fragmentu, přehrávač znovu zopakuje aktuální a různé fragmenty, aby obnovil přehrávání
- To je v pořádku. [AMP] Díky AMP tolerantnější k překrývajícím se fragmentům videa nebo zvuku

## <a name="223-official-update-january-9-2019"></a>2.2.3 (Oficiální aktualizace leden 9 2019) ##

### <a name="features"></a>Funkce ###

- To je v pořádku. To je v pořádku. Přidána nabídka zvukové stopy pro přehrávání Safari HLS

### <a name="bug-fixes-223"></a>Opravy chyb 2.2.3 ###

- [Oprava chyby] [AMP] [Přístupnost] Během živého vysílání nelze tlačítko "live" vybrat pomocí klávesnice
- [Oprava chyby] [AMP] Opraveny chyby při selhání testu MSE 0x0400003
- [Oprava chyby] [AMP] Opraven problém, kdy video mohlo při spuštění živého přenosu zamrznout

### <a name="changes-223"></a>Změny 2.2.3 ###

- To je v pořádku. [AMP] Do protokolu byly přidány další informace, které umožňují lepší diagnostiku.
- To je v pořádku. [AMP] Pokud je ve stejném rozlišení obrazovky k dispozici více než jeden přenosový tok, jsou všechny přenosové rychlosti k dispozici pro výběr

## <a name="222-official-update"></a>2.2.2 (Oficiální aktualizace) ##

### <a name="bug-fixes-222"></a>Opravy chyb 2.2.2 ###

- [Oprava chyby] [AMP] Když hráč narazí na přechodný výpadek sítě, okamžitě zastaví přehrávání
- [Oprava chyby] [AMP] [Přístupnost] Chybový dialog není přístupný pomocí klávesnice
- [Oprava chyby] [AMP] Nekonečná číselník zobrazená při přehrávání zvukového prostředku namísto nepodporované chyby

### <a name="changes-222"></a>Změny 2.2.2 ###

- To je v pořádku. [AMP] přidány lokalizované řetězce pro reklamní uI

## <a name="221-official-update"></a>2.2.1 (Oficiální aktualizace) ##

### <a name="features-221"></a>Funkce 2.2.1 ###

- To je v pořádku. TO JE V POŘÁDKU. Přidána podpora pro HLS CMAF

### <a name="bug-fixes"></a>Opravy chyb ###

- [Oprava chyby] [AMP] nejasné časovače v logice opakování, které přinášejí chyby přehrávání
- [Oprava chyby] [AMP] [Firefox] skončil událost není vystřelil na Firefox a Chrome, když zastavil živý program
- [Oprava chyby] [AMP] Ovládací prvky zobrazené po setsource, i když jsou ovládací prvky nastaveny na false v možnostech přehrávače

### <a name="changes"></a>Změny ###

- To je v pořádku. [Živé titulky] Změnil název rozhraní API pro titulky CEA z 608 na 708. Další informace naleznete v tématu [CEA708 Captions Settings](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (Oficiální vydání) ##

### <a name="features-220"></a>Funkce 2.2.0 ###

- To je v pořádku. [Azurehtml5JS] To je v pořádku. [LiveCaptions] Cea 708 titulky podporu v Azurehtml5JS a FlashSS tech pro jasné a AES obsah.

### <a name="bug-fixes-220"></a>Opravy chyb 2.2.0 ###

- [Oprava chyby] Detekce verze Flash nefunguje v Prohlížeči Chrome/Edge

### <a name="changes-220"></a>Změny 2.2.0 ###

- To je v pořádku. [AMP] To je v pořádku. Název heuriistického profilu byl změněn z funkce Rychlé zahájení na lowlatency.
- To je v pořádku. To je v pořádku. Změna přehrávače Flash pro detekci verzí umožňuje přehrávání obsahu AES pomocí nové aktualizace Adobe Flash.

## <a name="219-official-hotfix"></a>2.1.9 (Oficiální oprava hotfix) ##

### <a name="bug-fixes-219"></a>Opravy chyb 2.1.9 ###

- [Oprava chyby] Je to tak. Výjimka, ke které dochází při přechodu živých streamů na video na vyžádání / živé archivy

### <a name="changes-219"></a>Změny 2.1.9 ###

- To je v pořádku. To je v pořádku. To je v pořádku. Upravená logika Flash tech, aby nepoužívala sharedbytearrays pro dešifrování AES, protože společnost Adobe zablokovala použití od aplikace Flash 30. Upozorňujeme, že přehrávání bude fungovat až poté, co společnost Adobe nasadí novou verzi programu Flash kvůli chybě ve verzi 30. Další podrobnosti naleznete v [tématu Známé problémy](azure-media-player-known-issues.md)

## <a name="218-official-update"></a>2.1.8 (oficiální aktualizace) ##

### <a name="bug-fixes-218"></a>Opravy chyb 2.1.8 ###

- [Oprava chyby] Spinner občas nezobrazuje post seek a pre-play
- [Oprava chyby] Přehrávač se nespustí ztlumený, pokud je povolena možnost ztlumený
- [Oprava chyby] Posuvník hlasitosti se zobrazí, když jsou ovládací prvky nastaveny na hodnotu false
- [Oprava chyby] Přehrávání se občas opakuje, když uživatel přeskočí na živý okraj
- [Oprava chyby] [Firefox] Přehrávač občas vyvolá výjimku JavaScriptu při načtení
- [Oprava chyby] [Přístupnost] Tlačítko Přehrát/ Pozastavit/Pozastavit/Hlasitost ztratí obrys fokusu při výběru pomocí ovládacích prvků klávesnice
- [Oprava chyby] Opraven únik paměti na přehrávači je likvidován
- [Oprava chyby] Volání src() po chybách hráče neobnoví zdroj
- [Oprava chyby] Je to tak. AMP je ve stavu konstantnínačítání, když uživatel klikne na tlačítko Live po ukončení vysílání
- [Oprava chyby] To je v pořádku. Přehrávač přestane reagovat a přehrávání se nezdaří, když se prohlížeč minimalizuje na pozadí.

### <a name="changes-218"></a>Změny 2.1.8 ###

- To je v pořádku. Aktualizováno 0x0600001 errror zobrazit při přehrávání obsahu AES s Flash 30, protože není podporována v tomto okamžiku. Další podrobnosti naleznete v [tématu Známé problémy](azure-media-player-known-issues.md)
- To je v pořádku. Přidány další opakování pro živé scénáře při požadavky manifestu 404 nebo vrátí prázdné manifesty.

## <a name="217-official-update"></a>2.1.7 (oficiální aktualizace) ##

### <a name="features-217"></a>Funkce 2.1.7 ###

- To je v pořádku. [AzureHtml5JS] Přidána možnost konfigurace pro vyprázdnění zastaralých dat ve vyrovnávací paměti zdroje média

### <a name="bug-fixes-217"></a>Opravy chyb 2.1.7 ###

- [Oprava chyby] [Přístupnost] [Čtečka obrazovky] Odstraněno prázdné záhlaví, které hráč zahrnul, když není nastaven titul
- [Oprava chyby] To je v pořádku. AMP vyvolá výjimku při přehrávání v univerzální aplikaci pro Windows
- [Oprava chyby] [OSX] setActiveTextTrack() nefunguje v Safari na OSx
- [Oprava chyby] Je to tak. Kliknutím na live edge po likvidaci a re inicializaci hráče výnosy výjimku
- [Oprava chyby] To je v pořádku. Aktuální čas zkrácený pro určitá aktiva
- [Oprava chyby] Oprava [DRM] zahrnutá pro podporu přehrávání v prohlížečích, které podporují více CENC DRM

### <a name="changes-217"></a>Změny 2.1.7 ###

- To je v pořádku. [Ukázky] [Přístupnost] Do všech ukázek byla přidána značka jazyka

## <a name="216-official-update"></a>2.1.6 (Oficiální aktualizace) ##

### <a name="bug-fixes-216"></a>Opravy chyb 2.1.6 ###

- [Oprava chyby] AMP zobrazující nesprávnou dobu trvání pro konkrétní majetek
- [Oprava chyby] [Fairplay-HLS] Chyby fairplay, které se nešíří do uživatelského uživatelského bodu
- [Oprava chyby] Vlastní heuristické vlastnosti jsou ignorovány v AMP 2.1.5.

### <a name="changes-216"></a>Změny 2.1.6 ###

- To je v pořádku. [FairPlayDRM] Byl odstraněn časový režim pro žádost cert i žádost o licenci pro FairPlay, aby byla zajištěna parita s implementací PlayReady a Widevine
- To je v pořádku. Různé heuristické vylepšení pro boj s rozmazaným obsahem

### <a name="features-216"></a>Funkce 2.1.6 ###

- To je v pořádku. Přidána podpora mpd-time-cmaf formátu

## <a name="215-official-hotfix"></a>2.1.5 (Oficiální oprava hotfix) ##

### <a name="bug-fixes-215"></a>Opravy chyb 2.1.5 ###

- [Oprava chyby] [Titulky] Styl VTT není správně vykreslen přehrávačem
- [Oprava chyby] [Přístupnost] Tlačítko Live nemá štítek árie

## <a name="214-official-update"></a>2.1.4 (oficiální aktualizace) ##

### <a name="bug-fixes-214"></a>Opravy chyb 2.1.4 ###

- [Oprava chyby] [Přístupnost] [Focus] Uživatelé nemohou být pomocí karty, aby se zaměřili na vlastní tlačítka přidaná vpravo od tlačítka na celou obrazovku na ovládacím panelu
- [Oprava chyby] [IE11] [Pruh hlasitosti] Tabulátorem na hlasitost automaticky otevíraná kopie je celá obrazovka videa flash v IE11, zatímco v režimu celé obrazovky
- [Oprava chyby] [Kůže| Flush] Mezera zobrazená mezi ovládacím panelem a automaticky otevíraná okno panelu hlasitosti
- [Oprava chyby] [AMP] [Titulky] Staré vložené stopy nejsou vymazány při změně zdroje v existujícím přehrávači
- [Oprava chyby] [Přístupnost] To je v pořádku. Program Pro čtení z obrazovky nesprávně čte ovládání hlasitosti
- [Oprava chyby] To je v pořádku. Play Event občas není oheň z Flash tech
- [Oprava chyby] [AMP] [Focus] Přehrát/pozastavit vyžaduje dvě kliknutí, když má hráč zaostření a je v režimu celé obrazovky
- [Oprava chyby] [AMP] To je v pořádku. Na indikátoru průběhu pro konkrétní majetek se zobrazuje nesprávná doba trvání
- [Oprava chyby] To je v pořádku. To je v pořádku. Analyzátor VAST nezpracovává soubor VAST, který nemá událost průběhu
- [Oprava chyby] To je v pořádku. [AMP 2.1.1] Opraven problém s podporou pluginů Hive SDN
- [Oprava chyby] [Přístupnost] Předčítání čte "Tlačítko ztlumení půlnoci", když má uživatel zaostření tlačítka hlasitosti

### <a name="changes-214"></a>Změny 2.1.4 ###

- To je v pořádku. [Přístupnost] [Asistenční technologie] Tlačítka mají nyní aria-live vlastnost zlepšit zkušenosti s asistenční technologií
- To je v pořádku. [Přístupnost] [Tlačítko hlasitosti| Předčítání]Vylepšena možnost usnadnění hlasitosti úpravou funkce tabulátoru a chování posuvníku. Tyto změny usnadňují uživatelům klávesnice úpravu hlasitosti přehrávače
- To je v pořádku. Prodloužený časový rámec kontextové nabídky nečinnosti z 3 na 5 sekund
- To je v pořádku. [Přístupnost] [Světelnost] Vylepšený kontrastní poměr světelnosti v rozbalovacích nabídkách v nastavení titulků

## <a name="213-official-update"></a>2.1.3 (Oficiální aktualizace) ##

### <a name="bug-fixes-213"></a>Opravy chyb 2.1.3 ###

- [Oprava chyby] [Pluginy| Název Overlay] Název Overlay plugin vyvolá JS výjimky s AMP v2. X+
- [Oprava chyby] Událost Source Set je odeslána do konzoly JavaScript i v případě, že je protokolování vypnuto
- [Oprava chyby] To je v pořádku. Tipy času hráče jsou vykresleny mimo kontext přehrávače při najetí na jeden konec doby trvání bar
- [Oprava chyby] [Přístupnost] [Čtečka obrazovky] Předčítání čte "Orientační bod oblasti" nebo "Orientační bod pro oblast přehrávače videa", když se prohlížeč zaměřuje na přehrávač
- [Oprava chyby] [AMP] Nelze zakázat osnovu přehrávače prostřednictvím CSS
- [Oprava chyby] [Přístupnost] Pokud je uživatel v režimu celé obrazovky, nelze zaostřit na celý přehrávač.
- [Oprava chyby] To je v pořádku. Je to tak. Vzhled nereaguje na lokalizovaný text LIVE v japonštině
- [Oprava chyby] To je v pořádku. Doba trvání a aktuální čas se při > proudu > 60 min -[Oprava chyb][iPhone| Live]player zobrazuje text pro aktuální čas/dobu trvání na ovládacím panelu
- [Oprava chyby] [AMP] Volání heuristik přehrávače API dává výjimky JavaScript
- [Oprava chyby] [Nativní Html5|iOS] Videotag vlastnost "playsinline" není šíření do přehrávače
- [Oprava chyby] [iOS|iframe] Přehrávač nemůže vstoupit na celou obrazovku v iPhone, pokud je přehrávač načten do prvku iframe
- [Oprava chyby] [AMP] To je v pořádku. AMP vždy pracuje s hybridním profilem bez ohledu na možnosti hráče
- [Oprava chyby] [AMP| Win8.1]hází, když je hostován v aplikaci Win8.1 s webovým zobrazením

### <a name="changes-213"></a>Změny 2.1.3 ###

- To je v pořádku. [AMP] Přidány informace o koncovém bodu CDN v události FragmentDownloadComplete
- To je v pořádku. [AMP] Je to tak. Vylepšená a optimalizovaná latence živého streamování

## <a name="212-official-hotfix"></a>2.1.2 (Oficiální oprava hotfix) ##

### <a name="bug-fixes-212"></a>Opravy chyb 2.1.2 ####

- [Oprava chyby] [Přístupnost] [Windows Předčítání] Předčítání čte "Průběh půlnoci", pokud má uživatel kontext indikátoru průběhu a aktuální čas je 0:00
- [Oprava chyby] Velikost loga [Skin] je pevně zakódována v kódu JavaScriptu
- [Přístupnost] [Klávesové zkratky] Klávesové zkratky nejsou po klepnutí na přehrávač povoleny.

### <a name="changes-212"></a>Změny 2.1.2 ####

- To je v pořádku. To je v pořádku. Protokolovat adresu URL manifestu, když se hráči nepodaří načíst manifest

### <a name="features-212"></a>Funkce 2.1.2 ###

- To je v pořádku. [Výkon] [Optimalizace] Vylepšené časy načítání a spouštění hráčů

## <a name="211-official-update"></a>2.1.1 (Oficiální aktualizace) ##

### <a name="bug-fixes-211"></a>Opravy chyb 2.1.1 ####

- [Oprava chyby] [iOS] Nastavení automatického přehrávání na falešné výnosy nekonečné číselník v Safari pro iOS
- [Oprava chyby] Hledám čas větší než trvání obsahu výnosy nekonečné číselník
- [Oprava chyby] Klávesové zkratky vyžadují více karet klávesnice, aby se kontext přehrávače pracoval
- [Oprava chyby] Video zamrzne na několik sekund po změna velikosti přehrávače v určitých datových zdrojů
- [Oprava chyby] Nekonečné číselník (po hledání dokončí), když uživatel dělá více hledá rychle
- [Oprava chyby] Ovládací panel není během nečinnosti skrytý.
- [Oprava chyby] Otevření webové aplikace, která je hostitelem AMP, může způsobit, že webová stránka bude načtena dvakrát
- [Oprava chyby] Nekonečné při přehrávání obsahu určitých aktiv přes Flash Tech
- [Oprava chyby] Další nabídky Možností nejsou zobrazeny s pluginy třetích stran
- [Oprava chyby] [Kůže| Tube][Live] Dvě živé ikony jsou zobrazeny, když je hráč na živéhraně programu
- [Oprava chyby] To je v pořádku. Logo nelze zakázat.
- [Oprava chyby] [Obsah DD+] Průběžné číselník se zobrazí pro datové zdroje obsahující zvukovou stopu Dolby Digital
- [Oprava chyby] Nejnovější AMP zamrzne při přepínání zvukových jazykových stop během živého přenosu
- [Bug Fix] opraveno zmizení pozadí pro číselník
- [Oprava chyby] Nekonečné číselník v AES flash token statické ukázky opravy chyb

### <a name="changes-211"></a>Změny 2.1.1 ####

- To je v pořádku. Přidán kód chyby pro požadavek Widevine Https: od Chrome v58 musí `https://` být obsah widevine načten / přehrán přes protokol, jinak přehrávání selže.
- To je v pořádku. Přidán aria štítek pro vkládání číselník, takže asistenční technologie může vyprávějí "video loading" při načítání obsahu  

## <a name="210-official-release"></a>2.1.0 (Oficiální vydání) ##

### <a name="features-210"></a>Funkce 2.1.0 ###

- To je v pořádku. [AzureHtml5JS] VOD Ad Podpora pro pre-mid-post-rolích
- To je v pořádku. To je v pořádku. [AzureHtml5JS] Podpora živých reklam pro pre-mid-post-rolls
- To je v pořádku. Přidána nová možnost skinu - AMP-flush
- To je v pořádku. Přidány vylepšené aria štítky pro lepší integraci s čtečkami obrazovky / asistenční technologie
- To je v pořádku. To je v pořádku. Vzhled nyní zobrazuje všechny ikony a tlačítka jasně v režimu vysokého kontrastu

### <a name="bug-fixes-210"></a>Opravy chyb 2.1.0 ###

- [Oprava chyby] Počet oprav usnadnění přístupu a ui
- [Oprava chyby] AMP se v IE9 nenačítá správně

### <a name="changes-210"></a>Změny 2.1.0 ###

- To je v pořádku. Restrukturalizované prvky DOM v přehrávači tak, aby vyhovovaly reklamám
- To je v pořádku. Přechod z CSS na SCSS pro vývoj pokožky
- To je v pořádku. [Ukázky] Přidána ukázka pro VOD reklamy
- To je v pořádku. [Ukázky] Přidána ukázka pro rychlost přehrávání
- To je v pořádku. [Ukázky] Přidán vzorek pro flush skin

## <a name="200-beta-release"></a>2.0.0 (beta verze) ##

- [Změna]aktualizována na VJS5
- [funkce] Přidáno nové fluidní API pro tekutinu s odezvou hráčů
- To je v pořádku. Rychlost přehrávání
- To je v pořádku. Přechod z CSS na SCSS pro skin

## <a name="183-official-hotfix-update"></a>1.8.3 (oficiální aktualizace opravy hotfix) ##

### <a name="bug-fixes-183"></a>Opravy chyb 1.8.3 ###

- [Oprava chyby] [AzureHtml5JS] Některé datové zdroje se záporným systémem DTS se v Chromu nepřehrávají

## <a name="182-official-hotfix-update"></a>1.8.2 (oficiální aktualizace opravy hotfix) ##

### <a name="bug-fixes-182"></a>Opravy chyb 1.8.2 ###

- [Oprava chyby] [AzureHtml5JS] Vyšší přenosové rychlosti zvuku se přes AzureHtml5JS nepřehrají

## <a name="181-official-update"></a>1.8.1 (oficiální aktualizace) ##

### <a name="bug-fixes-181"></a>Opravy chyb 1.8.1 ###

- [Oprava chyby] [iOS] Titulky/titulky, které se nezobrazují v nativním přehrávači
- [Oprava chyby] [AMP] Adresy URL streamování podporované protokolem CDN připojené k ověřovacím tokenům, které se nepřehrávají
- [Oprava chyby] To je v pořádku. Kód chyby FairPlay chybí Tech ID (Bity [31-28] errorcode) viz Chybové kódy pro více informací
- [Oprava chyby] To je v pořádku. To je v pořádku. PlayReady obsah v Safari přináší nekonečné číselník

### <a name="changes-181"></a>Změny 1.8.1 ###

- To je v pořádku. [Html5] Změna nativní html5 tech podrobné protokoly obsahovat události z VideoTag

## <a name="180-official-update"></a>1.8.0 (Oficiální aktualizace) ##

### <a name="features-180"></a>Funkce 1.8.0 ###

- [Funkce] [DRM] Přidána podpora FairPlay (další informace najdete v [části Chráněný obsah)](azure-media-player-protected-content.md)

### <a name="bug-fixes-180"></a>Opravy chyb 1.8.0 ###

- [Oprava chyby] [AMP] Hledání uživatele neaktivuje událost čekání při omezení sítě
- [Oprava chyby] To je v pořádku. Výběr kvality v technologii flash tech vyvolá výjimku
- [Oprava chyby] [AMP] Dynamicky výběr kvality se zobrazuje v kontextové nabídce
- [Oprava chyby] To je v pořádku. Je obtížné vybrat poslední položku nabídky kontextových nabídek

### <a name="changes-180"></a>Změny 1.8.0 ###

- To je v pořádku. Aktualizováno přehrávač na aktuální požadavky Chrome EME
- To je v pořádku. Výchozí techOrder změnil tak, aby vyhovovaly nové tech- html5FairPlayHLS (viz [Chráněný obsah](azure-media-player-protected-content.md) pro více informací)
- To je v pořádku. [AzureHtml5JS] Povoleno Přehrávání MPEG-Dash v Safari
- To je v pořádku. [Ukázky] Změněny vzorky Multi-DRM pro FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (Oficiální aktualizace opravy hotfix) ##

### <a name="bug-fixes-174"></a>Opravy chyb 1.7.4 ###

- [Oprava chyby] To je v pořádku. Modrá osnova se zobrazí kolem popisovače hledání, když má uživatel kontext přehrávače
- [Oprava chyby] [IE9] JavaScript výjimka vyvolána při přehrávači naložené v IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (Oficiální aktualizace opravy hotfix) ##

### <a name="bug-fixes-173"></a>Opravy chyb 1.7.3 ###

- [Oprava chyby] [AzureHtml5JS] Vypršení časového limitu hráčů v omezených sítích

### <a name="changes-173"></a>Změny 1.7.3 ###

- To je v pořádku. Povolení webového šifrování na okraji pro dešifrování obsahu AES
- To je v pořádku. Optimalizace heuristiky AMP pro účet pro bloky dat uložené v mezipaměti
- To je v pořádku. [AzureHtml5JS] Optimalizace heuristiky snížením latence odhadu šířky pásma

## <a name="172-official-hotfix-update"></a>1.7.2 (oficiální aktualizace opravy hotfix) ##

### <a name="features-172"></a>Funkce 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- To je v pořádku. [AzureHtml5JS| Firefox] Povolit přehrávání Widevine s EME pro Firefox 47+
- To je v pořádku. Přidat událost pro hráče likvidace
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Opravy chyb 1.7.2 ###

- [Oprava chyby] Zakódované parametry dotazu ADRES URL CDN Akamai nejsou správně dekódovány
- [Oprava chyby] Výjimka je vyvolána na manifestPlayableWindowLength()
- [Oprava chyby] Prohlížeč nemůže vždy kliknout na přehrát na video poté, co video skončilo, aby se znovu sledovalo
- [Oprava chyby] Responzivní velikost neodpovídá rychlým změnám velikosti okna
- [Oprava chyby] [Hrana| IE] Responzivní dimenzování, které se neprojeví při načítání stránky pro width=x, height=auto
- [Oprava chyby] [Android| Chrome] Chrome žádá o oprávnění k přehrávání obsahu DRM, pokud obsah není šifrovaný
- [Oprava chyby] [Přístupnost] [Okraj] Ovládací prvky klávesnice nevybírají správně položky kontextové nabídky
- [Oprava chyby] [Přístupnost] Chybějící zobrazené ohraničení v režimu vysokého kontrastu
- [Oprava chyby] To je v pořádku. Naslouchací proces události myši není odebrán poté, co přehrávač disponuje výjimkou příčiny
- [Oprava chyby] To je v pořádku. Analýza adresy URL manifestu s kódované mezery
- [Oprava chyby] [iOS] Chyba typu při vyhodnocování technologie tech.featuresVolumeControl

### <a name="changes-172"></a>Změny 1.7.2 ###

- To je v pořádku. [DRM] Přesunuté kontroly DRM po nastavení zdroje pouze zkontrolovat, kdy je obsah šifrován
- To je v pořádku. To je v pořádku. Z požadavku na doručení klíče bylo odebráno nedefinované tělo typu/prostého textu.
- To je v pořádku. [Přístupnost] Předčítání systému Windows nyní čte "Media Player", když je kontext na přehrávači namísto vlastností

## <a name="171-official-hotfix-update"></a>1.7.1 (Oficiální aktualizace opravy hotfix) ##

### <a name="features-171"></a>Funkce 1.7.1 ###

- To je v pořádku. Přidána možnost pro hybridní heuriistický profil (tento profil je nastaven ve výchozím nastavení)

### <a name="bug-fixes-171"></a>Opravy chyb 1.7.1 ###

- [Oprava chyby] Responzivní návrh nefunguje podle standardu HTML5 (šířka = 100%, výška = auto)
- [Oprava chyby] Procentuální hodnoty šířky a výšky, které se nechovají očekávaným způsobem ve v1.7.0

## <a name="170-official-update"></a>1.7.0 (Oficiální aktualizace) ##

### <a name="features-170"></a>Funkce 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- To je v pořádku. [AzureHtml5JS] To je v pořádku. Přidáno currentMediaTime() pro získání času média kodéru aktuálního času v sekundách
- To je v pořádku. To je v pořádku. Implementována rozhraní API pro stahování telemetrií s videoBufferData() a audioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- To je v pořádku. To je v pořádku. Přidána událost downloadbitratechanged
- To je v pořádku. Doba načítání se zlepšila ve srovnání se staršími verzemi přehrávače
- To je v pořádku. Chyby jsou zaznamenány do konzoly JavaScript

### <a name="bug-fixes-170"></a>Opravy chyb 1.7.0 ###

- [Oprava chyby] Kódovaná adresa URL plakátu s parametry řetězce dotazu, které se v přehrávači nezobrazují
- [Oprava chyby] Výjimka vyvolána, když žádné tech naloženo a zesilovač rozhraní API. Player.poster() se nazývá
- [Oprava chyby] Výjimka vyvolána, když se funkce pokusí o přístup k přehrávači po vyřazení
- [Oprava chyby] [Přístupnost] Chybějící osnova na zaměření na vedoucí hledání indikátoru průběhu
- [Oprava chyby] [Přístupnost] Kontextové nabídky mají stín v režimu vysokého kontrastu
- [Oprava chyby] [iOS] nativní přehrávač WebVTT titulky přehrávání nefunguje
- [Oprava chyby] [AzureHtml5JS] Při přehrávání datového proudu HTTP na webu HTTPS by měla být zobrazena chyba 0x0100002, která místo toho poskytuje nekonečnou číselník v důsledku smíšeného obsahu.
- [Oprava chyby] [AzureHtml5JS] Chybějící koncový segment způsobující chybu kontroly stavu opakování zobrazující vnímaný nekonečný stav ukládání do vyrovnávací paměti
- [Oprava chyby] [AzureHtml5JS] Při použití kódů useManifestForLabel=false a kódů jazyka se třemi písmeny jsou použity nesprávné názvy zvukových stop v nabídce
- [Oprava chyby] [AzureHtml5JS| Chrome] Vnímaný nekonečný stav vyrovnávací paměti na konci obsahu způsobený nepřesností plovoucí desetinné čárky v době trvání s JavaScriptem v Chromu
- [Oprava chyby] To je v pořádku. Při vytvoření přehrávače Flash Player se na okamžik zobrazí nezávažná občasná chyba
- [Oprava chyby] To je v pořádku. Přehrávání selhává, když video a audio proudy používají různé časové osy kvůli selhání zaokrouhlení s "Fragment url (...) se nepodařilo vygenerovat FLVTagy"
- [Oprava chyby] To je v pořádku. Problémy s analýzou adres URL manifestu s kódovanými mezerami
- [Oprava chyby] To je v pořádku. Chybí kontrola, zda flash player verze >= 11.4, která způsobuje chybu v přehrávání namísto pádu zpět na další technologii v techOrder
- [Oprava chyby] To je v pořádku. To je v pořádku. Problémy s přijímáním tokenů AES s podtržítky v něm
- [Oprava chyby] [SilverlightSS| OSX] "//" předfixace manifestu namísto protokolu (HTTP nebo HTTPS) je rozpoznán jako místní soubor, který poskytuje nekonečnou číselník

### <a name="changes-170"></a>Změny 1.7.0 ###

- To je v pořádku. To je v pořádku. Sloučené skripty SWF ("MSAdaptiveStreamingPlugin-osmf2.0.swf" a "StrobeMediaPlayback.2.0.swf") do jediného souboru SWF nazvaného "StrobeMediaPlayback.2.0.swf"
- To je v pořádku. To je v pořádku. Byl aktualizován šíření kódu chyby, aby se získaly přesnější kódy chyb (např. 404s nyní za následek 0x30200194 namísto obecné chyby 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (oficiální aktualizace opravy hotfix) ##

### <a name="bug-fixes-163"></a>Opravy chyb 1.6.3 ###

- [Oprava chyby] Výjimka za běhu javascriptu při spuštění obslužné rutiny události klávesových zkratek po likvidaci přehrávače
- [Oprava chyby] [Android] [AzureHtml5JS] Žádné přehrávání na mobilním zařízení pomocí mobilní sítě
- [Oprava chyby] Aktualizováno Forge spustit jako webový pracovník uvolnit uživatelské hod

## <a name="162-official-hotfix-update"></a>1.6.2 (oficiální aktualizace opravy hotfix) ##

### <a name="features-162"></a>Funkce 1.6.2 ###

- To je v pořádku. Přidány další jazyky pro lokalizaci (další podrobnosti naleznete v dokumentaci)

### <a name="bug-fixes-162"></a>Opravy chyb 1.6.2 ###

- [Oprava chyby] [IE9-10] Kliknutím na oblasti kolem přehrávače minimalizované okno prohlížeče kvůli Chybě IE9/IE10, která minimalizuje na window.blur()
- [Oprava chyby] To je v pořádku. Nepřijetí tokenů AES s podtržítkem

## <a name="161-official-hotfix-update"></a>1.6.1 (oficiální aktualizace opravy hotfix) ##

### <a name="bug-fixes-161"></a>Opravy chyb 1.6.1 ###

- [Oprava chyby] [Flashss| Hrana,IE][SilverlightSS| IE] Nelze získat zaměření na jiné prvky uživatelského rozhraní pro vstupy nebo jiné v IE / Edge
- [Oprava chyby] Selhání přehrávání aes při falšení nedefinované
- [Oprava chyby] [Android] [AzureHtml5JS| Chrome] Kontinuální číselník nepřehrává obsah, když je ve smyčce kontroly stavu
- [Oprava chyby] [IE9] console.log() není podporován iE 9 způsobuje výjimku

## <a name="160-official-update"></a>1.6.0 (Oficiální aktualizace) ##

### <a name="features-160"></a>Funkce 1.6.0 ###

- [Funkce] 33% snížení velikosti azuremediaplayer.min.js
- To je v pořádku. [AzureHtml5JS| Edge][Netestovaný] Podpora zvukových proudů DD+ v Edge (bez přepínání kodeků po počáteční volbě). Aplikace musí vybrat správný zvukový proud v tomto okamžiku.
- To je v pořádku. Ovládací prvky kláves (další podrobnosti naleznete v dokumentech)
- To je v pořádku. Doba průběhu tip vznášet se pro čas přesné hledání
- To je v pořádku. Povolit asynchronní detekci pluginů, pokud setupDone metoda existuje v pluginu

### <a name="bug-fixes-160"></a>Opravy chyb 1.6.0 ###

- [Oprava chyby] Protokol paměti není vyprázdnění na getMemoryLog (true)
- [Oprava chyby] Výběrová pole oteknutí se resetuje při pohybu myší, což způsobuje problém při výběru nižších přenosových rychlostí pomocí ovládacího prvku myši
- [Oprava chyby] Mac Office v aplikaci se při provádění kontroly DRM zhroutí
- [Oprava chyby] Třídy CSS jsou snadno náhodně přepsány
- [Oprava chyby] To je v pořádku. Aktualizovat identifikaci z prohlížeče řetězců user-agent je Edge
- [Oprava chyby] [AzureHtml5JS] Tlačítko Titulky se nezobrazuje v liště nástrojů v Edge (Win10) nebo Chrome (Mac)
- [Oprava chyby] [Android] [AzureHtml5JS| Chrome] Výjimka InvalidStateError na endOfStream() volání krátkých videí
- [Oprava chyby] [Firefox] Odstranění VAROVÁNÍ DRM způsobené Firefoxem při kontrole možností prohlížeče
- [Oprava chyby] [Html5] Titulky/titulky nejsou zobrazeny s progresivním obsahem mp4
- [Oprava chyby] To je v pořádku. Zprávy s odpovídajícími časovými razítky byly zaznamenány v opačném pořadí
- [Oprava chyby] [Přístupnost] [Chrome| Firefox] Tab a vyberte ovládací prvky automaticky vybrat první položku nabídky
- [Oprava chyby] [Přístupnost] Tabulátorem pro ovládání tlačítka hlasitosti

### <a name="changes-160"></a>Změny 1.6.0 ###

- To je v pořádku. Použití doby dešifrování AES při výběru úrovně kvality
- To je v pořádku. Aktualizovat url rewriter používat HLS v4 před HLS v3 pro multi-audio proudy
- To je v pořádku. Nastavit nativeControlsForTouch jako false jako výchozí (musí být false pracovat správně)

## <a name="150-official-update"></a>1.5.0 (Oficiální aktualizace) ##

### <a name="features-150"></a>Funkce 1.5.0 ###

- To je v pořádku. Vylepšení obecného webového zabezpečení (prevence vstřikování, XSS atd.)
- To je v pořádku. SDN plugin integrace háčky pro sourceset události a options.sdn
- To je v pořádku. Robustní manipulace s chybami 5XX a 4XX během přehrávání

### <a name="bug-fixes-150"></a>Opravy chyb 1.5.0 ###

- [Oprava chyby] Aktualizace minifikace CSS pro použití kódů písem entity HTML pro tlačítka namísto unicode
- [Oprava chyby] [AzureHtml5JS] Obsah více DRM vždy výběrem tokenu prvního prvku z protectionInfo, který způsobuje selhání druhého DRM
- [Oprava chyby] [AzureHtml5JS] Hledání nikdy nedokončí při hledání v oblasti s chybějícísegmenty.
- [Oprava chyby] [AzureHtml5JS| Edge] Povolit předponou EME v aktualizaci Edge pro přehrávání PlayReady
- [Oprava chyby] [AzureHtml5JS| Firefox] Update EME zkontrolovat, aby Firefox v42 + (s MSE) na záložní silverlight pro chráněný obsah
- [Oprava chyby] To je v pořádku. Aktualizovat error.message z čísla na podrobný řetězec

### <a name="changes-150"></a>Změny 1.5.0 ###

- To je v pořádku. Plakáty v současné době fungují pouze jako absolutní adresy URL.

## <a name="140-official-update"></a>1.4.0 (Oficiální aktualizace) ##

### <a name="features-140"></a>Funkce 1.4.0 ###

- To je v pořádku. [AzureHtml5JS| Podpora chrome] Simple Widevine DRM
- To je v pořádku. [AzureHtml5JS] Robustní manipulace s chybami 404/412 během přehrávání

### <a name="bug-fixes-140"></a>Opravy chyb 1.4.0 ###

- [Oprava chyby] To je v pořádku. Vylepšení pro ověření parametrů

## <a name="130-official-update"></a>1.3.0 (Oficiální aktualizace) ##

### <a name="features-130"></a>Funkce 1.3.0 ###

- To je v pořádku. [AzureHtml5JS] To je v pořádku. Přepínání zvuku stejného kodeku multi-audio obsahu

### <a name="bug-fixes-130"></a>Opravy chyb 1.3.0 ###

- [Oprava chyby] [AzureHtml5JS| Chrome] Přerušovaný nekonečný číselník
- [Oprava chyby] [AzureHtml5JS| IE][Windows Phone] Výjimka způsobující, že Windows Phone má problémy s přehráváním
- [Oprava chyby] To je v pořádku. Automatické přehrávání nastavené na hodnotu false se nezdaří pro další instance
- [Oprava chyby] Problémy s nastavením velikosti nabídky ui

## <a name="120-official-update"></a>1.2.0 (Oficiální aktualizace) ##

### <a name="features-120"></a>Funkce 1.2.0 ###

- To je v pořádku. [AzureHtml5JS| Firefox] Podpora při povolení MSE
- To je v pořádku. Již nevyžadují aplikaci k poskytování cest pro záložní technické binární soubory (swf, xap). Cesta je relativní ke skriptu Azure Media Player.

### <a name="bug-fixes-120"></a>Opravy chyb 1.2.0 ###

- [Oprava chyby] [AzureHtml5JS| Chrome] Přehrávač se při přehrávání pohybuje za živým okrajem, když je hráč na pozadí
- [Oprava chyby] [AzureHtml5JS| Edge] Nefunguje celá obrazovka
- [Oprava chyby] [AzureHtml5JS] Protokolování nebylo správně povoleno, když bylo nastaveno v možnostech
- [Oprava chyby] To je v pořádku. Obě "vyrovnávací paměti" a vyrovnávací paměti ikona zobrazit během čekání události
- [Oprava chyby] Povolit pokračování přehrávání, pokud se nezdaří původní požadavek na šířku pásma
- [Oprava chyby] Přehrávač se nenačte při inicializování s nedefinovanými možnostmi
- [Oprava chyby] Při pokusu o vyřazení přehrávače poté, co je již uvolněn, dojde k výjimce vdata
- [Oprava chyby] Ikony pruhů kvality jsou nesprávně namapovány

## <a name="111-official-hotfix-update"></a>1.1.1 (oficiální aktualizace opravy hotfix) ##

### <a name="bug-fixes-111"></a>Opravy chyb 1.1.1 ###

- [Oprava chyby] Starší iE vydání na celou obrazovku
- [Oprava chyby] Pluginy již nejsou přepsány

## <a name="110-official-update"></a>1.1.0 (Oficiální aktualizace) ##

### <a name="features-110"></a>Funkce 1.1.0 ###

- To je v pořádku. Aktualizovat řetězce lokalizace ui

### <a name="bug-fixes-110"></a>Opravy chyb 1.1.0 ###

- [Oprava chyby] Tlačítko Big Play nemá dostatečný kontrast
- [Oprava chyby] Indikátor zaostření tabulátoru Vizuáž
- [Oprava chyby] Vybrat nabídku Bitrate nyní pomocí správných informací o rozlišení
- [Oprava chyby] Nabídka dalších možností nyní dynamicky dimenzovaná
- [Oprava chyby] Různé problémy s ui

## <a name="100-official-release"></a>1.0.0 (Oficiální vydání) ##

### <a name="features-100"></a>Funkce 1.0.0 ###

- To je v pořádku. Základní testování přístupnosti pro ovládání tabulátorů, ovládání fokusu, čtečku obrazovky, vysoce kontrastní ui
- To je v pořádku. Aktualizované ui
- To je v pořádku. Protokolování dev
- To je v pořádku. API pro dynamické nastavení titulků/titulků
- To je v pořádku. Základní lokalizační funkce
- To je v pořádku. Konsolidace kódu chyby napříč techniky
- To je v pořádku. Nový kód chyby, když nejsou nainstalovány pluginy (jako Flash nebo Silverlight)
- To je v pořádku. [AzureHtml5JS] Implementované základní diagnostické události

### <a name="bug-fixes-100"></a>Opravy chyb 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Oprava chyby] [AzureHtml5JS] Živé přehrávání zmrazení na mpd aktualizace, když tam jsou malé nepřesnosti v časovém razítku
- [Oprava chyby] [AzureHtml5JS] Zmírnění několika problémů s přehráváním živého vysílání
- [Oprava chyby] [AzureHtml5JS] Vyprázdnění vyrovnávacích pamětí, když je zapnuta heuristika velikosti okna a přejděte na obrazovku s vyšším rozlišením
- [Oprava chyby] [AzureHtml5JS] Chrome nyní správně ukazuje ukončenou událost. Souvisí s předchozím známým vydáním *Chrome nebude správně posílat&euro;â œendedâ&euro;událost při používání AzureHtml5JS. V základním prohlížeči došlo k problému.*
- [Oprava chyby] [AzureHtml5JS] Zakázané Safari pro tuto technologii s cílem řešit *problém přehrávání s OSX Yosemite s AzureHtml5JS tech. Existují problémy s implementací MSE. Dočasné zmírnění:&euro;síla â œflashSSâ&euro;, â&euro;&euro;œsilverlightSSâ jako tech pořadí pro tyto uživatelské agenty*
- [Oprava chyby] [FlashSS] loadstart aktivována po došlo k chybě

## <a name="020-beta"></a>0.2.0 (Beta) ##

### <a name="features-020"></a>Funkce 0.2.0 ###

- To je v pořádku. Dokončené testování pro PlayReady a AES na vyžádání a živě - viz matice kompatibility
- To je v pořádku. Manipulace s nespojitostmi
- To je v pořádku. Podpora časových razítek větších než 2^53
- To je v pořádku. Parametr dotazu URL se uchovává v požadavku manifestu.
- To je v pořádku. [Nevyzkoušený] Podpora `QuickStart` a `HighQuality` heuristiky profily
- To je v pořádku. [Nevyzkoušený] Vystavení informací o streamu videa pro přenosové rychlosti, šířku a výšku na AzureHtml5JS a FlashS
- To je v pořádku. [Nevyzkoušený] Vybrat bitový tok na AzureHtml5JS a FlashSS (viz dokumentace rozhraní API)

### <a name="bug-fixes-020"></a>Opravy chyb 0.2.0 ###

- [Bug Fix] velké tlačítko přehrávání nyní zobrazitelné na WP8.1
- [Bug Fix] opraveno více problémů s přehráváním živého přehrávání
- Tlačítko [Oprava chyby] unmute nyní funguje na ui
- [Oprava chyb] aktualizované prostředí pro načítání uživatelského prostředí pro režim automatického přehrávání
- [Oprava chyby] Problém zavaděče AMD a definování konfliktů metod
- [Oprava chyby] WP 8.1 Cordova App načítání problém
- [Oprava chyby] Chráněný obsah dotazy platformu / tech podporované ProtectionType vybrat vhodnou technologii pro přehrávání.  Opravuje předchozí známý problém _'PlayReady obsah na Chrome (desktop) / Safari 8 (na OSX Yosemite) v současné době není záložní silverlight přehrávač_'
- [Oprava chyb] nezachycená výjimka na winserveru 2012 R2 z důvodu media foundation není nainstalován a ve výchozím nastavení v tomto počítači.  Pokuste se použít rozhraní API značky videa HTML, které nejsou implementovány, a tím vyvolat chybu. Aktuální zmírnění je zachytit tuto chybu a vrátit false namísto vyvolání chyby.
- [Bug Fix] vždy získat init segment po hledání nebo http selhání, aby se zabránilo závady během přehrávání
- [Oprava chyb] vypnout sledování simulovaný průběh a aktualizace času, když došlo k chybě.
- [Bug Fix] odstranit pravým tlačítkem myši menu
- [Oprava chyby] Chybová zpráva [AzureHtml5JS] se nezobrazuje, když je pro obsah PlayReady nastaven neplatný token
- [Oprava chyby] [AzureHtml5JS] bude celá obrazovka během živého přehrávání nebyl s ohledem na velikost okna heuristiky v úvahu
- [Oprava chyby] To je v pořádku. Odstraněné zprávy strobe Media Player zobrazené zprávy tak, aby se zobrazovaly pouze zprávy programu Azure Media Player
- [Oprava chyby] [SilverlightSS] nedostává 'hledal' událost, když se snažíme po dobu trvání nebo méně než 0

## <a name="010-beta-release"></a>0.1.0 (beta verze) ##

Počáteční předběžná verze

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)
