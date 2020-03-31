---
title: Analýza výkonu hraničních uzlů v Azure CDN | Dokumenty společnosti Microsoft
description: Analyzujte výkon hraničních uzlů v microsoft azure cdn. Edge Performance Analytics poskytuje podrobný informační provoz a využití šířky pásma pro CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b8a65d4ae6aaac78e642c851a66b745a940fa0ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593901"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analýza výkonu hraničního uzlu v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Edge performance analytics poskytuje podrobné informace provoz a využití šířky pásma pro CDN. Tyto informace pak lze použít ke generování statistik trendů, které vám umožní získat přehled o tom, jak jsou vaše prostředky ukládány do mezipaměti a doručovány vašim klientům. Na druhé straně, to vám umožní vytvořit strategii o tom, jak optimalizovat doručování obsahu a určit, jaké problémy by měly být řešeny lépe využít CDN. V důsledku toho budete nejen moci zlepšit výkon doručování dat, ale budete také moci snížit náklady na CDN.

> [!NOTE]
> Všechny sestavy používají zápis UTC/GMT při zadávání data a času.
> 
> 

## <a name="reports-and-log-collection"></a>Sestavy a shromažďování protokolů
Data aktivity CDN musí být shromážděna modulem Edge Performance Analytics, aby na něm mohla generovat sestavy. Tento proces sběru probíhá jednou denně a zahrnuje aktivitu, která proběhla během předchozího dne. To znamená, že statistiky sestavy představují vzorek denní statistiky v době, kdy byla zpracována, a nemusí nutně obsahovat úplnou sadu dat pro aktuální den. Primární funkcí těchto zpráv je posoudit výkon. Neměly by být používány pro účely fakturace nebo přesné číselné statistiky.

> [!NOTE]
> Nezpracovaná data, ze kterých se generují přehledy Edge Performance Analytic, jsou k dispozici po dobu nejméně 90 dnů.
> 
> 

## <a name="dashboard"></a>Řídicí panel
Řídicí panel Edge Performance Analytics sleduje aktuální a historický provoz CDN prostřednictvím grafu a statistiky. Tento řídicí panel slouží ke zjišťování nejnovějších a dlouhodobých trendů ve výkonu provozu CDN pro váš účet.

Tento řídicí panel se skládá z:

* Interaktivní graf, který umožňuje vizualizaci klíčových metrik a trendů.
* Časová osa, která poskytuje pocit dlouhodobé vzory pro klíčové metriky a trendy.
* Klíčové metriky a statistické informace o tom, jak naše síť CDN zlepšuje návštěvnost webu měřenou celkovým výkonem, výkonem a efektivitou.

### <a name="accessing-the-edge-performance-dashboard"></a>Přístup k řídicímu panelu výkonu okrajů
1. V okně profilu CDN klepněte na tlačítko **Spravovat.**
   
    ![Tlačítko pro správu okna profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte na kartu **Analytics** a najeďte na informační rámeček **Edge Performance Analytics.**  Klikněte na **řídicí panel**.
   
    Zobrazí se řídicí panel analýzy hraničních uzlů.

### <a name="chart"></a>Graf
Řídicí panel obsahuje graf, který sleduje metriku za časové období vybrané na časové ose, která se zobrazí přímo pod ní.  Časová osa, která mapuje až poslední dva roky aktivity CDN, se zobrazí přímo pod grafem.

#### <a name="using-the-chart"></a>Použití grafu
* Ve výchozím nastavení bude zmapována míra efektivity mezipaměti za posledních 30 dní.
* Tento graf je generován z dat shromaždených denně.
* Když najedete přes den v čárovém grafu, bude uvádět datum a hodnotu metriky k tomuto datu.
* Kliknutím na Zvýraznit víkendy přepněte do grafu překrytí světle šedých svislých pruhů, které představují víkendy. Tento typ překrytí je užitečný pro identifikaci dopravních vzorců během víkendů.
* Kliknutím na Zobrazit před rokem přepnete do grafu překrytí aktivity předchozího roku za stejné časové období. Tento typ porovnání poskytuje přehled o dlouhodobé vzory použití CDN. Pravý horní roh grafu obsahuje legendu, která označuje barevný kód pro každý spojnicový graf.

#### <a name="updating-the-chart"></a>Aktualizace grafu
* Časový rozsah: Proveďte jednu z následujících akcí:
  * Vyberte požadovanou oblast na časové ose. Graf bude aktualizován daty, která odpovídají vybranému časovému období.
  * Poklepáním na graf zobrazíte všechna dostupná historická data maximálně dva roky.
* Metrika: Klikněte na ikonu grafu, která se zobrazí vedle požadované metriky. Graf a časová osa budou aktualizovány daty pro odpovídající metriku.

### <a name="key-metrics-and-statistics"></a>Klíčové metriky a statistiky
#### <a name="efficiency-metrics"></a>Metriky efektivity
Účelem těchto metrik je zjistit, zda lze zlepšit efektivitu mezipaměti. Hlavní výhody vyplývající z efektivity mezipaměti jsou:

* Snížené zatížení původního serveru, které může vést k:
  * Lepší výkon webového serveru.
  * Snížení provozních nákladů.
* Vylepšené zrychlení doručování dat, protože více požadavků bude doručeno přímo z CDN.

| Pole | Popis |
| --- | --- |
| Efektivita mezipaměti |Označuje procento přenesených dat, která byla doručena z mezipaměti. Tato metrika měří, když byla verze požadovaného obsahu uložená v mezipaměti doručena přímo z CDN (hraniční servery) uchazečům (např. webovému prohlížeči). |
| Míra přístupů |Označuje procento požadavků, které byly obsluhovány z mezipaměti. Tato metrika měří, když byla verze požadovaného obsahu uložená v mezipaměti doručena přímo z CDN (hraniční servery) uchazečům (např. webovému prohlížeči). |
| % vzdálených bajtů – konfigurace bez mezipaměti |Označuje procento provozu, který byl obsluhován ze serverů původu do sítě CDN (hraniční servery), která nebude uložena do mezipaměti v důsledku funkce Obejít mezipaměť (Modul pravidel HTTP). |
| % vzdálených bajtů – mezipaměť s ukončenou platností |Označuje procento provozu, který byl obsluhován ze serverů původu na CDN (hraniční servery) v důsledku opětovného ověření zastaralého obsahu. |

#### <a name="usage-metrics"></a>Metriky využití
Účelem těchto metrik je poskytnout přehled o následujících opatřeních ke snížení nákladů:

* Minimalizace provozních nákladů prostřednictvím cdn.
* Snížení výdajů CDN prostřednictvím efektivity mezipaměti a komprese.

> [!NOTE]
> Čísla objemu provozu představují provoz, který byl použit ve výpočtech poměrů a procent, a může zobrazovat pouze část celkového provozu pro velkoobjemové zákazníky.
> 
> 

| Pole | Popis |
| --- | --- |
| Ave bajty ven |Označuje průměrný počet bajtů přenesených pro každý požadavek obsluhovaný z CDN (hraniční servery) žadateli (např. webový prohlížeč). |
| Rychlost bajtů konfigurace konfigurace mezipaměti |Označuje procento provozu obsluhovaného z CDN (hraniční servery) žadateli (např. webovému prohlížeči), který nebude uložen do mezipaměti z důvodu funkce Obejít mezipaměť. |
| Rychlost komprimovaného bajtu |Označuje procento provozu odeslaného z CDN (hraniční servery) uchazečům (např. webový prohlížeč) v komprimovaném formátu. |
| Ubajtů ven |Označuje množství dat v bajtů, které byly dodány z CDN (hraniční servery) žadateli (např. webový prohlížeč). |
| Bajty v |Označuje množství dat v bajtů odeslaných od uchazečů (např. webového prohlížeče) do sítě CDN (hraniční servery). |
| Vzdálené bajty |Označuje množství dat v bajtů odeslaných ze serverů CDN a původu zákazníka do sítě CDN (hraniční servery). |

#### <a name="performance-metrics"></a>Metriky výkonu
Účelem těchto metrik je sledovat celkový výkon CDN pro váš provoz.

| Pole | Popis |
| --- | --- |
| Přenosová rychlost |Označuje průměrnou rychlost přenosu obsahu z sítě CDN na uchazeče. |
| Doba trvání |Označuje průměrnou dobu v milisekundách, po kterou bylo nutné doručit datový zdroj žadateli (např. webovému prohlížeči). |
| Rychlost komprimovaných požadavků |Označuje procento přístupů, které byly doručeny z CDN (hraniční servery) žadateli (např. webový prohlížeč) v komprimovaném formátu. |
| 4xx Chybová frekvence |Označuje procento přístupů, které vygenerovaly stavový kód 4xx. |
| 5xx Chybová frekvence |Označuje procento přístupů, které vygenerovaly stavový kód 5xx. |
| Hits |Označuje počet požadavků na obsah CDN. |

#### <a name="secure-traffic-metrics"></a>Metriky zabezpečeného provozu
Účelem těchto metrik je sledovat výkon CDN pro provoz HTTPS.

| Pole | Popis |
| --- | --- |
| Bezpečná efektivita mezipaměti |Označuje procento dat přenesených pro požadavky HTTPS, které byly obsluhovány z mezipaměti. Tato metrika měří, když byla verze požadovaného obsahu uložená v mezipaměti doručena přímo z CDN (hraniční servery) uchazečům (např. webovému prohlížeči) prostřednictvím protokolu HTTPS. |
| Bezpečná přenosová rychlost |Označuje průměrnou rychlost přenosu obsahu z CDN (hraničních serverů) na uchazeče (např. webové servery) přes protokol HTTPS. |
| Průměrná doba bezpečného zajištění |Označuje průměrnou dobu v milisekundách, po kterou bylo nutné doručit datový zdroj žadateli (např. webovému prohlížeči) přes protokol HTTPS. |
| Zabezpečené přístupy |Označuje počet požadavků HTTPS pro obsah CDN. |
| Bezpečné bajty ven |Označuje množství přenosů HTTPS v bajtech, které byly dodány z CDN (hraniční servery) žadateli (např. webovému prohlížeči). |

## <a name="reports"></a>Sestavy
Každá sestava v tomto modulu obsahuje graf a statistiky o šířce pásma a využití provozu pro různé typy metrik (např. stavové kódy HTTP, stavové kódy mezipaměti, adresa URL požadavku atd.). Tyto informace mohou být použity k podrobnějšímu prohloubení způsobu zobrazování obsahu vašim klientům a k jemnému doladění chování CDN za účelem zlepšení výkonu doručování dat.

### <a name="accessing-the-edge-performance-reports"></a>Přístup k přehledům výkonu hraničních
1. V okně profilu CDN klepněte na tlačítko **Spravovat.**
   
    ![Tlačítko pro správu okna profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte na kartu **Analytics** a najeďte na informační rámeček **Edge Performance Analytics.**  Klikněte na **HTTP Velký objekt**.
   
    Zobrazí se obrazovka sestav analýzy hraničních uzlů.

| Sestava | Popis |
| --- | --- |
| Denní souhrn |Umožňuje zobrazit denní trendy provozu za zadané časové období. Každý pruh v tomto grafu představuje konkrétní datum. Velikost pruhu označuje celkový počet přístupů, ke kterým k tomuto datu došlo. |
| Hodinový souhrn |Umožňuje zobrazit hodinové trendy provozu za zadané časové období. Každý pruh v tomto grafu představuje jednu hodinu k určitému datu. Velikost pruhu označuje celkový počet přístupů, ke kterým došlo během této hodiny. |
| Protokoly |Zobrazuje rozdělení provozu mezi protokoly HTTP a HTTPS. Graf koblihy označuje procento přístupů, ke kterým došlo pro každý typ protokolu. |
| Metody PROTOKOLU HTTP |Umožňuje získat rychlý přehled o tom, které metody HTTP se používají k vyžádání dat. Nejběžnější metody požadavku HTTP jsou obvykle GET, HEAD a POST. Graf donut označuje procento přístupů, ke kterým došlo pro každý typ metody požadavku HTTP. |
| URL – adresy |Obsahuje graf, který zobrazuje 10 nejvyšších požadovaných adres URL. Pro každou adresu URL se zobrazí pruh. Výška panelu označuje, kolik přístupů, které konkrétní adresa URL vygenerovala v časovém rozpětí pokrytém sestavou. Statistiky pro 100 požadovaných adres URL jsou zobrazeny přímo pod tímto grafem. |
| CNAMEs |Obsahuje graf, který zobrazuje 10 nejlepších CNAMEs používaných k vyžádání datových zdrojů v časovém rozpětí sestavy. Statistiky pro 100 požadovaných CNAMes jsou zobrazeny přímo pod tímto grafem. |
| Původ |Obsahuje graf, který zobrazuje 10 nejvyšších serverů CDN nebo původu zákazníka, ze kterých byly požadovány prostředky za zadané časové období. Statistiky pro 100 požadovaných serverů CDN nebo serverů původu zákazníka jsou zobrazeny přímo pod tímto grafem. Servery původu zákazníka jsou označeny názvem definovaným v možnosti Název adresáře. |
| Geografické POP |Zobrazuje, jak velká část provozu je směrována prostřednictvím určitého bodu přítomnosti (POP). Třípísmenná zkratka představuje pop v naší síti CDN. |
| Klienti |Obsahuje graf, který zobrazuje prvních 10 klientů, kteří požadovali majetek za zadané časové období. Pro účely této sestavy jsou všechny požadavky, které pocházejí ze stejné adresy IP, považovány za od stejného klienta. Statistiky pro top 100 klientů jsou zobrazeny přímo pod tímto grafem. Tato sestava je užitečná pro určení vzorců aktivit stahování pro nejlepší klienty. |
| Stavy mezipaměti |Poskytuje podrobný rozpis chování mezipaměti, které může odhalit přístupy ke zlepšení celkového prostředí koncového uživatele. Vzhledem k tomu, že nejrychlejší výkon pochází z přístupů do mezipaměti, můžete optimalizovat rychlost doručování dat minimalizací neúspěšných přístupů do mezipaměti a přístupů do mezipaměti, jejichž platnost vypršela. |
| NONE Podrobnosti |Obsahuje graf, který zobrazuje 10 hlavních adres URL pro datové zdroje, u kterých nebyla za zadané časové období zkontrolována čerstvost obsahu mezipaměti. Statistiky pro 100 hlavních adres pro tyto typy datových zdrojů jsou zobrazeny přímo pod tímto grafem. |
| podrobnosti o CONFIG_NOCACHE |Obsahuje graf, který zobrazuje 10 hlavních adres URL pro datové zdroje, které nebyly uloženy do mezipaměti z důvodu konfigurace CDN zákazníka. Tyto typy datových zdrojů byly obsluhovány přímo ze serveru původu. Statistiky pro 100 hlavních adres pro tyto typy datových zdrojů jsou zobrazeny přímo pod tímto grafem. |
| Podrobnosti o neuložení do mezipaměti |Obsahuje graf, který zobrazuje 10 hlavních adres, které obsahují 10 hlavních adres URL pro datové zdroje, které nelze uložit do mezipaměti z důvodu dat hlavičky požadavku. Statistiky pro 100 hlavních adres pro tyto typy datových zdrojů jsou zobrazeny přímo pod tímto grafem. |
| podrobnosti o TCP_HIT |Obsahuje graf, který zobrazuje 10 hlavních adres URL pro datové zdroje, které jsou obsluhovány okamžitě z mezipaměti. Statistiky pro 100 hlavních adres pro tyto typy datových zdrojů jsou zobrazeny přímo pod tímto grafem. |
| podrobnosti o TCP_MISS |Obsahuje graf, který zobrazuje 10 hlavních adres URL pro datové zdroje, které mají stav mezipaměti TCP_MISS. Statistiky pro 100 hlavních adres pro tyto typy datových zdrojů jsou zobrazeny přímo pod tímto grafem. |
| TCP_EXPIRED_HIT podrobnosti |Obsahuje graf, který zobrazuje 10 hlavních adres URL pro zastaralé datové zdroje, které byly obsluhovány přímo z protokolu POP. Statistiky pro 100 hlavních adres pro tyto typy datových zdrojů jsou zobrazeny přímo pod tímto grafem. |
| podrobnosti TCP_EXPIRED_MISS |Obsahuje graf, který zobrazuje 10 hlavních adres URL pro zastaralé datové zdroje, pro které bylo třeba načíst novou verzi ze zdrojového serveru. Statistiky pro 100 hlavních adres pro tyto typy datových zdrojů jsou zobrazeny přímo pod tímto grafem. |
| podrobnosti TCP_CLIENT_REFRESH_MISS |Obsahuje pruhový graf, který zobrazuje 10 hlavních adres URL pro datové zdroje, které byly načteny ze zdrojového serveru z důvodu požadavku klienta bez mezipaměti. Statistiky pro 100 hlavních adres url pro tyto typy požadavků jsou zobrazeny přímo pod tímto grafem. |
| Typy požadavků klienta |Označuje typ požadavků, které byly provedeny klienty HTTP (např. prohlížeče). Tato sestava obsahuje graf koblihy, který poskytuje představu o tom, jak jsou zpracovávány požadavky. Šířka pásma a informace o provozu pro každý typ požadavku se zobrazí pod grafem. |
| Uživatelský agent |Obsahuje sloupcový graf zobrazující 10 nejlepších uživatelských agentů, kteří požadují váš obsah prostřednictvím naší sítě CDN. Uživatelský maješ obvykle webový prohlížeč, přehrávač médií nebo prohlížeč mobilních telefonů. Statistiky pro 100 nejlepších uživatelských agentů jsou zobrazeny přímo pod tímto grafem. |
| Přišli |Obsahuje sloupcový graf zobrazující 10 hlavních odkazujících na obsah přístupný prostřednictvím našeho CDN. Odkazovaný odkazovaný objekt je obvykle adresa URL webové stránky nebo prostředku, který odkazuje na váš obsah. Podrobné informace jsou uvedeny pod grafem pro 100 hlavních odkazovaných. |
| Typy komprese |Obsahuje graf koblih, který rozděluje požadované prostředky podle toho, zda byly komprimovány našimi hraničními servery. Procento komprimovaných datových zdrojů se člení podle typu použité komprese. Podrobné informace jsou uvedeny pod grafem pro každý typ komprese a stav. |
| Typy souborů |Obsahuje sloupcový graf, který zobrazuje 10 nejlepších typů souborů, které byly požadovány prostřednictvím našeho CDN pro váš účet. Pro účely této sestavy je typ souboru definován příponou názvu datového zdroje a typem \[internetového\]média (např. text\]html/html \[,\]text htm/html \[, text aspx atd.). Podrobné informace jsou uvedeny pod grafem pro prvních 100 typů souborů. |
| Jedinečné soubory |Obsahuje graf, který vykresluje celkový počet jedinečných aktiv, které byly požadovány v určitý den za zadané časové období. |
| Přehled tokenu |Obsahuje výsečový graf, který poskytuje rychlý přehled o tom, zda byly požadované prostředky chráněny ověřováním na základě tokenu. Chráněné prostředky jsou zobrazeny v grafu podle výsledků jejich pokusu o ověření. |
| Podrobnosti o zamítnutí tokenu |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, které byly odepřeny z důvodu ověřování na základě tokenu. |
| Kódy odpovědí HTTP |Obsahuje rozpis stavových kódů HTTP (např. 200 OK, 403 Forbidden, 404 Not Found atd.), které byly doručeny vašim http klientům našimi hraničními servery. Výsečový graf umožňuje rychle posoudit, jak byl váš majetek obsluhován. Podrobné statistické údaje jsou uvedeny pro každý kód odpovědi pod grafem. |
| 404 Chyby |Obsahuje sloupcový graf, který umožňuje zobrazit prvních 10 požadavků, které vyústily v kód odpovědi 404 Not Found. |
| 403 Chyby |Obsahuje sloupcový graf, který umožňuje zobrazit prvních 10 požadavků, které vyústily v 403 Zakázaný kód odpovědi. 403 Zakázaný kód odpovědi nastane, když je požadavek odepřen serverem původu zákazníka nebo hraničním serverem na našem bodu POP. |
| 4xx Chyby |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, které vyústily v kód odpovědi v rozsahu 400. Z této sestavy jsou vyloučeny 403 nebyly nalezeny a 404 Zakázaných kódů odpovědí. Kód odpovědi 4xx obvykle nastane, když je požadavek odepřen v důsledku chyby klienta. |
| 504 Chyby |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, které vyústily v kód odpovědi 504 Gateway Timeout. Kód odpovědi 504 Gateway Timeout nastane, když dojde k časovému výpadku, když se proxy server HTTP pokouší komunikovat s jiným serverem. V případě našeho CDN kód odpovědi 504 Gateway Timeout obvykle dochází, když hraniční server není schopen navázat komunikaci se serverem původu zákazníka. |
| 502 Chyb |Obsahuje sloupcový graf, který umožňuje zobrazit prvních 10 požadavků, které vyústily v kód odpovědi 502 Bad Gateway. Kód odpovědi 502 Bad Gateway nastane, když dojde k selhání protokolu HTTP mezi serverem a proxy serverem HTTP. V případě našeho CDN kód odpovědi 502 Bad Gateway obvykle dochází, když server původu zákazníka vrátí neplatnou odpověď na hraniční server. Odpověď je neplatná, pokud ji nelze analyzovat nebo pokud je neúplná. |
| 5xx Chyby |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, které vyústily v kód odpovědi v rozsahu 500.  Z této sestavy jsou vyloučeny kódy odpovědí 502 Bad Gateway a 504 Gateway Timeout. |

## <a name="see-also"></a>Viz také
* [CDN Azure – Přehled](cdn-overview.md)
* [Statistiky v reálném čase v microsoft azure cdn](cdn-real-time-stats.md)
* [Přepsání výchozího chování PROTOKOLU HTTP pomocí modulu pravidel](cdn-rules-engine.md)
* [Rozšířené sestavy PROTOKOLU HTTP](cdn-advanced-http-reports.md)

