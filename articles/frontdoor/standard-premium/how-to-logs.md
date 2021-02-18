---
title: Protokolování Azure front-dveří Standard/Premium (Preview)
description: Tento článek vysvětluje, jak funguje protokolování ve službě Azure front-in standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2020
ms.author: duau
ms.openlocfilehash: 6a1cf3112cd936ec842c755eb90b2c7b094aa781
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098860"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Protokolování Azure front-dveří Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Přední dveře Azure poskytují jiné protokolování, které vám pomůžou sledovat, monitorovat a ladit přední dveře. 

* Protokoly přístupu mají podrobné informace o každém požadavku, který AFD přijímá, a umožňuje analyzovat a monitorovat vzory přístupu a problémy s laděním. 
* Protokoly aktivit poskytují přehled o operacích provedených v prostředcích Azure.  
* Protokoly sondy stavu poskytují protokoly pro všechny neúspěšné sondy k vašemu zdroji. 
* Protokoly firewallu webových aplikací (WAF) poskytují podrobné informace o požadavcích, které se protokolují pomocí režimu detekce nebo prevence koncového bodu služby Azure front-dveří. Pomocí těchto protokolů lze také zobrazit vlastní doménu, která je nakonfigurována pomocí WAF.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Protokoly přístupu, protokoly sondy stavu a protokoly WAF nejsou ve výchozím nastavení povolené. K povolení protokolování použijte následující postup. Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal. Protokoly můžou mít zpoždění až do několika minut. 

Protokoly můžete ukládat třemi způsoby: 

* **Účet úložiště:** Účty úložiště se nejlépe používají pro scénáře, kdy jsou protokoly uložené po delší dobu a v případě potřeby se přezkoumávají. 
* **Centra událostí:** Centra událostí jsou skvělou možností pro integraci s dalšími nástroji SIEM (Security Information and Event Management) nebo externími úložišti dat. Příklad: Splunk/služby Datadog/sumo. 
* **Log Analytics Azure:** Azure Log Analytics v Azure Monitor se nejlépe používá pro obecné monitorování v reálném čase a analýzu výkonu front-dveří Azure.

## <a name="configure-logs"></a>Konfigurovat protokoly

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyhledejte Azure front-end Standard/Premium a vyberte profil front-dveří Azure.

1. V profilu klikněte na **monitorování** a vyberte **nastavení diagnostiky**. Vyberte **Přidat nastavení diagnostiky**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Snímek obrazovky s úvodní stránkou nastavení diagnostiky":::

1. V části **nastavení diagnostiky** zadejte název pro  **název nastavení diagnostiky**.

1. Vyberte  **protokol** z **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** a **FrontDoorWebApplicationFirewallLog**.

1. Vyberte **Podrobnosti o cíli**. Možnosti cíle jsou: 

    * **Odeslání do Log Analytics**
        * Vyberte *předplatné* a *Log Analytics pracovní prostor*.
    * **Archivace do účtu úložiště**
        * Vyberte *předplatné* a *účet úložiště*. a nastavte dobu **uchovávání (dny)**.
    * **Streamování do centra událostí**
        * Vyberte *předplatné, obor názvů centra událostí, název centra událostí (nepovinné)* a *název zásady centra událostí*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Snímek obrazovky se stránkou nastavení diagnostiky":::

1. Klikněte na **Uložit**.

## <a name="access-log"></a>Přístup k protokolu

Přední dveře Azure aktuálně poskytují jednotlivé požadavky rozhraní API s každou položkou, která má následující schéma a jsou ve formátu JSON, jak je znázorněno níže. 

| Vlastnost | Popis |
|----------|-------------| 
| TrackingReference | Jedinečný referenční řetězec, který identifikuje požadavek, který obsluhuje AFD, se také pošle jako hlavička X-Azure-ref na klienta. Vyžaduje se pro hledání podrobností v protokolech přístupu pro konkrétní požadavek. |
| Čas | Datum a čas, kdy AFD Edge dodávalo požadovaný obsah klientovi (v UTC). |
| HttpMethod | Metoda HTTP použitá požadavkem: odstranit, získat, hlavní, možnosti, opravit, zveřejnit nebo vložit. |
| HttpVersion | Verze protokolu HTTP, kterou prohlížeč zadal v požadavku. |
| RequestUri | Identifikátor URI přijatého požadavku Toto pole je úplným schématem, portem, doménou, cestou a řetězcem dotazu. |
| Název hostitele | Název hostitele v požadavku od klienta. Pokud povolíte vlastní domény a máte zástupnou doménu (*. contoso.com), název hostitele je a.contoso.com. Pokud používáte doménu front-contoso.azurefd.net (Azure front-dveří), název hostitele je contoso.azurefd.net. |
| RequestBytes | Velikost zprávy požadavku HTTP v bajtech, včetně hlaviček požadavků a textu žádosti. Počet bajtů dat, které prohlížeč v žádosti obsahuje, včetně hlaviček. |
| ResponseBytes | Bajty odeslané back-end serverem jako odpověď |
| UserAgent | Typ prohlížeče, který klient použil. |
| ClientIp | IP adresa klienta, který vytvořil původní požadavek. Pokud v žádosti existovala hlavička X předaná-pro, pak je IP adresa klienta převzata ze stejné. |
| SocketIp | IP adresa přímého připojení k AFD Edge. Pokud klient pro odeslání žádosti použil proxy server HTTP nebo nástroj pro vyrovnávání zatížení, hodnota SocketIp je IP adresa proxy serveru nebo nástroje pro vyrovnávání zatížení. |
| Latence | Doba od času, kdy server AFD Edge obdrží požadavek klienta na čas, který AFD pošle poslední bajt odpovědi klientovi v milisekundách. Toto pole nebere v úvahu latenci sítě a ukládání do vyrovnávací paměti protokolu TCP. |
| RequestProtocol | Protokol, který klient zadal v požadavku: HTTP, HTTPS. |
| Tato SecurityProtocol | Verze protokolu TLS/SSL používaná požadavkem nebo hodnotou null, pokud není šifrování. Mezi možné hodnoty patří: protokolu SSLv3, TLSv1, TLSv 1.1, TLSv 1.2 |
| SecurityCipher | Když hodnota protokolu Request Protocol je HTTPS, toto pole indikuje šifrování TLS/SSL vyjednané klientem a AFD pro šifrování. |
| Koncový bod | Název domény koncového bodu AFD, například contoso.z01.azurefd.net |
| HttpStatusCode | Stavový kód HTTP vrácený z AFD. |
| Výstrah | Bod POP hraničního zobrazení, který odpověděl na žádost uživatele  |
| Stav mezipaměti | Poskytuje stavový kód, jak bude požadavek zpracovávat služba CDN, když přichází do mezipaměti. Možné hodnoty: požadavek HTTP byl obsluhován z mezipaměti POP AFD Edge. <br> **Neúspěšné**: požadavek HTTP byl obsluhován od počátku. <br/> **PARTIAL_HIT**: některé z bajtů z požadavku byly obsluhovány z mezipaměti pop AFD Edge, zatímco některé z bajtů byly obsluhovány od počátku pro scénáře dělení na objekty. <br> **CACHE_NOCONFIG**: předávání požadavků bez nastavení ukládání do mezipaměti, včetně scénáře obejití. <br/> **PRIVATE_NOSTORE**: nejsou v nastavení ukládání do mezipaměti konfigurována žádná mezipaměť pro zákazníky. <br> **REMOTE_HIT**: žádost byla obsluhována mezipamětí nadřazeného uzlu. <br/> Není **k dispozici**:* * požadavek, který byl odepřen nastavením podepsané adresy URL a pravidel. |
| MatchedRulesSetName | Názvy pravidel, která byla zpracována. |
| RouteName | Název trasy, kterou požadavek shodoval. |
| ClientPort | Port IP klienta, který odeslal požadavek. |
| Serveru | Adresa URL webu, který vytvořil požadavek. |
| TimetoFirstByte | Doba v milisekundách od AFD obdrží požadavek na čas, kdy se první bajt pošle klientovi, jak se měří na předních dveřích Azure. Tato vlastnost neměří data klienta. |
| Nastavena | Toto pole poskytuje podrobné informace o tokenu chyb pro každou odpověď. <br> **Chyba**: indikuje, že se nenašla žádná chyba. <br> **CertificateError**: Chyba obecného certifikátu SSL. <br> **CertificateNameCheckFailed**: název hostitele v certifikátu SSL je neplatný nebo neodpovídá. <br> **ClientDisconnected**: požadavek se nezdařil z důvodu připojení k síti klienta. <br> **ClientGeoBlocked**: klient byl zablokován v důsledku geografického umístění IP adresy. <br> **UnspecifiedClientError**: došlo k obecné chybě klienta. <br> **InvalidRequest**: neplatný požadavek. Může to být způsobeno chybnou hlavičkou, textem a adresou URL. <br> **DNSFailure**: selhání serveru DNS. <br> **DNSTimeout**: vypršel časový limit dotazu DNS na překlad back-endu. <br> **DNSNameNotResolved**: nepovedlo se přeložit název nebo adresu serveru. <br> **OriginConnectionAborted**: spojení s počátkem bylo neobvykle odpojeno. <br> **OriginConnectionError**: Chyba obecného připojení k původnímu zdroji. <br> **OriginConnectionRefused**: spojení s počátkem nebylo navázáno. <br> **OriginError**: Chyba obecného původce <br> **OriginInvalidRequest**: původce byl odeslán neplatný požadavek. <br> **ResponseHeaderTooBig**: počátek vrátil příliš velkou velikost hlavičky odpovědi. <br> **OriginInvalidResponse**:* * počátek vrátil neplatnou nebo nerozpoznanou odpověď. <br> **OriginTimeout**: vypršel časový limit pro vypršení platnosti žádosti o zdroj. <br> **ResponseHeaderTooBig**: počátek vrátil příliš velkou velikost hlavičky odpovědi. <br> **RestrictedIP**: požadavek byl zablokován kvůli omezené IP adrese. <br> **SSLHandshakeError**: nepovedlo se navázat spojení se zdrojem z důvodu selhání protřepení SSL. <br> **SSLInvalidRootCA**: rootca je neplatný. <br> **SSLInvalidCipher**: šifra byla neplatná, pro kterou bylo připojení HTTPS navázáno. <br> **OriginConnectionAborted**: spojení s počátkem bylo neobvykle odpojeno. <br> **OriginConnectionRefused**: spojení s počátkem nebylo navázáno. <br> **UnspecifiedError**: došlo k chybě, která se nevešla do žádné chyby v tabulce. |
| OriginURL | Úplná adresa URL původu, kam se odesílají žádosti Skládá se z schématu, hlavičky hostitele, portu, cesty a řetězce dotazu. <br> **Přepsání adresy URL**: Pokud je v sadě pravidel pravidlo pro přepsání adresy URL, cesta odkazuje na přepsanou cestu. <br> **Místní nabídka na hraničních** počítačích Pokud se jedná o možnost přístupů do mezipaměti na hraničním počítači POP, je původ N/A. <br> **Velký požadavek** Pokud je požadovaný obsah velký s několika požadavky v bloku, které se vrátí zpět na počátek, bude toto pole odpovídat prvnímu požadavku na počátek. Další informace najdete v tématu věnovaném vytváření bloků objektů další podrobnosti. |
| OriginIP | Počáteční IP adresa, která dosloužila k žádosti. <br> Možnost **přístupů do mezipaměti na hraničním počítači pop** Pokud se jedná o možnost přístupů do mezipaměti na hraničním počítači POP, je původ N/A. <br> **Velký požadavek** Pokud je požadovaný obsah velký s několika požadavky v bloku, které se vrátí zpět na počátek, bude toto pole odpovídat prvnímu požadavku na počátek. Další informace najdete v tématu věnovaném vytváření bloků objektů další podrobnosti. |
| Původce| Úplný název DNS (název hostitele v adrese URL zdroje) k počátku. <br> Možnost **přístupů do mezipaměti na hraničním počítači pop** Pokud se jedná o možnost přístupů do mezipaměti na hraničním počítači POP, je původ N/A. <br> **Velký požadavek** Pokud je požadovaný obsah velký s několika požadavky v bloku, které se vrátí zpět na počátek, bude toto pole odpovídat prvnímu požadavku na počátek. Další informace najdete v tématu věnovaném vytváření bloků objektů další podrobnosti. |

## <a name="health-probe-log"></a>Protokol sondy stavu

Protokoly sondy stavu poskytují protokolování pro všechny neúspěšné testy, které vám pomůžou diagnostikovat původní zdroj.Protokoly vám poskytnou informace, které můžete použít k převedení původu zpět na službu. Některé scénáře, pro které může být tento protokol užitečný:

* Všimli jste si, že provoz služby Azure front-dveří byl odeslán do některých původních zdrojů. Například pouze tři ze čtyř zdrojů, které přijímají provoz. Chcete zjistit, zda zdroje jsou přijímány sondy, a pokud není důvodem selhání.  

* Všimli jste si, že původní stav% je nižší, než se čekalo, a chcete zjistit, který původ selhal, a důvod selhání.

### <a name="health-probe-log-properties"></a>Vlastnosti protokolu sondy stavu

Každý protokol sondy stavu má následující schéma.

| Vlastnost | Popis |
| --- | --- |
| HealthProbeId  | Jedinečné ID pro identifikaci požadavku. |
| Čas | Čas dokončení testu |
| HttpMethod | Metoda HTTP používaná požadavkem na sondu stavu. Mezi hodnoty patří GET a HEAD na základě konfigurace sondy stavu. |
| Výsledek | Stav sondy stavu na počátek, hodnota zahrnuje úspěch a další text chyby. |
| HttpStatusCode  | Stavový kód HTTP vrácený počátkem. |
| ProbeURL (cíl) | Úplná adresa URL původu, kam se odesílají žádosti Skládá se z schématu, hlavičky hostitele, cesty a řetězce dotazu. |
| Původce  | Původ, kam se odesílají žádosti Toto pole pomáhá najít počátek zájmu, pokud je zdroj nakonfigurovaný tak, aby nezadávejte. |
| POP | Hraniční nabídka Edge, která odeslala požadavek sondy. |
| IP adresa původu | Cílová IP adresa cílového zdroje Toto pole je užitečné při hledání původních zájmů, pokud nakonfigurujete počátek pomocí nezadávejte. |
| TotolaLatency | Čas od AFDX Edge odesílá požadavek na počátek do zdroje času, který odešle Poslední odpověď na AFDX Edge. |
| ConnectionLatency| Doba trvání strávená při nastavení připojení TCP pro odeslání požadavku testu HTTP na počátek. | 
| Latence DNSResolution | Doba trvání strávená na překladu DNS, pokud je zdroj nakonfigurovaný jako nezadávejte místo IP. Není k dispozici, pokud je zdroj nakonfigurovaný na IP adresu. |

### <a name="health-probe-log-sample-in-json"></a>Ukázka protokolu sondy stavu ve formátu JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Protokoly aktivit

Protokoly aktivit obsahují informace o operacích provedených na službě Azure front-Premium Standard/Premium. Protokoly obsahují podrobnosti o tom, co a kdy byla operace zápisu provedena na frontách Azure. 

> [!NOTE]
> Protokoly aktivit nezahrnují operace GET. Nezahrnují také operace, které provádíte pomocí Azure Portal nebo původního rozhraní API pro správu. 

Přístup k protokolům aktivit ve front-Vratích nebo ve všech protokolech prostředků Azure v Azure Monitor.

Zobrazení protokolů aktivit:

1. Vyberte profil front-dveří.

1. Vyberte **Protokol aktivit.**

1. Zvolte rozsah filtrování a pak vyberte **použít**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [sestavách služby Azure front-end Standard/Premium (Preview)](how-to-reports.md).
- Přečtěte si o [metrikách monitorování v reálném čase na Azure front-end Standard/Premium (Preview)](how-to-monitor-metrics.md).
