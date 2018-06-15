---
title: Statistika využití analyzovat pomocí Azure CDN Rozšířené sestavy HTTP | Microsoft Docs
description: Naučte se vytvářet rozšířené sestavy HTTP v Microsoft Azure CDN. Tyto sestavy poskytují podrobné informace o aktivity CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 2dfbc046674b2da692f30c945aee3ea25ae524eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843613"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Statistika využití analyzovat pomocí Azure CDN Rozšířené sestavy HTTP
## <a name="overview"></a>Přehled
Tento dokument popisuje Rozšířené sestavy HTTP v Microsoft Azure CDN. Tyto sestavy poskytují podrobné informace o aktivity CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Přístup k rozšířené sestavy HTTP
1. Okno profil CDN, klikněte **spravovat** tlačítko.
   
    ![Tlačítko Spravovat okno profil CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** a potom přejděte myší **Rozšířené sestavy HTTP** plovoucím panelem.  Klikněte na **velké platformy HTTP**.
   
    ![Portál pro správu CDN - nabídky Rozšířené sestavy](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Zobrazí se možnosti sestav.

## <a name="geography-reports-map-based"></a>Sestavy Geography (mapy na základě)
Nejsou pět sestavy, které využívají mapu, která bude znamenat oblastí, ze kterých je vyžadován obsah. Tyto sestavy jsou světové mapy, mapy Spojených státech amerických, Kanada mapy, Evropa mapy a mapy Asie a Tichomoří.

Všechny sestavy založené na mapu řadí geografické entity (tj, zemích, stavy a provincie) podle Procento přístupů, které pochází z této oblasti. Kromě toho se můžete vizualizovat umístění, ze kterých je vyžadován obsah poskytuje mapu. To bude možné tomu barevné kódování každou oblast podle množství vyžádání zkušeného v této oblasti. Zapalovače podbarvené oblasti znamenat nižší požadavků na obsah, zatímco tmavšího oblasti znamenat vyšší úrovně požadavků na obsah.

Podrobné informace o provozu a šířky pásma pro každou oblast zajišťuje přímo pod mapy. To umožňuje můžete zobrazit celkový počet přístupů, Procento přístupů, celkové množství dat přenesených (v gigabajtech) a procento dat přenesených pro každou oblast. Zobrazte popis pro každou z těchto metriky. Nakonec po přesunutí ukazatele myši v oblasti (tj, země, stavu nebo provincii), název a Procento přístupů, k nimž došlo v oblasti, zobrazí se jako popisek.

Stručný popis najdete níže pro každý typ sestavy na základě mapy geography.

| Název sestavy | Popis |
| --- | --- |
| Světové mapy |Tato sestava vám umožní zobrazit po celém světě poptávka po obsahu CDN. Každé země, jsou rozlišené barevně na mapě world s procentem přístupů, které pochází z této oblasti. |
| Spojené státy mapy |Tato sestava vám umožní zobrazit poptávka po obsahu CDN ve Spojených státech amerických. Každý stav je barevné kódování na tuto mapu s procentem přístupů, které pochází z této oblasti. |
| Mapa Kanada |Tato sestava umožňuje zobrazit v Kanadě na vyžádání pro obsah CDN. Každý provincii je barevné kódování na tuto mapu s procentem přístupů, které pochází z této oblasti. |
| Evropa mapy |Tato sestava vám umožní zobrazit poptávka po obsahu CDN v Evropě. Každé země je barevné kódování na tuto mapu s procentem přístupů, které pochází z této oblasti. |
| Asie pacifického mapy |Tato sestava vám umožní zobrazit poptávka po obsahu CDN v Asii. Každé země je barevné kódování na tuto mapu s procentem přístupů, které pochází z této oblasti. |

## <a name="geography-reports-bar-charts"></a>Sestavy Geography (pruhové grafy)
Existují dva další sestavy, které obsahují statistické informace podle Geografie, horní města a horní zemích. Tyto sestavy zařadit a obsahuje města a zemích, v uvedeném pořadí, závislosti na počtu přístupů, které pochází z těchto oblastí. Při generování tento typ sestavy, označí pruhový graf top 10 města nebo země, které požadovaný obsah přes konkrétní platformu. Tento graf panel umožňuje rychle vyhodnocovat oblastí, které generují nejvyšší počet požadavků na obsah.

Na levé straně grafu (na ose y) určuje, kolik přístupů došlo k chybě v zadané oblasti. Přímo pod grafem (na ose x) zjistí popisek pro každý oblastí top 10.

### <a name="using-the-bar-charts"></a>Pomocí pruhové grafy
* Pokud se ukazatel myši přesunete panelu, název a celkový počet přístupů, k nimž došlo v oblasti, zobrazí se jako popisek.
* Popisek pro sestavu horní města identifikuje města jeho název, Kraj a zkratka země.
* Pokud města nebo oblasti (tj, Kraj), ze které pochází požadavek nebylo možné určit, pak ji bude znamenat, že neznámé. Pokud je země neznámý, pak dva otazníky (tj.)??), se zobrazí.
* Sestavy mohou zahrnovat metriky pro "Evropa" nebo "Asie/Tichomoří oblast." Tyto položky se nepředpokládá, že poskytují statistické informace o všechny IP adresy v těchto oblastech. Místo toho se vztahují pouze na požadavky, které pocházejí z IP adresy, které jsou umístěny na Evropa nebo Asie/Tichomoří místo pro konkrétní město nebo zemi.

Data, která byla použita k získání pruhový graf lze zobrazit pod ním. Existuje zjistíte celkový počet přístupů, Procento přístupů, množství dat přenesených (v gigabajtech) a procento dat přenesených pro horní 250 oblasti. Zobrazte popis pro každou z těchto metriky.

Oba typy sestav níže je uveden stručný popis.

| Název sestavy | Popis |
| --- | --- |
| Horní města |Tato sestava řadí města závislosti na počtu přístupů, které pochází z této oblasti. |
| Horní zemích |Tato sestava řadí zemích závislosti na počtu přístupů, které pochází z této oblasti. |

## <a name="daily-summary"></a>Denní souhrn
Denní souhrnnou sestavu můžete zobrazit celkový počet přístupů a data přenesená v konkrétní platformu každý den. Tyto informace lze rychle rozpoznat vzory aktivity CDN. Například tato sestava vám může pomoci zjistit, které dny zkušeného vyšší nebo nižší než očekávaném provozu.

Při generování tento typ sestavy, poskytne pruhový graf vizuální označení, množství specifické pro platformu vyžádání zkušeného každý den v období předmětem sestavy. Bude tak učinit zobrazením panelu pro každý den v sestavě. Například vyberte časové období názvem "Poslední týden" vytvoří pruhový graf s sedm řádky. Každý pruh označí celkový počet přístupů zkušeného v daný den.

Na levé straně grafu (na ose y) určuje, kolik přístupů došlo k chybě v zadaném datu. Přímo pod grafem (na ose x), najdou se popisek, který označuje datum (formát: rrrr-MM-DD) pro každý den, zahrnout do sestavy.

> [!TIP]
> Pokud se ukazatel myši přesunete panelu, zobrazí celkový počet přístupů, které došlo k tomuto datu jako popisek.
> 
> 

Data, která byla použita k získání pruhový graf lze zobrazit pod ním. Existuje zjistí celkový počet přístupů a množství dat přenesených (v gigabajtech) pro každý den předmětem sestavy.

## <a name="by-hour"></a>Za hodinu
Podle hodina sestavy můžete zobrazit celkový počet přístupů a data přenesená v konkrétní platformu hodinu. Tyto informace lze rychle rozpoznat vzory aktivity CDN. Například tato sestava vám může pomoci odhalit dobu během dne, které zaznamenat vyšší nebo nižší, než očekávaném provozu.

Při generování tento typ sestavy, bude pruhový graf poskytuje vizuální znázornění, množství specifické pro platformu vyžádání došlo hodinu za časové období se zpráva vztahuje. Bude tak učinit zobrazením panelu pro každou hodinu předmětem sestavy. Například výběr 24 hodin časové období se vygeneruje pruhový graf s pruhy dvacet čtyři. Každý pruh označí celkový počet přístupů zkušeného danou dobu.

Na levé straně grafu (na ose y) určuje, kolik přístupů došlo k chybě na určenou hodinu. Přímo pod grafem (na ose x), najdou se popisek, který označuje datum a čas (formát: rrrr-MM-DD hh: mm) pro každou hodinu zahrnuty do sestavy. Čas údajně formátu 24 hodin a není zadán v časovém pásmu UTC nebo GMT.

> [!TIP]
> Pokud se ukazatel myši přesunete panelu, celkový počet přístupů, které došlo k chybě danou dobu zobrazí jako popisek.
> 
> 

Data, která byla použita k získání pruhový graf lze zobrazit pod ním. Existuje zjistí celkový počet přístupů a množství dat přenesených (v gigabajtech) pro každou hodinu předmětem sestavy.

## <a name="by-file"></a>Pomocí souboru
Po souboru sestavy můžete zobrazit množství vyžádání a přenosy dat, které vznikly v průběhu konkrétní platformu nejvíce požadovaných prostředků. Při generování tento typ sestavy, pruhový graf vygeneruje na top 10 nejžádanějších prostředky v zadaném časovém období.

> [!NOTE]
> Pro účely této sestavy adresy URL CNAME edge převedou do adresy URL jejich ekvivalentní CDN. To umožňuje přesné tally pro celkový počet přístupů, které jsou přidružené k prostředek bez ohledu na to, CDN nebo Microsoft edge CNAME URL slouží k vyžádání ho.
> 
> 

Na levé straně grafu (na ose y) určuje počet požadavků pro každý prostředek za zadané časové období. Přímo pod grafem (na ose x), zjistíte štítek, který určuje název souboru pro každou top 10 požadované prostředky.

Data, která byla použita k získání pruhový graf lze zobrazit pod ním. Zde najdete následující informace pro každý z horní 250 požadovaných prostředků: relativní cestu, celkový počet přístupů, Procento přístupů, množství dat přenesených (v gigabajtech) a přenáší procento data.

## <a name="by-file-detail"></a>Pomocí souboru podrobností
Sestava podle podrobností souboru umožňuje zobrazit množství vyžádání a přenosy dat, které vznikly v průběhu konkrétní platformu pro konkrétní prostředek. Velmi horní části této sestavy je soubor podrobnosti pro možnost. Tato možnost poskytuje seznam vaše nejžádanějších prostředky ve vybrané platformě. Chcete-li vygenerovat sestavu Podrobnosti o souboru, musíte vybrat požadovaného assetu ze souboru podrobnosti pro možnost. A pruhový graf označí množství denní vyžádání generovaný za zadané časové období.

Na levé straně grafu (na ose y) určuje celkový počet požadavků, že prostředek došlo v určitý den. Přímo pod grafem (na ose x), najdou se popisek, který označuje datum (formát: rrrr-MM-DD) pro které CDN byla nahlášena vyžádání pro asset.

Data, která byla použita k získání pruhový graf lze zobrazit pod ním. Existuje zjistí celkový počet přístupů a množství dat přenesených (v gigabajtech) pro každý den předmětem sestavy.

## <a name="by-file-type"></a>Podle typu souboru
Podle typu souboru sestavy můžete zobrazit množství vyžádání a přenosy dat způsobené typ souboru. Při generování tento typ sestavy, označí graf prstenec Procento přístupů generovaných nejvyšší typy 10 souborů.

> [!TIP]
> Pokud se ukazatel myši přesunete na výseč prstenec grafu, Internet typ média, typ souboru se zobrazí jako popisek.
> 
> 

Data, která byla použita k vygenerování prstenec grafu lze zobrazit pod ním. Zde najdete typ média rozšíření nebo Internet název souboru, celkový počet přístupů, Procento přístupů, množství dat přenášených (v gigabajtech) a procento dat přenosu pro jednotlivé typy souborů horní 250.

## <a name="by-directory"></a>Pomocí adresáře
Adresář pomocí sestavy můžete zobrazit množství vyžádání a přenosy dat, které vznikly v průběhu konkrétní platformu pro obsah z určitého adresáře. Při generování tento typ sestavy, označí pruhový graf celkový počet přístupů, které jsou generované obsah v adresáři top 10.

### <a name="using-the-bar-chart"></a>Pomocí pruhový graf
* Najeďte myší panelu zobrazíte relativní cestu k adresáři odpovídající.
* Obsah uložený v podsložce adresáře nepočítá při výpočtu požadavků službou Active directory. Tento výpočet spoléhá výhradně na počet požadavků, které jsou generovány pro obsah uložený v adresáři skutečný.
* Pro účely této sestavy adresy URL CNAME edge převedou do adresy URL jejich ekvivalentní CDN. To umožňuje přesné tally pro přidružený asset bez ohledu na to, CDN nebo Microsoft edge CNAME URL slouží k vyžádání se všechny statistické údaje.

Na levé straně grafu (na ose y) určuje celkový počet požadavků na obsah uložený ve vašich prvních 10 adresářích. Každý pruh v grafu představuje adresář. Pomocí schéma barevné kódování odpovídat panelu do adresáře uvedené v části horní 250 úplné adresáře.

Data, která byla použita k získání pruhový graf lze zobrazit pod ním. Zde najdete následující informace pro každý adresář nejvyšší 250: relativní cestu, celkový počet přístupů, Procento přístupů, množství dat přenesených (v gigabajtech) a přenáší procento data.

## <a name="by-browser"></a>Pomocí prohlížeče
Pomocí prohlížeče sestav umožňuje zobrazit prohlížečů, které jste použili k požadavek na obsah. Při generování tento typ sestavy, označí výsečového grafu procento žádostí zpracovávaných top 10 prohlížeče.

### <a name="using-the-pie-chart"></a>Použití výsečového grafu
* Najeďte myší na výseč kruhového grafu můžete zobrazit název a verze prohlížeče.
* Pro účely této sestavy se považuje za každé kombinaci jedinečný verze prohlížeče nebo jiný prohlížeč.
* Řez názvem "Ostatní" informuje o procentu požadavků zpracovává všechny ostatní prohlížeče a verze.

Data, která byla použita k získání výsečového grafu lze zobrazit pod ním. Existuje zjistíte číslo verze nebo typu prohlížeče, celkový počet přístupů a Procento přístupů pro každou z prohlížečů 250.

## <a name="by-referrer"></a>Podle odkazující server
Odkazující server pomocí sestavy vám umožní zobrazit nejčastější odkazující servery k obsahu ve vybrané platformě. Odkazující server určuje název hostitele, ze které byla vygenerována žádost. Při generování tento typ sestavy, označí pruhový graf množství vyžádání (tj, přístupů) generované top 10 odkazující servery.

Na levé straně grafu (na ose y) určuje celkový počet požadavků, že prostředek došlo odkazující na každý server. Každý pruh v grafu představuje odkazující server. Spárujte řádku, aby odkazující server uvedené v části odkazující horní 250 server pomocí schéma barevné kódování.

Data, která byla použita k získání pruhový graf lze zobrazit pod ním. Existuje zjistíte adresu URL, celkový počet přístupů a Procento přístupů generované z každé nejčastější 250 odkazující servery.

## <a name="by-download"></a>Můžete si je stáhnout
Sestavy ve stahovat umožňuje analyzovat vzory stahování pro váš nejvíce požadovaný obsah. Horní části sestavy obsahuje pruhový graf, že se porovná pokusil stahování s dokončené stahování top 10 požadovaných prostředků. Každý řádek je barevně podle zda je pokusu o stažení (modré) nebo dokončené stahování (zelený).

> [!NOTE]
> Pro účely této sestavy adresy URL CNAME edge převedou do adresy URL jejich ekvivalentní CDN. To umožňuje přesné tally pro přidružený asset bez ohledu na to, CDN nebo Microsoft edge CNAME URL slouží k vyžádání se všechny statistické údaje.
> 
> 

Na levé straně grafu (na ose y) označuje název soubor pro každou top 10 požadované prostředky. Přímo pod grafem (na ose x) zjistíte, popisky, které indikují celkový počet souborů ke stažení, pokus o dokončení.

Přímo pod pruhový graf, následující informace budou uvedené pro horní 250 požadovaných prostředků: relativní cestu (včetně názvu souboru), počet opakování byl stažen do dokončení, počet pokusů, které byla vyžádána a procento požadavky, jejichž výsledkem dokončení stahování.

> [!TIP]
> Naše CDN není informována klientem HTTP (tj. prohlížeč) Pokud byl prostředek zcela stažen. V důsledku toho se musí vypočítat, zda prostředek zcela stažen podle stavové kódy a rozsah bajtů požadavků. První věcí, kterou jsme vyhledejte při provádění tohoto výpočtu je, jestli žádost výsledkem 200 OK stavový kód. Pokud ano, pak podíváme na rozsah bajtů požadavků k zajištění, aby pokrývaly celý asset. Nakonec jsme porovnat množství dat přenesených na velikost požadovaný prostředek. Pokud data přenesená je rovna nebo větší velikost souboru a žádosti o rozsah bajtů, které jsou vhodné pro tento prostředek, bude podle počítají jako dokončení stahování.
> 
> Z důvodu interpretive povaha této sestavy byste měli mít na paměti následující body, které mohou změnit konzistence a přesnost této sestavy.
> 
> * Vzory přenosů dat nelze přesně předpovědět, když Uživatelští agenti chovat jinak. To může vytvořit výsledky dokončené stahování, které jsou větší než 100 %.
> * Prostředky, které budou využívat protokol HTTP progresivní stahování nemusí přesně v této sestavě. Toto je z důvodu uživatelé vyhledávání na jiné místo ve video.
> 
> 

## <a name="by-404-errors"></a>Podle chyb 404
Sestava podle chyb 404 umožňuje určit typ obsahu, který generuje největším počtem 404 stavové kódy nebyl nalezen. Horní části sestavy obsahuje pruhový graf u prvních 10 prostředků, pro které byl vrácen stavový kód 404 nebyl nalezen. Tento pruhový graf porovnává celkový počet požadavků s požadavky, jejichž výsledkem 404 nebyl nalezen stavový kód pro tyto prostředky. Každý panelu jsou rozlišené barevně. Žlutý pruh slouží k označení, že žádost výsledkem stavový kód 404 nebyl nalezen. Červený pruh slouží k označení celkový počet požadavků za tento prostředek.

> [!NOTE]
> Pro účely této sestavy pamatujte na následující:
> 
> * Stiskněte klávesu představuje každá žádost o prostředek bez ohledu na to, stavový kód.
> * Adresy URL Edge CNAME se převedou na adresy URL jejich ekvivalentní CDN. To umožňuje přesné tally pro přidružený asset bez ohledu na to, CDN nebo Microsoft edge CNAME URL slouží k vyžádání se všechny statistické údaje.
> 
> 

Na levé straně grafu (na ose y) označuje název soubor pro každou top 10 požadovaných prostředků, jejichž výsledkem stavový kód 404 nebyl nalezen. Přímo pod grafem (na ose x) zjistí štítků, které uvádí celkový počet požadavků a počet požadavků, jejichž výsledkem stavový kód 404 nebyl nalezen.

Přímo pod pruhový graf, následující informace budou uvedené pro horní 250 požadovaných prostředků: relativní cesta (včetně název souboru), počet požadavků, jejichž výsledkem 404 nebyl nalezen stavový kód, celkový počet pokusů, které požadoval prostředku, a Procento požadavků, jejichž výsledkem stavový kód 404 nebyl nalezen.

## <a name="see-also"></a>Viz také
* [Přehled Azure CDN](cdn-overview.md)
* [Statistiky v reálném čase v Microsoft Azure CDN](cdn-real-time-stats.md)
* [Přepsání výchozího nastavení HTTP používá stroj pravidel](cdn-rules-engine.md)
* [Analýza výkonu Edge](cdn-edge-performance.md)

