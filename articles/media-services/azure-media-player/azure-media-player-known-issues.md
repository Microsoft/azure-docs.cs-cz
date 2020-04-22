---
title: Známé problémy přehrávače médií Azure
description: Aktuální verze má následující známé problémy.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727213"
---
# <a name="known-issues"></a>Známé problémy #

Aktuální verze má následující známé problémy:

## <a name="azure-media-player"></a>Přehrávač médií Azure ##

- Nesprávně nakonfigurované kodéry mohou způsobit problémy s přehráváním
- Datové proudy s časovými razítky větší minež2^53 mohou mít problémy s přehráváním.
  - Zmírnění: Použijte 90-kHz video a 44,1-kHz audio časové osy
- Žádné automatické přehrávání na mobilních zařízeních bez interakce s uživatelem. Je to blokováno platformou.
- Hledání blízkých nespojitostí může způsobit selhání přehrávání.
- Stažení velkých prezentací může způsobit uzamčení ui.
- Po ukončení prezentace nelze automaticky znovu přehrát stejný zdroj.
  - Chcete-li přehrát zdroj po jeho ukončení, je nutné jej znovu nastavit.
- Prázdné manifesty mohou způsobit problémy s přehrávačem.
  - K tomuto problému může dojít při spuštění živého datového proudu a není dostatek bloky jsou nalezeny v manifestu.
- Pozice přehrávání možná mimo panel hledání ui.
- Řazení událostí není konzistentní u všech techniků.
- Vlastnost s vyrovnávací pamětí není konzistentní mezi techniky.
- nativeControlsForTouch musí být false (výchozí), aby se zabránilo "Objekt nepodporuje vlastnost nebo metodu 'setControls'"
- Plakáty musí být nyní absolutní urls
- Při použití režimu vysokého kontrastu zařízení se v ui mohou vyskytnout drobné estetické problémy.
- Adresy URL obsahující %nebo "+" v plně dekódovaném řetězci mohou mít problémy s nastavením zdroje

## <a name="ad-insertion"></a>Vkládání reklam ##

- Reklamy mohou mít problémy s vkládáním (na vyžádání nebo živě), když je v prohlížeči nainstalován blokátor reklam
- Mobilní zařízení mohou mít problémy s přehráváním reklam.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- V okně DVR živého obsahu bude po dokončení obsahu časová osa nadále růst, dokud se nepokusí te se dostat do oblasti nebo nedosáhnete konce prezentace.
- Živé prezentace ve Firefoxu s povolenou MSE mají některé problémy
- Datové zdroje, které jsou zvukové nebo video, se nepřehrávají ani prostřednictvím technologie AzureHtml5JS.
  - Pokud chcete přehrávat datové zdroje bez zvuku nebo videa, můžete tak učinit vložením prázdného zvuku nebo videa pomocí [nástroje Průzkumník Avismu Pro Mediální služby Azure](https://aka.ms/amse)
    - Návod, jak vložit tichý zvuk, naleznete [zde](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>Flash ##

- Obsah AES se nepřehrává ve verzi Flash 30.0.0.134 kvůli chybě v logice ukládání do mezipaměti společnosti Adobe. Společnost Adobe problém opravila a vydala v článku 30.0.0.154
- Selhání technologií a http (například 404 síťových timeoutů) bude obnovení přehrávači trvat déle než ostatní technici.
- Klikněte na video oblasti s flashSS tech nebude hrát / pozastavit přehrávač.
- Pokud má uživatel nainstalovaný Flash, ale nedává oprávnění k jeho načtení na webu, může dojít k nekonečnému odstřeďování. Je to proto, že hráč si myslí, že plugin je nainstalován a k dispozici, a to si myslí, že plugin běží obsah. JavaScript kód byl odeslán, ale nastavení prohlížeče zablokovaly plugin od spuštění, dokud uživatel přijme výzvu k povolení plugin. K tomu může dojít ve všech prohlížečích.  

## <a name="silverlight"></a>Silverlight ##

- Chybějící funkce
- Selhání technologií a http (například 404 síťových timeoutů) bude obnovení přehrávači trvat déle než ostatní technici.
- Safari a Firefox na Mac přehrávání s `"http://` Silverlight vyžaduje explicitně definování nebo `https://` pro zdroj.
- Pokud rozhraní API chybí pro tuto technologii, obecně vrátí null.
- Pokud má uživatel nainstalovaný Flash, ale nedává oprávnění k jeho načtení na webu, může dojít k nekonečnému odstřeďování. Je to proto, že hráč si myslí, že plugin je nainstalován a k dispozici, a to si myslí, že plugin běží obsah. JavaScript kód byl odeslán, ale nastavení prohlížeče zablokovaly plugin od spuštění, dokud uživatel přijme výzvu k povolení plugin. K tomu může dojít ve všech prohlížečích.  

## <a name="native-html5"></a>Nativní HTML5 ##

- Html5 tech je pouze odesílání canplaythrough událost pro první soubor zdroje.
- Tato technologie podporuje pouze to, co prohlížeč implementoval.  Některé funkce mohou v této technologii chybět.  
- Pokud rozhraní API chybí pro tuto technologii, obecně vrátí null.
- Existují problémy s titulky a titulky na této technologii. Mohou nebo nemusí být k dispozici nebo viditelné na této technologii.
- S omezenou šířku pásma v HLS / Html5 tech scénář má za následek přehrávání zvuku bez videa.

### <a name="microsoft"></a>Microsoft ###

- Přehrávání iE8 v současné době nefunguje z důvodu nekompatibility s ECMAScript 5
- V aplikaci IE a některých verzích edge nelze zadat celou obrazovku tabulátorem na tlačítko a jeho výběrem nebo pomocí klávesové zkratky F/f.

## <a name="google"></a>Google ##

- Více profilů kódování ve stejném manifestu má v Chromu některé problémy s přehráváním a nedoporučuje se.
- Chrome nemůže přehrávat HE-AAC s AzureHtml5JS. Sledujte podrobnosti o [sledovači chyb](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- Od prohlížeče Chrome v58 musí být obsah widevine načten / přehrán zpět pomocí protokolu https:// jinak přehrávání selže.

## <a name="mozilla"></a>Mozilla ##

- Přepínač zvukového datového proudu vyžaduje, aby zvukové proudy měly při používání AzureHtml5JS stejná soukromá data kodeku. Firefox platforma vyžaduje toto.

## <a name="apple"></a>Apple ##

- Safari na Macu často umožňuje režim Spořiče energie ve výchozím nastavení s nastavením "Stop plug-ins pro úsporu energie", který blokuje pluginy jako Flash a Silverlight, když se domnívají, že není ve prospěch uživatele. Tento blok neblokuje plugin existuje, pouze schopnosti. Vzhledem k výchozí techOrder, to může způsobit problémy při pokusu o přehrávání
  - Zmírnění rizika 1: Pokud je přehrávač videa "vpředu a uprostřed" (hranice 3000 x 3000 pixelů začínající v levém horním rohu dokumentu), měl by se stále přehrávat.
  - Zmírnění 2: Změna techOrder pro Safari být ["azureHtml5JS", "html5"]. Toto zmírnění má důsledky tím, že nedostává všechny funkce, které jsou k dispozici v technologii FlashSS.
- Obsah PlayReady přes Silverlight může mít problémy s přehráváním v Safari.
- AES a omezený obsah tokenu se nepřehrává pomocí iOS a starších zařízení Android.
  - K dosažení tohoto scénáře musí být do služby přidán proxy server.
- Výchozí vzhled pro iOS Phone se promítá.
- Přehrávání iPhone se vždy odehrává v nativním přehrávači na celou obrazovku.
  - Funkce, včetně titulků, nemusí v tomto neinline přehrávání přetrvávat.
- Po ukončení živé prezentace zařízení se systémem iOS prezentaci řádně neukončí.
- iOS neumožňuje funkce DVR.
- Přepínač zvukového datového proudu iOS lze provést pouze v případě, že nativní přehrávač iOS uI a vyžaduje zvukové proudy, které mají stejná soukromá data kodeku
- Starší verze Safari mohou mít potenciálně problémy s přehráváním živých přenosů.

## <a name="older-android"></a>Starší Android ##

- AES a omezený obsah tokenu se nepřehrává pomocí iOS a starších zařízení Android.
  - K dosažení tohoto scénáře musí být do služby přidán proxy server.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)