---
title: Azure Media Player známé problémy
description: Aktuální verze obsahuje následující známé problémy.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 9d2289f1cec0d3679b0e9400f75c75cf9fece60b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448810"
---
# <a name="known-issues"></a>Známé problémy #

Aktuální verze má následující známé problémy:

## <a name="azure-media-player"></a>Přehrávač médií Azure ##

- Nesprávně nakonfigurované kodéry můžou způsobovat problémy s přehráváním.
- Datové proudy s časovými razítky většími než 2 ^ 53 můžou mít problémy s přehráváním.
  - Omezení rizik: použití videa 90 – kHz a 44,1-kHz audio s časovými měřítky
- Bez zásahu uživatele není automatické přehrávání na mobilních zařízeních. Je blokováno platformou.
- Hledání v blízkosti výpadků může způsobit selhání přehrávání.
- Stažení velkých prezentací může způsobit zamrznutí uživatelského rozhraní.
- Po ukončení prezentace nelze automaticky přehrát stejný zdroj.
  - Pro přehrání zdroje po jeho ukončení je nutné znovu nastavit zdroj.
- Prázdné manifesty můžou způsobovat problémy s přehrávačem.
  - K tomuto problému může dojít při spuštění živého datového proudu a není-li v manifestu nalezen dostatek bloků dat.
- Pozice přehrávání je možná mimo rozhraní SeekBar uživatelského rozhraní.
- Řazení událostí není konzistentní napříč všemi technickými pracovníky.
- Vlastnost uložená do vyrovnávací paměti není mezi technickými pracovníky konzistentní.
- nativeControlsForTouch musí mít hodnotu false (výchozí), aby se zabránilo tomu, že objekt nepodporuje vlastnost nebo metodu setControls.
- Plakáty musí nyní obsahovat absolutní adresy URL.
- K menšímu estetickému problému může dojít v uživatelském rozhraní při použití režimu vysokého kontrastu zařízení.
- Adresy URL obsahující "%" nebo "+" v plně dekódování řetězce mohou mít problémy s nastavením zdroje

## <a name="ad-insertion"></a>Vložení reklamy ##

- Reklamy můžou mít vložené problémy (na vyžádání nebo živé), když se v prohlížeči nainstaluje blokování reklam.
- Mobilní zařízení můžou mít problémy s přehráváním reklam.
- Azure Media Player v současné době nepodporuje reklamu MP4 Midroll.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Když se obsah dokončí, v okně DVR živého obsahu se bude časová osa dál zvětšovat, dokud se nevrátí do oblasti nebo dokud nedosáhne konce prezentace.
- Živé prezentace v prohlížeči Firefox se zapnutým programem MSE mají problémy

- Prostředky, které jsou jenom zvuk, se nebudou přehrávat přes AzureHtml5JS tech.
  - Pokud se chcete vrátit zpět k prostředkům bez zvuku, můžete to udělat tak, že vložíte prázdný zvuk pomocí [nástroje Azure Media Services Explorer](https://aka.ms/amse) .
  - Pokyny pro vložení tichého zvuku najdete [tady](../previous/media-services-advanced-encoding-with-mes.md#silent_audio) .

## <a name="flash"></a>Blikající ##

- Obsah AES se nehraje ve verzi Flash 30.0.0.134 z důvodu chyby v logice mezipaměti Adobe. Společnost Adobe vyřešila problém a uvolnila ho v 30.0.0.154
- Chyby Tech a http (například 404 vypršení časových limitů sítě), přehrávač bude trvat déle než jiné technické pracovníky.
- Kliknutím na oblast videa s bleskem nehrajete nebo nezastavíte přehrávač.
- Pokud má uživatel nainstalovaný Flash, ale nemá oprávnění ho načíst na webu, může dojít k nekonečnému odstřeďování. Je to proto, že hráč považuje modul plug-in za nainstalovaný a dostupný a předpokládá, že modul plug-in spouští obsah. Kód jazyka JavaScript byl odeslán, ale nastavení prohlížeče zablokovalo spuštění modulu plug-in, dokud uživatel nepřijme výzvu, aby povolil modul plug-in. Tato situace může nastat ve všech prohlížečích.  

## <a name="silverlight"></a>Silverlight ##

- Chybějící funkce
- Chyby Tech a http (například 404 vypršení časových limitů sítě), přehrávač bude trvat déle než jiné technické pracovníky.
- Prohlížeč Safari a Firefox při přehrávání Mac pomocí programu Silverlight vyžaduje explicitní definování `"http://` nebo `https://` pro zdroj.
- Pokud pro tento technologický technologický objekt chybí rozhraní API, bude obecně vracet hodnotu null.
- Pokud má uživatel nainstalovaný Flash, ale nemá oprávnění ho načíst na webu, může dojít k nekonečnému odstřeďování. Je to proto, že hráč považuje modul plug-in za nainstalovaný a dostupný a předpokládá, že modul plug-in spouští obsah. Kód jazyka JavaScript byl odeslán, ale nastavení prohlížeče zablokovalo spuštění modulu plug-in, dokud uživatel nepřijme výzvu, aby povolil modul plug-in. Tato situace může nastat ve všech prohlížečích.  

## <a name="native-html5"></a>Nativní HTML5 ##

- Technologie Html5 tech posílá pouze událost canplaythrough pro první sadu zdrojů.
- Tento techer podporuje jenom to, co prohlížeč implementuje.  V této technické části můžou chybět některé funkce.  
- Pokud pro tento technologický technologický objekt chybí rozhraní API, bude obecně vracet hodnotu null.
- Existují problémy s titulky a titulky na tomto Techu. Tyto pracovníky můžou nebo nemusí být k dispozici ani zobrazitelné na této technické nabídce.
- Výsledkem omezené šířky pásma ve HLS/technickém scénáři Html5 je přehrávání zvuku bez videa.

### <a name="microsoft"></a>Microsoft ###

- Přehrávání IE8 aktuálně nefunguje v důsledku nekompatibility s ECMAScript 5.
- V IE a některých verzích hraničních zařízení nemůže být celá obrazovka vložená pomocí klávesy tabulátorem k tlačítku a výběrem nebo pomocí klávesové zkratky F/f.

## <a name="google"></a>Google ##

- Několik profilů kódování ve stejném manifestu má některé problémy s přehráváním v Chrome a nedoporučuje se.
- Chrome nemůže přehrát HE-AAC pomocí AzureHtml5JS. Sledujte podrobnosti o [sledování chyb](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- Pro Chrome v58 se musí obsah Widevine načíst nebo přehrát prostřednictvím protokolu https://. jinak přehrávání selže.

## <a name="mozilla"></a>Mozilla ##

- Přepínač zvukového streamu vyžaduje, aby při použití AzureHtml5JS používaly zvukové streamy se stejnými soukromými daty. Platforma Firefox to vyžaduje.

## <a name="apple"></a>Apple ##

- Prohlížeč Safari v systému Mac ve výchozím nastavení často povoluje režim napájení pomocí nastavení "zastavit moduly plug-in, aby ušetřil napájení", které blokuje moduly plug-in, jako je Flash a Silverlight, pokud se domnívají, že se uživateli nelíbí. Tento blok neblokuje existující, jenom možnosti modulu plug-in. S ohledem na výchozí techOrder to může způsobit problémy při pokusu o přehrání.
  - Zmírnění 1: Pokud je přehrávač videa "front a Center" (v rámci hranice 3000 x 3000 pixelů, který začíná v levém horním rohu dokumentu), měl by se pořád přehrávat.
  - Zmírnění 2: Změňte techOrder pro Safari na ["azureHtml5JS", "HTML5"]. Toto zmírnění má za následek nezískání všech funkcí, které jsou k dispozici na technické stránce Flash.
- Obsah PlayReady prostřednictvím Silverlight může mít problémy, které se přehrávají v Safari.
- Obsah AES a omezeného tokenu se nehraje pomocí zařízení se systémem iOS a starším.
  - Aby bylo možné dosáhnout tohoto scénáře, musí být do vaší služby přidán proxy server.
- Výchozí vzhled pro telefon iOS se zobrazuje prostřednictvím.
- přehrávání iPhonu se vždycky objevuje v celoobrazovkovém režimu na celé obrazovce.
  - Funkce, včetně titulků, se nemusí uchovávat v tomto nevloženém přehrávání.
- Když živá prezentace skončí, zařízení se systémem iOS nebudou prezentace správně ukončena.
- iOS nepovoluje možnost DVR.
- přepínač zvukového streamu iOS se dá udělat jenom v případě, že je v nativním uživatelském rozhraní iOS a vyžaduje, aby zvukové proudy měly stejné privátní údaje.
- Starší verze prohlížeče Safari můžou mít problémy s přehráváním živých streamů.

## <a name="older-android"></a>Starší verze Androidu ##

- Obsah AES a omezeného tokenu se nehraje pomocí zařízení se systémem iOS a starším.
  - Aby bylo možné dosáhnout tohoto scénáře, musí být do vaší služby přidán proxy server.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)
