---
title: Vytvoření přístupné aplikace mapy s Azure Maps | Mapy Microsoft Azure
description: Seznamte se s informacemi o přístupnosti v Azure Maps. Podívejte se, jaké funkce jsou k dispozici pro usnadnění přístupu k aplikacím mapy a zobrazení tipů pro usnadnění přístupu.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: ec88437a0fad3a6bd94a67a5ef5c75b3e506f9e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88006209"
---
# <a name="building-an-accessible-application"></a>Vytvoření přístupné aplikace

K dispozici je více než 20% uživatelů internetu, kteří potřebují přístupné webové aplikace. V takovém případě je důležité zajistit, aby byla aplikace navržena tak, aby ji kterýkoli uživatel mohl snadno použít. Místo toho, aby bylo možné přihlížet jako k dokončení sady úkolů, si ji můžete představit jako součást celkového uživatelského prostředí. Přístupnější aplikace, více lidí, kteří je můžou používat. 

Když přichází na bohatě interaktivní obsah, jako je mapa, některé běžné požadavky na přístupnost jsou:
- Podpora čtečky obrazovky pro uživatele, kteří mají potíže se zobrazením webové aplikace
- Pro interakci s webovou aplikací, jako je myš, dotykové ovládání a klávesnice, máte několik metod.
- Zajistěte, aby byl barevný kontrast, takže se barvy vzájemně neliší a nemusíte je odlišit od sebe. 

Sada Azure Maps Web SDK je dodávána předem s mnoha funkcemi pro usnadnění, jako například:
- Program pro čtení obrazovky popisuje, kdy se mapa přesune a když se uživatel zaměřuje na ovládací prvek nebo místní nabídku.
- Podpora myši, dotyku a klávesnice
- Dostupná podpora barevného kontrastu ve stylu mapy cest
- Podpora vysokého kontrastu.

Podrobnosti o plném přístupu pro všechny produkty Microsoftu najdete [tady](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Vyhledejte "Azure Maps Web" a vyhledejte dokument speciálně pro sadu Azure Maps Web SDK. 

## <a name="navigating-the-map"></a>Navigace v mapě

Existuje několik různých způsobů, jak lze mapu zvětšit, vytočit, otočit a rozteč. Následující podrobnosti jsou všechny různými způsoby navigace v mapě.

**Zvětšit mapu**

- Pomocí myši poklikejte na mapu, abyste přiblížili jednu úroveň.
- Pomocí myši posuňte kolečko k přiblížení mapy.
- S dotykovou obrazovkou dotykovou obrazovku můžete přiblížit dvěma prsty a gesto roztažení prstů dohromady, abyste se přiblížili nebo rozblížili prsty vedle sebe.
- Pomocí dotykové obrazovky poklikejte na mapu, abyste ji přiblížili na jednu úroveň.
- S fokusem mapy použijte znaménko plus ( `+` ) nebo symbol rovná se ( `=` ) pro přiblížení na jednu úroveň.
- S soustředěnou mapou použijte znaménko mínus, pomlčku ( `-` ) nebo podtržítko ( `_` ), abyste mohli zmenšit jednu úroveň.
- Použití ovládacího prvku Lupa pomocí myši, dotyku nebo klávesy TAB/kláves ENTER
- Stiskněte a podržte `Shift` tlačítko a stiskněte levé tlačítko myši na mapě a přetažením myši nakreslete oblast pro přiblížení mapy.
- Pomocí některých doplňků pro více dotykové ovládání můžete k přiblížení nebo zmenšení snížit velikost přetažením dvou prsty.

**Posouvání mapy**

- Pomocí myši, stiskněte dolů a levým tlačítkem myši na mapě a táhněte v libovolném směru.
- Pomocí dotykové obrazovky se připojte k mapě a přetáhněte je v libovolném směru.
- S fokusem mapy přesuňte mapu pomocí kláves se šipkami.

**Otočit mapu**

- Pomocí myši podržte stisknuté pravé tlačítko myši na mapě a přetáhněte doleva nebo doprava. 
- Pomocí dotykové obrazovky se připojte k mapě dvěma prsty a otáčejte.
- S fokusem mapy použijte klávesu SHIFT a levou nebo pravou šipku.
- Použití ovládacího prvku otočení pomocí myši, dotyku nebo klávesy TAB/kláves ENTER

**Rozteč mapy**

- Pomocí myši podržte stisknuté pravé tlačítko myši na mapě a přetáhněte je nahoru nebo dolů. 
- Pomocí dotykové obrazovky můžete mapu kontaktovat dvěma prsty a přetáhnout je nahoru nebo dolů.
- S fokusem mapy použijte klávesu SHIFT a šipky nahoru nebo dolů. 
- Použití ovládacího prvku sklonu s myší, dotykem nebo klávesou TAB/ENTER Keys.

## <a name="change-the-map-style"></a>Změna stylu mapy

Ne všichni vývojáři chtějí mít k dispozici všechny možné styly mapy ve svých aplikacích. Pokud vývojář zobrazí ovládací prvek pro výběr stylu mapy, může uživatel změnit styl mapy pomocí myši, dotyku nebo klávesnice s klávesou nebo klávesou ENTER. Vývojář může určit, které styly mapy mají být k dispozici v ovládacím prvku Výběr stylu mapy. Kromě toho může vývojář programově nastavit a změnit styl mapy.

**Použít režim Vysoký kontrast**

- Když je mapový ovládací prvek načten, zkontroluje, zda je povolen vysoký kontrast a prohlížeč ho podporuje.
- Mapový ovládací prvek nemonitoruje režim vysokého kontrastu zařízení. Pokud se změní režim zařízení, mapa nebude. Proto bude uživatel muset znovu načíst mapu tím, že stránku aktualizuje.
- Když se zjistí vysoký kontrast, styl mapy se automaticky přepne na vysoký kontrast a všechny předdefinované ovládací prvky budou používat styl s vysokým kontrastem. Například ZoomControl, PitchControl, CompassControl, StyleControl a další integrované ovládací prvky budou používat styl vysokého kontrastu.
- Existují dva typy vysokého kontrastu, světlé a tmavé. Pokud typ vysokého kontrastu lze detekovat ovládacími prvky mapy, chování mapování se odpovídajícím způsobem upraví. Pokud má světlo světla, načtou se styl mapy grayscale_light. Pokud typ nebyl nalezen nebo je tmavý, bude načten high_contrast_dark styl.
- Pokud vytváříte vlastní ovládací prvky, je vhodné zjistit, zda předdefinované ovládací prvky používají styl vysokého kontrastu. Vývojáři mohou přidat třídu šablony stylů CSS na div kontejneru map pro kontrolu. Třídy šablony stylů CSS, které by se měly přidat, jsou `high-contrast-dark` a `high-contrast-light` . Pokud chcete kontrolovat používání JavaScriptu, použijte:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

nebo použijte:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

Mapa má několik vestavěných klávesových zkratek, které usnadňují používání mapy. Tyto klávesové zkratky fungují, když má mapa fokus.

| Klíč      | Akce                            |
|----------|-----------------------------------|
| `Tab` | Navigace mezi ovládacími prvky a automaticky otevíraných oken v mapě. |
| `ESC` | Přesuňte fokus z libovolného prvku v mapě k elementu mapy nejvyšší úrovně. |
| `Ctrl` + `Shift` + `D` | Přepnout úroveň podrobností čtečky obrazovky  |
| Šipka vlevo | Posune mapu vlevo 100 pixelů. |
| Šipka vpravo | Posunout mapu vpravo o 100 pixelů |
| Klávesa šipka dolů | Posunout mapu dolů na 100 pixelů |
| Klávesa šipka nahoru | Posouvání mapy o 100 pixelů |
| `Shift` + Šipka nahoru | Zvětšit rozteč mapy o 10 stupňů |
| `Shift` + Šipka dolů | Zmenšit rozteč v mapě o 10 stupňů |
| `Shift` + šipka doprava | Otočit mapu o 15 stupňů po směru hodinových ručiček |
| `Shift` + šipka doleva | Otočit mapu o 15 stupňů po směru hodinových ručiček |
| Znaménko plus ( `+` ) nebo symbol <sup>*</sup> rovná se ( `=` ) | Přiblížit |
| Symbol mínus, spojovník ( `-` ) nebo <sup>*</sup> podtržítko ( `_` ) | Oddálit | 
| `Shift` + Přetáhnutím myši na mapu pro vykreslení oblasti | Přiblížit oblast |

<sup>*</sup> Tyto klávesové zkratky obvykle sdílejí stejný klíč na klávesnici. Tyto klávesové zkratky byly přidány pro zlepšení uživatelského prostředí. Bez ohledu na to, jestli uživatel používá klávesu SHIFT nebo ne pro tyto klávesové zkratky.

## <a name="screen-reader-support"></a>Podpora čtečky obrazovky

Uživatelé mohou procházet mapu pomocí klávesnice. Pokud je spuštěný čtečka obrazovky, mapa upozorní uživatele na změny jeho stavu. Například uživatelé jsou upozorňováni na změny v mapě, když je mapa vyvážení nebo zvětšena. Ve výchozím nastavení mapa poskytuje zjednodušené popisy, které vylučují úroveň přiblížení a souřadnice středu mapy. Uživatel může přepínat úroveň podrobností těchto popisů pomocí krátkého vyjmutí klávesnice `Ctrl`  +  `Shift`  +  `D` .

Všechny další informace, které jsou umístěny na základní mapě, by měly mít odpovídající textové informace pro uživatele čtečky obrazovky. V případě potřeby nezapomeňte přidat [dostupné bohatý internetové aplikace (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/), ALT a atributy nadpisu. 

## <a name="make-popups-keyboard-accessible"></a>Zpřístupnění klávesnice pro místní přístup

Značka nebo symbol se často používá k reprezentaci umístění na mapě. Další informace o umístění se obvykle zobrazují v automaticky otevíraném okně, když uživatel komunikuje se značkou. Ve většině aplikací se automaticky otevíraná okna zobrazí, když uživatel klikne nebo klepne na značku. Když ale kliknete a klepnete na vyžadovat, aby uživatel použil myš a dotykovou obrazovku, v uvedeném pořadí. Dobrým postupem je umožnit přístup k místním přidaným při používání klávesnice. Tuto funkci je možné dosáhnout tak, že vytvoříte místní nabídku pro každý datový bod a přidáte ji do mapy. 

Následující příklad načte body zájmů na mapě pomocí vrstvy symbolů a přidá místní nabídku na mapu pro každý bod zájmu. Odkaz na každé místní okno je uložen ve vlastnostech každého datového bodu. Lze ji také načíst pro značku, například při kliknutí na značku. Když se na mapě zaměřuje fokus, stisknutím klávesy TAB umožníte uživateli procházet každé místní nabídky na mapě.

<br/>

<iframe height='500' scrolling='no' title='Vytvoření přístupné aplikace' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero a <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>zpřístupněte aplikaci</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Další tipy k usnadnění přístupu

Tady je několik dalších tipů, jak usnadnit přístup k aplikaci mapování webu.

- Pokud na mapě zobrazujeme mnoho dat s interaktivním bodem, zvažte snížení zbytečných a používání clusteringu. 
- Zajistěte, aby byl poměr barev barvy mezi textem, symboly a barvami pozadí 4,5:1 nebo více.
- Ponechejte své zprávy pro čtečku obrazovky (v případě atributů ARIA, ALT a title) krátké, popisné a smysluplné. Vyhněte se zbytečným žargonuům a akronymům.
- Zkuste optimalizovat zprávy odeslané do čtečky obrazovky, abyste poskytovali krátké smysluplné informace, které se uživatelům snadno vytráví. Například pokud chcete, aby se čtečka obrazovky aktualizovala s vysokou frekvencí, například při přesunutí mapy, zvažte následující body:
    - Počkejte, dokud se mapa nedokončila přesunutím, aby se aktualizovala čtečka obrazovky.
    - Omezí aktualizace na každých několik sekund. 
    - Logickým způsobem kombinuje zprávy. 
- Nepoužívejte barvy jako jediný způsob sdělování informací. K doplnění nebo nahrazení barvy použijte text, ikony nebo vzory. Některé okolnosti:
    - Pokud použijete bublinovou vrstvu k zobrazení relativní hodnoty mezi datovými body, zvažte možnost škálovat poloměr každého bublinového obrázku, vybarvit bublinu nebo obojí. 
    - Zvažte použití vrstvy symbolů s různými ikonami pro různé kategorie metrik, například trojúhelníky, hvězdičky a čtverce. Vrstva symbolů také podporuje škálování velikosti ikony. Lze také zobrazit textový popisek.
    - Pokud se zobrazují data čáry, může se šířka použít k reprezentaci váhy nebo velikosti. Vzor přerušovaného pole lze použít k reprezentaci různých kategorií řádků. Vrstvu symbolů lze použít v kombinaci s čárou pro překrytí ikon podél čáry. Použití ikony šipky je užitečné pro zobrazení toku nebo směru čáry.
    - Pokud se zobrazují data mnohoúhelníku, lze jako alternativu k barvě použít vzor, jako jsou pruhy. 
- Některé vizualizace, jako jsou Heat mapy, vrstvy dlaždic a vrstvy obrázků, nejsou dostupné pro uživatele se zhoršenými vadami. Některé okolnosti:
    - Má čtečka obrazovky popis toho, co se vrstva zobrazuje, když se přidá k mapě. Například pokud se zobrazuje vrstva paprskových dlaždic s počasí, pak program pro čtení obrazovky znamená, že se na mapě překrývají data o počasí v paprskovém zobrazení.
- Omezte množství funkcí, které vyžadují najetí myší. Tyto funkce budou nedostupné uživatelům, kteří k interakci s aplikací používají klávesnice nebo dotykové zařízení. Všimněte si, že je stále vhodný styl najetí myší pro interaktivní obsah, jako jsou například ikony, odkazy a tlačítka.
- Zkuste aplikaci navigovat pomocí klávesnice. Ujistěte se, že je řazení tabulátoru logické.
- Pokud vytváříte klávesové zkratky, zkuste ji omezit na dva nebo méně. 

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o usnadnění v modulech webové sady SDK.

> [!div class="nextstepaction"]
> [Usnadnění nástrojů pro kreslení](drawing-tools-interactions-keyboard-shortcuts.md)

Seznamte se s vývojem přístupných aplikací s Microsoft Learn:

> [!div class="nextstepaction"]
> [Postup výuky v akční cestě digitálního BADGE](https://ready.azurewebsites.net/learning/track/2940)

Podívejte se na tyto užitečné nástroje pro usnadnění přístupu:
> [!div class="nextstepaction"]
> [Vývoj přístupných aplikací](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [POČKA – přehled standardu ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Nástroj pro vyhodnocení dostupnosti webu (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim kontrolu kontrastu barev](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Bez simulátoru kávy](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
