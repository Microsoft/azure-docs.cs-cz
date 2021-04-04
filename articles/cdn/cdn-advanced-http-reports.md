---
title: Analýza statistiky využití pomocí Azure CDN pokročilých sestav HTTP | Microsoft Docs
description: Naučte se vytvářet rozšířené sestavy HTTP v Microsoft Azure CDN. Tyto sestavy poskytují podrobné informace o aktivitě CDN.
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
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e2ef1367cbbb1fb71159c4520c3d1481904c1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779318"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analýza statistik využití pomocí pokročilých sestav HTTP služby Azure CDN
## <a name="overview"></a>Přehled
Tento dokument vysvětluje rozšířené vytváření sestav protokolu HTTP v Microsoft Azure CDN. Tyto sestavy poskytují podrobné informace o aktivitě CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Přístup k pokročilým zprávám HTTP
1. V okně profilu CDN klikněte na tlačítko **Spravovat** .
   
    ![Tlačítko Spravovat okno profilu CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte myší na kartu **Analýza** a potom najeďte myší na rozšířený informační rámeček **sestav http** .  Klikněte na **Rozsáhlá platforma http**.
   
    ![Portál pro správu CDN – nabídka pokročilých sestav](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Zobrazí se možnosti sestavy.

## <a name="geography-reports-map-based"></a>Geografické sestavy (založené na mapě)
Existuje pět sestav, které využívají mapu k označení oblastí, ze kterých se váš obsah požaduje. Tyto sestavy jsou mapa světa, mapa USA, Kanada, Evropa a mapa Asie a Tichomoří.

Každá sestava založená na mapě řadí geografické entity (tj. země nebo oblasti), které vám pomůžou vizualizovat umístění, ze kterých se váš obsah požaduje. To je možné tak, že barevně kóduje jednotlivé oblasti podle toho, jaké množství poptávky v této oblasti je v této oblasti. Světlejší šedivé oblasti označují nižší požadavky na obsah, zatímco tmavší oblasti znamenají vyšší úrovně požadavků na obsah.

Podrobné informace o provozu a šířce pásma pro jednotlivé oblasti jsou k dispozici přímo pod mapou. To vám umožní zobrazit celkový počet přístupů, procento přístupů, celkové množství přenesených dat (v gigabajtech) a procento přenesených dat pro jednotlivé oblasti. Podívejte se na popis každé z těchto metrik. Nakonec, když najedete myší na oblast (tj. země/oblast, stát nebo provincie), název a procento přístupů, ke kterým došlo v oblasti, se zobrazí jako popis.

Pro každý typ geografické sestavy založené na mapě je níže uveden stručný popis.

| Report Name | Description |
| --- | --- |
| Mapa světa |Tato sestava vám umožní zobrazit celosvětovou poptávku pro obsah CDN. Každá země nebo oblast je Barevně odlišená na mapě světa, aby označovala procento přístupů, které pocházejí z dané oblasti. |
| Mapa USA |Tato sestava vám umožní zobrazit v USA požadavek na obsah CDN. Každý stav je na této mapě barevně odlišený, aby označoval procento přístupů, které pocházejí z dané oblasti. |
| Mapa Kanady |Tato sestava vám umožní zobrazit poptávku obsahu CDN v Kanadě. Každá provincie má na této mapě barevně odlišenou barvu, která označuje procento přístupů z dané oblasti. |
| Mapa Evropy |Tato sestava vám umožní zobrazit poptávku obsahu CDN v Evropě. U každé země nebo oblasti má tato mapa barevně odlišenou barvu, aby označovala procento přístupů, které pocházejí z dané oblasti. |
| Mapa Asie a Tichomoří |Tato sestava vám umožní zobrazit poptávku obsahu CDN v Asii. U každé země nebo oblasti má tato mapa barevně odlišenou barvu, aby označovala procento přístupů, které pocházejí z dané oblasti. |

## <a name="geography-reports-bar-charts"></a>Geografické sestavy (pruhové grafy)
Existují dvě další sestavy, které poskytují statistické informace podle geografického umístění, které jsou hlavní města a hlavní země. Tyto sestavy vyřadí města a země nebo oblasti v závislosti na počtu přístupů, které pocházejí z těchto zemí nebo oblastí. Po vygenerování tohoto typu sestavy bude pruhový graf označovat prvních 10 měst nebo zemí nebo oblastí, které požadovaly obsah přes konkrétní platformu. Tento pruhový graf vám umožní rychle vyhodnotit oblasti, které generují nejvyšší počet požadavků na váš obsah.

Levá strana grafu (osa y) indikuje, kolik přístupů v zadané oblasti proběhlo. Přímo pod grafem (osa x) najdete popisek pro každou horní 10 oblastí.

### <a name="using-the-bar-charts"></a>Použití pruhových grafů
* Pokud najedete myší na určitý pruh, název a celkový počet přístupů, ke kterým došlo v oblasti, se zobrazí jako popis.
* Popis sestavy horních měst identifikuje město podle názvu, kraje a zkratky země/oblasti.
* Pokud město nebo oblast (tj. stát/provincie), ze které pochází požadavek, nelze určit, bude označovat, že nejsou známy. Pokud země/oblast není známa, zobrazí se dvě otazníky (tj.??).
* Sestava může obsahovat metriky pro Evropu nebo oblast Asie a Tichomoří. Tyto položky nejsou určeny k poskytování statistických informací o všech IP adresách v těchto oblastech. Místo toho se vztahují pouze na žádosti, které pocházejí z IP adres, které jsou rozloženy přes Evropa nebo Asie a Tichomoří místo konkrétního města nebo země nebo oblasti.

Data, která byla použita k vygenerování pruhového grafu, lze zobrazit pod ní. Najdete tu celkový počet přístupů, procento přístupů, objem přenesených dat (v gigabajtech) a procento přenesených dat pro nejvyšší 250 oblasti. Podívejte se na popis každé z těchto metrik.

Stručný popis je k dispozici pro oba typy sestav níže.

| Report Name | Description |
| --- | --- |
| Hlavní města |Tato sestava řadí města podle počtu přístupů, které pocházejí z dané oblasti. |
| Hlavní země |Tato sestava řadí země nebo oblasti podle počtu přístupů, které pocházejí z dané země nebo oblasti. |

## <a name="daily-summary"></a>Denní souhrn
Sestava denní souhrn vám umožní zobrazit celkový počet přístupů a data přenesená na základě konkrétní platformy denně. Tyto informace se dají využít k rychlému nerozlišujeí vzorců aktivit CDN. Pomocí této sestavy můžete například zjistit, které dny byly vyšší nebo nižší než očekávaný provoz.

Po vygenerování tohoto typu sestavy bude pruhový graf poskytovat vizuální indikaci na množství poptávky specifického pro konkrétní platformu v průběhu času, který je pokrytý sestavou. Provedete to tak, že zobrazíte pruh pro každý den v sestavě. Například výběrem časového období s názvem "poslední týden" bude vygenerován pruhový graf se sedmi pruhy. Každý pruh bude označovat celkový počet přístupů, které v daném dni nastaly.

Levá strana grafu (osa y) indikuje počet přístupů k zadanému datu. Přímo pod grafem (osa x) najdete popisek, který označuje datum (Format: RRRR-MM-DD) pro každý den zahrnutý v sestavě.

> [!TIP]
> Pokud najedete myší na určitý pruh, zobrazí se jako popis tento celkový počet přístupů k tomuto datu.
> 
> 

Data, která byla použita k vygenerování pruhového grafu, lze zobrazit pod ní. Zjistíte celkový počet přístupů a množství přenesených dat (v gigabajtech) pro každý den, na který se sestava vztahuje.

## <a name="by-hour"></a>Po hodinách
Sestava po hodinách vám umožní zobrazit celkový počet přístupů a data přenesená na základě jednotlivých platforem po hodinách. Tyto informace se dají využít k rychlému nerozlišujeí vzorců aktivit CDN. Například tato sestava vám může pomáhat detekovat časová období v průběhu dne, kdy dochází k vyšší nebo nižší než očekávaný provoz.

Po vygenerování tohoto typu sestavy bude pruhový graf poskytovat vizuální indikaci na množství poptávky specifického pro konkrétní platformu na základě hodin, které jsou pokryté sestavou. To provedete tak, že zobrazíte pruh pro každou hodinu pokrytou sestavou. Například když vyberete 24 hodinové časové období, vygeneruje se pruhový graf s dvacet čtyři pruhy. Každý pruh bude označovat celkový počet přístupů, ke kterým během této hodiny došlo.

Levá strana grafu (osa y) indikuje, kolik přístupů v zadané hodině proběhlo. Přímo pod grafem (osa x) najdete popisek, který označuje datum a čas (formát: RRRR-MM-DD hh: mm) pro každou hodinu zahrnutou v sestavě. Čas se oznamuje pomocí 24hodinovém formátu a je zadaný pomocí časového pásma UTC/GMT.

> [!TIP]
> Pokud najedete myší na určitý pruh, zobrazí se jako popis tento celkový počet přístupů, ke kterým došlo během dané hodiny.
> 
> 

Data, která byla použita k vygenerování pruhového grafu, lze zobrazit pod ní. Zjistíte celkový počet přístupů a množství přenesených dat (v gigabajtech) pro každou hodinu pokrytou sestavou.

## <a name="by-file"></a>Podle souboru
Sestava podle souboru vám umožňuje zobrazit množství požadavků a přenosů, které se uskutečnily na konkrétní platformě, na maximum požadovaných prostředků. Po vygenerování tohoto typu sestavy se v zadaném časovém období vygeneruje pruhový graf na prvních 10 nejvíc požadovaných prostředků.

> [!NOTE]
> Pro účely této sestavy se adresy URL hraničních záznamů CNAME převedou na odpovídající adresy URL CDN. To umožňuje přesné množství přístupů k celkovému počtu přístupů přidružených k assetu bez ohledu na adresu URL CDN nebo hraničního záznamu CNAME, která se používá k vyžádání.
> 
> 

Levá strana grafu (osa y) označuje počet požadavků na jednotlivé prostředky v zadaném časovém období. Přímo pod grafem (osa x) najdete popisek, který označuje název souboru pro každý z prvních deseti požadovaných prostředků.

Data, která byla použita k vygenerování pruhového grafu, lze zobrazit pod ní. Pro každý z hlavních 250 požadovaných prostředků najdete následující informace: relativní cesta, celkový počet přístupů, procento přístupů, objem přenesených dat (v gigabajtech) a procento přenesených dat.

## <a name="by-file-detail"></a>Podle podrobností o souboru
Sestava Podrobnosti o souboru vám umožňuje zobrazit množství požadavků a přenosů, které se uskutečnily na konkrétní platformě pro určitý prostředek. V horní části této sestavy jsou podrobnosti o souboru pro možnost. Tato možnost poskytuje seznam nejvíce požadovaných prostředků na vybrané platformě. Aby bylo možné vygenerovat sestavu s podrobnostmi o souboru, bude nutné vybrat požadovaný Asset z podrobností souboru pro možnost. Potom pruhový graf bude označovat množství denních požadavků, které se vygenerovaly během zadaného časového období.

Levá strana grafu (osa y) označuje celkový počet požadavků, které Asset v určitý den zjistil. Přímo pod grafem (osa x) najdete popisek, který označuje datum (Format: RRRR-MM-DD), pro které se nahlásila žádost CDN pro daný Asset.

Data, která byla použita k vygenerování pruhového grafu, lze zobrazit pod ní. Zjistíte celkový počet přístupů a množství přenesených dat (v gigabajtech) pro každý den, na který se sestava vztahuje.

## <a name="by-file-type"></a>Podle typu souboru
Sestava podle typu souboru umožňuje zobrazit množství požadavků a přenosů, které vznikají typu souboru. Po vygenerování tohoto typu sestavy bude prstencový graf indikovat procento přístupů generovaných horními 10 typy souborů.

> [!TIP]
> Pokud najedete myší na řez v prstencovém grafu, jako popis se zobrazí typ internetového média tohoto typu.
> 
> 

Data, která byla použita k vygenerování prstencového grafu, lze zobrazit pod ní. Najdete tu příponu názvu souboru/typ internetového média, celkový počet přístupů, procento přístupů, objem přenesených dat (v gigabajtech) a procento přenášených dat pro každý z prvních 250 typů souborů.

## <a name="by-directory"></a>Podle adresáře
Sestava podle adresáře umožňuje zobrazit množství požadavků a přenosů, které byly na konkrétní platformě pro obsah z konkrétního adresáře. Po vygenerování tohoto typu sestavy bude pruhový graf označovat celkový počet přístupů generovaných obsahem v prvních 10 adresářích.

### <a name="using-the-bar-chart"></a>Použití pruhového grafu
* Najeďte myší na pruh a zobrazte relativní cestu k odpovídajícímu adresáři.
* Obsah uložený v podsložce adresáře se nepočítá při výpočtu požadavků podle adresáře. Tento výpočet spoléhá výhradně na počet požadavků vygenerovaných pro obsah uložený ve skutečném adresáři.
* Pro účely této sestavy se adresy URL hraničních záznamů CNAME převedou na odpovídající adresy URL CDN. To umožňuje přesné shromáždění všech statistik přidružených k assetu bez ohledu na adresu URL CDN nebo hraničního záznamu CNAME, která se používá k vyžádání.

Levá strana grafu (osa y) označuje celkový počet požadavků na obsah uložený ve deseti hlavních adresářích. Každý pruh grafu představuje adresář. Pomocí schématu barevného kódování Porovnejte seznam s adresářem uvedeným v horní části 250 úplné adresáře.

Data, která byla použita k vygenerování pruhového grafu, lze zobrazit pod ní. Pro každý z prvních 250 adresářů najdete tyto informace: relativní cesta, celkový počet přístupů, procento přístupů, objem přenesených dat (v gigabajtech) a procento přenesených dat.

## <a name="by-browser"></a>Podle prohlížeče
Sestava podle prohlížeče umožňuje zobrazit, které prohlížeče byly použity pro vyžádání obsahu. Po vygenerování tohoto typu sestavy bude výsečový graf označovat procento požadavků zpracovávaných 10 nejoblíbenějších prohlížečů.

### <a name="using-the-pie-chart"></a>Použití výsečového grafu
* Pokud chcete zobrazit název a verzi prohlížeče, najeďte myší na výseč ve výsečovém grafu.
* Pro účely této sestavy se každou jedinečnou kombinaci prohlížečů a verzí považuje za jiný prohlížeč.
* Řez s názvem "jiné" označuje procento požadavků zpracovávaných všemi ostatními prohlížeči a verzemi.

Data, která byla použita k vygenerování výsečového grafu, lze zobrazit pod ním. Najdete tu číslo typu nebo verze prohlížeče, celkový počet přístupů a procento přístupů pro každý z prvních 250 prohlížečů.

## <a name="by-referrer"></a>Podle odkazujícího serveru
Sestava podle odkazujícího serveru umožňuje zobrazit hlavní odvoditelné obsahy na vybrané platformě. Odkazující objekt označuje název hostitele, ze kterého byla vygenerována žádost. Po vygenerování tohoto typu sestavy bude pruhový graf označovat množství poptávky (tj. počet přístupů) generovaných horními 10 hlavními servery.

Levá strana grafu (osa y) označuje celkový počet požadavků, které Asset vyhledá u každého odkazujícího serveru. Každý pruh grafu představuje odkazujícího serveru. Pomocí schématu barevného kódování můžete porovnávat pruh s odkazujícím na adresu uvedenou v horní části 250.

Data, která byla použita k vygenerování pruhového grafu, lze zobrazit pod ní. Najdete tu adresu URL, celkový počet přístupů a procento přístupů generovaných z prvních 250 serverů.

## <a name="by-download"></a>Stažením
Sestava po stažení umožňuje analyzovat vzory stahování pro váš nejvíc požadovaný obsah. Horní část sestavy obsahuje pruhový graf, který porovnává pokusy o stažení s dokončenými stahováními za prvních 10 požadovaných prostředků. Každý pruh je barevně kódovaný podle toho, zda se jedná o pokus o stažení (modrý) nebo dokončené stažení (zelený).

> [!NOTE]
> Pro účely této sestavy se adresy URL hraničních záznamů CNAME převedou na odpovídající adresy URL CDN. To umožňuje přesné shromáždění všech statistik přidružených k assetu bez ohledu na adresu URL CDN nebo hraničního záznamu CNAME, která se používá k vyžádání.
> 
> 

Levá strana grafu (osa y) označuje název souboru pro každý z prvních deseti požadovaných prostředků. Přímo pod grafem (osa x) najdete popisky, které označují celkový počet pokusů o stažení/dokončení stahování.

Přímo pod pruhovým grafem se zobrazí následující informace o horních 250 požadovaných prostředcích: relativní cesta (včetně názvu souboru), počet pokusů o dokončení stažení, počet pokusů o dokončení a procento požadavků, které vedly k úplnému stažení.

> [!TIP]
> Po úplném stažení assetu neoznamuje služba CDN klienta protokolu HTTP (tj. prohlížeč). V důsledku toho je potřeba vypočítat, jestli se Asset úplně stáhl podle stavových kódů a požadavků na rozsah bajtů. První věc, kterou hledáte při provádění tohoto výpočtu, je, že výsledkem požadavku je stavový kód 200 OK. Pokud ano, podíváme se na požadavky na rozsah bajtů, aby se zajistilo, že budou pokrývat celý Asset. Nakonec porovnáváme množství dat přenesených do velikosti požadovaného prostředku. Pokud se přenesená data rovnají nebo jsou větší než velikost souboru a požadavky na rozsah v bajtech jsou pro daný prostředek vhodné, bude se tento počet započítat jako úplné stažení.
> 
> Z důvodu interpretace této sestavy byste měli mít na paměti následující body, které mohou změnit konzistenci a přesnost této sestavy.
> 
> * Vzorce provozu nelze přesně předpovědět, pokud se uživatelské agenti chovají jinak. To může způsobit dokončené výsledky stahování, které jsou větší než 100%.
> * Prostředky, které využívají výhod progresivního stahování HTTP, nemusí být přesně reprezentované touto sestavou. Důvodem je, že uživatelé hledají různé pozice ve videu.
> 
> 

## <a name="by-404-errors"></a>Chyby 404
Zpráva o chybách podle 404 umožňuje identifikovat typ obsahu, který generuje nejvyšší počet nenalezených stavových kódů 404. Horní část sestavy obsahuje pruhový graf pro prvních 10 prostředků, pro které se vrátil stavový kód 404 Nenalezeno. Tento pruhový graf porovnává celkový počet požadavků s požadavky, které vedly k 404 nenalezeného stavového kódu pro tyto prostředky. Každý pruh má barevně kódovaný. Žlutý pruh se používá k indikaci, že žádost vedla k 404 nenalezeného stavového kódu. K určení celkového počtu požadavků na prostředek se používá červený pruh.

> [!NOTE]
> Pro účely této sestavy mějte na paměti následující:
> 
> * Volání představuje všechny požadavky na prostředek bez ohledu na stavový kód.
> * Adresy URL hraničních záznamů CNAME jsou převáděny na odpovídající adresy URL CDN. To umožňuje přesné shromáždění všech statistik přidružených k assetu bez ohledu na adresu URL CDN nebo hraničního záznamu CNAME, která se používá k vyžádání.
> 
> 

Levá strana grafu (osa y) označuje název souboru pro každý z prvních 10 požadovaných prostředků, který má za následek 404 nenalezený stavový kód. Přímo pod grafem (osa x) najdete popisky, které označují celkový počet požadavků a počet požadavků, které byly výsledkem 404. nenalezeny stavový kód.

Přímo pod pruhovým grafem se zobrazí následující informace o horních 250 požadovaných materiálech: relativní cesta (včetně názvu souboru), počet požadavků, které byly způsobeny stavovým kódem 404 Nenalezeno, celkový počet pokusů o vyřízení prostředků a procento požadavků, které vedly k nenalezení stavového kódu 404.

## <a name="see-also"></a>Viz také
* [CDN Azure – Přehled](cdn-overview.md)
* [Statistiky v reálném čase v Microsoft Azure CDN](cdn-real-time-stats.md)
* [Přepsání výchozího chování HTTP pomocí modulu pravidel](./cdn-verizon-premium-rules-engine.md)
* [Analýza výkonu Edge](cdn-edge-performance.md)