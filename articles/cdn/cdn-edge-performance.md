---
title: Analýza výkonu hraničních uzlů v Azure CDN | Dokumentace Microsoftu
description: Analýza výkonu hraničního uzlu v Microsoft Azure CDN. Analýza výkonu hranice poskytuje podrobné informace o provozu a využití šířky pásma pro CDN.
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
ms.openlocfilehash: 7dfa252c29121adca2ecc77c08b2fca81d56e575
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320069"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analýza výkonu hraničního uzlu v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Analýza výkonu hranice poskytuje podrobné informace o provozu a využití šířky pásma pro CDN. Tyto informace lze potom použít ke generování statistik trendů. navíc, které vám umožní získat přehled o tom, jak se vaše assety ukládají do mezipaměti a doručují klientům. Pak díky tomu můžete k vytvoření strategii a o tom, jak optimalizovat doručování vašeho obsahu a určit, co by měl být problémy řešeny lepší využití CDN. V důsledku toho nejen bude je možné ke zlepšení výkonu při doručování dat, ale také moct snížit náklady na síť CDN.

> [!NOTE]
> Všechny sestavy používají UTC/GMT notation při zadávání datum/čas.
> 
> 

## <a name="reports-and-log-collection"></a>Sestavy a shromažďování protokolů
Data aktivit CDN je třeba shromažďovat modulem Analýza výkonu hranice předtím, než ho můžete generovat sestavy na něj. Tento proces kolekce proběhne jednou denně a popisuje aktivity, ke kterým došlo během předchozího dne. To znamená, že představujících statistiky sestavy ukázka denních statistik v době zpracování a nejsou nutně obsahovat úplnou sadu dat pro aktuální den. Primární funkce tyto sestavy je k vyhodnocení výkonu. Používají by neměla pro účely fakturace nebo přesnou číselné statistiky.

> [!NOTE]
> Nezpracovaná data, ze které se generují Edge výkonu analytické sestavy je k dispozici po dobu minimálně 90 dnů.
> 
> 

## <a name="dashboard"></a>Řídicí panel
Řídicí panel Analýza výkonu hranice sleduje aktuální a historický CDN provoz prostřednictvím grafu a statistiky. Pomocí tohoto řídicího panelu ke zjištění poslední a dlouhodobé trendy na výkon CDN provoz pro váš účet.

Tento řídicí panel se skládá ze:

* Interaktivní graf, který umožňuje vizualizace klíčových metrik a trendy.
* Časová osa, který poskytuje představu o dlouhodobém horizontu vzory pro klíčové metriky a trendy.
* Klíčové metriky a statistické informace o naší síti CDN zlepšuje provoz lokality měřený podle celkový výkon, využití a efektivitu.

### <a name="accessing-the-edge-performance-dashboard"></a>Přístup k řídicím panelu edge výkonu
1. Z okna profil CDN, klikněte na tlačítko **spravovat** tlačítko.
   
    ![Tlačítko Spravovat okno profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** kartu a pak najeďte myší **Analýza výkonu hranice** Kontextová nabídka.  Klikněte na **řídicí panel**.
   
    Zobrazí se řídicí panel analýza uzlu edge.

### <a name="chart"></a>Graf
Řídicí panel obsahuje graf, který sleduje metriky za časové období na časové ose, které se zobrazí přímo pod něj vybrána.  Časové osy, grafy až na poslední dva roky CDN aktivity se zobrazí přímo pod grafem.

#### <a name="using-the-chart"></a>Pomocí grafu
* Ve výchozím nastavení budou zobrazena frekvence efektivitu mezipaměti za posledních 30 dní.
* Tento graf se vygeneruje z dat porovnávány každý den.
* Najetí myší za den na spojnicový graf označí data a hodnota metriky k tomuto datu.
* Kliknutím zvýrazněte víkendy, chcete-li přepnout překrytí světle šedá svislé pruhy, které představují víkendy do grafu. Tento typ překrytí je užitečné pro identifikaci vzorů provozu přes víkendy.
* Klikněte na tlačítko zobrazit jeden rok před přepnete překrytí předchozího roku aktivity za stejné období do grafu. Tento typ porovnání, nabízí pohled na dlouhodobé vzorce využití CDN. Pravém dolním rohu grafu obsahuje legendu, která určuje kód barvy pro každý spojnicový graf.

#### <a name="updating-the-chart"></a>Aktualizace grafu
* Časový rozsah: Proveďte jeden z následujících akcí:
  * Vyberte požadovanou oblast na časové ose. Grafu se aktualizují data, která odpovídá vybrané časové období.
  * Klikněte dvakrát na grafu pro zobrazení všech dostupných historických dat až do maximálního počtu dva roky.
* Metrika: Klikněte na ikonu, která se zobrazí vedle požadovanou metriku. S daty odpovídající metriky se budou aktualizovat grafu a na časové ose.

### <a name="key-metrics-and-statistics"></a>Klíčové metriky a statistiky
#### <a name="efficiency-metrics"></a>Efektivita metriky
Účelem těchto metrikách je chcete zobrazit, zda se může zlepšit efektivitu mezipaměti. Hlavní výhody odvozený od efektivitu mezipaměti jsou:

* Na původním serveru, což může vést ke snížení zatížení:
  * Lepší výkon webového serveru.
  * Snížit provozní náklady.
* Zrychlení doručování dat lepší, protože žádosti více bude obsluhovat přímo z CDN.

| Pole | Popis |
| --- | --- |
| Efektivitu mezipaměti |Informuje o procentu přenesená data, která byla obsluhovat z mezipaměti. Zpracování této metriky opatření, pokud verze uložené v mezipaměti požadovaného obsahu přímo z CDN (hraniční servery) na žadatelů o deklaraci (například webový prohlížeč) |
| Počet přístupů |Informuje o procentu požadavků, které se obsluhují z mezipaměti. Tato metrika opatření při verze uložené v mezipaměti požadovaného obsahu vyřídila přímo z CDN (hraniční servery) na žadatelů o deklaraci (například webový prohlížeč). |
| % bajtů vzdálené - žádné konfigurace mezipaměti |Informuje o procentu přenosů, které se obsluhují z původních serverů do sítě CDN (hraniční servery), která nebudou zapisována do mezipaměti jako výsledek funkce paměť Cache můžete přeskočit (stroj pravidel HTTP). |
| % Vzdálené bajtů - vypršení platnosti mezipaměti |Informuje o procentu přenosů, které se obsluhují z původních serverů do sítě CDN (hraniční servery) v důsledku zastaralé opětovné ověření obsahu. |

#### <a name="usage-metrics"></a>Metriky využití
Účelem těchto metrikách je k poskytování přehledů o následující míry společné náklady:

* Minimalizovat provozní náklady prostřednictvím CDN.
* Snižuje výdaje CDN prostřednictvím efektivitu mezipaměti a provádí kompresi.

> [!NOTE]
> Čísla multilicenčních provoz představují provoz, který byl použit ve výpočtech poměry a procenta a může zobrazit jenom část z celkového provozu pro vysoký počet zákazníků.
> 
> 

| Pole | Popis |
| --- | --- |
| Odchozí bajty uložit |Označuje průměrný počet bajtů přenesených pro každý požadavek poskytnutá z CDN (hraniční servery) žadateli (například webový prohlížeč). |
| Frekvence Config bajt bez mezipaměti |Informuje o procentu provoz poskytnutá z CDN (hraniční servery) žadateli (například webový prohlížeč), která nebudou zapisována do mezipaměti kvůli funkci paměť Cache můžete přeskočit. |
| Míra komprimované bajtů |Informuje o procentu provoz odeslaný na žadatelů o deklaraci (například webový prohlížeč) z CDN (hraniční servery) ve formátu komprimované. |
| Odchozí bajty |Určuje množství dat v bajtech, které byly dodány z CDN (hraniční servery) žadateli (například webový prohlížeč). |
| Bajty |Určuje množství dat v bajtech odeslaných z žadatelů o deklaraci (například webový prohlížeč) do sítě CDN (hraniční servery). |
| Vzdálené bajtů |Určuje množství dat v bajtech odeslaných ze sítě CDN a zákazníka původních serverů do CDN (hraniční servery). |

#### <a name="performance-metrics"></a>Metriky výkonu
Účelem těchto metrikách je sledovat celkový výkon CDN pro provoz.

| Pole | Popis |
| --- | --- |
| Přenosová rychlost |Určuje průměrnou rychlost, jakou bylo na žadatele obsah převedeno z CDN. |
| Doba trvání |Označuje, Průměrná doba v milisekundách, jakou trvalo pro doručení assetu do žadateli (například webový prohlížeč). |
| Komprimované žádostí |Informuje o procentu přístupů, které byly dodány z CDN (hraniční servery) žadateli (například webový prohlížeč) ve formátu komprimované. |
| Míra chyb 4xx |Informuje o procentu přístupů, které generují stavový kód 4xx. |
| Míra chyb 5xx |Informuje o procentu přístupů, které generují stavový kód 5xx. |
| Přístupy |Označuje počet žádostí o obsahu CDN. |

#### <a name="secure-traffic-metrics"></a>Zabezpečení provozu metriky
Účelem těchto metrikách je sledovat výkon sítě CDN pro komunikaci přes protokol HTTPS.

| Pole | Popis |
| --- | --- |
| Zabezpečení efektivitu mezipaměti |Informuje o procentu data přenesená požadavkům HTTPS, které byly obsluhovat z mezipaměti. Tato metrika opatření při verze uložené v mezipaměti požadovaného obsahu vyřídila přímo z CDN (hraniční servery) na žadatelů o deklaraci (například webový prohlížeč) přes protokol HTTPS. |
| Zabezpečené přenosová rychlost |Určuje průměrnou rychlost, jakou bylo obsah převedeno z CDN (hraniční servery) žadatelů o deklaraci (třeba webové servery) přes protokol HTTPS. |
| Průměrná doba trvání zabezpečené |Označuje, Průměrná doba v milisekundách, jakou trvalo pro doručení assetu do žadateli (například webový prohlížeč) přes protokol HTTPS. |
| Zabezpečené přístupů |Označuje počet požadavky HTTPS k obsahu CDN. |
| Zabezpečit odchozí bajty |Určuje objem provozu HTTPS v bajtech, které byly dodány z CDN (hraniční servery) žadateli (například webový prohlížeč). |

## <a name="reports"></a>Reports
Každá sestava v tomto modulu obsahuje graf a statistiky o využití šířky pásma a provoz pro různé typy metriky (například stavové kódy HTTP, mezipaměť stavové kódy, žádost o adresu URL, atd.). Tyto informace slouží delve hlouběji do jak se obsah obsluhuje klienty a optimalizovat chování CDN ke zlepšení výkonu při doručování dat.

### <a name="accessing-the-edge-performance-reports"></a>Přístup k hraniční sestavy výkonu
1. Z okna profil CDN, klikněte na tlačítko **spravovat** tlačítko.
   
    ![Tlačítko Spravovat okno profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** kartu a pak najeďte myší **Analýza výkonu hranice** Kontextová nabídka.  Klikněte na **velkého objektu HTTP**.
   
    Zobrazí se obrazovka hraniční uzel analytics sestavy.

| Sestava | Popis |
| --- | --- |
| Denní souhrn |Umožňuje zobrazit trendy každodenní provoz za zadané časové období. Každý řádek v tomto grafu představuje konkrétní datum. Velikost panelu indikuje celkový počet přístupů, ke kterým došlo k tomuto datu. |
| Hodinový přehled |Umožňuje zobrazit trendy provozu po hodinách za zadané časové období. Každý řádek v tomto grafu představuje jednu hodinu v konkrétní den. Velikost panelu indikuje celkový počet přístupů, ke kterým došlo během dané hodiny. |
| Protokoly |Zobrazí rozpis provoz mezi protokoly HTTP a HTTPS. Prstencový graf informuje o procentu úspěšných, ke kterým došlo u každého typu protokolu. |
| Metody HTTP |Umožňuje získat rychlý přehled o tom HTTP, které metody se používají k vyžádání vaše data. Obvykle jsou nejběžnější metody požadavku HTTP GET, HEAD a příspěvku. Prstencový graf informuje o procentu úspěšných, ke kterým došlo u každého typu metoda požadavku HTTP. |
| Adresy URL |Obsahuje graf zobrazující hlavní 10 požadované adresy URL. Panelu se zobrazí pro každou adresu URL. Výška pruhu určuje počet přístupů, generovaných určité adresy URL za časové období, kterou sestava postihuje. Statistika pro 100 nejoblíbenějších požadovaného že adresy URL se zobrazí přímo pod tohoto grafu. |
| CNAMEs |Obsahuje graf zobrazující top 10 záznamů CNAME používaná pro požádání o prostředky čase span sestavy. Statistika pro 100 nejoblíbenějších požadovaného že záznamy CNAME se zobrazí přímo pod tohoto grafu. |
| Počátky |Obsahuje graf zobrazující hlavní 10 CDN nebo zákazník původních serverů, ze kterých byly požadované prostředky v zadaném časovém období. Statistika pro 100 nejoblíbenějších požadovaného přímo pod tento graf se zobrazují servery původu CDN nebo zákazníků. Zákazník původních serverů jsou identifikovány názvem definovaným ve variantě pro název adresáře. |
| Geograficky POP |Ukazuje, jak velká část provozu je směrovány přes určitého bodu of-presence (POP). Dvoupísmennou zkratku představuje místní nabídky v naší síti CDN. |
| Klienti |Obsahuje graf zobrazující hlavní 10 klientů, které požadované prostředky v zadaném časovém období. Pro účely této sestavy se všechny požadavky, které pocházejí ze stejné IP adresy jsou považovány za ze stejného klienta. Přímo pod tento graf se zobrazují statistiky pro začátek 100 klientů. Tato sestava je užitečná pro určení vzory aktivity ke stažení pro začátek klienty. |
| Stavy mezipaměti |Je podrobný rozpis chování mezipaměti, což může vést k odhalení přístupy k zlepšení celkové prostředí koncového uživatele. Vzhledem k tomu, abyste dosáhli nejlepšího výkonu pochází z přístupů k mezipaměti, můžete optimalizovat doručování rychlosti dat tak, že minimalizovat Neúspěšné přístupy do mezipaměti a přístupů k mezipaměti vypršela platnost. |
| ŽÁDNÉ podrobnosti |Obsahuje graf zobrazující hlavní 10 adresy URL pro prostředky, u kterých nebyla zkontrolována aktuálnosti obsahu mezipaměti po zadanou dobu. Přímo pod tento graf se zobrazují statistiky pro hlavní 100 adresy URL pro tyto typy prostředků. |
| Podrobnosti o CONFIG_NOCACHE |Obsahuje graf, který zobrazuje prvních 10 adres URL pro prostředky, které nebyly uloženy v mezipaměti z důvodu konfigurace CDN zákazníka. Tyto typy prostředků nebyly obsluhovat přímo server původu. Přímo pod tento graf se zobrazují statistiky pro hlavní 100 adresy URL pro tyto typy prostředků. |
| UNCACHEABLE podrobnosti |Obsahuje graf zobrazující hlavní 10 adresy URL pro prostředky, které nelze uložit do mezipaměti kvůli data hlavičky žádosti. Přímo pod tento graf se zobrazují statistiky pro hlavní 100 adresy URL pro tyto typy prostředků. |
| Podrobnosti o TCP_HIT |Obsahuje graf zobrazující hlavní 10 adresy URL pro prostředky, které jsou okamžitě obsluhovat z mezipaměti. Přímo pod tento graf se zobrazují statistiky pro hlavní 100 adresy URL pro tyto typy prostředků. |
| Podrobnosti o TCP_MISS |Obsahuje graf zobrazující hlavní 10 adresy URL pro prostředky, které mají stav mezipaměti TCP_MISS. Přímo pod tento graf se zobrazují statistiky pro hlavní 100 adresy URL pro tyto typy prostředků. |
| Podrobnosti o TCP_EXPIRED_HIT |Obsahuje graf zobrazující hlavní 10 adresy URL pro zastaralé prostředky, které byly obsluhovat přímo server POP. Přímo pod tento graf se zobrazují statistiky pro hlavní 100 adresy URL pro tyto typy prostředků. |
| Podrobnosti o TCP_EXPIRED_MISS |Obsahuje graf zobrazující hlavní 10 adresy URL pro zastaralé prostředky, u kterých musel načíst ze zdrojového serveru novou verzi. Přímo pod tento graf se zobrazují statistiky pro hlavní 100 adresy URL pro tyto typy prostředků. |
| TCP_CLIENT_REFRESH_MISS Details |Obsahuje pruhový graf, který zobrazuje prvních 10 adres URL pro prostředky, které byly získány ze zdrojového serveru z důvodu no-cache žádosti z klienta. Přímo pod tento graf se zobrazují statistiky pro hlavní 100 adresy URL pro tyto typy požadavků. |
| Typy žádostí klienta |Určuje typy požadavků, které byly provedeny klienty HTTP (například prohlížeče). Tato sestava obsahuje prstencový graf, který poskytuje představu, jak se právě zpracovává požadavky. Informace o šířku pásma a provoz pro každý typ žádosti se zobrazí pod grafem. |
| Identifikační řetězec |Obsahuje pruhový graf zobrazující hlavní 10 Uživatelští agenti pro vyžádání obsahu prostřednictvím naší síti CDN. Uživatelský agent je obvykle webový prohlížeč, media player nebo prohlížeči mobilního telefonu. Přímo pod tento graf se zobrazují statistiky pro začátek 100 Uživatelští agenti. |
| Odkazující servery |Obsahuje pruhový graf zobrazující hlavní 10 odkazující servery k obsahu přistupovat prostřednictvím naší síti CDN. Adresa URL webové stránky nebo prostředek, který obsahuje odkazy na váš obsah je obvykle odkazující server. Podrobné informace jsou uvedeny níže grafu pro 100 nejoblíbenějších odkazující servery. |
| Kompresní typy |Obsahuje prstencový graf, který rozděluje požadovaných prostředků tak, zda byly komprimované naše servery edge. Procento komprimované prostředky je rozdělené podle typu použít kompresi. Podrobné informace jsou poskytovány pod grafem pro každý typ komprese a stav. |
| Typy souborů |Obsahuje pruhový graf zobrazující hlavní typy 10 souborů, které bylo vyžádáno prostřednictvím naší síti CDN pro váš účet. Pro účely této sestavy je definován typ souboru příponu názvu souboru assetu a typ média Internetu (například HTML \[text/html\], .htm \[text/html\], aspx \[text/html\]atd.). Podrobné informace jsou uvedeny níže grafu pro hlavní typy 100 souborů. |
| Jedinečné soubory |Obsahuje graf, který znázorňuje celkový počet jedinečných prostředky, které bylo vyžádáno v určitý den v zadaném časovém období. |
| Token Souhrn ověření |Obsahuje výsečový graf, který poskytuje rychlý přehled o Určuje, zda byly požadovaných prostředků chráněny pomocí ověřování založeného na tokenech. Chráněné prostředky jsou zobrazené v grafu podle výsledků neúspěšné pokusy o ověření. |
| Ověření tokenu odepřít podrobnosti |Obsahuje sloupcový graf, který vám umožní zobrazit hlavních 10 požadavků, které byly odmítnut na základě ověřování pomocí tokenu. |
| Kódy odpovědí protokolu HTTP |Obsahuje rozpis systémů stavové kódy HTTP (například 200 OK, 403 Zakázáno, 404 Nenalezeno, atd.), který se doručují klientům protokolu HTTP naše servery edge. Výsečový graf umožňuje rychle posoudit, jak se vaše prostředky služby. Poskytuje podrobné statistická data pro každý z kódů odpovědi pod grafem. |
| Chyby 404 |Obsahuje sloupcový graf, který vám umožní zobrazit hlavních 10 požadavků, z kterých vzniklo 404 kód odpovědi nebyl nalezen. |
| Chyby 403 |Obsahuje sloupcový graf, který vám umožní zobrazit hlavních 10 požadavků, z kterých vzniklo kód odpovědi 403 Zakázáno. Kód odpovědi 403 Zakázáno nastane, pokud zdrojový server zákazníka nebo hraniční server v našem POP odepření požadavku. |
| Chyb 4xx |Obsahuje sloupcový graf, který vám umožní zobrazit hlavních 10 požadavků, z kterých vzniklo kódem odpovědi v rozsahu 400. Vyloučené z této sestavy jsou 403 nebyl nalezen a kódů odpovědi 404 zakázáno. Kód odpovědi 4xx obvykle, dochází, když požadavek byl odepřen v důsledku chyby klienta. |
| 504 chyby |Obsahuje sloupcový graf, který vám umožní zobrazit hlavních 10 požadavků, z kterých vzniklo 504 kód odpovědi vypršel časový limit brány. 504 kód odpovědi vypršel časový limit brány nastane, když dojde k vypršení časového limitu, když proxy server HTTP se pokouší komunikovat s jiným serverem. V případě naší síti CDN 504 kód odpovědi vypršel časový limit brány obvykle dochází, když hraniční server nelze navázat komunikaci se serverem původu zákazníka. |
| 502 chyby |Obsahuje sloupcový graf, který vám umožní zobrazit hlavních 10 požadavků, z kterých vzniklo 502 Chybná brána kód odpovědi. Kód odpovědi 502 Chybná brána nastane, pokud dojde k chybě protokolu HTTP mezi serverem a proxy server HTTP. V případě naší síti CDN 502 Chybná brána kód odpovědi obvykle dochází, když zákazník zdrojový server vrátí neplatnou odpověď na hraniční server. Odpověď je neplatný, pokud ho nelze analyzovat nebo pokud je neúplný. |
| Chyby 5xx |Obsahuje sloupcový graf, který vám umožní zobrazit hlavních 10 požadavků, z kterých vzniklo kódem odpovědi v rozsahu 500.  Vyloučené z této sestavy jsou 502 – Chybná brána a 504 kódy odpovědí vypršel časový limit brány. |

## <a name="see-also"></a>Další informace najdete v tématech
* [Azure CDN – přehled](cdn-overview.md)
* [Statistiky v reálném čase v Microsoft Azure CDN](cdn-real-time-stats.md)
* [Přepisování výchozího chování HTTP pomocí stroje pravidel](cdn-rules-engine.md)
* [Pokročilé sestavy HTTP](cdn-advanced-http-reports.md)

