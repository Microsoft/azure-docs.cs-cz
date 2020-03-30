---
title: Vytvoření aplikace pro přístupné mapy pomocí Azure Maps | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vytvořit aplikaci s funkcemi usnadnění pomocí Microsoft Azure Maps.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473315"
---
# <a name="building-an-accessible-application"></a>Vytváření přístupné aplikace

Více než 20% uživatelů internetu má potřebu přístupných webových aplikací. Proto je důležité zajistit, aby vaše aplikace byla navržena tak, aby ji každý uživatel mohl snadno používat. Spíše než přemýšlet o přístupnosti jako soubor úkolů k dokončení, myslet na to jako součást vašeho celkového uživatelského prostředí. Čím přístupnější je vaše aplikace, tím více lidí ji může používat. 

Pokud jde o bohatý interaktivní obsah, jako je mapa, některé běžné aspekty usnadnění jsou:
- Podpora čtečky obrazovky pro uživatele, kteří mají potíže se zobrazením webové aplikace.
- Mějte několik metod pro interakci s webovou aplikací a její navigaci, jako je myš, dotykové ovládání a klávesnice.
- Zajistěte, aby byl barevný kontrast takový, aby se barvy nemínaly dohromady a aby se od sebe těžko odlišovaly. 

Webová sada Azure Maps Web SDK je předem vybavená mnoha funkcemi usnadnění, jako jsou:
- Program pro čtení z obrazovky popisuje, kdy se mapa pohybuje a kdy se uživatel zaměřuje na ovládací prvek nebo vyskakovací okno.
- Podpora myši, dotykového ovládání a klávesnice.
- Podpora přístupného barevného kontrastu ve stylu silniční mapy.

Úplné podrobnosti o shodě přístupnosti pro všechny produkty společnosti Microsoft naleznete [zde](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Vyhledejte "Web Azure Maps" a vyhledejte dokument speciálně pro webovou sadu Azure Maps SDK. 

## <a name="navigating-the-map"></a>Navigace v mapě

Mapu lze přiblížit, posouvat, otáčet a rozesílat několika různými způsoby. Následující podrobnosti všechny různé způsoby navigace v mapě.

**Zvětšení mapy**

- Poklepáním na mapu můžete přiblížit jednu úroveň.
- Pomocí myši můžete mapu přiblížit posunutím kolečka.
- Pomocí dotykové obrazovky se dotkněte mapy dvěma prsty a sevřete ji, abyste je oddálili nebo roztáhli, abyste je přiblížili.
- Pomocí dotykové obrazovky dvakrát klepněte na mapu pro zvětšení jedné úrovně.
- Když je mapa zaostřená,`+`použijte znaménko Plus ( ) nebo znaménko Rovná se (`=`) pro zvětšení jedné úrovně.
- Když je mapa zaměřená, oddálí`-`se jedna`_`úroveň pomocí znaménka Mínus, Pomlčka ( ) nebo Podtržítko ( ).
- Pomocí ovládání zoomu pomocí myši, dotykové karty nebo klávesnice /zadejte klávesy.
- Stiskněte a `Shift` podržte tlačítko a stiskněte levé tlačítko myši na mapě a tažením nakreslete oblast pro zvětšení mapy.

**Posouvání mapy**

- Pomocí myši stiskněte tlačítko myši na mapě levým tlačítkem myši a táhněte v libovolném směru.
- Pomocí dotykové obrazovky se dotkněte mapy a táhněte v libovolném směru.
- Když je mapa zaměřená, pomocí kláves se šipkami mapu přesuňte.

**Otočení mapy**

- Pomocí myši stiskněte tlačítko myši na mapě a táhněte doleva nebo doprava. 
- Pomocí dotykové obrazovky se dotkněte mapy dvěma prsty a otáčejte.
- Když je mapa zaměřená, použijte klávesu shift a šipku doleva nebo doprava.
- Pomocí ovládání otáčení pomocí myši, dotykové karty nebo klávesnice / zadejte klávesy.

**Rozteč mapy**

- Pomocí myši stiskněte tlačítko myši na mapě a táhněte nahoru nebo dolů. 
- Pomocí dotykové obrazovky se dotkněte mapy dvěma prsty a přetáhněte ji nahoru nebo dolů.
- S mapou zaměřenou, použijte klávesu shift plus šipky nahoru nebo dolů. 
- Pomocí ovládání rozteče pomocí myši, dotykové karty nebo klávesnice / zadejte klávesy.

## <a name="change-the-map-style"></a>Změna stylu mapy

Ne všichni vývojáři chtějí, aby všechny možné styly map byly k dispozici v jejich aplikaci. Pokud vývojář zobrazí ovládací prvek výběrstylu mapy, může uživatel změnit styl mapy pomocí myši, dotyku nebo klávesnice pomocí karty nebo klávesy Enter. Vývojář může určit, které styly mapy chtějí zpřístupnit v ovládacím prvku výběr stylu mapy. Vývojář může také programově nastavit a změnit styl mapy.

**Použít režim Vysoký kontrast**

- Když je ovládací prvek mapy načten, zkontroluje, zda je povolen vysoký kontrast a prohlížeč jej podporuje.
- Ovládací prvek mapy nesleduje režim vysokého kontrastu zařízení. Pokud se režim zařízení změní, mapa nikoli. Uživatel tedy bude muset znovu načíst mapu aktualizací stránky.
- Když je zjištěn vysoký kontrast, styl mapy se automaticky přepne na vysoký kontrast a všechny vestavěné ovládací prvky budou používat styl vysokého kontrastu. Například ZoomControl, PitchControl, CompassControl, StyleControl a další předdefinované ovládací prvky budou používat styl vysokého kontrastu.
- Existují dva typy vysokého kontrastu, světlé a tmavé. Pokud lze pomocí ovládacích prvků mapy zjistit typ vysokého kontrastu, chování mapy se odpovídajícím způsobem upraví. Pokud je světlo, pak se načte grayscale_light styl mapy. Pokud typ nelze zjistit nebo je tmavý, bude načten high_contrast_dark styl.
- Pokud vytváříte vlastní ovládací prvky, je užitečné vědět, jestli integrované ovládací prvky používají styl vysokého kontrastu. Vývojáři mohou přidat css třídu na mapě kontejneru div zkontrolovat. Css třídy, které `high-contrast-dark` by `high-contrast-light`byly přidány jsou a . Chcete-li zkontrolovat pomocí JavaScriptu, použijte:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

nebo použijte:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

Mapa obsahuje řadu zabudovaných klávesových zkratek, které usnadňují používání mapy. Tyto klávesové zkratky fungují, když je na mapě fokus.

| Klíč      | Akce                            |
|----------|-----------------------------------|
| `Tab` | Přejděte přes ovládací prvky a vyskakovací okno v mapě. |
| `ESC` | Přesunutí fokusu z libovolného prvku v mapě na prvek mapy nejvyšší úrovně. |
| `Ctrl` + `Shift` + `D` | Přepnout úroveň podrobností čtečky obrazovky.  |
| Šipka vlevo | Posuzujte mapu doleva o 100 pixelů |
| Šipka vpravo | Posuzujte mapu o 100 pixelů doprava |
| Šipka dolů | Posun o 100 pixelů dolů |
| Šipka nahoru | Posun o 100 pixelů nahoru |
| `Shift`+ šipka nahoru | Zvýšení rozteče mapy o 10 stupňů |
| `Shift`+ šipka dolů | Snížení rozteče mapy o 10 stupňů |
| `Shift`+ šipka doprava | Otočení mapy o 15 stupňů ve směru hodinových ručiček |
| `Shift`+ šipka doleva | Otočení mapy o 15 stupňů proti směru hodinových ručiček |
| Znaménko plus (`+`) nebo <sup>*</sup>znaménko Rovná se (`=`) | Přiblížit |
| Znaménko`-`mínus, pomlčka ( ) nebo <sup>*</sup>podtržítko (`_`) | Oddálit | 
| `Shift`+ tažením myší na mapě k kreslení oblasti | Přiblížení oblasti |

<sup>*</sup>Tyto klávesové zkratky obvykle sdílejí stejnou klávesu na klávesnici. Tyto zkratky byly přidány ke zlepšení uživatelského prostředí. Také nezáleží na tom, zda uživatel používá klávesu shift nebo ne pro tyto zkratky.

## <a name="screen-reader-support"></a>Podpora pro Screen Reader

Uživatelé mohou procházet mapu pomocí klávesnice. Pokud je spuštěnprogram pro čtení z obrazovky, mapa upozorní uživatele na změny jeho stavu. Uživatelé jsou například upozorněni na změny mapy, když je mapa posunuta nebo zvětšena. Ve výchozím nastavení mapa poskytuje zjednodušené popisy, které vylučují úroveň přiblížení a souřadnice středu mapy. Uživatel může přepnout úroveň podrobností těchto popisů pomocí `Ctrl`  +  `Shift`  +  `D`zkratky klávesnice .

Jakékoli další informace, které jsou umístěny na základní mapě, by měly mít odpovídající textové informace pro uživatele čtečky obrazovky. Nezapomeňte přidat [přístupné bohaté internetové aplikace (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/), alt a titul atributy, kde je to vhodné. 

## <a name="make-popups-keyboard-accessible"></a>Zpřístupnění místní klávesnice

Značka nebo symbol se často používá k reprezentaci umístění na mapě. Další informace o umístění se obvykle zobrazují ve vyskakovacím centru, když uživatel pracuje se značkou. Ve většině aplikací se automaticky otevíraná místa zobrazí, když uživatel klikne nebo klepne na značku. Klepnutí a klepnutí však vyžaduje, aby uživatel používal myš a dotykovou obrazovku. Dobrým postupem je zpřístupnit vyskakovací okno při použití klávesnice. Této funkce lze dosáhnout vytvořením vyskakovacího dne pro každý datový bod a jeho přidáním do mapy. 

Následující příklad načte body zájmu na mapě pomocí vrstvy symbolů a přidá do mapy vyskakovací okno pro každý bod zájmu. Odkaz na každé vyskakovací okno je uložen ve vlastnostech každého datového bodu. Lze ji také načíst pro značku, například při klepnutí na značku. Když se zaměříte na mapu, stisknutím klávesy tabulátoru umožníte uživateli projít každé vyskakovací okno na mapě.

<br/>

<iframe height='500' scrolling='no' title='Vytvořit přístupnou aplikaci' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Vytvořit<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>přístupnou aplikaci</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Další tipy pro usnadnění přístupu

Zde je několik dalších tipů, jak usnadnit přístup k aplikaci pro mapování webu.

- Pokud zobrazujete mnoho interaktivních dat bodů na mapě, zvažte snížení nepořádku a použijte clustering. 
- Zajistěte, aby poměr barevného kontrastu mezi textem/symboly a barvami pozadí byl 4,5:1 nebo více.
- Udržujte zprávy čtečky obrazovky (ARIA, alt a atributy názvu) krátké, popisné a smysluplné. Vyhněte se zbytečným žargonům a zkratkám.
- Pokuste se optimalizovat zprávy odeslané programové přetavce obrazovky poskytnout krátké smysluplné informace, které je pro uživatele snadno strávit. Chcete-li například aktualizovat program pro čtení z obrazovky s vysokou frekvencí, například při pohybu mapy, zvažte následující body:
    - Počkejte, až se mapa přestěhuje, a aktualizujte program pro čtení z obrazovky.
    - Omezení aktualizace jednou za několik sekund. 
    - Logicky zkombinujte zprávy dohromady. 
- Nepoužívejte barvy jako jediný způsob sdělování informací. Pomocí textu, ikon nebo vzorků doplníte nebo nahraďte barvu. Některé úvahy:
    - Pokud používáte bublinovou vrstvu k zobrazení relativní hodnoty mezi datovými body, zvažte změnu velikosti poloměru každé bubliny, barvení bubliny nebo obojí. 
    - Zvažte použití vrstvy symbolů s různými ikonami pro různé kategorie metrik, jako jsou trojúhelníky, hvězdy a čtverce. Vrstva symbolů také podporuje změnu velikosti ikony. Lze také zobrazit textový popisek.
    - Při zobrazení dat čáry lze šířku použít k reprezentaci hmotnosti nebo velikosti. Vzorek pomlčkového pole lze použít k reprezentaci různých kategorií čar. Vrstvu symbolů lze použít v kombinaci s čárou k překrytí ikon podél čáry. Použití ikony šipky je užitečné pro zobrazení toku nebo směru čáry.
    - Pokud zobrazujete mnohozonová data, lze jako alternativu k barvě použít vzorek, například pruhy. 
- Některé vizualizace, jako jsou heatmapy, vrstvy dlaždic a vrstvy obrazů, nejsou přístupné uživatelům se zrakovým postižením. Některé úvahy:
    - Chcete, aby čtečka obrazovky popsala, co se vrstva zobrazuje při přidání do mapy. Pokud je například zobrazena vrstva dlaždic meteorologického radaru, pak program pro čtení z obrazovky říká " Data meteorologického radaru jsou na mapě překryta."
- Omezte velikost funkcí, které vyžadují umístění myší. Tyto funkce budou nepřístupné uživatelům, kteří používají klávesnici nebo dotykové zařízení pro interakci s vaší aplikací. Všimněte si, že je stále vhodné mít styl najezení pro interaktivní obsah, jako jsou ikony, odkazy a tlačítka, na které lze kliknout.
- Zkuste aplikaci procházet pomocí klávesnice. Ujistěte se, že pořadí karet je logické.
- Pokud vytváříte klávesové zkratky, zkuste je omezit na dvě nebo méně klávesy. 

## <a name="next-steps"></a>Další kroky

Informace o usnadnění přístupu v modulech web SDK.

> [!div class="nextstepaction"]
> [Usnadnění přístupu kreslicích nástrojů](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o vývoji přístupných aplikací pomocí Microsoft Learn:

> [!div class="nextstepaction"]
> [Studijní program pro digitální odznak y usnadnění v akci](https://ready.azurewebsites.net/learning/track/2940)

Podívejte se na tyto užitečné nástroje pro usnadnění:
> [!div class="nextstepaction"]
> [Vývoj přístupných aplikací](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA - přehled](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Nástroj pro vyhodnocení přístupnosti webu (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Kontrola barevného kontrastu WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Žádný simulátor kávy](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
