---
title: Azure Application Gateway brány firewall webových aplikací CRS a skupin pravidel CRS pravidla
description: Tato stránka obsahuje informace o skupin pravidel CRS firewallu webových aplikací a pravidla.
documentationcenter: na
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 4/16/2018
ms.author: victorh
ms.openlocfilehash: 15a86410e8ca853c2ca2431cb9a62de628972703
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320097"
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Nabízí seznam skupin pravidel CRS firewallu webových aplikací a pravidla

Firewall webových aplikací brány aplikací (WAF) chrání webové aplikace z nejběžnějších ohrožení zabezpečení a zneužitím. To se provádí prostřednictvím pravidla, které jsou definovány podle základní sady pravidel OWASP 2.2.9 nebo 3.0. Tato pravidla je možné zakázat na základě pravidla pomocí pravidla. Tento článek obsahuje aktuální pravidla a nabízí sady pravidel.

V následujících tabulkách jsou skupiny pravidel a pravidla, která jsou k dispozici při použití aplikační brány s firewallem webových aplikací.  Každá tabulka představuje nenašla ve skupině pravidlo na konkrétní verzi CRS pravidla.

## <a name="owasp30"></a> OWASP_3.0


### <a name="crs911"></a> <p x-ms-format-detection="none">ŽÁDOST O 911 ZPŮSOB VYNUCENÍ</p>

|RuleId|Popis|
|---|---|
|911011|Pravidlo 911011|
|911012|Pravidlo 911012|
|911100|Metoda není povolena zásadami|
|911013|Pravidlo 911013|
|911014|Pravidlo 911014|
|911015|Pravidlo 911015|
|911016|Pravidlo 911016|
|911017|Pravidlo 911017|
|911018|Pravidlo 911018|


### <a name="crs913"></a> <p x-ms-format-detection="none">ŽÁDOST O 913 SKENER DETEKCE</p>

|RuleId|Popis|
|---|---|
|913011|Pravidlo 913011|
|913012|Pravidlo 913012|
|913100|Najít User-Agent přidružené kontrolu zabezpečení|
|913110|Najít záhlaví požadavku přidružený k kontrolu zabezpečení|
|913120|Nalezena žádost o název souboru/argument přidružený k kontrolu zabezpečení|
|913013|Pravidlo 913013|
|913014|Pravidlo 913014|
|913101|Najít související s klientem HTTP/generické uživatelského agenta|
|913102|Najít přidružené webové prohledávacího modulu/bot uživatelského agenta|
|913015|Pravidlo 913015|
|913016|Pravidlo 913016|
|913017|Pravidlo 913017|
|913018|Pravidlo 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">ŽÁDOST O 920 PROTOKOL VYNUCENÍ</p>

|RuleId|Popis|
|---|---|
|920011|Pravidlo 920011|
|920012|Pravidlo 920012|
|920100|Neplatný požadavek HTTP řádku|
|920130|Nepovedlo se analyzovat datovou část požadavku.|
|920140|S více částmi. žádost subjektu selhala přísný ověřovací = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     SOUBOR %@{MULTIPART_FILE_LIMIT_EXCEEDED %@{MULTIPART_INVALID_HEADER_FOLDING IH %@{MULTIPART_INVALID_QUOTING IQ SM %@{MULTIPART_SEMICOLON_MISSING}}}}|
|920160|Hlavičku HTTP Content-Length není číselná.|
|920170|ZÍSKÁNÍ nebo HEAD žádost s obsah textu.|
|920180|Chybí hlavička Content-Length požadavek POST.|
|920190|Rozsah = poslední bajt je neplatný.|
|920210|Více/konfliktní Data záhlaví připojení nenašel.|
|920220|Pokus o útok URL kódování urážlivého příspěvku|
|920240|Pokus o útok URL kódování urážlivého příspěvku|
|920250|UTF8 Pokus o útok zneužití kódování|
|920260|Pokus o útok Unicode plné/poloviční šířku urážlivý příspěvek|
|920270|Neplatný znak v požadavku (znak null)|
|920280|Požadavku chybí hlavička hostitele|
|920290|Hlavička hostitele prázdný|
|920310|Požadavek má prázdnou hlavičky Accept|
|920311|Požadavek má prázdnou hlavičky Accept|
|920330|Záhlaví prázdného uživatelského agenta|
|920340|Požádat o obsahující obsahu, ale hlavička Content-Type chybí|
|920350|Hlavička hostitele je číselný IP adresa|
|920380|V požadavku má moc argumentů|
|920360|Příliš dlouhý název argumentu|
|920370|Hodnota argumentu je příliš dlouhý|
|920390|Překročila se velikost celkový počet argumentů|
|920400|Nahraný soubor příliš velký.|
|920410|Celkový počet odeslaných souborů příliš velký.|
|920420|Typ obsahu požadavku není povolena zásadami|
|920430|Verze protokolu HTTP není povolena zásadami|
|920440|Adresa URL přípona souboru je omezena zásadami|
|920450|Hlavička protokolu HTTP je omezený zásad (%@{MATCHED_VAR})|
|920013|Pravidlo 920013|
|920014|Pravidlo 920014|
|920200|Rozsah = příliš mnoho polí (6 nebo více)|
|920201|Rozsah = příliš mnoho polí pro požadavek pdf (35 nebo více)|
|920230|Zjištěn více kódování URL|
|920300|Požadavku chybí hlavičky Accept|
|920271|Neplatný znak v požadavku (bez tisknutelných znaků)|
|920320|Chybí hlavička uživatelského agenta|
|920015|Pravidlo 920015|
|920016|Pravidlo 920016|
|920272|Neplatný znak v požadavku (mimo tisknutelných znaků pod ascii 127)|
|920017|Pravidlo 920017|
|920018|Pravidlo 920018|
|920202|Rozsah = příliš mnoho polí pro požadavek pdf (6 nebo více)|
|920273|Neplatný znak v požadavku (mimo velmi přísná set)|
|920274|Neplatný znak v záhlaví požadavku (mimo velmi přísná set)|
|920460|Pravidlo 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">ŽÁDOST O 921 PROTOKOL ÚTOKU</p>

|RuleId|Popis|
|---|---|
|921011|Pravidlo 921011|
|921012|Pravidlo 921012|
|921100|Útok podvržení požadavku HTTP.|
|921110|Požadavek HTTP pašování útoku|
|921120|Odpověď HTTP, rozdělování útoku|
|921130|Odpověď HTTP, rozdělování útoku|
|921140|Útok prostřednictvím injektáže záhlaví HTTP přes záhlaví|
|921150|Útok prostřednictvím injektáže záhlaví HTTP přes datová část (zjistil znaků CR/LF)|
|921160|Útok prostřednictvím injektáže záhlaví HTTP přes datová část (CR/LF a zjištěna název hlavičky)|
|921013|Pravidlo 921013|
|921014|Pravidlo 921014|
|921151|Útok prostřednictvím injektáže záhlaví HTTP přes datová část (zjistil znaků CR/LF)|
|921015|Pravidlo 921015|
|921016|Pravidlo 921016|
|921170|Pravidlo 921170|
|921180|Znečištění HTTP parametr (% @{TX.1})|
|921017|Pravidlo 921017|
|921018|Pravidlo 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">ŽÁDOST-930 – APLIKACE ÚTOKU LFI</p>

|RuleId|Popis|
|---|---|
|930011|Pravidlo 930011|
|930012|Pravidlo 930012|
|930100|Cesty přechodu útoku (/.. /)|
|930110|Cesty přechodu útoku (/.. /)|
|930120|Pokus o přístup k souboru operačního systému|
|930130|Pokus o přístup k souboru s omezeným přístupem|
|930013|Pravidlo 930013|
|930014|Pravidlo 930014|
|930015|Pravidlo 930015|
|930016|Pravidlo 930016|
|930017|Pravidlo 930017|
|930018|Pravidlo 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">ŽÁDOST-931 – APLIKACE ÚTOKU RFI</p>

|RuleId|Popis|
|---|---|
|931011|Pravidlo 931011|
|931012|Pravidlo 931012|
|931100|Možných útoků zahrnutí vzdáleného souboru (RFI) = parametr adresy URL pomocí IP adresy|
|931110|Možných útoků zahrnutí vzdáleného souboru (RFI) = běžný RFI zranitelné parametr název používaný w nebo adresa URL datové části|
|931120|Je to možné vzdáleného souboru (RFI) útok = adresy URL datové části používá w a koncové otázku Označit znak (?)|
|931013|Pravidlo 931013|
|931014|Pravidlo 931014|
|931130|Je to možné vzdáleného souboru (RFI) útok = vypnout domény odkazu|
|931015|Pravidlo 931015|
|931016|Pravidlo 931016|
|931017|Pravidlo 931017|
|931018|Pravidlo 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">ŽÁDOST-932 – APLIKACE ÚTOKU RCE</p>

|RuleId|Popis|
|---|---|
|932011|Pravidlo 932011|
|932012|Pravidlo 932012|
|932120|Vzdálené spouštění příkazů Windows Powershellu příkaz nenašel =|
|932130|Spuštění vzdálených příkazů = výraz prostředí Unix nalezen|
|932140|Spuštění vzdálených příkazů = Windows pro, nebo pokud se najde příkaz|
|932160|Vzdálené spouštění příkazů prostředí Unix kód =|
|932170|Spuštění vzdálených příkazů = Shellshock (CVE 2014 6271)|
|932171|Spuštění vzdálených příkazů = Shellshock (CVE 2014 6271)|
|932013|Pravidlo 932013|
|932014|Pravidlo 932014|
|932015|Pravidlo 932015|
|932016|Pravidlo 932016|
|932017|Pravidlo 932017|
|932018|Pravidlo 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">ŽÁDOST-933 – APLIKACE ÚTOKU – PHP</p>

|RuleId|Popis|
|---|---|
|933011|Pravidlo 933011|
|933012|Pravidlo 933012|
|933100|PHP injektáže = nalezena otevírací nebo uzavírací značka|
|933110|PHP injektáže = nalezen nahrávání souborů skriptů PHP|
|933120|PHP injektáže = direktivě konfiguraci nalezen|
|933130|PHP injektáže = proměnné nalezen|
|933150|PHP injektáže = název funkce s vysokým rizikem PHP nalezen.|
|933160|PHP injektáže = volání funkce PHP s vysokým rizikem, nalezena|
|933180|PHP injektáže = proměnné funkce volání nalezen|
|933013|Pravidlo 933013|
|933014|Pravidlo 933014|
|933151|PHP injektáže = najít název funkce PHP střední riziko|
|933015|Pravidlo 933015|
|933016|Pravidlo 933016|
|933131|PHP injektáže = proměnné nalezen|
|933161|PHP injektáže = PHP s nízkou hodnotu volání funkce nalezen|
|933111|PHP injektáže = nalezen nahrávání souborů skriptů PHP|
|933017|Pravidlo 933017|
|933018|Pravidlo 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">ŽÁDOST-941 – APLIKACE ÚTOKU XSS</p>

|RuleId|Popis|
|---|---|
|941011|Pravidlo 941011|
|941012|Pravidlo 941012|
|941100|XSS útoku zjištěna prostřednictvím libinjection|
|941110|Filtr XSS – kategorie 1 = Vector značky skriptu|
|941130|Filtr XSS – kategorie 3 = atribut vektoru|
|941140|Filtr XSS – kategorie 4 = Vector URI jazyka Javascript|
|941150|Filtr XSS – kategorie 5 = atributů zakázaného HTML|
|941180|Uzel ověření Blacklist klíčová slova|
|941190|Filtruje - IE XSS zjistil útoku.|
|941200|Filtruje - IE XSS zjistil útoku.|
|941210|Filtruje - IE XSS zjistil útoku.|
|941220|Filtruje - IE XSS zjistil útoku.|
|941230|Filtruje - IE XSS zjistil útoku.|
|941240|Filtruje - IE XSS zjistil útoku.|
|941260|Filtruje - IE XSS zjistil útoku.|
|941270|Filtruje - IE XSS zjistil útoku.|
|941280|Filtruje - IE XSS zjistil útoku.|
|941290|Filtruje - IE XSS zjistil útoku.|
|941300|Filtruje - IE XSS zjistil útoku.|
|941310|US ASCII poškozený kódování XSS filtru – zjištěna útoku.|
|941350|Kódování UTF-7 kódování aplikace Internet Explorer XSS - zjistil útoku.|
|941013|Pravidlo 941013|
|941014|Pravidlo 941014|
|941320|Je to možné XSS útoku zjistil - obslužnou rutinu značky HTML|
|941015|Pravidlo 941015|
|941016|Pravidlo 941016|
|941017|Pravidlo 941017|
|941018|Pravidlo 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">ŽÁDOST-942 – APLIKACE ÚTOKU SQLI</p>

|RuleId|Popis|
|---|---|
|942011|Pravidlo 942011|
|942012|Pravidlo 942012|
|942100|SQL zjistila útoku prostřednictvím injektáže prostřednictvím libinjection|
|942140|Útok prostřednictvím injektáže SQL = běžné názvy DB zjistil|
|942160|Zjistí skrytá sqli testy pomocí sleep() nebo benchmark().|
|942170|Detekuje pokusy o srovnávací test a přejít do režimu spánku útok prostřednictvím injektáže SQL, včetně podmíněného dotazů|
|942230|Zjistí podmíněného pokusu o Injektáž SQL|
|942270|Hledáte základní sql injection. Běžné útoku řetězec pro mysql oracle a dalších.|
|942290|Vyhledá pokusu o Injektáž SQL základní MongoDB|
|942320|Zjistí, MySQL a PostgreSQL uložené procedury/funkce injektáže|
|942350|Zjistí vkládání MySQL UDF a manipulaci s další data nebo strukturu pokusí|
|942013|Pravidlo 942013|
|942014|Pravidlo 942014|
|942150|Útok prostřednictvím injektáže SQL|
|942410|Útok prostřednictvím injektáže SQL|
|942440|Zjištěna pořadí Komentář SQL.|
|942450|SQL šestnáctkové kódování identifikovat|
|942015|Pravidlo 942015|
|942016|Pravidlo 942016|
|942251|Zjistí injektáže HAVING|
|942460|Výstraha detekce anomálií meta znak – opakované mimoslovní znaky|
|942017|Pravidlo 942017|
|942018|Pravidlo 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Popis|
|---|---|
|943011|Pravidlo 943011|
|943012|Pravidlo 943012|
|943100|Útok záznam možné relace = nastavení hodnoty souboru Cookie ve formátu HTML|
|943110|Útok záznam možné relace = název parametru ID relace s odkazující server vypnout domény|
|943120|Útok záznam možné relace = název parametru ID relace se žádné odkazující server|
|943013|Pravidlo 943013|
|943014|Pravidlo 943014|
|943015|Pravidlo 943015|
|943016|Pravidlo 943016|
|943017|Pravidlo 943017|
|943018|Pravidlo 943018|

## <a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|Popis|
|---|---|
|960911|Neplatný požadavek HTTP řádku|
|981227|Chyba Apache = neplatný identifikátor URI požadavku.|
|960912|Nepovedlo se analyzovat datovou část požadavku.|
|960914|S více částmi. žádost subjektu selhala přísný ověřovací = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     SOUBOR %@{MULTIPART_FILE_LIMIT_EXCEEDED %@{MULTIPART_INVALID_HEADER_FOLDING IH %@{MULTIPART_INVALID_QUOTING IQ SM %@{MULTIPART_SEMICOLON_MISSING}}}}|
|960915|S více částmi. analyzátor zjistil možné bezkonkurenční hranice.|
|960016|Hlavičku HTTP Content-Length není číselná.|
|960011|ZÍSKÁNÍ nebo HEAD žádost s obsah textu.|
|960012|Chybí hlavička Content-Length požadavek POST.|
|960902|Neplatné použití Identity kódování.|
|960022|Můžete očekávejte záhlaví není povolený pro protokol HTTP 1.0.|
|960020|Záhlaví – Direktiva pragma vyžaduje hlavičku Cache-Control pro požadavky HTTP/1.1.|
|958291|Rozsah = pole existuje a začíná na 0.|
|958230|Rozsah = poslední bajt je neplatný.|
|958295|Více/konfliktní Data záhlaví připojení nenašel.|
|950107|Pokus o útok URL kódování urážlivého příspěvku|
|950109|Zjištěn více kódování URL|
|950108|Pokus o útok URL kódování urážlivého příspěvku|
|950801|UTF8 Pokus o útok zneužití kódování|
|950116|Pokus o útok Unicode plné/poloviční šířku urážlivý příspěvek|
|960901|Neplatný znak v požadavku|
|960018|Neplatný znak v požadavku|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Popis|
|---|---|
|960008|Požadavku chybí hlavička hostitele|
|960007|Hlavička hostitele prázdný|
|960015|Požadavku chybí hlavičky Accept|
|960021|Požadavek má prázdnou hlavičky Accept|
|960009|Požadavku chybí záhlaví Agent uživatele|
|960006|Záhlaví prázdného uživatelského agenta|
|960904|Požádat o obsahující obsahu, ale hlavička Content-Type chybí|
|960017|Hlavička hostitele je číselný IP adresa|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|Popis|
|---|---|
|960209|Příliš dlouhý název argumentu|
|960208|Hodnota argumentu je příliš dlouhý|
|960335|V požadavku má moc argumentů|
|960341|Překročila se velikost celkový počet argumentů|
|960342|Nahraný soubor příliš velký.|
|960343|Celkový počet odeslaných souborů příliš velký.|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|Popis|
|---|---|
|960032|Metoda není povolena zásadami|
|960010|Typ obsahu požadavku není povolena zásadami|
|960034|Verze protokolu HTTP není povolena zásadami|
|960035|Adresa URL přípona souboru je omezena zásadami|
|960038|Hlavička protokolu HTTP je omezen pomocí zásad|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|Popis|
|---|---|
|990002|Žádost o označuje, že kontrola zabezpečení zkontrolovat webu|
|990901|Žádost o označuje, že kontrola zabezpečení zkontrolovat webu|
|990902|Žádost o označuje, že kontrola zabezpečení zkontrolovat webu|
|990012|Neautorizovaný webu prohledávacího modulu|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|Popis|
|---|---|
|960024|Výstraha detekce anomálií meta znak – opakované mimoslovní znaky|
|950008|Vkládání značky aplikace nezdokumentovaný ColdFusion|
|950010|Útok prostřednictvím injektáže LDAP|
|950011|Vkládání SSI útoku|
|950018|Adresa URL univerzální XSS PDF zjištěna.|
|950019|Útok prostřednictvím injektáže e-mailu|
|950012|Útok podvržení požadavku HTTP.|
|950910|Odpověď HTTP, rozdělování útoku|
|950911|Odpověď HTTP, rozdělování útoku|
|950117|Vzdálený soubor zahrnutí útoku|
|950118|Vzdálený soubor zahrnutí útoku|
|950119|Vzdálený soubor zahrnutí útoku|
|950120|Je to možné vzdáleného souboru (RFI) útok = vypnout domény odkazu|
|981133|Pravidlo 981133|
|981134|Pravidlo 981134|
|950009|Útok záznam relace|
|950003|Záznam relace|
|950000|Záznam relace|
|950005|Vzdálený soubor k pokusu o přístup|
|950002|Přístup k systému příkaz|
|950006|Injektáž příkazů systému|
|959151|Útok prostřednictvím injektáže PHP|
|958976|Útok prostřednictvím injektáže PHP|
|958977|Útok prostřednictvím injektáže PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Popis|
|---|---|
|981231|Zjištěna pořadí Komentář SQL.|
|981260|SQL šestnáctkové kódování identifikovat|
|981320|Útok prostřednictvím injektáže SQL = běžné názvy DB zjistil|
|981300|Pravidlo 981300|
|981301|Pravidlo 981301|
|981302|Pravidlo 981302|
|981303|Pravidlo 981303|
|981304|Pravidlo 981304|
|981305|Pravidlo 981305|
|981306|Pravidlo 981306|
|981307|Pravidlo 981307|
|981308|Pravidlo 981308|
|981309|Pravidlo 981309|
|981310|Pravidlo 981310|
|981311|Pravidlo 981311|
|981312|Pravidlo 981312|
|981313|Pravidlo 981313|
|981314|Pravidlo 981314|
|981315|Pravidlo 981315|
|981316|Pravidlo 981316|
|981317|Výstrahy detekce anomálií SQL příkazu SELECT|
|950007|Útok prostřednictvím injektáže SQL skrytá|
|950001|Útok prostřednictvím injektáže SQL|
|950908|Útok prostřednictvím injektáže SQL.|
|959073|Útok prostřednictvím injektáže SQL|
|981272|Zjistí skrytá sqli testy pomocí sleep() nebo benchmark().|
|981250|Detekuje pokusy o srovnávací test a přejít do režimu spánku útok prostřednictvím injektáže SQL, včetně podmíněného dotazů|
|981241|Zjistí podmíněného pokusu o Injektáž SQL|
|981276|Hledáte základní sql injection. Běžné útoku řetězec pro mysql oracle a dalších.|
|981270|Vyhledá pokusu o Injektáž SQL základní MongoDB|
|981253|Zjistí, MySQL a PostgreSQL uložené procedury/funkce injektáže|
|981251|Zjistí vkládání MySQL UDF a manipulaci s další data nebo strukturu pokusí|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Popis|
|---|---|
|973336|Filtr XSS – kategorie 1 = Vector značky skriptu|
|973338|Filtr XSS – kategorie 3 = Vector URI jazyka Javascript|
|981136|Pravidlo 981136|
|981018|Pravidlo 981018|
|958016|Útoky skriptování napříč weby (XSS)|
|958414|Útoky skriptování napříč weby (XSS)|
|958032|Útoky skriptování napříč weby (XSS)|
|958026|Útoky skriptování napříč weby (XSS)|
|958027|Útoky skriptování napříč weby (XSS)|
|958054|Útoky skriptování napříč weby (XSS)|
|958418|Útoky skriptování napříč weby (XSS)|
|958034|Útoky skriptování napříč weby (XSS)|
|958019|Útoky skriptování napříč weby (XSS)|
|958013|Útoky skriptování napříč weby (XSS)|
|958408|Útoky skriptování napříč weby (XSS)|
|958012|Útoky skriptování napříč weby (XSS)|
|958423|Útoky skriptování napříč weby (XSS)|
|958002|Útoky skriptování napříč weby (XSS)|
|958017|Útoky skriptování napříč weby (XSS)|
|958007|Útoky skriptování napříč weby (XSS)|
|958047|Útoky skriptování napříč weby (XSS)|
|958410|Útoky skriptování napříč weby (XSS)|
|958415|Útoky skriptování napříč weby (XSS)|
|958022|Útoky skriptování napříč weby (XSS)|
|958405|Útoky skriptování napříč weby (XSS)|
|958419|Útoky skriptování napříč weby (XSS)|
|958028|Útoky skriptování napříč weby (XSS)|
|958057|Útoky skriptování napříč weby (XSS)|
|958031|Útoky skriptování napříč weby (XSS)|
|958006|Útoky skriptování napříč weby (XSS)|
|958033|Útoky skriptování napříč weby (XSS)|
|958038|Útoky skriptování napříč weby (XSS)|
|958409|Útoky skriptování napříč weby (XSS)|
|958001|Útoky skriptování napříč weby (XSS)|
|958005|Útoky skriptování napříč weby (XSS)|
|958404|Útoky skriptování napříč weby (XSS)|
|958023|Útoky skriptování napříč weby (XSS)|
|958010|Útoky skriptování napříč weby (XSS)|
|958411|Útoky skriptování napříč weby (XSS)|
|958422|Útoky skriptování napříč weby (XSS)|
|958036|Útoky skriptování napříč weby (XSS)|
|958000|Útoky skriptování napříč weby (XSS)|
|958018|Útoky skriptování napříč weby (XSS)|
|958406|Útoky skriptování napříč weby (XSS)|
|958040|Útoky skriptování napříč weby (XSS)|
|958052|Útoky skriptování napříč weby (XSS)|
|958037|Útoky skriptování napříč weby (XSS)|
|958049|Útoky skriptování napříč weby (XSS)|
|958030|Útoky skriptování napříč weby (XSS)|
|958041|Útoky skriptování napříč weby (XSS)|
|958416|Útoky skriptování napříč weby (XSS)|
|958024|Útoky skriptování napříč weby (XSS)|
|958059|Útoky skriptování napříč weby (XSS)|
|958417|Útoky skriptování napříč weby (XSS)|
|958020|Útoky skriptování napříč weby (XSS)|
|958045|Útoky skriptování napříč weby (XSS)|
|958004|Útoky skriptování napříč weby (XSS)|
|958421|Útoky skriptování napříč weby (XSS)|
|958009|Útoky skriptování napříč weby (XSS)|
|958025|Útoky skriptování napříč weby (XSS)|
|958413|Útoky skriptování napříč weby (XSS)|
|958051|Útoky skriptování napříč weby (XSS)|
|958420|Útoky skriptování napříč weby (XSS)|
|958407|Útoky skriptování napříč weby (XSS)|
|958056|Útoky skriptování napříč weby (XSS)|
|958011|Útoky skriptování napříč weby (XSS)|
|958412|Útoky skriptování napříč weby (XSS)|
|958008|Útoky skriptování napříč weby (XSS)|
|958046|Útoky skriptování napříč weby (XSS)|
|958039|Útoky skriptování napříč weby (XSS)|
|958003|Útoky skriptování napříč weby (XSS)|
|973300|Je to možné XSS útoku zjistil - obslužnou rutinu značky HTML|
|973301|Útoky XSS zjistil|
|973302|Útoky XSS zjistil|
|973303|Útoky XSS zjistil|
|973304|Útoky XSS zjistil|
|973305|Útoky XSS zjistil|
|973306|Útoky XSS zjistil|
|973307|Útoky XSS zjistil|
|973308|Útoky XSS zjistil|
|973309|Útoky XSS zjistil|
|973311|Útoky XSS zjistil|
|973313|Útoky XSS zjistil|
|973314|Útoky XSS zjistil|
|973331|Filtruje - IE XSS zjistil útoku.|
|973315|Filtruje - IE XSS zjistil útoku.|
|973330|Filtruje - IE XSS zjistil útoku.|
|973327|Filtruje - IE XSS zjistil útoku.|
|973326|Filtruje - IE XSS zjistil útoku.|
|973346|Filtruje - IE XSS zjistil útoku.|
|973345|Filtruje - IE XSS zjistil útoku.|
|973324|Filtruje - IE XSS zjistil útoku.|
|973323|Filtruje - IE XSS zjistil útoku.|
|973348|Filtruje - IE XSS zjistil útoku.|
|973321|Filtruje - IE XSS zjistil útoku.|
|973320|Filtruje - IE XSS zjistil útoku.|
|973318|Filtruje - IE XSS zjistil útoku.|
|973317|Filtruje - IE XSS zjistil útoku.|
|973329|Filtruje - IE XSS zjistil útoku.|
|973328|Filtruje - IE XSS zjistil útoku.|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|Popis|
|---|---|
|950103|Cesty přechodu útoku|

### <a name="crs45"></a> crs_45_trojans

|RuleId|Popis|
|---|---|
|950110|Přístup přes zadní vrátka|
|950921|Přístup přes zadní vrátka|
|950922|Přístup přes zadní vrátka|

## <a name="next-steps"></a>Další postup

Zjistěte, jak zakázat pravidla firewallu webových aplikací návštěvou: [pravidla firewallu webových aplikací přizpůsobení](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png
