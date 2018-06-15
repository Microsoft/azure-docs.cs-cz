---
title: Analýza výkonu hraničního uzlu v Azure CDN | Microsoft Docs
description: Analýza výkonu hraničního uzlu v Microsoft Azure CDN. Analýza výkonu Edge poskytuje podrobné informace o provozu a využití šířky pásma pro CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: ad285b4e2226c85859acb22ba214cc44c77c08e2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "23843690"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analýza výkonu hraničního uzlu v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Analýza výkonu Edge poskytuje podrobné informace o provozu a využití šířky pásma pro CDN. Tyto informace lze potom použít ke generování trendů statistiky, které vám umožní získat přehled o tom, jak bude vaše prostředky do mezipaměti a doručit vašim klientům. Pak můžete tak formuláři strategie o tom, jak optimalizovat doručování obsahu a určit, co by měly být potíže řešit lepší využívání CDN. V důsledku toho nejen se nebudete moct zlepšit výkon doručování dat, ale také moct snížit náklady na CDN.

> [!NOTE]
> Všechny sestavy použít čas UTC nebo GMT zápis při zadávání data a času.
> 
> 

## <a name="reports-and-log-collection"></a>Sestavy a kolekce protokolu
Data aktivit CDN se sbírají modulem analýzy výkonu Edge předtím, než to může generovat sestavy na něm. Tento proces shromažďování nastane jednou denně a popisuje aktivity, ke kterým došlo za předchozí den. To znamená, které sestavy statistik představují ukázku statistik den v době zpracování a není nutně obsahovat kompletní sadu dat pro aktuální den. Primární funkce tyto sestavy je k vyhodnocení výkonu. Není vhodné používat pro účely fakturace nebo přesné číselné statistiky.

> [!NOTE]
> Nezpracovaná data, ze které se generují Edge výkonu analytické sestavy je k dispozici alespoň 90 dní.
> 
> 

## <a name="dashboard"></a>Řídicí panel
Řídicí panel analýzy výkonu Edge sleduje aktuálním a historickém přenos CDN prostřednictvím grafu a statistiky. Tento řídicí panel použijte k detekci poslední a dlouhodobé trendů na výkon CDN provoz pro váš účet.

Tento řídicí panel se skládá z:

* Interaktivní graf, který umožňuje vizualizaci klíčové metriky a trendů.
* Časová osa poskytuje představu o dlouhodobé vzory pro klíčové metriky a trendy.
* Klíčové metriky a statistické informace o tom, zlepšuje naše síť CDN webový provoz měřený podle celkového výkonu, využití a efektivitu.

### <a name="accessing-the-edge-performance-dashboard"></a>Přístup k řídicím panelu výkonu edge
1. Okno profil CDN, klikněte **spravovat** tlačítko.
   
    ![Tlačítko Spravovat okno profil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** a potom přejděte myší **Edge prováděcí Analytics** plovoucím panelem.  Klikněte na **řídicí panel**.
   
    Zobrazí se řídicí panel analytics uzlu edge.

### <a name="chart"></a>Graf
Řídicí panel obsahuje graf, který sleduje metriky v období vybrána na časové ose, který se zobrazí pod ním.  Termín grafy až na poslední dva roky CDN aktivity se zobrazí přímo pod grafem.

#### <a name="using-the-chart"></a>Pomocí graf
* Ve výchozím nastavení bude grafu zobrazena rychlost efektivitu mezipaměti za posledních 30 dní.
* Tento graf se generují z dat kompletován každý den.
* Ukazatele myši na den na spojnicový graf označí datum a hodnotu metriky na datum.
* Kliknutím zvýrazněte víkendů k přepnutí překrytí světla šedé svislých pruhů, které představují víkendů do grafu. Tento typ překrytí je užitečné pro identifikaci vzory přenosů dat přes víkendech.
* Klikněte na tlačítko zobrazit jeden rok před k přepnutí překrytí aktivity předchozího roku v stejné období do grafu. Tento typ porovnání poskytuje přehled o způsobech dlouhodobé používání CDN. Pravém dolním rohu grafu obsahuje určující kód barvu pro každý řádek grafu legendu.

#### <a name="updating-the-chart"></a>Aktualizace grafu
* Časové rozmezí: Proveďte jeden z následujících akcí:
  * Vyberte požadovanou oblast v časové ose. Graf bude aktualizována data, která odpovídá vybrané časové období.
  * Dvakrát klikněte na graf pro zobrazení všech dostupných historických dat až do maximálního počtu dva roky.
* Metrika: Klikněte na ikonu graf, který se zobrazí vedle požadované metriku. Data pro odpovídající metrika bude aktualizován graf a časovou osu.

### <a name="key-metrics-and-statistics"></a>Klíčové metriky a statistiky
#### <a name="efficiency-metrics"></a>Efektivita metriky
Účelem tyto metriky je chcete zobrazit, zda je možné zlepšit efektivitu mezipaměti. Hlavní výhody odvozené od efektivitu mezipaměti:

* Zdrojový server, což může vést ke snížení zatížení:
  * Lepší výkon webového serveru.
  * Snižuje provozní náklady.
* Vylepšené akcelerace doručování dat, vzhledem k tomu, že další žádosti se zpracuje přímo z CDN.

| Pole | Popis |
| --- | --- |
| Efektivitu mezipaměti |Informuje o procentu přenesených dat, který byl zpracoval z mezipaměti. Zpracování této metriky míry při uložené v mezipaměti verzi požadovaného obsahu přímo z CDN (servery edge) pro žadatele (např. webový prohlížeč) |
| Rychlost přístupů do |Informuje o procentu požadavků, které se spouští z mezipaměti. Tento metriky míry, když v mezipaměti verzi požadovaný obsah zpracování přímo z CDN (servery edge) pro žadatele (např. webový prohlížeč). |
| % Vzdálené bajtů – bez konfigurace mezipaměti |Informuje o procentu provoz, který zpracování z počátku serverů k CDN (servery edge), která nebudou zapisována do mezipaměti v důsledku funkci vynechat mezipaměti (HTTP pravidla stroj). |
| % Vzdálené bajtů – platnost mezipaměti |Informuje o procentu provoz, který zpracování z počátku serverů k CDN (hraniční servery) v důsledku zastaralé opětovné ověření obsahu. |

#### <a name="usage-metrics"></a>Metriky využití
Účelem tyto metriky je poskytnout přehled o následující náklady vyjímání míry:

* Minimalizace provozní náklady prostřednictvím CDN.
* Snižuje výdaje CDN prostřednictvím efektivitu mezipaměti a komprese.

> [!NOTE]
> Provoz svazku čísla představují provoz, který byl použit ve výpočtech poměr a procent a může zobrazit pouze část celkového provozu pro vysoký počet zákazníků.
> 
> 

| Pole | Popis |
| --- | --- |
| Odeslané bajty průměr |Označuje průměrný počet bajtů přenesených pro každý požadavek zpracovat od CDN (servery edge) žadateli (např. webový prohlížeč). |
| Žádné rychlost bajtů konfigurace mezipaměti |Informuje o procentu přenosu zpracovaných od CDN (servery edge) žadateli (např. webový prohlížeč), která nebudou zapisována do mezipaměti z důvodu funkci vynechat mezipaměti. |
| Míra komprimované bajtů |Informuje o procentu data odesílaná do žadatelů o (např. webový prohlížeč) od CDN (hraniční servery) v komprimovaném formátu. |
| Odeslané bajty |Určuje množství dat v bajtech, které byly dodány od CDN (servery edge) žadateli (např. webový prohlížeč). |
| Bajtů |Určuje množství dat v bajtech odeslaný žadatelů o (např. webový prohlížeč) k CDN (servery edge). |
| Vzdálené bajtů |Určuje množství dat, bajtů, odeslaných ze serverů původu CDN a zákazník CDN (servery edge). |

#### <a name="performance-metrics"></a>Metriky výkonu
Účelem tyto metriky je sledovat celkový výkon CDN pro provozu.

| Pole | Popis |
| --- | --- |
| Rychlost přenosu |Určuje průměrnou rychlost, kdy byl přenést obsah z CDN pro žadatele. |
| Doba trvání |Určuje průměrnou dobu (v milisekundách) trvalo doručit prostředek žadatel (např. webový prohlížeč). |
| Rychlost komprimované požadavků |Informuje o procentu přístupů, které byly dodány od CDN (servery edge) žadateli (např. webový prohlížeč) v komprimovaném formátu. |
| Míra chyb 4xx |Informuje o procentu přístupů, které generuje stavový kód 4xx. |
| Míra chyb 5xx |Informuje o procentu přístupů, které generuje 5xx stavový kód. |
| Přístupy |Určuje počet požadavků na obsahu CDN. |

#### <a name="secure-traffic-metrics"></a>Zabezpečený provoz metriky
Účelem tyto metriky je sledovat výkon CDN pro komunikaci přes protokol HTTPS.

| Pole | Popis |
| --- | --- |
| Zabezpečení efektivitu mezipaměti |Informuje o procentu data přenesená pro požadavky HTTPS, které byly obsluhovat z mezipaměti. Tento metriky míry, když v mezipaměti verzi požadovaný obsah byl zpracovat přímo z CDN (servery edge) žadatelů o (např. webový prohlížeč) přes protokol HTTPS. |
| Zabezpečení rychlost přenosu |Určuje průměrnou rychlost, kdy byl přenést obsah z CDN (servery edge) na žadatelů o (například webové servery) přes protokol HTTPS. |
| Průměrná doba trvání zabezpečení |Určuje průměrnou dobu (v milisekundách) trvalo doručit prostředek žadatel (např. webový prohlížeč) přes protokol HTTPS. |
| Zabezpečené přístupů |Určuje počet požadavků protokolu HTTPS pro obsahu CDN. |
| Zabezpečení odeslané bajty |Určuje množství komunikaci přes protokol HTTPS, v bajtech, které byly dodány od CDN (servery edge) žadateli (např. webový prohlížeč). |

## <a name="reports"></a>Reports
Každou sestavu v tomhle module obsahuje graf a statistiku využití šířky pásma a přenosy dat pro různé typy metriky (např. stavové kódy HTTP, mezipaměti stavových kódů, požadavek adresy URL, atd.). Tyto informace lze pustíte hlubší do jak se zpracování obsahu pro klienty a optimalizovat výkon doručování dat CDN chování.

### <a name="accessing-the-edge-performance-reports"></a>Přístup k okraj sestavy pro zvýšení výkonu
1. Okno profil CDN, klikněte **spravovat** tlačítko.
   
    ![Tlačítko Spravovat okno profil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** a potom přejděte myší **Edge prováděcí Analytics** plovoucím panelem.  Klikněte na **velkého objektu HTTP**.
   
    Hraniční uzel analytics sestavy obrazovka se zobrazí.

| Sestava | Popis |
| --- | --- |
| Denní souhrn |Umožňuje zobrazit každodenní provoz trendy za zadané časové období. Každý panelu na tomto grafu představuje konkrétní datum. Velikost panelu určuje celkový počet přístupů, které došlo k tomuto datu. |
| Hodinové souhrn |Umožňuje zobrazit každou hodinu provoz trendy za zadané časové období. Každý pruh na tomto grafu představuje jednu hodinu v konkrétní datum. Velikost panelu určuje celkový počet přístupů, které došlo k chybě danou dobu. |
| Protokoly |Zobrazí výčet provozu mezi protokoly HTTP a HTTPS. Graf prstenec informuje o procentu přístupů, které pro každý typ protokolu došlo k chybě. |
| Metody HTTP |Umožňuje získáte rychlý přehled HTTP, které metody jsou používány k žádosti o data. Obvykle jsou nejběžnější metody požadavek HTTP GET, POST a HEAD. Graf prstenec informuje o procentu přístupů, které pro každý typ metoda požadavku HTTP došlo k chybě. |
| URLs |Obsahuje graf zobrazující hlavní 10 požadované adresy URL. Pro každou adresu URL se zobrazí panelu. Výška panelu určuje, kolik přístupů generovaných konkrétní adresu URL přes časové rozpětí předmětem sestavy. Statistika pro prvních 100 požadovaného že adresy URL se zobrazí pod tohoto grafu. |
| Záznamů CNAME |Obsahuje graf zobrazující top 10 záznamů CNAME, které slouží k vyžádání prostředky přes časový rozsah sestavy. Statistika pro prvních 100 požadovaného že záznamů CNAME se zobrazí pod tohoto grafu. |
| Počátky |Obsahuje graf zobrazující hlavní 10 CDN nebo zákazníků původu servery, ze kterých se požadovaly prostředky v zadaném časovém období. Statistika pro prvních 100 požadovaného přímo níže tohoto grafu se zobrazují servery původu CDN nebo zákazníků. Zákazník počátek servery se identifikují podle názvu definované v možnosti název adresáře. |
| Geograficky bodů POP |Zobrazuje, kolik provozu je směrovány přes konkrétní point of presence (POP). Zkratka tří písmen představuje POP v naše síť CDN. |
| Klienti |Obsahuje graf zobrazující hlavní 10 klientů, kteří požadované prostředky v zadaném časovém období. Pro účely této sestavy jsou považovány za všechny požadavky, které pocházejí z stejnou IP adresu jako ze stejného klienta. Statistika pro horní 100 klientů se zobrazí pod tohoto grafu. Tato sestava je užitečné pro určení aktivity vzory stahování pro horní klienty. |
| Stavy mezipaměti |Poskytuje podrobné rozpis chování mezipaměti, který může odhalit přístupy pro zlepšení celkové činnost koncového uživatele. Vzhledem k tomu, že nejrychlejší výkon pochází z přístupů k mezipaměti, můžete optimalizovat rychlosti doručování dat minimalizovat Neúspěšné přístupy do mezipaměti a přístupů k mezipaměti vypršela platnost. |
| ŽÁDNÉ podrobnosti |Obsahuje graf zobrazující hlavní 10 adresy URL pro prostředky, pro které nebyla zkontrolována aktuálnost obsahu mezipaměti po zadanou dobu. Statistiky pro horní 100 adresy URL pro tyto typy prostředků se zobrazí pod tohoto grafu. |
| Podrobnosti o CONFIG_NOCACHE |Obsahuje graf, který zobrazí prvních 10 adresy URL pro prostředky, které nebyly uloženy v mezipaměti z důvodu konfigurace CDN zákazníka. Tyto typy prostředků byly zpracovat přímo ze zdrojového serveru. Statistiky pro horní 100 adresy URL pro tyto typy prostředků se zobrazí pod tohoto grafu. |
| UNCACHEABLE podrobnosti |Obsahuje graf zobrazující hlavní 10 adresy URL pro prostředky, které nelze uložit do mezipaměti z důvodu data hlavičky žádosti. Statistiky pro horní 100 adresy URL pro tyto typy prostředků se zobrazí pod tohoto grafu. |
| Podrobnosti o TCP_HIT |Obsahuje graf zobrazující hlavní 10 adresy URL pro prostředky, které se zpracovávají okamžitě z mezipaměti. Statistiky pro horní 100 adresy URL pro tyto typy prostředků se zobrazí pod tohoto grafu. |
| Podrobnosti o TCP_MISS |Obsahuje graf zobrazující hlavní 10 adresy URL pro prostředky, které mají stav mezipaměti TCP_MISS. Statistiky pro horní 100 adresy URL pro tyto typy prostředků se zobrazí pod tohoto grafu. |
| Podrobnosti o TCP_EXPIRED_HIT |Obsahuje graf zobrazující hlavní 10 adresy URL pro zastaralé prostředky, které byly obsluhovat přímo ze serveru POP. Statistiky pro horní 100 adresy URL pro tyto typy prostředků se zobrazí pod tohoto grafu. |
| Podrobnosti o TCP_EXPIRED_MISS |Obsahuje graf zobrazující hlavní 10 adresy URL pro zastaralé prostředků, pro které musel být načtena ze zdrojového serveru na novou verzi. Statistiky pro horní 100 adresy URL pro tyto typy prostředků se zobrazí pod tohoto grafu. |
| Podrobnosti o TCP_CLIENT_REFRESH_MISS |Obsahuje pruhový graf, který zobrazí prvních 10 adresy URL pro prostředky, které byly získány ze zdrojový server z důvodu ne mezipaměti požadavek od klienta. Přímo níže tento graf se zobrazují statistiky pro horní 100 adresy URL pro tyto typy požadavků. |
| Typy žádostí klienta |Označuje typ požadavků, které byly provedeny podle klientů protokolu HTTP (například prohlížeče). Tato sestava obsahuje prstenec graf, který poskytuje představu o tom, jak dochází ke zpracování požadavků. Informace o šířku pásma a provozu pro každý typ žádosti se zobrazí pod grafem. |
| Identifikační řetězec |Obsahuje zobrazení top 10 Uživatelští agenti pro vyžádání obsahu prostřednictvím našich CDN pruhový graf. Uživatelský agent je obvykle webový prohlížeč, přehrávač médií nebo prohlížeče mobilního telefonu. Statistika pro horní 100 Uživatelští agenti se zobrazí pod tento graf. |
| Odkazující servery |Obsahuje zobrazení top 10 odkazující servery k obsahu přistupovat prostřednictvím našich CDN pruhový graf. Obvykle odkazující server je adresa URL webové stránky nebo prostředek, který obsahuje odkazy na obsah. Podrobné informace najdete níže grafu pro odkazujících prvních 100 serverů. |
| Typy komprese |Obsahuje prstenec grafu, jež rozdělí požadované prostředky podle jestli byly komprimované servery edge. Procento komprimované prostředky je členěné podle typ komprese použít. Podrobné informace najdete níže grafu pro každý typ komprese a stav. |
| Typy souborů |Obsahuje pruhový graf zobrazující hlavní typy 10 souborů, které bylo vyzváno prostřednictvím našich CDN pro váš účet. Pro účely této sestavy je definován typ souboru příponu názvu souboru assetu a typ média Internetu (například .html \[text/html\], .htm \[text/html\], .aspx \[text/html\]atd.). Podrobné informace najdete níže grafu pro hlavní typy 100 souborů. |
| Jedinečný soubory |Obsahuje graf, který ukazuje zeměpisný celkový počet jedinečných prostředky, které si vyžádali v určitý den v zadaném časovém období. |
| Souhrn tokenů ověřování |Obsahuje výsečový graf poskytující rychlý přehled o tom, zda byly požadovaných prostředků chráněny pomocí ověřování na základě tokenu. Chráněné prostředky se zobrazí v grafu podle výsledků pokusu o ověření. |
| Token Auth odepřít podrobnosti |Obsahuje pruhový graf, který vám umožní zobrazit top 10 požadavků, které byly odepřen v důsledku ověřování na základě tokenu. |
| Kódy odpovědí HTTP |Obsahuje rozpis systémů stavové kódy HTTP (například 200 OK 403 Zakázáno, 404 nebyl nalezen, apod.), byly doručeny do vaší klientů protokolu HTTP servery edge. Výsečový graf umožňuje rychle posoudit, jak byly obsluhovat vaše prostředky. Podrobné statistické údaje se poskytuje pro každý kód odpovědi pod grafem. |
| Chyb 404 |Obsahuje pruhový graf, který vám umožní zobrazit top 10 požadavků, které 404 kód odpovědi nebyl nalezen. |
| Chyby 403 |Obsahuje pruhový graf, který vám umožní zobrazit top 10 požadavků, které 403 kód odpovědi zakázáno. Kód odpovědi 403 Zakázáno nastane, když požadavek je odepřen zdrojový server zákazníka nebo hraniční server na našem POP. |
| 4xx chyby |Obsahuje pruhový graf, který vám umožní zobrazit top 10 požadavků, které kód odpovědi od 400. Vyloučeny z této sestavy jsou 403 nebyl nalezen a kódů odpovědi 404 zakázáno. Kód odpovědi 4xx obvykle nastane, když požadavek byl odepřen v důsledku chyby klienta. |
| 504 chyby |Obsahuje pruhový graf, který vám umožní zobrazit top 10 požadavků, které 504 kód odpovědi limitu brány. 504 kód odpovědi vypršel časový limit brány nastane, když dojde k vypršení časového limitu, pokud server proxy protokolu HTTP se pokouší komunikovat s jiným serverem. V případě naší CDN 504 kód odpovědi vypršel časový limit brány obvykle dochází, když nelze navázat komunikaci s původním serveru zákazníka hraniční server. |
| Chyby 502 |Obsahuje pruhový graf, který vám umožní zobrazit top 10 požadavků, které 502 kód odpovědi Chybná brána. Kód odpovědi 502 Chybná brána nastane, když dojde k chybě protokolu HTTP mezi serverem a proxy serveru HTTP. V případě naší CDN 502 kód odpovědi Chybná brána obvykle dojde v případě neplatnou odpověď na hraniční server vrátí zdrojový server zákazníka. Odpověď je neplatný, pokud nelze analyzovat nebo pokud je neúplný. |
| Chyby 5xx |Obsahuje pruhový graf, který vám umožní zobrazit top 10 požadavků, které kód odpovědi v rozsahu 500.  Vyloučeny z této sestavy jsou 502 Chybná brána a kódů odpovědi 504 limitu brány. |

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled Azure CDN](cdn-overview.md)
* [Statistiky v reálném čase v Microsoft Azure CDN](cdn-real-time-stats.md)
* [Přepsání výchozího nastavení HTTP používá stroj pravidel](cdn-rules-engine.md)
* [Sestavy pokročilé HTTP](cdn-advanced-http-reports.md)

