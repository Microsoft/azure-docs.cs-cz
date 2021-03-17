---
title: Sestavy služby Azure front-end Standard/Premium (Preview)
description: Tento článek vysvětluje, jak funguje vytváření sestav v frontách Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099288"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Sestavy služby Azure front-end Standard/Premium (Preview)

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sestavy služby Azure front-Premium Standard/Premium poskytují integrovaný a celý pohled na to, jak se přední dveře Azure chovají spolu s přidruženými metrikami firewallu webových aplikací. Můžete také využít výhod protokolů přístupu k dalšímu řešení potíží a ladění. Sestavy analýzy front-dveří pro Azure zahrnují sestavy provozu a sestavy zabezpečení.

| Sestavy | Podrobnosti |
|---------|---------|
| Přehled klíčových metrik | Zobrazuje celková data, která se poslala z okrajů front Azure do klientů.<br/>– Špičková šířka pásma<br/>– Požadavky <br/>– Poměr přístupů do mezipaměti<br/> – Celková latence<br/>– míra chyb 5XX |
| Provoz podle domény | – Poskytuje přehled všech domén pod profilem.<br/>– Rozpis dat přenesených z AFD Edge na klienta<br/>– Celkový počet požadavků<br/>-3XX/4XX/5XX kód odpovědi podle domén |
| Provoz podle umístění | -Zobrazuje mapu pro žádosti a použití v hlavních zemích.<br/>– Zobrazení trendu hlavních zemí |
| Využití | – Zobrazuje přenos dat z hraničních zařízení Azure do klientů.<br/>– Přenos dat ze zdroje do AFD Edge<br/>-Šířka pásma od AFD Edge po klienty<br/>-Šířka pásma od počátku do AFD okraje<br/>– Požadavky<br/>– Celková latence<br/>– Trend počtu požadavků podle kódu stavu HTTP |
| Ukládání do mezipaměti | -Zobrazuje poměr přístupů do mezipaměti podle počtu požadavků<br/>– Zobrazení trendů žádostí o přijetí změn a neúspěšných volání |
| Hlavní adresa URL | – Zobrazuje počet žádostí <br/>-Data přenesená <br/>– Poměr přístupů do mezipaměti <br/>– Distribuce stavového kódu odpovědi pro nejvíce vyžádané prostředky 50. |
| Hlavní odkazující | – Zobrazuje počet žádostí <br/>-Data přenesená <br/>– Poměr přístupů do mezipaměti <br/>– Distribuce stavového kódu odpovědi pro hlavní 50 odkazující servery, které generují provoz. |
| Hlavní Agent pro uživatele | – Zobrazuje počet žádostí <br/>-Data přenesená <br/>– Poměr přístupů do mezipaměti <br/>– Distribuce stavového kódu odpovědi pro prvních 50 uživatelských agentů, kteří se použili k vyžádání obsahu. |

| Sestavy zabezpečení | Podrobnosti |
|---------|---------|
| Přehled klíčových metrik | -Zobrazuje spárované WAF pravidla<br/>– Vyhovující OWASP pravidla<br/>– Odpovídající pravidla pro roboty<br/>– Odpovídající vlastní pravidla |
| Metriky podle dimenzí | – Rozpis odpovídajících pravidel WAF podle akce<br/>– Prstencový graf událostí podle typu sady pravidel a události podle skupiny pravidel<br/>– Rozdělte seznam hlavních událostí podle ID pravidla, země, IP adresy, adresy URL a uživatelského agenta.  |

> [!NOTE]
> Sestavy zabezpečení jsou dostupné jenom u SKU Azure front-SKU Premium.

Většina sestav je založena na protokolech přístupu a zákazníkům na předních dveřích Azure zdarma nabízí bezplatné poplatky. Zákazník nemusí povolit protokoly přístupu nebo provádět žádnou konfiguraci pro zobrazení těchto sestav. Sestavy jsou přístupné prostřednictvím portálu a rozhraní API. Podporuje se i stahování souboru CSV. 

Sestavy podporují libovolný vybraný rozsah kalendářních dat v předchozích 90 dnech. U datových bodů každých 5 minut, každou hodinu nebo každý den na základě vybraného rozsahu dat. Normálně můžete zobrazit data se zpožděním během hodiny a občas s prodlevou až několik hodin. 

## <a name="access-reports-using-the-azure-portal"></a>Přístup k sestavám pomocí Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte svůj profil služby Azure front-end Standard/Premium.

1. V navigačním podokně vyberte **sestavy nebo zabezpečení** v části *Analýza*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Snímek cílové stránky se sestavami":::

1. Pro různé dimenze je sedm karet, vyberte rozměr zájmu.

   * Provoz podle domény
   * Využití 
   * Provoz podle umístění
   * Mezipaměť
   * Hlavní adresa URL
   * Hlavní odkazující
   * Hlavní Agent pro uživatele

1. Po výběru dimenze můžete vybrat jiné filtry.
  
    1. **Zobrazit data pro** – vyberte rozsah kalendářních dat, pro který chcete zobrazit provoz podle domény. Dostupné rozsahy:
        
        * Posledních 24 hodin
        * Posledních 7 dní
        * Posledních 30 dnů
        * Posledních 90 dní
        * This month
        * Minulý měsíc
        * Vlastní datum

         Ve výchozím nastavení se data zobrazují v posledních sedmi dnech. U karet s spojnicými grafy se členitost dat do rozsahů data, které jste vybrali jako výchozí chování. 
    
        * 5 minut – jeden datový bod každých 5 minut pro rozsahy dat kratší nebo rovna 24 hodinám.
        * Podle hodin – jedna data každé hodiny pro rozsah dat mezi 24 hodinami a 30 dny
        * Podle dne – jedna data za den pro rozsahy dat větší než 30 dní.

        Agregaci lze vždy použít ke změně výchozí členitosti agregace. Poznámka: 5 minut nefunguje pro rozsah dat delší než 14 dní. 

    1. **Umístění** – vyberte jedno nebo více umístění klientů podle země. Země jsou seskupené do šesti oblastí: Severní Amerika, Asie, Evropa, Afrika, Oceánie a a Jižní Amerika. Přečtěte si téma [Mapování oblasti/země](https://en.wikipedia.org/wiki/Subregion). Ve výchozím nastavení jsou vybrány všechny země.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Snímek sestav pro dimenzi umístění":::
   
    1. **Protokol** – pro zobrazení dat přenosů vyberte buď http, nebo HTTPS.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Snímek sestav pro dimenzi protokolu":::
    
    1. **Domény** – vyberte jeden nebo více koncových bodů nebo vlastní domény. Ve výchozím nastavení jsou vybrány všechny koncové body a vlastní domény. 
    
        * Pokud odstraníte koncový bod nebo vlastní doménu v jednom profilu a pak znovu vytvoříte stejný koncový bod nebo doménu v jiném profilu. Koncový bod se považuje za druhý koncový bod.  
        * Pokud zobrazujete sestavy podle vlastní domény – když odstraníte jednu vlastní doménu a svážete ji s jiným koncovým bodem. Budou se považovat za jednu vlastní doménu. Pokud se zobrazí podle koncového bodu, budou se považovat za samostatné položky. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Snímek sestav pro dimenzi domény":::

1. Pokud chcete exportovat data do souboru CSV, vyberte na vybrané kartě odkaz *Stáhnout sdílený svazek clusteru* .

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Snímek obrazovky se stažením souboru CSV pro sestavy":::

### <a name="key-metrics-for-all-reports"></a>Klíčové metriky pro všechny sestavy

| Metric | Popis |
|---------|---------|
| Přenesená data | Zobrazuje data přenesená z AFD Edge POP do klienta pro vybraný časový rámec, umístění klientů, domény a protokoly. |
| Šířka pásma ve špičce | Špičkové využití šířky pásma v bitech za sekundu z hraničních zařízení Azure pro vybraný časový rámec, umístění klientů, domén a protokolů. | 
| Požadavky celkem | Počet požadavků, které AFD Edge pop odpověděli na klienta pro vybraný časový rámec, umístění klientů, domény a protokoly. |
| Poměr přístupů do mezipaměti | Procento všech požadavků, které jsou v mezipaměti, pro které AFD obsluhován obsah z jeho hraničních mezipamětí pro vybraný časový rámec, umístění klientů, domény a protokoly. |
| Míra chyb 5XX | Procento požadavků, u kterých byl stavový kód protokolu HTTP klienta 5XX pro vybraný časový rámec, umístění klienta, domény a protokoly. |
| Celková latence | Průměrná latence všech požadavků pro vybraný časový rámec, umístění klientů, domény a protokoly. Latence pro každý požadavek se měří jako celková doba, po kterou klientská dvířka Azure obdrží požadavek klienta, dokud se nepošle poslední bajt odpovědi z front Azure na klienta. |

## <a name="traffic-by-domain"></a>Provoz podle domény

Provoz podle domény poskytuje zobrazení mřížky všech domén v rámci tohoto profilu front-dveří Azure. V této sestavě můžete zobrazit: 
* Žádosti
* Data přenesená z front-dveří z Azure do klienta
* Požadavky se stavovým kódem (3XX, 4Xx a 5XX) v každé doméně

K doménám patří koncový bod a vlastní domény, jak je vysvětleno v tématu přístup k relaci sestavy.  

Pokud zjistíte metriky pod očekáváním, můžete přejít na další karty a prozkoumat další informace nebo zobrazit protokol přístupu. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Snímek obrazovky cílové stránky pro sestavy":::


## <a name="usage"></a>Využití

Tato sestava zobrazuje trendy provozu a stavového kódu odezvy různými dimenzemi, včetně:

* Data přenesená z Edge do klienta a od počátku po Edge v spojnicovém grafu. 

* Data přenesená z Edge do klienta podle protokolu v spojnicovém grafu. 

* Počet požadavků od Edge na klienty v spojnicovém grafu.  

* Počet požadavků od Edge na klienty podle protokolu, HTTP a HTTPS ve spojnicovém grafu. 

* Šířka pásma od Edge po klienta v spojnicovém grafu. 

* Celková latence, která měří celkovou dobu od žádosti klienta přijaté předními dveřmi, dokud neuplyne poslední bajt odpovědi odeslaný z předních dveří na klienta.

* Počet požadavků od Edge na klienty podle stavového kódu HTTP v spojnicovém grafu. Každý požadavek vygeneruje stavový kód HTTP. Stavový kód HTTP se zobrazí v HTTPStatusCode v nezpracovaném protokolu. Stavový kód popisuje, jak hraniční síť CDN zpracovala požadavek. Například stavový kód 2xx označuje, že žádost byla úspěšně zpracována klientovi. I když stavový kód 4xx označuje, že došlo k chybě. Další informace o stavových kódech HTTP najdete v tématu seznam stavových kódů HTTP. 

* Počet požadavků od Edge na klienty podle stavového kódu HTTP. Procentuální podíl požadavků na stavový kód HTTP mezi všemi požadavky v mřížce 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Snímek obrazovky se sestavami podle využití" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Provoz podle umístění

Tato sestava zobrazuje nejvyšší 50 umístění podle země návštěvníků, kteří mají přístup k vašemu assetu nejvíc. Tato sestava obsahuje také rozpis metrik podle země a poskytuje celkový přehled zemí, ve kterých je vygenerována většina provozu. Nakonec vidíte, která země má vyšší poměr přístupů do mezipaměti nebo kódy chyb 4XX/5XX.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Snímek sestav podle umístění" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

V sestavách jsou zahrnuté tyto sestavy:

* Pohled světové mapy na horních 50 zemí podle přenášených dat nebo požadavků podle vašeho výběru.
* Dva spojnicové grafy zobrazují trend v horních pěti zemích na základě přenášených dat a požadavků podle vašeho výběru. 
* Mřížka hlavních zemí s odpovídajícími daty přenesených z AFD na klienty, data přenesená z% ze všech zemí, požadavky, požadavek% mezi všechny země, poměr přístupů do mezipaměti, kód odpovědi 4XX a kód odpovědi 5XX.

## <a name="caching"></a>Ukládání do mezipaměti

V sestavách do mezipaměti najdete graf přístupů do mezipaměti a jejich neúspěšných a poměr přístupů do mezipaměti na základě požadavků. Tyto klíčové metriky vysvětlují, jak CDN ukládá obsah do mezipaměti, protože nejrychlejší výsledky výkonu z důvodu přístupů do mezipaměti. Pomocí minimalizace neúspěšných přístupů do mezipaměti můžete optimalizovat rychlost doručování dat. Tato sestava zahrnuje:

* Trend počtu přístupů a přístupů do mezipaměti v spojnicovém grafu.

* Poměr přístupů do mezipaměti v spojnicovém grafu

Přístupy do mezipaměti a Neúspěšné přístupy popisují přístupy do mezipaměti a Neúspěšné přístupy do mezipaměti pro požadavky klientů.

* Přístupy: požadavky klienta, které jsou obsluhovány přímo z Azure CDN hraničních serverů. Odkazuje na ty žádosti, jejichž hodnoty pro CacheStatus v nezpracovaných protokolech jsou, PARTIAL_HIT nebo vzdálené PŘÍSTUPŮ. 

* Chybíš: požadavky klienta obsluhované Azure CDN hraniční servery, které načítají obsah od počátku. Odkazuje na ty žádosti, jejichž hodnoty pro pole CacheStatus v nezpracovaných protokolech nejsou k disminutě. 

**Poměr přístupů do mezipaměti** popisuje procento požadavků v mezipaměti, které jsou obsluhovány přímo z Edge. Vzorec poměru přístupů do mezipaměti je: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

Tato sestava bere v úvahu scénáře ukládání do mezipaměti a požadavky, které splňují následující požadavky, jsou převzaty do výpočtu. 

* Požadovaný obsah byl uložen do mezipaměti na bodu POP, který je nejbližší žadateli nebo štítku původu. 

* Částečný obsah v mezipaměti pro blokování objektů.

Vyloučí všechny následující případy: 

* Žádosti, které jsou zamítnuté kvůli sadě pravidel

* Požadavky obsahující sadu odpovídajících pravidel, která byla nastavena na zakázanou mezipaměť. 

* Požadavky blokované nástrojem WAF. 

* Hlavičky odpovědi na původ označují, že by neměly být uloženy do mezipaměti. Například Cache-Control: Private, Cache-Control: no-cache nebo pragma: no-cache Headers zabrání v ukládání prostředku do mezipaměti. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Snímek obrazovky se sestavami pro ukládání do mezipaměti":::

## <a name="top-urls"></a>Hlavní adresy URL

Hlavní adresy URL umožňují zobrazit objem provozu, který se uskutečnil v rámci určitého koncového bodu nebo vlastní domény. V průběhu posledních 90 dnů se zobrazí data pro nejvíce vyžádané assety 50. V oblíbených adresách URL se zobrazí následující hodnoty. Uživatel může řadit adresy URL podle počtu požadavků, požadavku%, přenesených dat a přenesených dat%. Všechny metriky se agreguje po hodinách a můžou se lišit podle vybraného časového rámce. Adresa URL odkazuje na hodnotu RequestUri v protokolu Access. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Snímek obrazovky se sestavami pro hlavní adresu URL":::

* Adresa URL odkazuje na úplnou cestu požadovaného prostředku ve formátu `http(s)://contoso.com/index.html/images/example.jpg` . 
* Počty požadavků. 
* Požadavek% z celkového počtu požadavků, který obsluhuje přední dvířka Azure. 
* Přenesená data. 
* Přenesená data:%. 
* Poměr přístupů do mezipaměti%
* Žádosti s kódem odpovědi jako 4XX
* Žádosti s kódem odpovědi jako 5XX

> [!NOTE]
> Hlavní adresy URL se můžou v průběhu času měnit a získat přesný seznam hlavních adres URL 50, přičemž přední dveře Azure počítají všechny vaše žádosti o adresu URL po hodinách a udržují průběžný součet v průběhu dne. Adresy URL v dolní části adres URL 500 se můžou v seznamu v průběhu dne rozvolávat nebo vyřadit. celkový počet těchto adres URL je tak přibližný.  
>
> Horní 50 adres URL může v seznamu proniknout, ale v seznamu zmizí jenom zřídka, takže čísla pro hlavní adresy URL jsou obvykle spolehlivá. Když adresa URL nezmizí ze seznamu a znovu se rozroste za den, počet žádostí během období, kdy v seznamu chybí, je odhadovaný na základě čísla žádosti adresy URL, která se zobrazí v daném období.  
>
> Stejná logika platí pro horního uživatelského agenta. 

## <a name="top-referrers"></a>Hlavní odvoditelné

Hlavní doporučující zákazníci můžou zákazníkům zobrazit horní 50ový bod, který vytvořil nejvíc požadavků na obsah konkrétního koncového bodu nebo vlastní domény. Data můžete zobrazit po dobu v posledních 90 dnech. Odkazující objekt označuje adresu URL, ze které byla vygenerována žádost. Odkazující web může pocházet z vyhledávacího webu nebo jiných webů. Pokud uživatel zadá adresu URL (například http (s)://contoso.com/index.html) přímo do adresního řádku prohlížeče, bude odkazující objekt pro požadovanou hodnotu "Empty". Sestava hlavních odkazujících obsahuje následující hodnoty. Můžete řadit podle počtu požadavků, žádosti o%, přenesených dat a přenesených dat%. Všechny metriky se agreguje po hodinách a můžou se lišit podle vybraného časového rámce. 

* Odkazující, hodnota odkazujícího serveru v nezpracovaných protokolech 
* Počty požadavků 
* Požadavek% z celkového počtu požadavků zpracovaných Azure CDN ve vybraném časovém období. 
* Přenesená data 
* Přenesená data% 
* Poměr přístupů do mezipaměti%
* Žádosti s kódem odpovědi jako 4XX
* Žádosti s kódem odpovědi jako 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Snímek sestav pro hlavní odkazujícího serveru.":::

## <a name="top-user-agent"></a>Hlavní Agent pro uživatele

Tato sestava vám umožní zobrazit grafické a statistické zobrazení prvních 50 uživatelských agentů, které se použily k vyžádání obsahu. Třeba
* Mozilla/5.0 (Windows NT 10,0; WOW64 
* AppleWebKit/537.36 (KHTML, jako je prostředí Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

V mřížce se zobrazí počty požadavků, požadavek%, přenesená data a přenesená data, poměr přístupů do mezipaměti%, žádosti s kódem odpovědi jako 4XX a požadavky s kódem odpovědi jako 5XX. Uživatelský agent odkazuje na hodnotu UserAgent v protokolech přístupu.

## <a name="security-report"></a>Sestava zabezpečení

Tato sestava vám umožní zobrazit grafické a statistické zobrazení vzorů WAF podle různých dimenzí.

| Dimenze | Description |
|---------|---------|
| Přehled metrik – vyhovující WAF pravidla | Žádosti, které odpovídají vlastním pravidlům WAF, spravovaným pravidlům WAF a robot Manageru. |
| Přehled metrik – Blokované požadavky | Procento požadavků, které jsou blokovány pravidly WAF mezi všemi požadavky, které odpovídají pravidlům WAF. |
| Přehled metrik – shodná spravovaná pravidla | Čtyři spojnicové grafy trend pro požadavky blokující, protokol, povolení a přesměrování. |
| Přehled metrik – odpovídající vlastní pravidlo | Žádosti, které odpovídají vlastním WAF pravidlům. |
| Přehled metrik – vyhovující pravidlo robota | Žádosti, které odpovídají správci bot. |
| WAF trend žádosti podle akce | Čtyři spojnicové grafy trend pro požadavky blokující, protokol, povolení a přesměrování. |
| Události podle typu pravidla | Prstencový graf WAF požadavků distribuce podle typu pravidla, např. robot, vlastní pravidla a spravovaná pravidla. |
| Události podle skupiny pravidel | Prstencový graf distribuce požadavků WAF podle skupiny pravidel |
| Žádosti podle akcí | Tabulka požadavků podle akcí v sestupném pořadí. |
| Žádosti podle hlavních ID pravidel | Tabulka požadavků podle hlavních 50 ID pravidel v sestupném pořadí. |
| Žádosti podle hlavních zemí |  Tabulka požadavků podle hlavních 50 zemí v sestupném pořadí. |
| Požadavky na hlavní IP adresy klientů |  Tabulka požadavků podle hlavních 50 IP adres v sestupném pořadí. |
| Žádosti podle hlavní adresy URL požadavku |  Tabulka požadavků podle hlavních 50 adres URL v sestupném pořadí. |
| Žádost podle hlavních názvů hostitelů | Tabulka požadavků podle hlavních 50 hostitelů v sestupném pořadí. |
| Žádosti od nejvyšších uživatelských agentů | Tabulka požadavků podle hlavních 50 uživatelských agentů v sestupném pořadí. |

## <a name="cvs-format"></a>Formát CVS

Soubory CSV můžete stáhnout pro různé karty v sestavách. Tato část popisuje hodnoty v jednotlivých souborech CSV.

### <a name="general-information-about-the-cvs-report"></a>Obecné informace o sestavě CVS

Každé hlášení CSV obsahuje některé obecné informace a informace jsou k dispozici ve všech souborech CSV. proměnné založené na sestavě, kterou stáhnete. 


| Hodnota | Popis |
|---------|---------|
| Sestava | Název sestavy | 
| Domény | Seznam koncových bodů nebo vlastních domén pro sestavu. |
| StartDateUTC | Začátek rozsahu dat, pro který jste sestavu vygenerovali, v koordinovaném světovým čase (UTC) |
| EndDateUTC | Konec rozsahu kalendářních dat, pro který jste sestavu vygenerovali, v koordinovaném světovým čase (UTC) |
| GeneratedTimeUTC | Datum a čas, kdy jste sestavu vygenerovali v koordinovaném světovým čase (UTC) |
| Umístění | Seznam zemí, ve kterých pocházely požadavky klienta. Hodnota je ve výchozím nastavení nastavená na hodnotu vše. Nelze použít pro sestavu zabezpečení. |
| Protokol | Protokol požadavku, protokolu HTTP nebo HTTPs. Neplatí pro hlavní adresu URL a provoz na základě uživatelského agenta v sestavách a sestavách zabezpečení. |
| Agregace | Členitost agregace dat v jednotlivých řádcích, každých 5 minut, každou hodinu a každý den. Neplatí pro provoz podle domény, hlavní adresy URL a provozu uživatelským agentem v sestavách a sestavách zabezpečení. |

### <a name="data-in-traffic-by-domain"></a>Data v provozu podle domény

* Doména 
* Celkový požadavek 
* Poměr přístupů do mezipaměti 
* Žádosti 3XX 
* Žádosti 4XX 
* Žádosti 5XX 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Data v provozu podle umístění

* Umístění
* TotalRequests
* Request
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Data v použití

V tomto souboru CSV jsou tři sestavy. Jednu pro protokol HTTP, jednu pro protokol HTTPS a jednu pro stavový kód HTTP. 

Sestavy pro HTTP a HTTPs sdílejí stejnou datovou sadu. 

* Čas 
* Protokol 
* Přenesené (bajty) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Sestava stavového kódu HTTP. 

* Čas 
* Přenesené (bajty) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Data v mezipaměti 

* Čas
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Data v horní adrese URL 

* URL 
* TotalRequests 
* Request 
* Přenesené (bajty) 
* Převod datatransferu% 

### <a name="data-in-user-agent"></a>Data v uživatelském agentovi 

* UserAgent 
* TotalRequests 
* Request 
* Přenesené (bajty) 
* Převod datatransferu% 

### <a name="security-report"></a>Sestava zabezpečení 

Níže jsou uvedená sedm tabulek se stejnými poli.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

Sedm tabulek je čas, ID pravidla, země, IP adresa, adresa URL, název hostitele, agent pro uživatele. 

## <a name="next-steps"></a>Další kroky

Přečtěte si o [metrikách monitorování v reálném čase služby Azure front-end Standard/Premium](how-to-monitor-metrics.md).
