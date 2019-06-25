---
title: Analýza statistiky o využití s Azure CDN, Rozšířené sestavy HTTP | Dokumentace Microsoftu
description: Naučíte se vytvářet pokročilé sestavy HTTP v Microsoft Azure CDN. Tyto sestavy poskytují podrobné informace o činnosti CDN.
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
ms.openlocfilehash: 67bab857e66edd8e9fae7d45c6157f018da81ccf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64867040"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analýza statistiky o využití s Azure CDN, Rozšířené sestavy HTTP
## <a name="overview"></a>Přehled
Tento dokument popisuje pokročilých sestav HTTP v Microsoft Azure CDN. Tyto sestavy poskytují podrobné informace o činnosti CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Přístup k rozšířené sestavy HTTP
1. Z okna profil CDN, klikněte na tlačítko **spravovat** tlačítko.
   
    ![Tlačítko Spravovat okno profilu CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** kartu a pak najeďte myší **pokročilé sestavy HTTP** Kontextová nabídka.  Klikněte na **velké platformy HTTP**.
   
    ![Portál pro správu CDN – nabídka Rozšířené sestavy](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Možnosti sestavy jsou zobrazeny.

## <a name="geography-reports-map-based"></a>Sestavy zeměpisné oblasti (založený mapě)
Existuje pět sestav, které budou využívat mapu, která bude určit oblasti, ze kterých je požadovaný obsah. Tyto sestavy jsou mapu světa, mapa USA, Kanadě mapy, mapy Evropa a Asie a Tichomoří mapy.

Každá sestava mapovou řadí geografické entity (to znamená, zemích nebo oblastech, mapy je k dispozici pro lepší vizualizaci umístění, ze které je požadováno váš obsah. Je možné to tak, že se zdají nezvyklé každé oblasti závisí na množství vyžádání zkušení v dané oblasti. Zapalovače vystínovanou oblasti označení nižšími požadavky pro obsah, zatímco tmavší oblastech znamenat vyšší úrovně pro váš obsah na vyžádání.

Podrobné informace o provozu a šířky pásma pro každou oblast je k dispozici přímo pod mapou. Díky tomu můžete zobrazit celkový počet přístupů, procenta přístupů, celkové množství dat přenosu (v gigabajtech) a procentuální podíl data přenesená pro každou oblast. Zobrazte popis pro každé z těchto měřítek. Nakonec se při najetí myší na oblasti (to znamená, země/oblast, stát nebo kraj), název a procenta přístupů, ke kterým došlo v oblasti se zobrazí jako popisek.

Níže je uvedený stručný popis pro každý typ sestavy na základě mapy zeměpisné oblasti.

| Název sestavy | Popis |
| --- | --- |
| Mapu světa |Tato sestava umožňuje zobrazit po celém světě poptávka po vaší obsahu CDN. Každé země/oblasti, jsou rozlišené na mapu světa označuje Procento přístupů, které pochází z dané oblasti. |
| Mapa USA |Tato sestava umožňuje zobrazit poptávka po vaší obsahu CDN ve Spojených státech. Každý stav je barevné označení na tuto mapu k označení procenta přístupů, které pochází z dané oblasti. |
| Kanada mapy |Tato sestava umožňuje zobrazit poptávka po vaší obsahu CDN v Kanadě. Každý Kraj je barevné označení na tuto mapu k označení procenta přístupů, které pochází z dané oblasti. |
| Evropa mapy |Tato sestava umožňuje zobrazit poptávka po vaší obsahu CDN v Evropě. Každé země/oblasti je barevné označení na tuto mapu k označení procenta přístupů, které pochází z dané oblasti. |
| Asie a Tichomoří mapy |Tato sestava umožňuje zobrazit poptávka po vaší obsahu CDN v Asii. Každé země/oblasti je barevné označení na tuto mapu k označení procenta přístupů, které pochází z dané oblasti. |

## <a name="geography-reports-bar-charts"></a>Sestavy zeměpisné oblasti (pruhové grafy)
Existují dva další sestav, které poskytují statistické údaje podle zeměpisné oblasti, které jsou horní měst a horní zemích. Tyto sestavy řadit měst a země nebo oblasti, v uvedeném pořadí podle počtu přístupů, které pochází z těchto zemí nebo oblastí. Při generování tento typ sestavy, pruhového grafu označí 10 nejčastějších měst nebo země/oblasti, které požadovaný obsah přes konkrétní platformy. Tomto pruhovém grafu umožňuje rychle posoudit oblastí, které generují nejvyšším počtem žádostí pro váš obsah.

Levá strana příkazu grafu (y) označuje, kolik přístupů došlo k chybě v zadané oblasti. Přímo pod grafem (osa x) zjistíte popisek pro každou hlavní oblasti 10.

### <a name="using-the-bar-charts"></a>Pomocí pruhových grafů
* Pokud najedete myší na pruh, název celkový počet přístupů, ke kterým došlo v oblasti se zobrazí jako popisek.
* Popis pro sestavu horní měst identifikuje Město podle jeho názvu, Kraj a země/oblast – zkratka.
* Pokud nelze určit město nebo oblast (například stát/kraj), ze které žádost pochází, pak ho bude označovat, že neznámé. Pokud země/oblast je neznámý, pak dvě otazníky (tj.)??), zobrazí se.
* Sestavy mohou zahrnovat metriky pro "Evropě" nebo "Oblast Asie/Tichomoří." Tyto položky nejsou určeny k poskytování statistické údaje na všechny IP adresy v těchto oblastech. Místo toho že platí jenom pro požadavky, které pocházejí z IP adresy, které jsou umístěny na Evropa nebo Asie/Tichomoří místo pro konkrétní město nebo země/oblast.

Data, která se použil k vygenerování pruhovém grafu lze zobrazit pod ní. Zde zjistíte celkový počet přístupů, procenta přístupů, objemu dat přenesených (v gigabajtech) a procentuální podíl data přenesená pro oblasti horní 250. Zobrazte popis pro každé z těchto měřítek.

Stručný popis se poskytuje pro oba typy sestav níže.

| Název sestavy | Popis |
| --- | --- |
| Horní měst |Tato sestava řadí měst podle počtu přístupů, které pocházely z této oblasti. |
| Hlavní země |Tato sestava řadí země/oblasti podle počtu přístupů, které pochází z zemi/oblast. |

## <a name="daily-summary"></a>Denní souhrn
Denní souhrn sestava umožňuje zobrazit celkový počet přístupů a data přenesená prostřednictvím jednotlivých platformách každý den. Tyto informace lze rychle rozpoznat vzory aktivity CDN. Například tato sestava vám může pomoct zjistit, které dny zkušení vyšší nebo nižší než očekávaném provozu.

Při generování tento typ sestavy, pruhového grafu poskytne vizuální označení jde o množství specifické pro platformu vyžádání zkušení každý den za časové období, kterou sestava postihuje. Provede to tím, že zobrazuje pruh za každý den v sestavě. Například vyberte časové období názvem "Minulý týden" vygeneruje pruhový graf s sedm pruhy. Každý panel označí celkový počet přístupů zkušení v daný den.

Levá strana příkazu grafu (y) označuje, kolik přístupů došlo k zadanému datu. Přímo pod grafem (osa x), zjistíte popisek, který označuje datum (formát: YYYY-MM-DD) pro každý den v sestavě zahrnuté.

> [!TIP]
> Pokud najedete myší na pruh, celkový počet přístupů, ke kterým došlo k tomuto datu se zobrazí jako popisek.
> 
> 

Data, která se použil k vygenerování pruhovém grafu lze zobrazit pod ní. Zde zjistíte celkový počet přístupů a objemu dat přenesených (v gigabajtech) pro každý den, kterou sestava postihuje.

## <a name="by-hour"></a>By Hour
Podle hodin sestava umožňuje zobrazit celkový počet přístupů a data přenesená prostřednictvím jednotlivých platformách po hodinách. Tyto informace lze rychle rozpoznat vzory aktivity CDN. Například tato sestava vám může pomoci odhalit časová období během dne, které prostředí vyšší nebo nižší, než očekávaném provozu.

Při generování tento typ sestavy, pruhového grafu poskytne vizuální označení jde o množství došlo po hodinách za časové období, kterou sestava postihuje vyžádání specifické pro platformu. Provede to tím, že zobrazuje pruh pro každou hodinu, kterou sestava postihuje. Například výběrem 24 hodin časové období bude generovat pruhový graf s dvacet čtyři pruhy. Každý panel označí celkový počet přístupů došlo během dané hodiny.

Levá strana příkazu grafu (y) označuje, kolik přístupů došlo k chybě na určenou hodinu. Přímo pod grafem (osa x), zjistíte popisek, který určuje datum a čas (formát: YYYY-MM-DD hh: mm) pro každou hodinu zahrnuty do sestavy. Čas se použije v hlášení pomocí 24hodinový formát a je zadán v časovém pásmu UTC/GMT.

> [!TIP]
> Pokud najedete myší na pruh, zobrazí se celkový počet přístupů, ke kterým došlo během dané hodiny jako popisek.
> 
> 

Data, která se použil k vygenerování pruhovém grafu lze zobrazit pod ní. Zde zjistíte celkový počet přístupů a objemu dat přenesených (v gigabajtech) pro každou hodinu, kterou sestava postihuje.

## <a name="by-file"></a>Pomocí souboru
Sestava po souboru umožňuje zobrazení částky poptávky a provozu, které vznikly v průběhu konkrétní platformu nejčastěji požadovaných prostředků. Při generování tento typ sestavy, pruhový graf se vygeneruje na začátek 10 nejžádanějších prostředků za zadané časové období.

> [!NOTE]
> Pro účely této sestavy adresy URL CNAME edge se převedou na jejich odpovídající adresy URL CDN. To umožňuje přesné záznamů pro celkový počet přístupů, které jsou přidružené k prostředku bez ohledu na to CDN nebo edge CNAME adresa URL používaná pro požádání o ho.
> 
> 

Levá strana příkazu grafu (y) označuje počet požadavků pro každý prostředek za zadané časové období. Přímo pod grafem (osa x), zjistíte popisek, který určuje název souboru pro každou hlavní 10 požadovaných prostředků.

Data, která se použil k vygenerování pruhovém grafu lze zobrazit pod ní. Zde najdete následující informace pro každou hlavní 250 požadovaných prostředků: relativní cestu, celkový počet přístupů, procenta přístupů, objemu dat přenesených (v gigabajtech) a procentuální podíl data přenést.

## <a name="by-file-detail"></a>Pomocí podrobností souboru
Sestava podrobnosti o souboru umožňuje uživateli zobrazit objem poptávky a provoz, které vznikly v průběhu konkrétní platformu pro konkrétní prostředek. Na začátek této sestavy je možnost podrobnosti pro soubor. Tato možnost poskytuje seznam nejžádanějších prostředky ve vybrané platformě. Pokud chcete vygenerovat sestavu Podrobnosti o souboru, bude nutné vybrat požadovaný prostředek ze souboru podrobnosti pro možnost. A pruhový graf označí množství denní vyžádání, který je generován za zadané časové období.

Levá strana příkazu grafu (y) označuje celkový počet požadavků, že prostředek došlo v určitý den. Přímo pod grafem (osa x), zjistíte popisek, který označuje datum (formát: YYYY-MM-DD) pro CDN, která byla nahlášena poptávka po assetu.

Data, která se použil k vygenerování pruhovém grafu lze zobrazit pod ní. Zde zjistíte celkový počet přístupů a objemu dat přenesených (v gigabajtech) pro každý den, kterou sestava postihuje.

## <a name="by-file-type"></a>Podle typu souboru
Podle typu souboru sestavy můžete zobrazit objem poptávky a provoz účtují podle typu souboru. Při generování tento typ sestavy, označí prstencový graf procenta přístupů vygenerovaný hlavní typy 10 souborů.

> [!TIP]
> Pokud najedete myší na výseč prstencový graf, Internet typ média, typ souboru se zobrazí jako popisek.
> 
> 

Pod ní lze zobrazit data, která se použil k vygenerování prstencový graf. Zde najdete typ média souboru název rozšíření/Internet, celkový počet přístupů, procenta přístupů, množství dat přenášených (v gigabajtech) a procentuální podíl data přenesená pro každý typ souborů horní 250.

## <a name="by-directory"></a>Službou Active Directory
Sestavy podle adresáře umožňuje uživateli zobrazit objem poptávky a provoz, které vznikly v průběhu konkrétní platformu pro obsah z konkrétního adresáře. Při generování tento typ sestavy, označí pruhový graf celkový počet přístupů generovaný obsah z hlavní 10 adresářů.

### <a name="using-the-bar-chart"></a>Pomocí pruhový graf
* Najeďte myší na pruh zobrazíte relativní cestu k adresáři odpovídající.
* Obsah uložený v podsložce v adresáři nepočítá při výpočtu vyžádání službou Active directory. Tento výpočet spoléhá výhradně na počet požadavkům vygenerovaným obsahu uloženého v adresáři skutečný.
* Pro účely této sestavy adresy URL CNAME edge se převedou na jejich odpovídající adresy URL CDN. To umožňuje přesné záznamů pro všechny statistiky, které jsou přidružené k prostředku bez ohledu na to CDN nebo edge CNAME adresa URL používaná pro požádání o ho.

Levá strana příkazu grafu (y) označuje celkový počet požadavků obsahu uloženého v prvních 10 adresářů. Každý panel v grafu představuje adresáře. Použijte schéma barevného kódování shodu se panel do adresáře uvedených v části horní 250 úplné adresáře.

Data, která se použil k vygenerování pruhovém grafu lze zobrazit pod ní. Zde najdete následující informace pro každý z adresáře horní 250: relativní cestu, celkový počet přístupů, procenta přístupů, objemu dat přenesených (v gigabajtech) a procentuální podíl data přenést.

## <a name="by-browser"></a>Pomocí prohlížeče
Pomocí prohlížeče sestav umožňuje zobrazit, které prohlížeče se používaly pro požadavek na obsah. Při generování tento typ sestavy, výsečový graf indikuje procentuální podíl požadavků zpracovávaných hlavní 10 prohlížeče.

### <a name="using-the-pie-chart"></a>Použití výsečového grafu
* Najeďte myší na výseč výsečový graf, chcete-li zobrazit název a verze prohlížeče.
* Pro účely této sestavy se považuje každá kombinace jedinečné prohlížeče jiný prohlížeč.
* Řez nazývá "Ostatní" informuje o procentu žádosti zpracovat všechny ostatní prohlížeče a verze.

Data, která se použil k vygenerování výsečového grafu lze zobrazit pod ní. Zde najdete číslo typu a verzi prohlížeče, celkový počet přístupů a procenta přístupů pro každou z prohlížečů 250.

## <a name="by-referrer"></a>Podle odkazující server
Sestavy podle odkazující server umožňuje zobrazit horní odkazující servery obsahu ve vybrané platformě. Odkazující server určuje název hostitele, ze kterého byl vytvořen požadavek. Při generování tento typ sestavy, označí pruhový graf množství poptávky (například přístupů) vygenerovaný hlavní 10 odkazující servery.

Levá strana příkazu grafu (y) označuje celkový počet požadavků, že došlo k prostředku pro každý odkaz poskytuje. Každý panel v grafu představuje odkazující server. Použijte schéma barevného kódování shodu se panelu se referrer uvedených v části Referrer 250 nahoru.

Data, která se použil k vygenerování pruhovém grafu lze zobrazit pod ní. Zde najdete adresu URL, celkový počet přístupů a procenta přístupů vygenerované ze všech nejvyšší 250 odkazující servery.

## <a name="by-download"></a>Stažením
Podle stáhnout sestavu můžete analyzovat vzory stahování pro váš nejžádanějších obsah. Horní části sestavy obsahuje pruhový graf, že porovná se pokusil počet jejích stažení dokončené soubory ke stažení pro začátek 10 požadovaných prostředků. Každý panel, jsou rozlišené podle Určuje, zda je pokusu o stažení (modrá) nebo stahování dokončeno (zelený).

> [!NOTE]
> Pro účely této sestavy adresy URL CNAME edge se převedou na jejich odpovídající adresy URL CDN. To umožňuje přesné záznamů pro všechny statistiky, které jsou přidružené k prostředku bez ohledu na to CDN nebo edge CNAME adresa URL používaná pro požádání o ho.
> 
> 

Levá strana příkazu grafu (y) určuje název souboru pro každou hlavní 10 požadovaných prostředků. Přímo pod grafem (osa x) zjistíte popisků, které se zobrazí celkový počet pokusili a dokončí stahování.

Přímo pod pruhový graf, následující informace budou uvedené pro začátek 250 požadovaných prostředků: relativní cestu (včetně názvu souboru), počet případů, kdy byl stažen do konce, počet případů, kdy byla vyžádána a procento požadavky na, z kterých vzniklo kompletní soubor ke stažení.

> [!TIP]
> Naše CDN není informována klientem HTTP (například prohlížeče) když prostředek kompletně stáhl. Díky tomu budeme mít k výpočtu, jestli určitý prostředek kompletně stáhl podle stavové kódy a rozsah bajtů požadavky. Všimnou si nejprve vyhledáme při provádění tohoto výpočtu je, zda požadavek vede ke kódu stavový kód 200 OK. Pokud ano, pak podíváme na rozsah bajtů požadavků k zajištění, že pokrývají celou asset. Nakonec jsme porovnat objem data přenesená do velikosti požadovaný prostředek. Pokud jsou vhodné pro příslušný prostředek žádosti o rozsah bajtů přenesených dat je roven nebo větší než velikost souboru, bude shodu počítat jako kompletní soubor ke stažení.
> 
> Z důvodu interpretativní povaha této sestavy byste měli mít na paměti následující body, které mohou změnit konzistence a přesnost této sestavy.
> 
> * Vzory provozu nemůže být předpovězen přesně při Uživatelští agenti chovat jinak. To může způsobit výsledky stahování dokončeno, které jsou větší než 100 %.
> * Prostředky, které budou využívat protokol HTTP progresivní stahování nemusí přesně reprezentovat v této sestavě. To je způsobené uživateli, kteří se snaží jinou pozici ve videu.
> 
> 

## <a name="by-404-errors"></a>Pomocí chyby 404
Pomocí chyby 404 sestava umožňuje identifikovat typ obsahu, který generuje nejvíce 404 Nenalezeno stavové kódy. Horní části sestavy obsahuje pruhový graf pro prvních 10 prostředky, pro které byl vrácen stavový kód 404 nebyl nalezen. Tento panel srovnává celkový počet požadavků s žádostí, z kterých vzniklo 404 Nenalezeno stavový kód pro tyto prostředky. Každý panel, jsou rozlišené. Žlutý pruh se používá k označení, že žádost způsobila stavový kód 404 nebyl nalezen. Červený panel slouží k označení celkový počet požadavků pro prostředek.

> [!NOTE]
> Pro účely této sestavy vezměte na vědomí následující:
> 
> * Nalezený představuje jakoukoli žádost pro určitý prostředek, bez ohledu na stavový kód.
> * Adresy URL CNAME Edge se převedou na jejich odpovídající adresy URL pro CDN. To umožňuje přesné záznamů pro všechny statistiky, které jsou přidružené k prostředku bez ohledu na to CDN nebo edge CNAME adresa URL používaná pro požádání o ho.
> 
> 

Levá strana příkazu grafu (y) určuje název souboru pro každou hlavní 10 požadovaných prostředků, z kterých vzniklo stavový kód 404 nebyl nalezen. Přímo pod grafem (osa x) zjistíte popisky, který indikuje celkový počet požadavků a počet požadavků, z kterých vzniklo stavový kód 404 nebyl nalezen.

Přímo pod pruhový graf, následující informace budou uvedené pro začátek 250 požadovaných prostředků: relativní cestu (včetně názvu souboru), počet požadavků, z kterých vzniklo 404 Nenalezeno stavový kód, celkový počet případů, kdy se prostředek byl vyžádán, a Procento požadavků, z kterých vzniklo stavový kód 404 nebyl nalezen.

## <a name="see-also"></a>Další informace najdete v tématech
* [Azure CDN – přehled](cdn-overview.md)
* [Statistiky v reálném čase v Microsoft Azure CDN](cdn-real-time-stats.md)
* [Přepisování výchozího chování HTTP pomocí stroje pravidel](cdn-rules-engine.md)
* [Analýza výkonu hraničního](cdn-edge-performance.md)

