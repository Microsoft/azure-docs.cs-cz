---
title: Analýza statistik využití pomocí rozšířených sestav HTTP Azure CDN | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvářet pokročilé sestavy HTTP v microsoft azure cdn. Tyto zprávy poskytují podrobné informace o činnosti CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 0b0eec2425f8a1663eb7a09c83a6bad037d1d79c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594114"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analýza statistik využití pomocí pokročilých sestav HTTP Azure CDN
## <a name="overview"></a>Přehled
Tento dokument vysvětluje pokročilé vytváření sestav PROTOKOLU HTTP v microsoft azure cdn. Tyto zprávy poskytují podrobné informace o činnosti CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Přístup k pokročilým sestavm HTTP
1. V okně profilu CDN klepněte na tlačítko **Spravovat.**
   
    ![Tlačítko pro správu okna profilu CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte na kartu **Analytics** a najeďte na informační rámeček **Rozšířené přehledy HTTP.**  Klikněte na **HTTP Velké platformy**.
   
    ![Portál pro správu CDN - Nabídka Rozšířené sestavy](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Zobrazí se možnosti sestavy.

## <a name="geography-reports-map-based"></a>Sestavy zeměpisu (založené na mapě)
Existuje pět sestav, které využívají mapy k označení oblastí, ze kterých je váš obsah požadován. Jedná se o mapu světa, mapu Spojených států, mapu Kanady, mapu Evropy a mapu Asie a Tichomoří.

Každá sestava založená na mapě řadí geografické entity (tj. země nebo oblasti, je k dispozici mapa, která vám pomůže vizualizovat místa, ze kterých je váš obsah požadován. Je toho schopen tak učinit barevným kódováním každého regionu podle množství poptávky, které se v této oblasti vyskytly. Světlejší stínované oblasti označují nižší poptávku po obsahu, zatímco tmavší oblasti označují vyšší úroveň poptávky po obsahu.

Podrobné informace o provozu a šířce pásma pro každou oblast jsou k dispozici přímo pod mapou. To umožňuje zobrazit celkový počet přístupů, procento přístupů, celkové množství přenesených dat (v gigabajtů) a procento dat přenesených pro každou oblast. Zobrazení popisu pro každou z těchto metrik. Nakonec, když najedete na oblast (tj. zemi nebo oblast, stát nebo provincii), název a procento přístupů, ke kterým došlo v oblasti, se zobrazí jako popis.

Stručný popis je uveden níže pro každý typ sestavy zeměpisu založené na mapě.

| Report Name | Popis |
| --- | --- |
| Mapa světa |Tato sestava umožňuje zobrazit celosvětovou poptávku po obsahu CDN. Každá země nebo oblast je barevně odlišena na mapě světa, aby bylo označení procenta přístupů, které pocházejí z této oblasti. |
| Mapa Spojených států |Tato sestava umožňuje zobrazit poptávku po obsahu CDN ve Spojených státech. Každý stav je barevně rozlišen na této mapě k označení procenta přístupů, které pocházejí z této oblasti. |
| Mapa Kanady |Tato sestava umožňuje zobrazit poptávku po obsahu CDN v Kanadě. Každá provincie je na této mapě barevně rozlišena, aby označila procento přístupů, které pocházejí z této oblasti. |
| Mapa Evropy |Tato sestava umožňuje zobrazit poptávku po obsahu CDN v Evropě. Každá země nebo oblast je na této mapě barevně odlišena a označuje procento přístupů, které pocházejí z této oblasti. |
| Mapa Asie a Tichomoří |Tato sestava umožňuje zobrazit poptávku po obsahu CDN v Asii. Každá země nebo oblast je na této mapě barevně odlišena a označuje procento přístupů, které pocházejí z této oblasti. |

## <a name="geography-reports-bar-charts"></a>Sestavy zeměpisu (pruhové grafy)
Existují dvě další sestavy, které poskytují statistické informace podle zeměpisné polohy, což jsou nejlepší města a země. Tyto sestavy řadí města a země nebo oblasti podle počtu přístupů, které pocházejí z těchto zemí nebo oblastí. Při generování tohoto typu sestavy bude pruhový graf označovat prvních 10 měst nebo zemí nebo oblastí, které požadovaly obsah přes konkrétní platformu. Tento pruhový graf umožňuje rychle posoudit oblasti, které generují nejvyšší počet žádostí o obsah.

Levá strana grafu (osa y) označuje, kolik přístupů došlo v zadané oblasti. Přímo pod grafem (osa x) najdete popisek pro každou z 10 nejlepších oblastí.

### <a name="using-the-bar-charts"></a>Použití pruhových grafů
* Pokud najedete ukazatel na panel, zobrazí se jako popis ekonovaný název a celkový počet přístupů, ke kterým v oblasti došlo.
* Popisek sestavy Nejvyšší města identifikuje město podle názvu, státu/provincie a zkratky země/oblasti.
* Pokud nelze určit město nebo oblast (tj. stát/provincii), ze kterého žádost pochází, pak to bude znamenat, že nejsou známy. Pokud je země nebo oblast neznámá, zobrazí se dva otazníky (tj. ??).
* Přehled může obsahovat metriky pro "Evropa" nebo "Oblast Asie a Tichomoří". Tyto položky nejsou určeny k poskytování statistických informací o všech IP adresách v těchto oblastech. Místo toho se vztahují pouze na požadavky, které pocházejí z IP adres, které jsou rozloženy po Evropě nebo Asii a Tichomoří místo na konkrétní město nebo zemi nebo oblast.

Data, která byla použita ke generování pruhového grafu, lze zobrazit pod ním. Zde najdete celkový počet přístupů, procento přístupů, množství přenesených dat (v gigabajtů) a procento dat přenesených pro prvních 250 oblastí. Zobrazení popisu pro každou z těchto metrik.

Stručný popis je uveden pro oba typy zpráv níže.

| Report Name | Popis |
| --- | --- |
| Nejlepší města |Tato sestava řadí města podle počtu přístupů, které pocházejí z tohoto regionu. |
| Nejlepší země |Tato sestava řadí země nebo oblasti podle počtu přístupů, které pocházejí z této země nebo oblasti. |

## <a name="daily-summary"></a>Denní souhrn
Sestava Denní souhrn umožňuje zobrazit celkový počet přístupů a dat přenášených přes určitou platformu na denní bázi. Tyto informace lze použít k rychlému rozpoznání vzorců aktivity CDN. Tato sestava vám například může pomoci zjistit, ve kterých dnech došlo k vyššímu nebo nižšímu než očekávanému provozu.

Při generování tohoto typu sestavy bude pruhový graf poskytovat vizuální indikaci, pokud jde o množství poptávky specifické pro platformu, ke které dochází denně v časovém období, na které se sestava vztahuje. Provede tak zobrazením pruhu pro každý den v sestavě. Například výběrem časového období s názvem "Minulý týden" se vygeneruje pruhový graf se sedmi pruhy. Každý pruh bude uvádět celkový počet stisků v ten den.

Levá strana grafu (osa y) označuje, kolik přístupů došlo k zadanému datu. Přímo pod grafem (osa x) najdete popisek, který označuje datum (Formát: YYYY-MM-DD) pro každý den zahrnutý do sestavy.

> [!TIP]
> Pokud najedete na panel, zobrazí se jako popisek celkový počet přístupů, ke kterým k tomuto datu došlo.
> 
> 

Data, která byla použita ke generování pruhového grafu, lze zobrazit pod ním. Zde najdete celkový počet přístupů a množství přenesených dat (v gigabajtů) za každý den, na který se sestava vztahuje.

## <a name="by-hour"></a>Podle hodiny
Sestava Podle hodin umožňuje zobrazit celkový počet přístupů a dat přenesených přes určitou platformu na hodinovém základě. Tyto informace lze použít k rychlému rozpoznání vzorců aktivity CDN. Tato sestava vám například může pomoci zjistit časová období během dne, u kterých dochází k vyššímnebo nižším než očekávaným přenosům.

Při generování tohoto typu sestavy bude pruhový graf poskytovat vizuální indikaci, pokud jde o množství poptávky specifické pro platformu, ke které dochází na hodinovém základě v časovém období, na které se sestava vztahuje. Učiní tak zobrazením pruhu pro každou hodinu, na kterou se sestava vztahuje. Například výběrem časového období 24 hodin se vygeneruje pruhový graf s dvaceti čtyřmi pruhy. Každý pruh bude uvádět celkový počet přístupů během této hodiny.

Levá strana grafu (osa y) označuje, kolik přístupů došlo v zadané hodině. Přímo pod grafem (osa x) najdete popisek, který označuje datum a čas (Formát: YYYY-MM-DD hh:mm) pro každou hodinu zahrnutou v sestavě. Čas je hlášen ve formátu 24 hodin a je určen pomocí časového pásma UTC/GMT.

> [!TIP]
> Pokud najedete ukazatel na panel, zobrazí se jako popisek celkový počet přístupů, ke kterým během této hodiny došlo.
> 
> 

Data, která byla použita ke generování pruhového grafu, lze zobrazit pod ním. Zde najdete celkový počet přístupů a množství přenesených dat (v gigabajtů) za každou hodinu, na kterou se sestava vztahuje.

## <a name="by-file"></a>Podle souboru
Sestava Podle souboru umožňuje zobrazit množství poptávky a provoz vzniklý přes konkrétní platformu pro nejžádanější prostředky. Po vygenerování tohoto typu sestavy bude v zadaném časovém období vygenerován pruhový graf v horních 10 nejžádanějších aktivech.

> [!NOTE]
> Pro účely této sestavy jsou adresy URL CNAME na okraji převedeny na ekvivalentní adresy URL CDN. To umožňuje přesný součet celkového počtu přístupů přidružených k datovému zdroji bez ohledu na adresu URL CN nebo okrajovou adresu URL použitou k jeho vyžádání.
> 
> 

Levá strana grafu (osa y) označuje počet požadavků na každý datový zdroj za zadané časové období. Přímo pod grafem (osa x) najdete popisek, který označuje název souboru pro každý z 10 nejlepších požadovaných datových zdrojů.

Data, která byla použita ke generování pruhového grafu, lze zobrazit pod ním. Zde najdete následující informace pro každý z 250 nejlepších požadovaných datových zdrojů: relativní cesta, celkový počet přístupů, procento přístupů, množství přenesených dat (v gigabajtů) a procento přenesených dat.

## <a name="by-file-detail"></a>Podle podrobností souboru
Sestava Podle podrobností souboru umožňuje zobrazit množství poptávky a provoz vzniklý přes konkrétní platformu pro konkrétní majetek. V horní části této sestavy je možnost Podrobnosti o souboru. Tato možnost poskytuje seznam nejžádanějších datových zdrojů na vybrané platformě. Chcete-li vygenerovat sestavu podle podrobností souboru, budete muset vybrat požadovaný datový zdroj z možnosti Podrobnosti souboru. Poté bude pruhový graf uvádět množství denní poptávky, kterou generuje za zadané časové období.

Levá strana grafu (osa y) označuje celkový počet požadavků, které má aktivum k určitému dni. Přímo pod grafem (osa x) najdete popisek, který označuje datum (Formát: YYYY-MM-DD), pro který byla hlášena poptávka CDN po majetku.

Data, která byla použita ke generování pruhového grafu, lze zobrazit pod ním. Zde najdete celkový počet přístupů a množství přenesených dat (v gigabajtů) za každý den, na který se sestava vztahuje.

## <a name="by-file-type"></a>Podle typu souboru
Sestava Podle typu souboru umožňuje zobrazit množství poptávky a provoz vzniklý typem souboru. Při generování tohoto typu sestavy bude graf koblihy označovat procento přístupů generovaných top 10 typy souborů.

> [!TIP]
> Pokud najedete na výseč v grafu koblih, bude jako popisek zobrazen typ internetového média tohoto typu souboru.
> 
> 

Data, která byla použita ke generování grafu koblihy lze zobrazit pod ním. Tam najdete název souboru rozšíření / typ média Internet, celkový počet přístupů, procento přístupů, množství přenesených dat (v gigabajtů), a procento dat přenesených pro každý z top 250 typů souborů.

## <a name="by-directory"></a>Podle adresáře
Sestava Podle adresáře umožňuje zobrazit množství poptávky a provoz vzniklý přes určitou platformu pro obsah z určitého adresáře. Při generování tohoto typu sestavy bude pruhový graf uvádět celkový počet přístupů generovaných obsahem v prvních 10 adresářích.

### <a name="using-the-bar-chart"></a>Použití pruhového grafu
* Najeďte nad pruhem a zobrazte relativní cestu k odpovídajícímu adresáři.
* Obsah uložený v podsložce adresáře se nepočítá při výpočtu poptávky podle adresáře. Tento výpočet závisí výhradně na počtu požadavků generovaných pro obsah uložený ve skutečném adresáři.
* Pro účely této sestavy jsou adresy URL CNAME na okraji převedeny na ekvivalentní adresy URL CDN. To umožňuje přesný součet pro všechny statistiky spojené s datovým zdrojem bez ohledu na adresu URL CDN nebo okraj CNAME, která se používá k jeho vyžádání.

Levá strana grafu (osa y) označuje celkový počet požadavků na obsah uložený v prvních 10 adresářích. Každý pruh v grafu představuje adresář. Pomocí schématu barevného kódování můžete přizpůsobit pruh adresáři uvedenému v části Prvních 250 úplných adresářů.

Data, která byla použita ke generování pruhového grafu, lze zobrazit pod ním. Zde najdete následující informace pro každý z 250 nejlepších adresářů: relativní cesta, celkový počet přístupů, procento přístupů, množství přenesených dat (v gigabajtů) a procento přenesených dat.

## <a name="by-browser"></a>Podle prohlížeče
Přehled Podle prohlížeče umožňuje zobrazit, které prohlížeče byly použity k vyžádání obsahu. Při generování tohoto typu sestavy bude výsečový graf označovat procento požadavků zpracovaných 10 nejzeminací.

### <a name="using-the-pie-chart"></a>Použití výsečového grafu
* Najeďte nad výsečí ve výsečovém grafu a zobrazte název a verzi prohlížeče.
* Pro účely této sestavy je každá jedinečná kombinace prohlížeče a verze považována za jiný prohlížeč.
* Výseč s názvem "Ostatní" označuje procento požadavků zpracovaných všemi ostatními prohlížeči a verzemi.

Data použitá ke generování výsečového grafu lze zobrazit pod ním. Tam najdete typ prohlížeče / číslo verze, celkový počet přístupů a procento přístupů pro každý z top 250 prohlížečů.

## <a name="by-referrer"></a>Podle Referrer
Sestava Podle odkazovat umožňuje zobrazit horní referrers k obsahu na vybrané platformě. Odkazovatelist označuje název hostitele, ze kterého byl vygenerován požadavek. Při generování tohoto typu sestavy bude pruhový graf označovat množství poptávky (tj. přístupů) generovaných 10 odkazujícími osobami.

Levá strana grafu (osa y) označuje celkový počet požadavků, které má aktivum pro každou odkazovnou. Každý pruh v grafu představuje odkazovat. Pomocí schématu barevného kódování přiřazujete pruh odkazujícímu subjektu uvedenému v části 250 odkazujících barev.

Data, která byla použita ke generování pruhového grafu, lze zobrazit pod ním. Tam najdete ADRESU URL, celkový počet přístupů a procento přístupů generovaných z každého z nejlepších 250 odkazujících.

## <a name="by-download"></a>Podle stažení
Přehled Podle stažení umožňuje analyzovat vzory stahování pro nejžádanější obsah. Horní část sestavy obsahuje pruhový graf, který porovnává pokusy o stažení s dokončenými soubory ke stažení pro prvních 10 požadovaných datových zdrojů. Každý pruh je barevně rozlišen podle toho, zda se jedná o pokus o stažení (modrý) nebo o dokončené stahování (zelené).

> [!NOTE]
> Pro účely této sestavy jsou adresy URL CNAME na okraji převedeny na ekvivalentní adresy URL CDN. To umožňuje přesný součet pro všechny statistiky spojené s datovým zdrojem bez ohledu na adresu URL CDN nebo okraj CNAME, která se používá k jeho vyžádání.
> 
> 

Levá strana grafu (osa y) označuje název souboru pro každý z prvních 10 požadovaných datových zdrojů. Přímo pod grafem (osa x) najdete popisky, které udávají celkový počet pokusů/dokončených stažení.

Přímo pod pruhovým grafem budou uvedeny následující informace pro 250 požadovaných datových zdrojů: relativní cesta (včetně názvu souboru), počet stažení do dokončení, počet žádostí o požadovaný počet a procento požadavky, které vedly k úplnému stažení.

> [!TIP]
> Naše CDN není informována klientem HTTP (tj. prohlížečem), pokud byl datový zdroj zcela stažen. V důsledku toho musíme vypočítat, zda byl datový zdroj zcela stažen podle stavových kódů a požadavků na rozsah bajtů. První věc, kterou hledáme při provádění tohoto výpočtu je, zda požadavek má za následek stavový kód 200 OK. Pokud ano, pak se podíváme na požadavky rozsahu bajtů, abychom zajistili, že pokrývají celý majetek. Nakonec porovnáme množství přenesených dat s velikostí požadovaného majetku. Pokud jsou přenesená data rovna nebo větší než velikost souboru a požadavky na rozsah bajtů jsou vhodné pro tento datový zdroj, bude požadavek na přístup k přístupu započítán jako úplné stažení.
> 
> Vzhledem k interpretační povaze této zprávy byste měli mít na paměti následující body, které mohou změnit konzistenci a přesnost této zprávy.
> 
> * Vzorce provozu nelze přesně předpovědět, když se agenti uživatelů chovají jinak. To může způsobit dokončení výsledků stahování, které jsou větší než 100 %.
> * Prostředky, které využívají progresivní stahování HTTP, nemusí být touto sestavou přesně reprezentovány. To je způsobeno tím, že uživatelé hledají různé pozice ve videu.
> 
> 

## <a name="by-404-errors"></a>Podle 404 chyb
Sestava Chyby podle 404 umožňuje identifikovat typ obsahu, který generuje největší počet stavových kódů 404 Nenalezeno. Horní část sestavy obsahuje pruhový graf pro prvních 10 datových zdrojů, pro které byl vrácen stavový kód 404 Not Found. Tento pruhový graf porovnává celkový počet požadavků s požadavky, které vedly k 404 nebyl nalezen stavový kód pro tyto prostředky. Každý pruh je barevně rozlišený. Žlutý pruh se používá k označení, že požadavek za následek 404 nebyl nalezen stavový kód. Červený pruh se používá k označení celkového počtu požadavků na datový zdroj.

> [!NOTE]
> Pro účely této zprávy je třeba poznamenat, že:
> 
> * Požadavek na přístup k přístupu představuje jakýkoli požadavek na prostředek bez ohledu na stavový kód.
> * Adresy URL CNAME okrajů jsou převedeny na ekvivalentní adresy URL CDN. To umožňuje přesný součet pro všechny statistiky spojené s datovým zdrojem bez ohledu na adresu URL CDN nebo okraj CNAME, která se používá k jeho vyžádání.
> 
> 

Levá strana grafu (osa y) označuje název souboru pro každý z prvních 10 požadovaných datových zdrojů, který vyústil ve stavový kód 404 Nebyl nalezen. Přímo pod grafem (osa x) najdete popisky označující celkový počet požadavků a počet požadavků, které vedly ke stavu 404 Nenalezeno.

Přímo pod pruhovým grafem budou uvedeny následující informace pro 250 požadovaných prostředků: relativní cesta (včetně názvu souboru), počet požadavků, které vedly ke stavovému kódu 404 Not Found, celkový počet žádostí o majetek a procento požadavků, které vedly ke stavovému kódu 404 Not Found.

## <a name="see-also"></a>Viz také
* [CDN Azure – Přehled](cdn-overview.md)
* [Statistiky v reálném čase v microsoft azure cdn](cdn-real-time-stats.md)
* [Přepsání výchozího chování PROTOKOLU HTTP pomocí modulu pravidel](cdn-rules-engine.md)
* [Analýza výkonu hran](cdn-edge-performance.md)

