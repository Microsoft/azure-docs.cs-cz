---
title: Analýza výkonu hraničního uzlu v Azure CDN | Microsoft Docs
description: Analyzujte výkon hraničního uzlu v Microsoft Azure CDN. Analýza výkonu Edge poskytuje podrobné informace o provozu a využití šířky pásma pro CDN.
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
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d8119c56c26ed32db136064d93775d21a71f238f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778663"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analýza výkonu hraničního uzlu v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Analýza výkonu Edge poskytuje podrobné informace o provozu a využití šířky pásma pro CDN. Tyto informace se pak dají použít ke generování statistik trendů, které vám umožní získat přehled o tom, jak se vaše prostředky ukládají do mezipaměti a doručují do vašich klientů. To umožňuje vytvořit strategii pro optimalizaci doručování obsahu a určit, jaké problémy by se měly řešit, aby lépe využili CDN. V důsledku toho nebudete moct zlepšit výkon při doručování dat, ale budete moct taky snížit náklady na síť CDN.

> [!NOTE]
> Všechny sestavy používají zápis UTC/GMT při zadávání data a času.
> 
> 

## <a name="reports-and-log-collection"></a>Sestavy a shromažďování protokolů
Data aktivity CDN musí být shromážděna modulem Edge Performance Analytics předtím, než bude moci generovat sestavy. Tento proces shromažďování dat probíhá jednou denně, který pokrývá aktivity, které se uskutečnily během předchozího dne. To znamená, že Statistika sestavy představuje ukázku statistiky dne v době zpracování a nemusí nutně obsahovat úplnou sadu dat pro aktuální den. Primární funkcí těchto sestav je vyhodnocení výkonu. Neměly by se používat pro účely fakturace nebo přesné číselné statistiky.

> [!NOTE]
> Nezpracovaná data, ze kterých jsou generovány analytické sestavy výkonu, jsou k dispozici alespoň 90 dnů.
> 
> 

## <a name="dashboard"></a>Řídicí panel
Řídicí panel analýzy hraničního výkonu sleduje aktuální a historický provoz CDN prostřednictvím grafu a statistiky. Pomocí tohoto řídicího panelu můžete zjistit nedávné a dlouhodobé trendy výkonu provozu CDN pro váš účet.

Tento řídicí panel se skládá z těchto:

* Interaktivní graf, který umožňuje vizualizaci klíčových metrik a trendů.
* Časová osa, která poskytuje smysl dlouhodobých vzorů klíčových metrik a trendů.
* Klíčové metriky a statistické informace o tom, jak naše síť CDN vylepšuje provoz lokality na základě celkového výkonu, využití a efektivity.

### <a name="accessing-the-edge-performance-dashboard"></a>Přístup k řídicímu panelu výkonu Edge
1. V okně profilu CDN klikněte na tlačítko **Spravovat** .
   
    ![Tlačítko Spravovat okno profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte myší na kartu **Analytics** a pak najeďte myší na informační rámeček **analýzy výkonu Edge** .  Klikněte na **řídicí panel** .
   
    Zobrazí se řídicí panel analýzy hraničního uzlu.

### <a name="chart"></a>Graf
Řídicí panel obsahuje graf, který sleduje metriku za časové období vybrané na časové ose, která se zobrazuje přímo pod ní.  Časová osa, ve které se grafy doplní na poslední dva roky aktivity CDN, se zobrazí přímo pod grafem.

#### <a name="using-the-chart"></a>Používání grafu
* Ve výchozím nastavení bude v grafu sazba efektivity mezipaměti za posledních 30 dní.
* Tento graf je vygenerován z dat, která jsou seřazena každý den.
* Při najetí myší na spojnicový graf bude v daném dni uvedeno datum a hodnota metriky.
* Kliknutím na zvýraznit víkendy přepnete překryvy lehkých šedých svislých pruhů, které reprezentují víkendy na graf. Tento typ překrytí je vhodný pro identifikaci vzorců přenosů přes víkendy.
* Kliknutím na zobrazit před jedním rokem přepnete překrytí aktivity předchozího roku v rámci stejného časového období do grafu. Tento typ porovnání nabízí přehled o dlouhodobém způsobu použití CDN. Pravý horní roh grafu obsahuje legendu, která označuje barevný kód pro každý spojnicový graf.

#### <a name="updating-the-chart"></a>Aktualizace grafu
* Časový rozsah: proveďte jednu z následujících akcí:
  * Vyberte požadovanou oblast na časové ose. Graf se aktualizuje daty, která odpovídají vybranému časovému období.
  * Dvojitým kliknutím na graf zobrazíte všechna dostupná historická data až do maximálního počtu dvou let.
* Metrika: klikněte na ikonu grafu, která se zobrazí vedle požadované metriky. Graf a časová osa se aktualizují daty odpovídající metriky.

### <a name="key-metrics-and-statistics"></a>Klíčové metriky a statistiky
#### <a name="efficiency-metrics"></a>Metriky efektivity
Účelem těchto metrik je zjistit, zda lze zlepšit efektivitu mezipaměti. Hlavní výhody odvozené od efektivity mezipaměti:

* Omezené zatížení na zdrojovém serveru, což může vést k těmto akcím:
  * Lepší výkon webového serveru.
  * Snížené provozní náklady.
* Vylepšená akcelerace doručování dat, protože další požadavky budou obsluhovány přímo ze sítě CDN.

| Pole | Popis |
| --- | --- |
| Efektivita mezipaměti |Určuje procento přenesených dat, která byla obsluhována z mezipaměti. Tato metrika měří, když byla verze požadovaného obsahu v mezipaměti doručována přímo z sítě CDN (hraniční servery) žadatelům (například ve webovém prohlížeči). |
| Rychlost volání |Označuje procento požadavků, které byly obsluhovány z mezipaměti. Tato metrika měří, když byla verze požadovaného obsahu v mezipaměti doručována přímo z sítě CDN (hraniční servery) žadateli (například webový prohlížeč). |
| % Vzdálených bajtů – žádná konfigurace mezipaměti |Určuje procento přenosů, které byly obsluhovány ze serverů původu, do sítě CDN (hraniční servery), které nebudou uloženy v mezipaměti v důsledku funkce vynechat mezipaměť (modul pravidel HTTP). |
| % Vzdálených bajtů – vyprší platnost mezipaměti |Označuje procento přenosů dat, které byly obsluhovány ze serverů od počátku do sítě CDN (hraniční servery) v důsledku opětovného ověření zastaralého obsahu. |

#### <a name="usage-metrics"></a>Metriky využití
Účelem těchto metrik je poskytnout přehled o následujících nákladových měřeních:

* Minimalizace provozních nákladů prostřednictvím sítě CDN.
* Snížení nákladů na síť CDN díky efektivitě mezipaměti a kompresi.

> [!NOTE]
> Počty objemů provozu představují provoz, který se použil při výpočtech poměrů a procent, a může zobrazit jenom část celkového provozu pro zákazníky s vysokým objemem.
> 
> 

| Pole | Popis |
| --- | --- |
| Ave bajty z |Označuje průměrný počet přenesených bajtů pro každý požadavek, který je poskytován od sítě CDN (hraniční servery) žadateli (například webový prohlížeč). |
| Žádná četnost bajtů konfigurace mezipaměti |Označuje procento provozu poskytovaného ze sítě CDN (hraniční servery) žadateli (například webový prohlížeč), který nebude uložen do mezipaměti z důvodu funkce vynechat mezipaměť. |
| Komprimovaná bajtová rychlost |Označuje procento provozu odeslaného ze sítě CDN (hraniční servery) žadatelům (například webový prohlížeč) v komprimovaném formátu. |
| Odchozí bajty |Určuje množství dat (v bajtech), které byly doručeny ze sítě CDN (hraniční servery) žadateli (například webový prohlížeč). |
| Počet bajtů v |Určuje množství dat (v bajtech) odeslaných od žadatelů (například webový prohlížeč) do sítě CDN (hraniční servery). |
| Bajty vzdálené |Určuje množství dat (v bajtech) odeslaných ze serverů CDN a zákaznického původu do sítě CDN (hraniční servery). |

#### <a name="performance-metrics"></a>Metriky výkonu
Účelem těchto metrik je sledovat celkový výkon sítě CDN pro váš provoz.

| Pole | Popis |
| --- | --- |
| Přenosová rychlost |Určuje průměrnou rychlost přenosu obsahu z CDN do žadatele. |
| Doba trvání |Označuje průměrný čas (v milisekundách), který trvalo doručení assetu žadateli (například webový prohlížeč). |
| Počet komprimovaných požadavků |Označuje procento přístupů, které byly doručeny ze sítě CDN (hraniční servery) žadateli (například webový prohlížeč) v komprimovaném formátu. |
| Míra chyb 4xx |Určuje procento přístupů, které vygenerovaly stavový kód 4xx. |
| Míra chyb 5xx |Určuje procento přístupů, které vygenerovaly stavový kód 5xx. |
| Hits |Označuje počet požadavků na obsah CDN. |

#### <a name="secure-traffic-metrics"></a>Metriky zabezpečeného provozu
Účelem těchto metrik je sledovat výkon sítě CDN pro přenosy přes protokol HTTPS.

| Pole | Popis |
| --- | --- |
| Efektivita zabezpečené mezipaměti |Označuje procento přenesených dat pro požadavky HTTPS, které byly obsluhovány z mezipaměti. Tato metrika měří, když byla verze požadovaného obsahu v mezipaměti doručována přímo z sítě CDN (hraniční servery) do žadatelů (například webový prohlížeč) prostřednictvím protokolu HTTPS. |
| Rychlost zabezpečeného přenosu |Určuje průměrnou rychlost, s jakou byl obsah přenesen ze sítě CDN (hraniční servery) na žadatele (např. webové servery) prostřednictvím protokolu HTTPS. |
| Průměrná doba trvání zabezpečení |Označuje průměrný čas (v milisekundách), který trvalo doručení assetu žadateli (například webovému prohlížeči) prostřednictvím protokolu HTTPS. |
| Zabezpečené přístupy |Označuje počet požadavků HTTPS na obsah CDN. |
| Zabezpečené bajty odchozích bajtů |Označuje počet přenosů HTTPS (v bajtech), které byly doručeny ze sítě CDN (hraniční servery) žadateli (například webový prohlížeč). |

## <a name="reports"></a>Sestavy
Každá sestava v tomto modulu obsahuje graf a statistiku využití šířky pásma a provozu pro různé typy metrik (například stavové kódy HTTP, stavové kódy mezipaměti, adresa URL požadavku atd.). Tyto informace se dají využít k hlubšímu zvyšování způsobu poskytování obsahu klientům a k doladění chování CDN, aby se zlepšil výkon při doručování dat.

### <a name="accessing-the-edge-performance-reports"></a>Přístup k sestavám výkonnosti Edge
1. V okně profilu CDN klikněte na tlačítko **Spravovat** .
   
    ![Tlačítko Spravovat okno profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte myší na kartu **Analytics** a pak najeďte myší na informační rámeček **analýzy výkonu Edge** .  Klikněte na **large object http** .
   
    Zobrazí se obrazovka sestavy analýzy hraničního uzlu.

| Sestava | Popis |
| --- | --- |
| Denní souhrn |Umožňuje zobrazit denní trendy provozu v zadaném časovém období. Každý pruh v tomto grafu představuje konkrétní datum. Velikost pruhu označuje celkový počet přístupů k danému datu. |
| Hodinový souhrn |Umožňuje zobrazit hodinové trendy provozu v zadaném časovém období. Každý pruh v tomto grafu představuje jednu hodinu na konkrétní datum. Velikost pruhu označuje celkový počet přístupů, ke kterým došlo během dané hodiny. |
| Protokoly |Zobrazuje rozpis přenosů mezi protokoly HTTP a HTTPS. Prstencový graf označuje procento přístupů, ke kterým došlo pro každý typ protokolu. |
| Metody HTTP |Umožňuje rychle získat představu o tom, které metody HTTP se používají k vyžádání vašich dat. Nejběžnější metody žádosti HTTP jsou obvykle GET, HEAD a POST. Prstencový graf označuje procento přístupů, ke kterým došlo u každého typu metody požadavku HTTP. |
| Adresy URL |Obsahuje graf, který zobrazuje prvních 10 požadovaných adres URL. Pro každou adresu URL se zobrazí pruh. Výška pruhu označuje počet přístupů, které konkrétní adresa URL vygenerovala za časové období pokryté sestavou. Statistika pro nejvyšší 100 požadované adresy URL se zobrazuje přímo pod tímto grafem. |
| Záznamů CNAME |Obsahuje graf, který zobrazuje prvních 10 záznamů CNAME používaných k vyžádání prostředků v časovém intervalu sestavy. Statistika pro horní 100y požadované záznamy CNAME se zobrazí přímo pod tímto grafem. |
| Zdroje |Obsahuje graf, který zobrazuje prvních 10 serverů CDN nebo zákaznického původu, ze kterých se v zadaném období požadovaly prostředky. Statistika pro nejvyšší 100 vyžádané servery CDN nebo zákaznického původu se zobrazí přímo pod tímto grafem. Servery původu zákazníka se identifikují podle názvu definovaného v možnosti název adresáře. |
| Geografické body POP |Ukazuje, jak velká část provozu je směrována přes konkrétní bod (POP). Zkratka pro tři číslice představuje bod POP v naší síti CDN. |
| Klienti |Obsahuje graf, který zobrazuje prvních 10 klientů, které požadovaly prostředky v zadaném časovém období. Pro účely této sestavy se všechny požadavky pocházející ze stejné IP adresy považují za stejného klienta. Statistika pro nejvyšší 100 klientů se zobrazuje přímo pod tímto grafem. Tato sestava je užitečná pro určení vzorů aktivity stahování pro hlavní klienty. |
| Stavy mezipaměti |Poskytuje podrobný přehled o chování mezipaměti, což může odhalit přístupy k vylepšení celkového prostředí koncových uživatelů. Vzhledem k tomu, že nejrychlejší výkon přichází z hlediska přístupů do mezipaměti, můžete optimalizovat rychlost doručování dat díky minimalizaci přístupů do mezipaměti a přístupů do mezipaměti s vypršenou platností. |
| ŽÁDNÉ podrobnosti |Obsahuje graf, který zobrazuje prvních 10 adres URL pro prostředky, jejichž aktuálnost obsahu mezipaměti nebyl v zadaném časovém období zkontrolován. Statistika pro hlavní adresy URL 100 pro tyto typy assetů se zobrazuje přímo pod tímto grafem. |
| Podrobnosti CONFIG_NOCACHE |Obsahuje graf, který zobrazuje prvních 10 adres URL pro prostředky, které nebyly uloženy v mezipaměti z důvodu konfigurace CDN zákazníka. Tyto typy prostředků byly obsluhovány přímo ze zdrojového serveru. Statistika pro hlavní adresy URL 100 pro tyto typy assetů se zobrazuje přímo pod tímto grafem. |
| Podrobnosti o neukládání do mezipaměti |Obsahuje graf, který zobrazuje prvních 10 adres URL pro prostředky, které nebylo možné uložit do mezipaměti z důvodu dat hlavičky požadavku. Statistika pro hlavní adresy URL 100 pro tyto typy assetů se zobrazuje přímo pod tímto grafem. |
| Podrobnosti TCP_HIT |Obsahuje graf, který zobrazuje prvních 10 adres URL pro prostředky, které jsou obsluhovány okamžitě z mezipaměti. Statistika pro hlavní adresy URL 100 pro tyto typy assetů se zobrazuje přímo pod tímto grafem. |
| Podrobnosti TCP_MISS |Obsahuje graf, který zobrazuje prvních 10 adres URL pro prostředky, které mají stav mezipaměti TCP_MISS. Statistika pro hlavní adresy URL 100 pro tyto typy assetů se zobrazuje přímo pod tímto grafem. |
| Podrobnosti TCP_EXPIRED_HIT |Obsahuje graf, který zobrazuje prvních 10 adres URL zastaralých assetů, které byly obsluhovány přímo z POP. Statistika pro hlavní adresy URL 100 pro tyto typy assetů se zobrazuje přímo pod tímto grafem. |
| Podrobnosti TCP_EXPIRED_MISS |Obsahuje graf, který zobrazuje prvních 10 adres URL pro zastaralé prostředky, pro které se nová verze musela načíst ze zdrojového serveru. Statistika pro hlavní adresy URL 100 pro tyto typy assetů se zobrazuje přímo pod tímto grafem. |
| Podrobnosti TCP_CLIENT_REFRESH_MISS |Obsahuje pruhový graf, který zobrazuje prvních 10 adres URL pro prostředky, které byly načteny ze zdrojového serveru z důvodu žádosti o nemezipaměť z klienta. Statistika pro hlavní adresy URL 100 pro tyto typy požadavků se zobrazuje přímo pod tímto grafem. |
| Typy požadavků klienta |Určuje typ požadavků, které provedli klienti HTTP (např. prohlížeče). Tato sestava obsahuje prstencový graf, který poskytuje smysl pro zpracování požadavků. V grafu se zobrazí informace o šířce pásma a provozu každého typu požadavku. |
| Uživatelský agent |Obsahuje pruhový graf, který zobrazuje prvních 10 uživatelských agentů k vyžádání obsahu prostřednictvím našeho CDN. Uživatelský agent je typicky webový prohlížeč, přehrávač médií nebo prohlížeč mobilních telefonů. Statistika pro nejčastější uživatelské agenty 100 se zobrazuje přímo pod tímto grafem. |
| Odkazujících serverů |Obsahuje pruhový graf, který zobrazuje horních 10 odkazujících na obsah, který je k dispozici prostřednictvím naší sítě CDN. Odkazujícím objektem je obvykle adresa URL webové stránky nebo prostředku, který odkazuje na váš obsah. Podrobné informace jsou uvedené pod grafem pro hlavní servery 100. |
| Typy komprese |Obsahuje prstencový graf, který rozděluje požadované prostředky podle toho, zda byly komprimovány našimi hraničními servery. Procento komprimovaných prostředků je rozděleno podle použitého typu komprese. Podrobné informace jsou uvedeny pod grafem pro každý typ a stav komprese. |
| Typy souborů |Obsahuje pruhový graf, který zobrazuje prvních 10 typů souborů, které byly vyžádány prostřednictvím našeho CDN pro váš účet. Pro účely této sestavy je typ souboru definovaný příponou názvu souboru prostředku a typem internetového média (např. HTML \[ text/HTML \] ,. htm \[ text/HTML \] ,. aspx \[ text/HTML \] atd.). Podrobné informace jsou uvedené pod grafem pro nejvyšší 100 typy souborů. |
| Jedinečné soubory |Obsahuje graf, který zobrazuje celkový počet jedinečných prostředků, které byly vyžádány v určitém dni určitého dne v zadaném časovém období. |
| Souhrn ověření tokenu |Obsahuje výsečový graf, který poskytuje rychlý přehled o tom, zda byly požadované prostředky chráněny ověřováním Token-Based. Chráněné prostředky se v grafu zobrazí podle výsledků jejich pokusů o ověření. |
| Podrobnosti zamítnutí ověření tokenu |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, které byly zamítnuty kvůli Token-Based ověřování. |
| Kódy odpovědí HTTP |Poskytuje rozpis stavových kódů HTTP (např. 200 OK, 403 zakázané, 404 Nenalezeno atd.), které byly doručeny vašim klientům HTTP prostřednictvím našich hraničních serverů. Výsečový graf vám umožní rychle posoudit, jak byly vaše prostředky obsluhovány. Podrobné statistické údaje jsou k dispozici pro každý kód odpovědi pod grafem. |
| chyby 404 |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, jejichž výsledkem byl 404 nenalezený kód odpovědi. |
| chyby 403 |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, které vedly k 403 zakázanému kódu odpovědi. Pokud je žádost zamítnuta serverem původu zákazníka nebo hraničním serverem na našem místním serveru, bude kód odpovědi 403 zakázaný. |
| 4xx chyby |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, jejichž výsledkem byl kód odpovědi v rozsahu 400. Vyloučené z této sestavy nejsou nalezené 403 a kódy odpovědí 404 jsou zakázané. Kód odpovědi 4xx obvykle nastane, pokud je žádost zamítnuta v důsledku chyby klienta. |
| chyby 504 |Obsahuje pruhový graf, který umožňuje zobrazit 10 nejčastějších žádostí, jejichž výsledkem byl kód odpovědi na časový limit brány 504. K vypršení časového limitu v případě, kdy se proxy server HTTP pokouší komunikovat s jiným serverem, dojde k vypršení časového limitu odpovědi brány 504. V případě našeho CDN se kód odpovědi na časový limit brány 504 obvykle objevuje, když hraniční Server nemůže navázat komunikaci se serverem zákazníka. |
| chyby 502 |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, které vedly k 502 chybnému kódu odpovědi brány. 502 špatný kód odpovědi brány nastane, pokud dojde k selhání protokolu HTTP mezi serverem a proxy serverem HTTP. V případě našeho CDN se k 502 chybnému kódu odezvy brány obvykle dochází v případě, že zdrojový server zákazníka vrátí neplatnou odpověď na hraniční Server. Odpověď není platná, pokud ji nelze analyzovat, nebo pokud je neúplná. |
| 5xx chyby |Obsahuje pruhový graf, který umožňuje zobrazit prvních 10 požadavků, jejichž výsledkem byl kód odpovědi v rozsahu 500.  Vyloučení z této sestavy je 502 chybných kódů a odpovědí na časový limit brány 504. |

## <a name="see-also"></a>Viz také
* [CDN Azure – Přehled](cdn-overview.md)
* [Statistiky v reálném čase v Microsoft Azure CDN](cdn-real-time-stats.md)
* [Přepsání výchozího chování HTTP pomocí modulu pravidel](./cdn-verizon-premium-rules-engine.md)
* [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)