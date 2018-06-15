---
title: Azure Application Gateway brány firewall webových aplikací řádku pravidel skupiny a pravidel
description: Tato stránka obsahuje informace o webové aplikaci brány firewall řádku pravidlo skupiny a pravidel.
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
ms.openlocfilehash: 5ba291eaa93a48e3aadc11b1f0f7b48b01683b07
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31515522"
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Nabízí seznam webových aplikací brány firewall řádku pravidlo skupiny a pravidel

Brány firewall webových aplikací aplikace brány (firewall webových aplikací) chrání webových aplikací ze známých chyb zabezpečení a zneužití. To se provádí prostřednictvím pravidel, která jsou definována podle sady pravidel základní OWASP 2.2.9 nebo 3.0. Tato pravidla lze vypnout na základě podle pravidla. Tento článek obsahuje aktuální pravidla a sady pravidel, které nabízí.

V následujících tabulkách jsou skupiny pravidel a pravidel, které jsou k dispozici při Application Gateway pomocí brány firewall webových aplikací.  Každá tabulka představuje pravidel ve skupině pravidlo na konkrétní verzi řádku.

## <a name="owasp30"></a> OWASP_3.0


### <a name="crs911"></a> <p x-ms-format-detection="none">ŽÁDOST O 911 METODA VYNUCENÍ</p>

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


### <a name="crs913"></a> <p x-ms-format-detection="none">ŽÁDOST O 913 SKENER ZJIŠTĚNÍ</p>

|RuleId|Popis|
|---|---|
|913011|Pravidlo 913011|
|913012|Pravidlo 913012|
|913100|Najít User-Agent přidružené kontrolu zabezpečení|
|913110|Najít hlavičky žádosti přidružené kontrolu zabezpečení|
|913120|Nalezena žádost o název souboru nebo argument přidružené kontrolu zabezpečení|
|913013|Pravidlo 913013|
|913014|Pravidlo 913014|
|913101|Najít User-Agent přidružené klienta HTTP/obecné|
|913102|Najít User-Agent přidružené webové prohledávacího modulu nebo robota|
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
|920130|Nepodařilo se analyzovat textu požadavku.|
|920140|S více částmi žádost textu se nezdařila striktní ověření = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} VF %@{MULTIPART_HEADER_FOLDING} LF %@{MULTIPART_LF_LINE} SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} vhodné %@{MULTIPART_INVALID_HEADER_FOLDING} %@{MULTIPART_FILE_LIMIT_EXCEEDED soubor}|
|920160|Hlavičku HTTP Content-Length není číslo.|
|920170|GET nebo HEAD žádost s obsah textu.|
|920180|Požadavek POST chybí hlavičku Content-Length.|
|920190|Rozsah = neplatný poslední hodnotu bajtů.|
|920210|Více/konfliktní Data záhlaví připojení nalezena.|
|920220|Pokus o útok URL kódování zneužití|
|920240|Pokus o útok URL kódování zneužití|
|920250|UTF8 Pokus o útok zneužití kódování|
|920260|Pokus o útok Unicode plné a poloviční šířky zneužití|
|920270|Neplatný znak v požadavku (znak hodnoty null)|
|920280|Žádosti chybí hlavička hostitele|
|920290|Hlavička hostitele prázdný|
|920310|Žádost obsahuje prázdnou hlavičky Accept|
|920311|Žádost obsahuje prázdnou hlavičky Accept|
|920330|Záhlaví Agent prázdný uživatele|
|920340|Žádosti o obsahující obsahu, ale chybí záhlaví typu obsahu|
|920350|Hlavička hostitele je číselné IP adresa|
|920380|Příliš mnoho argumentů v požadavku|
|920360|Argument název je příliš dlouhý|
|920370|Hodnota argumentu příliš dlouhý|
|920390|Celkový počet argumentů velikost překročen|
|920400|Nahrávaný soubor příliš velký.|
|920410|Celkový počet odeslaných souborů příliš velký.|
|920420|Typ obsahu požadavku není povolen zásadami|
|920430|Verzi protokolu HTTP není povolena zásad|
|920440|Adresa URL souboru rozšíření je omezené na základě zásad|
|920450|Hlavičky protokolu HTTP je omezené na základě zásad (%@{MATCHED_VAR})|
|920013|Pravidlo 920013|
|920014|Pravidlo 920014|
|920200|Rozsah = příliš mnoho polí (6 nebo více)|
|920201|Rozsah = příliš mnoho polí pro požadavek pdf (35 nebo více)|
|920230|Zjištěno více kódování URL|
|920300|Žádosti chybí hlavičky Accept|
|920271|Neplatný znak v požadavku (bez tisknutelná znaky)|
|920320|Chybí záhlaví agenta uživatele|
|920015|Pravidlo 920015|
|920016|Pravidlo 920016|
|920272|Neplatný znak v požadavku (mimo tisknutelná znaků nižší než 127 znaků ascii)|
|920017|Pravidlo 920017|
|920018|Pravidlo 920018|
|920202|Rozsah = příliš mnoho polí pro požadavek pdf (6 nebo více)|
|920273|Neplatný znak v požadavku (mimo velmi přísná set)|
|920274|Neplatný znak v hlavičkách žádosti (mimo velmi přísná set)|
|920460|Pravidlo 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">ŽÁDOST O 921 PROTOKOL ÚTOK</p>

|RuleId|Popis|
|---|---|
|921011|Pravidlo 921011|
|921012|Pravidlo 921012|
|921100|Podvržení útoku požadavku HTTP.|
|921110|Útok pašování požadavku HTTP|
|921120|Útok rozdělení odpovědi HTTP|
|921130|Útok rozdělení odpovědi HTTP|
|921140|Útok prostřednictvím injektáže hlavičky protokolu HTTP přes hlavičky|
|921150|Útok prostřednictvím injektáže hlavičky protokolu HTTP přes datová část (zjistil CR/LF)|
|921160|Útok prostřednictvím injektáže hlavičky protokolu HTTP přes datová část (CR/LF a zjistil název hlavičky)|
|921013|Pravidlo 921013|
|921014|Pravidlo 921014|
|921151|Útok prostřednictvím injektáže hlavičky protokolu HTTP přes datová část (zjistil CR/LF)|
|921015|Pravidlo 921015|
|921016|Pravidlo 921016|
|921170|Pravidlo 921170|
|921180|Znečištění HTTP parametr (% @{TX.1})|
|921017|Pravidlo 921017|
|921018|Pravidlo 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">ŽÁDOST O-930APLIKACE ÚTOKU LFI</p>

|RuleId|Popis|
|---|---|
|930011|Pravidlo 930011|
|930012|Pravidlo 930012|
|930100|Cesta k procházení útoku (/... /)|
|930110|Cesta k procházení útoku (/... /)|
|930120|Pokus o přístup k souboru operačního systému|
|930130|Pokus o přístup k souboru s omezeným přístupem|
|930013|Pravidlo 930013|
|930014|Pravidlo 930014|
|930015|Pravidlo 930015|
|930016|Pravidlo 930016|
|930017|Pravidlo 930017|
|930018|Pravidlo 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">ŽÁDOST O-931APLIKACE ÚTOKU RFI</p>

|RuleId|Popis|
|---|---|
|931011|Pravidlo 931011|
|931012|Pravidlo 931012|
|931100|Možný útok zahrnutí vzdáleného souboru (RFI) = parametr URL pomocí IP adresy|
|931110|Možný útok zahrnutí vzdáleného souboru (RFI) = běžný RFI ohrožené parametr název používá w nebo adresa URL datové části|
|931120|Možné vzdáleného souboru zahrnutí (RFI) útoku = adresu URL datové části použít koncové/w otázku označit znaku (?)|
|931013|Pravidlo 931013|
|931014|Pravidlo 931014|
|931130|Možné vzdáleného souboru zahrnutí (RFI) útoku = Off domény odkazem|
|931015|Pravidlo 931015|
|931016|Pravidlo 931016|
|931017|Pravidlo 931017|
|931018|Pravidlo 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">ŽÁDOST O-932APLIKACE ÚTOKU RCE</p>

|RuleId|Popis|
|---|---|
|932011|Pravidlo 932011|
|932012|Pravidlo 932012|
|932120|Vzdálené spouštění příkazů = nalezen příkaz prostředí PowerShell systému Windows|
|932130|Vzdálené spouštění příkazů = výraz prostředí Unix nalezen|
|932140|Vzdálené spouštění příkazů = Windows pro / Pokud příkaz nalezen|
|932160|Vzdálené spouštění příkazů = kód prostředí Unix nalezen|
|932170|Vzdálené spouštění příkazů = Shellshock (CVE 2014 6271)|
|932171|Vzdálené spouštění příkazů = Shellshock (CVE 2014 6271)|
|932013|Pravidlo 932013|
|932014|Pravidlo 932014|
|932015|Pravidlo 932015|
|932016|Pravidlo 932016|
|932017|Pravidlo 932017|
|932018|Pravidlo 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">ŽÁDOST O-933APLIKACE ÚTOKU PHP</p>

|RuleId|Popis|
|---|---|
|933011|Pravidlo 933011|
|933012|Pravidlo 933012|
|933100|Útok prostřednictvím injektáže PHP = otevírání/uzavírací značku nalezen|
|933110|Útok prostřednictvím injektáže PHP = nahrávání souborů skriptu PHP nalezen|
|933120|Útok prostřednictvím injektáže PHP = direktiva konfigurace nalezen|
|933130|Útok prostřednictvím injektáže PHP = proměnné nalezen|
|933150|Útok prostřednictvím injektáže PHP = název funkce s vysokým rizikem PHP nalezen.|
|933160|Útok prostřednictvím injektáže PHP = volání funkce s vysokým rizikem PHP nalezen|
|933180|Útok prostřednictvím injektáže PHP = volání proměnné funkce Najít|
|933013|Pravidlo 933013|
|933014|Pravidlo 933014|
|933151|Útok prostřednictvím injektáže PHP = název funkce střední riziko PHP nalezen|
|933015|Pravidlo 933015|
|933016|Pravidlo 933016|
|933131|Útok prostřednictvím injektáže PHP = proměnné nalezen|
|933161|Útok prostřednictvím injektáže PHP = nízká hodnota PHP volání funkce Najít|
|933111|Útok prostřednictvím injektáže PHP = nahrávání souborů skriptu PHP nalezen|
|933017|Pravidlo 933017|
|933018|Pravidlo 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">ŽÁDOST O-941APLIKACE ÚTOKU XSS</p>

|RuleId|Popis|
|---|---|
|941011|Pravidlo 941011|
|941012|Pravidlo 941012|
|941100|XSS útoku zjištěna prostřednictvím libinjection|
|941110|Filtr XSS – kategorie 1 = Vector značky skriptu|
|941130|Filtr XSS – kategorie 3 = Vector atribut|
|941140|Filtr XSS – kategorie 4 = Vector Javascript URI|
|941150|Filtr XSS – kategorie 5 = atributy nepovoleném HTML|
|941180|Uzel ověření zakázaných klíčová slova|
|941190|Filtry IE XSS - útoku zjištěna.|
|941200|Filtry IE XSS - útoku zjištěna.|
|941210|Filtry IE XSS - útoku zjištěna.|
|941220|Filtry IE XSS - útoku zjištěna.|
|941230|Filtry IE XSS - útoku zjištěna.|
|941240|Filtry IE XSS - útoku zjištěna.|
|941260|Filtry IE XSS - útoku zjištěna.|
|941270|Filtry IE XSS - útoku zjištěna.|
|941280|Filtry IE XSS - útoku zjištěna.|
|941290|Filtry IE XSS - útoku zjištěna.|
|941300|Filtry IE XSS - útoku zjištěna.|
|941310|US ASCII poškozený kódování XSS filtr - útoku zjištěna.|
|941350|Znakové sady UTF-7 kódování IE XSS - útoku zjištěna.|
|941013|Pravidlo 941013|
|941014|Pravidlo 941014|
|941320|Zjištění útoku možné XSS - obslužná rutina značky HTML|
|941015|Pravidlo 941015|
|941016|Pravidlo 941016|
|941017|Pravidlo 941017|
|941018|Pravidlo 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">ŽÁDOST O-942APLIKACE ÚTOKU SQLI</p>

|RuleId|Popis|
|---|---|
|942011|Pravidlo 942011|
|942012|Pravidlo 942012|
|942100|SQL vkládání útoku zjištěna prostřednictvím libinjection|
|942140|Útok prostřednictvím injektáže SQL = běžné názvy DB zjistil|
|942160|Zjistí skrytá sqli testů pomocí sleep() nebo benchmark().|
|942170|Zjistí, pokusy o Injektáž SQL srovnávacího testu a režimu spánku, včetně dotazů na podmíněného|
|942230|Zjistí podmíněného pokusu o Injektáž SQL|
|942270|Hledá vkládání základní sql. Běžné útoku řetězec pro mysql oracle a další.|
|942290|Vyhledá pokusu o Injektáž SQL základní MongoDB|
|942320|Zjistí, MySQL a PostgreSQL uložená procedura nebo funkce injekce|
|942350|Zjistí vkládání MySQL UDF a dalších dat nebo struktura manipulace se pokusí|
|942013|Pravidlo 942013|
|942014|Pravidlo 942014|
|942150|Útok prostřednictvím injektáže SQL|
|942410|Útok prostřednictvím injektáže SQL|
|942440|Pořadí Komentář SQL zjištěna.|
|942450|SQL šestnáctkových kódování identifikovat|
|942015|Pravidlo 942015|
|942016|Pravidlo 942016|
|942251|Zjistí HAVING injekce|
|942460|Výstraha detekce anomálií metaznaků – opakovaných znaků aplikace Word|
|942017|Pravidlo 942017|
|942018|Pravidlo 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Popis|
|---|---|
|943011|Pravidlo 943011|
|943012|Pravidlo 943012|
|943100|Možné relace záznam útoku = nastavení hodnoty souborů Cookie ve formátu HTML|
|943110|Možné relace záznam útoku = název parametru SessionID s odkazující server vypnout domény|
|943120|Možné relace záznam útoku = název parametru SessionID s žádné odkazující server|
|943013|Pravidlo 943013|
|943014|Pravidlo 943014|
|943015|Pravidlo 943015|
|943016|Pravidlo 943016|
|943017|Pravidlo 943017|
|943018|Pravidlo 943018|

##<a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|Popis|
|---|---|
|960911|Neplatný požadavek HTTP řádku|
|981227|Apache Chyba = neplatný identifikátor URI v požadavku.|
|960912|Nepodařilo se analyzovat textu požadavku.|
|960914|S více částmi žádost textu se nezdařila striktní ověření = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} VF %@{MULTIPART_HEADER_FOLDING} LF %@{MULTIPART_LF_LINE} SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} vhodné %@{MULTIPART_INVALID_HEADER_FOLDING} %@{MULTIPART_FILE_LIMIT_EXCEEDED soubor}|
|960915|S více částmi analyzátor zjistil možné neodpovídající hranice.|
|960016|Hlavičku HTTP Content-Length není číslo.|
|960011|GET nebo HEAD žádost s obsah textu.|
|960012|Požadavek POST chybí hlavičku Content-Length.|
|960902|Neplatné použití kódování Identity.|
|960022|Očekávejte hlavičky není povoleno pro protokol HTTP 1.0.|
|960020|Záhlaví – Direktiva pragma vyžaduje hlavička Cache-Control pro požadavky HTTP/1.1.|
|958291|Rozsah = pole existuje a začíná 0.|
|958230|Rozsah = neplatný poslední hodnotu bajtů.|
|958295|Více/konfliktní Data záhlaví připojení nalezena.|
|950107|Pokus o útok URL kódování zneužití|
|950109|Zjištěno více kódování URL|
|950108|Pokus o útok URL kódování zneužití|
|950801|UTF8 Pokus o útok zneužití kódování|
|950116|Pokus o útok Unicode plné a poloviční šířky zneužití|
|960901|Neplatný znak v požadavku|
|960018|Neplatný znak v požadavku|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Popis|
|---|---|
|960008|Žádosti chybí hlavička hostitele|
|960007|Hlavička hostitele prázdný|
|960015|Žádosti chybí hlavičky Accept|
|960021|Žádost obsahuje prázdnou hlavičky Accept|
|960009|Chybí záhlaví uživatelského agenta žádosti|
|960006|Záhlaví Agent prázdný uživatele|
|960904|Žádosti o obsahující obsahu, ale chybí záhlaví typu obsahu|
|960017|Hlavička hostitele je číselné IP adresa|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|Popis|
|---|---|
|960209|Argument název je příliš dlouhý|
|960208|Hodnota argumentu příliš dlouhý|
|960335|Příliš mnoho argumentů v požadavku|
|960341|Celkový počet argumentů velikost překročen|
|960342|Nahrávaný soubor příliš velký.|
|960343|Celkový počet odeslaných souborů příliš velký.|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|Popis|
|---|---|
|960032|Metoda není povolena zásadami|
|960010|Typ obsahu požadavku není povolen zásadami|
|960034|Verzi protokolu HTTP není povolena zásad|
|960035|Adresa URL souboru rozšíření je omezené na základě zásad|
|960038|Hlavičky protokolu HTTP je omezené na základě zásad|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|Popis|
|---|---|
|990002|Žádost o označuje prohlížela kontrolu zabezpečení webu|
|990901|Žádost o označuje prohlížela kontrolu zabezpečení webu|
|990902|Žádost o označuje prohlížela kontrolu zabezpečení webu|
|990012|Webový server podvodný prohledávacího modulu|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|Popis|
|---|---|
|960024|Výstraha detekce anomálií metaznaků – opakovaných znaků aplikace Word|
|950008|Vkládání Nedokumentovanými ColdFusion značky|
|950010|Útok prostřednictvím injektáže LDAP|
|950011|Vkládání SSI útoku|
|950018|Adresa URL Universal PDF XSS zjištěna.|
|950019|Útok prostřednictvím injektáže e-mailu|
|950012|Podvržení útoku požadavku HTTP.|
|950910|Útok rozdělení odpovědi HTTP|
|950911|Útok rozdělení odpovědi HTTP|
|950117|Útok zahrnutí vzdáleného souboru|
|950118|Útok zahrnutí vzdáleného souboru|
|950119|Útok zahrnutí vzdáleného souboru|
|950120|Možné vzdáleného souboru zahrnutí (RFI) útoku = Off domény odkazem|
|981133|Pravidlo 981133|
|981134|Pravidlo 981134|
|950009|Útok záznam relace|
|950003|Záznam relace|
|950000|Záznam relace|
|950005|Pokus o přístup vzdáleného souboru|
|950002|Přístup k systému příkaz|
|950006|Příkaz vkládání systému|
|959151|Útok prostřednictvím injektáže PHP|
|958976|Útok prostřednictvím injektáže PHP|
|958977|Útok prostřednictvím injektáže PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Popis|
|---|---|
|981231|Pořadí Komentář SQL zjištěna.|
|981260|SQL šestnáctkových kódování identifikovat|
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
|981317|Výstraha detekce anomálií příkazu SELECT SQL|
|950007|Útok prostřednictvím injektáže SQL skrytá|
|950001|Útok prostřednictvím injektáže SQL|
|950908|Útok prostřednictvím injektáže SQL.|
|959073|Útok prostřednictvím injektáže SQL|
|981272|Zjistí skrytá sqli testů pomocí sleep() nebo benchmark().|
|981250|Zjistí, pokusy o Injektáž SQL srovnávacího testu a režimu spánku, včetně dotazů na podmíněného|
|981241|Zjistí podmíněného pokusu o Injektáž SQL|
|981276|Hledá vkládání základní sql. Běžné útoku řetězec pro mysql oracle a další.|
|981270|Vyhledá pokusu o Injektáž SQL základní MongoDB|
|981253|Zjistí, MySQL a PostgreSQL uložená procedura nebo funkce injekce|
|981251|Zjistí vkládání MySQL UDF a dalších dat nebo struktura manipulace se pokusí|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Popis|
|---|---|
|973336|Filtr XSS – kategorie 1 = Vector značky skriptu|
|973338|Filtr XSS – kategorie 3 = Vector Javascript URI|
|981136|Pravidlo 981136|
|981018|Pravidlo 981018|
|958016|Útok skriptování (XSS)|
|958414|Útok skriptování (XSS)|
|958032|Útok skriptování (XSS)|
|958026|Útok skriptování (XSS)|
|958027|Útok skriptování (XSS)|
|958054|Útok skriptování (XSS)|
|958418|Útok skriptování (XSS)|
|958034|Útok skriptování (XSS)|
|958019|Útok skriptování (XSS)|
|958013|Útok skriptování (XSS)|
|958408|Útok skriptování (XSS)|
|958012|Útok skriptování (XSS)|
|958423|Útok skriptování (XSS)|
|958002|Útok skriptování (XSS)|
|958017|Útok skriptování (XSS)|
|958007|Útok skriptování (XSS)|
|958047|Útok skriptování (XSS)|
|958410|Útok skriptování (XSS)|
|958415|Útok skriptování (XSS)|
|958022|Útok skriptování (XSS)|
|958405|Útok skriptování (XSS)|
|958419|Útok skriptování (XSS)|
|958028|Útok skriptování (XSS)|
|958057|Útok skriptování (XSS)|
|958031|Útok skriptování (XSS)|
|958006|Útok skriptování (XSS)|
|958033|Útok skriptování (XSS)|
|958038|Útok skriptování (XSS)|
|958409|Útok skriptování (XSS)|
|958001|Útok skriptování (XSS)|
|958005|Útok skriptování (XSS)|
|958404|Útok skriptování (XSS)|
|958023|Útok skriptování (XSS)|
|958010|Útok skriptování (XSS)|
|958411|Útok skriptování (XSS)|
|958422|Útok skriptování (XSS)|
|958036|Útok skriptování (XSS)|
|958000|Útok skriptování (XSS)|
|958018|Útok skriptování (XSS)|
|958406|Útok skriptování (XSS)|
|958040|Útok skriptování (XSS)|
|958052|Útok skriptování (XSS)|
|958037|Útok skriptování (XSS)|
|958049|Útok skriptování (XSS)|
|958030|Útok skriptování (XSS)|
|958041|Útok skriptování (XSS)|
|958416|Útok skriptování (XSS)|
|958024|Útok skriptování (XSS)|
|958059|Útok skriptování (XSS)|
|958417|Útok skriptování (XSS)|
|958020|Útok skriptování (XSS)|
|958045|Útok skriptování (XSS)|
|958004|Útok skriptování (XSS)|
|958421|Útok skriptování (XSS)|
|958009|Útok skriptování (XSS)|
|958025|Útok skriptování (XSS)|
|958413|Útok skriptování (XSS)|
|958051|Útok skriptování (XSS)|
|958420|Útok skriptování (XSS)|
|958407|Útok skriptování (XSS)|
|958056|Útok skriptování (XSS)|
|958011|Útok skriptování (XSS)|
|958412|Útok skriptování (XSS)|
|958008|Útok skriptování (XSS)|
|958046|Útok skriptování (XSS)|
|958039|Útok skriptování (XSS)|
|958003|Útok skriptování (XSS)|
|973300|Zjištění útoku možné XSS - obslužná rutina značky HTML|
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
|973331|Filtry IE XSS - útoku zjištěna.|
|973315|Filtry IE XSS - útoku zjištěna.|
|973330|Filtry IE XSS - útoku zjištěna.|
|973327|Filtry IE XSS - útoku zjištěna.|
|973326|Filtry IE XSS - útoku zjištěna.|
|973346|Filtry IE XSS - útoku zjištěna.|
|973345|Filtry IE XSS - útoku zjištěna.|
|973324|Filtry IE XSS - útoku zjištěna.|
|973323|Filtry IE XSS - útoku zjištěna.|
|973348|Filtry IE XSS - útoku zjištěna.|
|973321|Filtry IE XSS - útoku zjištěna.|
|973320|Filtry IE XSS - útoku zjištěna.|
|973318|Filtry IE XSS - útoku zjištěna.|
|973317|Filtry IE XSS - útoku zjištěna.|
|973329|Filtry IE XSS - útoku zjištěna.|
|973328|Filtry IE XSS - útoku zjištěna.|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|Popis|
|---|---|
|950103|Cesta k procházení útoku|

### <a name="crs45"></a> crs_45_trojans

|RuleId|Popis|
|---|---|
|950110|Zadní vrátka přístup|
|950921|Zadní vrátka přístup|
|950922|Zadní vrátka přístup|

## <a name="next-steps"></a>Další postup

Zjistěte, jak zakázat pravidla firewall webových aplikací tak, že navštívíte: [pravidla přizpůsobit firewall webových aplikací](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png