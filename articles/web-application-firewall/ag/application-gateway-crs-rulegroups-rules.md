---
title: Skupiny a pravidla počítačových rezervačních systémů
titleSuffix: Azure Web Application Firewall
description: Tato stránka obsahuje informace o skupinách pravidel a pravidlech brány firewall brány firewall webových aplikací.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 0303f09e5e704a18576bf50d1f00007f7f86f320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279244"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Skupiny a pravidla serveru CRS brány firewall webových aplikací

Brána firewall webových aplikací Application Gateway (WAF) chrání webové aplikace před běžnými chybami zabezpečení a zneužitím. To se provádí prostřednictvím pravidel, která jsou definována na základě základních sad pravidel OWASP 3.1, 3.0 nebo 2.2.9. Tato pravidla lze zakázat na základě pravidla podle pravidla. Tento článek obsahuje aktuální pravidla a nabízené sady pravidel.

## <a name="core-rule-sets"></a>Základní sady pravidel

Waf aplikační brány je ve výchozím nastavení dodáván s předkonfigurovanou funkcí CRS 3.0. Místo toho však můžete použít CRS 3.1 nebo CRS 2.2.9. CRS 3.1 nabízí nové sady pravidel bránící se proti infekcím Java, počáteční sadu kontrol nahrávání souborů, opravené falešně pozitivní výsledky a další. CRS 3.0 nabízí snížené falešně pozitivní výsledky ve srovnání s CRS 2.2.9. Můžete také [přizpůsobit pravidla tak, aby vyhovovala vašim potřebám](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![Spravuje pravidla](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF chrání před následujícími chybami zabezpečení webu:

- Útoky injektáže SQL
- Útoky skriptování mezi weby
- Další běžné útoky, jako je například injektáž příkazů, pašování požadavků HTTP, rozdělení odpovědi HTTP a vzdálené zahrnutí souborů
- Porušení protokolu HTTP
- Anomálie protokolu HTTP, například chybějící hostitelský uživatelský agent a přijímat záhlaví
- Roboty, prohledávací moduly a skenery
- Běžné chybné konfigurace aplikací (například Apache a IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3.1

CRS 3.1 obsahuje 13 skupin pravidel, jak je znázorněno v následující tabulce. Každá skupina obsahuje více pravidel, která lze zakázat.

|Skupina pravidla|Popis|
|---|---|
|**[Obecné](#general-31)**|Obecná skupina|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Metody uzamčení (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Ochrana před skenery portů a prostředí|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Ochrana před problémy s protokolem a kódováním|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Ochrana před vkládáním záhlaví, žádostmi o pašování a rozdělením odpovědí|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Ochrana před útoky na soubory a cesty|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Ochrana před útoky vzdáleného zahrnutí souborů (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Znovu chránit útoky vzdáleného spuštění kódu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Ochrana před útoky php-injection|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Ochrana před útoky skriptování mezi weby|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Ochrana před útoky injektáže SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Ochrana před útoky fixace relace|
|**[REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-31)**|Ochrana před útoky JAVA|

### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 obsahuje 12 skupin pravidel, jak je znázorněno v následující tabulce. Každá skupina obsahuje více pravidel, která lze zakázat.

|Skupina pravidla|Popis|
|---|---|
|**[Obecné](#general-30)**|Obecná skupina|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Metody uzamčení (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Ochrana před skenery portů a prostředí|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Ochrana před problémy s protokolem a kódováním|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Ochrana před vkládáním záhlaví, žádostmi o pašování a rozdělením odpovědí|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Ochrana před útoky na soubory a cesty|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Ochrana před útoky vzdáleného zahrnutí souborů (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Znovu chránit útoky vzdáleného spuštění kódu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Ochrana před útoky php-injection|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Ochrana před útoky skriptování mezi weby|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Ochrana před útoky injektáže SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Ochrana před útoky fixace relace|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 obsahuje 10 skupin pravidel, jak je znázorněno v následující tabulce. Každá skupina obsahuje více pravidel, která lze zakázat.

|Skupina pravidla|Popis|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Ochrana před porušením protokolu (například neplatné znaky nebo GET s tělem požadavku)|
|**[crs_21_protocol_anomalies](#crs21)**|Ochrana před nesprávnými informacemi záhlaví|
|**[crs_23_request_limits](#crs23)**|Ochrana před argumenty nebo soubory, které překračují omezení|
|**[crs_30_http_policy](#crs30)**|Ochrana před omezenými metodami, záhlavími a typy souborů|
|**[crs_35_bad_robots](#crs35)**|Ochrana před webovými prohledávači a skenery|
|**[crs_40_generic_attacks](#crs40)**|Ochrana před obecnými útoky (jako je fixace relace, vzdálené zahrnutí souborů a injektáž PHP)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Ochrana před útoky injektáže SQL|
|**[crs_41_xss_attacks](#crs41xss)**|Ochrana před útoky skriptování mezi weby|
|**[crs_42_tight_security](#crs42)**|Ochrana před útoky na cesty|
|**[crs_45_trojans](#crs45)**|Ochrana proti zadním vrátkům trojských koní|

Následující skupiny pravidel a pravidla jsou k dispozici při použití brány firewall webových aplikací v bráně aplikace.

# <a name="owasp-31"></a>[OWASP 3.1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a>Sady pravidel

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Obecné</p>

|RuleId|Popis|
|---|---|
|200004|Možná vícedílná neodpovídající hranice.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Popis|
|---|---|
|911100|Metoda není povolena zásadami.|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Popis|
|---|---|
|913100|Byl nalezen user-agent přidružený ke skeneru zabezpečení.|
|913101|Byl nalezen user-agent přidružený ke skriptovacímu/obecnému klientovi HTTP|
|913102|Byl nalezen user-agent přidružený k webovému prohledávači/botovi|
|913110|Hlavička požadavku nalezena přidružená ke skeneru zabezpečení|
|913120|Byl nalezen název souboru/argument požadavku přidružený ke skeneru zabezpečení.|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Popis|
|---|---|
|920100|Neplatný řádek požadavku HTTP|
|920120|Pokus o obtok vícečástí/dat formuláře|
|920121|Pokus o obtok vícečástí/dat formuláře|
|920130|Nepodařilo se analyzovat tělo požadavku.|
|920140|Tělo vícedílného požadavku selhalo striktně validací|
|920160|Hlavička HTTP o délce obsahu není číselná.|
|920170|GET nebo HEAD Request with Body Content.|
|920171|GET nebo HEAD Request s kódováním přenosu.|
|920180|Požadavek POST chybí záhlaví délky obsahu.|
|920190|Rozsah = Neplatná hodnota posledního bajtu.|
|920200|Rozsah = Příliš mnoho polí (6 nebo více)|
|920201|Rozsah = Příliš mnoho polí pro požadavek pdf (35 nebo více)|
|920202|Rozsah = Příliš mnoho polí pro požadavek pdf (6 nebo více)|
|920210|Bylo nalezeno více nebo konfliktních dat záhlaví připojení.|
|920220|Pokus o zneužití zneužití kódování adresy URL|
|920230|Bylo zjištěno více násobné kódování adres URL.|
|920240|Pokus o zneužití zneužití kódování adresy URL|
|920250|Pokus o zneužití zneužití uUTF8|
|920260|Pokus o zneužití unicode s úplnou/poloviční šířkou|
|920270|Neplatný znak v požadavku (prázdný znak)|
|920271|Neplatný znak v požadavku (netisknutelné znaky)|
|920272|Neplatný znak v požadavku (mimo tisknutelné znaky pod ascii 127)|
|920273|Neplatný znak v požadavku (mimo velmi přísnou sadu)|
|920274|Neplatný znak v hlavičkách požadavku (mimo velmi přísnou sadu)|
|920280|Požadavek na chybějící záhlaví hostitele|
|920290|Prázdné záhlaví hostitele|
|920300|Požadavek na chybějící hlavičku přijmout|
|920310|Požadavek má prázdné záhlaví přijmout|
|920311|Požadavek má prázdné záhlaví přijmout|
|920320|Chybějící záhlaví uživatelského agenta|
|920330|Prázdné záhlaví uživatelského agenta|
|920340|Požadavek obsahující obsah, ale chybějící záhlaví typu obsahu|
|920341|Požadavek obsahující obsah vyžaduje záhlaví typu obsahu.|
|920350|Hlavička hostitele je číselná adresa IP.|
|920360|Název argumentu je příliš dlouhý.|
|920370|Hodnota argumentu je příliš dlouhá.|
|920380|Příliš mnoho argumentů v požadavku|
|920390|Celková velikost argumentů byla překročena.|
|920400|Příliš velká velikost nahraného souboru|
|920410|Celková velikost nahraných souborů je příliš velká.|
|920420|Typ obsahu požadavku není povolen zásadami.|
|920430|Verze protokolu HTTP není povolena zásadami.|
|920440|Přípona souboru URL je omezena zásadami.|
|920450|Hlavička protokolu HTTP je omezena zásadami (%@{MATCHED_VAR})|
|920460|Abnormální řídicí znaky|
|920470|Záhlaví typu neplatného obsahu|
|920480|Omezit parametr znakové sady v záhlaví typu obsahu|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Popis|
|---|---|
|921110|Http žádost pašování útok|
|921120|Útok rozdělení odpovědi HTTP|
|921130|Útok rozdělení odpovědi HTTP|
|921140|Útok injektáží hlavičky HTTP prostřednictvím záhlaví|
|921150|Útok vkládáním hlavičky HTTP prostřednictvím datové části (zjištěncr/LF)|
|921151|Útok vkládáním hlavičky HTTP prostřednictvím datové části (zjištěncr/LF)|
|921160|Útok injektáží hlavičky HTTP prostřednictvím datové části (zjištěncr/LF a název záhlaví)|
|921170|Znečištění parametrů HTTP|
|921180|Znečištění parametru HTTP (%{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Popis|
|---|---|
|930100|Cesta Traversal Attack (/. /)|
|930110|Cesta Traversal Attack (/. /)|
|930120|Pokus o přístup k souboru operačního systému|
|930130|Pokus o přístup k souborům s omezeným přístupem|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Popis|
|---|---|
|931100|Možný útok vzdáleného zařazení souborů (RFI) = parametr URL pomocí IP adresy|
|931110|Možný útok vzdáleného zahrnutí souborů (RFI) = společný název ohroženého parametru RFI použitý s datovou ženou w/URL|
|931120|Možné vzdálené zahrnutí souboru (RFI) Útok = URL užitečné zatížení Použité w / Koncové otazník znak (?)|
|931130|Možný útok vzdáleného zahrnutí souborů (RFI) = odkaz/odkaz mimo doménu|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Popis|
|---|---|
|932100|Vzdálené spuštění příkazu: Unix Command Injection|
|932105|Vzdálené spuštění příkazu: Unix Command Injection|
|932106|Vzdálené spuštění příkazu: Unix Command Injection|
|932110|Vzdálené spuštění příkazu: Injektáž příkazů systému Windows|
|932115|Vzdálené spuštění příkazu: Injektáž příkazů systému Windows|
|932120|Vzdálené spuštění příkazu = Byl nalezen příkaz prostředí Windows PowerShell|
|932130|Vzdálené spuštění příkazu = byl nalezen výraz unixového prostředí|
|932140|Vzdálené spuštění příkazu = byl nalezen příkaz Windows FOR/IF|
|932160|Vzdálené spuštění příkazu = byl nalezen kód unixového prostředí|
|932170|Vzdálené spuštění příkazu = Shellshock (CVE-2014-6271)|
|932171|Vzdálené spuštění příkazu = Shellshock (CVE-2014-6271)|
|932180|Pokus o nahrání souboru s omezeným přístupem|
|932190|Vzdálené spuštění příkazu: Pokus o obchvat zástupných karet|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Popis|
|---|---|
|933100|PHP Injection Attack = Otevření / Uzavření Tag Nalezeno|
|933110|PHP Injection Attack = PHP Script File Upload Nalezeno|
|933111|PHP Injekce Attack: PHP Script File Upload Nalezeno|
|933120|PHP Injection Attack = Konfigurace směrnice nalezeno|
|933130|PHP Injection Attack = Nalezené proměnné|
|933131|PHP Injekce Attack: Proměnné Nalezeno|
|933140|PHP Injekce Útok: I / O Stream nalezeno|
|933150|PHP Injection Attack = Nalezen název funkce PHP s vysokým rizikem|
|933151|PHP Injection Attack: Středně rizikový název funkce PHP nalezen|
|933160|PHP Injection Attack = High-Risk PHP funkce volání nalezeno|
|933161|PHP Injekce Attack: Low-Value PHP funkce volání nalezeno|
|933170|PHP Injection Attack: Serialized Object Injection|
|933180|PHP Injection Attack = Nalezeno volání proměnné funkce|
|933190|PHP Injekce Attack: PHP Closing Tag Nalezeno|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Popis|
|---|---|
|941100|XSS Útok zjištěn pomocí libinjection|
|941101|XSS Útok zjištěn pomocí libinjection|
|941110|Filtr XSS - kategorie 1 = Vektor značky skriptu|
|941130|Filtr XSS - kategorie 3 = Vektor atributu|
|941140|Filtr XSS – kategorie 4 = vektor identifikátoru URI javascriptu|
|941150|Filtr XSS – kategorie 5 = Nepovolené atributy HTML|
|941160|NoScript XSS InjectionChecker: Vkládání HTML|
|941170|NoScript XSS InjectionChecker: Vkládání atributů|
|941180|Klíčová slova blacklist node-Validator|
|941190|XSS pomocí šablon stylů|
|941200|XSS pomocí snímků VML|
|941210|XSS pomocí zamlženého Javascriptu|
|941220|XSS pomocí zamlženého skriptu VB|
|941230|XSS pomocí značky 'vložit'|
|941240|XSS pomocí atributu "import" nebo 'implementation'|
|941250|IE XSS Filtry - Útok zjištěn|
|941260|XSS pomocí značky 'meta'|
|941270|XSS pomocí 'link' href|
|941280|XSS pomocí značky 'base'|
|941290|XSS pomocí značky 'applet'|
|941300|XSS pomocí značky 'objekt'|
|941310|US-ASCII Poškozený kódkódovací filtr XSS - byl zjištěn útok.|
|941320|Možný útok XSS zjištěn - obslužná rutina značky HTML|
|941330|IE XSS Filtry - Útok zjištěn.|
|941340|IE XSS Filtry - Útok zjištěn.|
|941350|UTF-7 Kódování IE XSS - Útok zjištěn.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Popis|
|---|---|
|942100|Sql injection útok zjištěn prostřednictvím libinjection|
|942110|Útok vstřikování SQL: Bylo zjištěno běžné testování vstřikování|
|942130|Útok injektáží SQL: Byla zjištěna tautologie SQL.|
|942140|Útok injektáží SQL = byly zjištěny běžné názvy DB|
|942150|Útok injektáží SQL|
|942160|Detekuje slepé sqli testy pomocí sleep() nebo benchmark().|
|942170|Detekuje sql benchmark a pokusy o vkládání spánku včetně podmíněných dotazů|
|942180|Detekuje základní pokusy o obejít ověřování SQL 1/3|
|942190|Detekuje pokusy o spuštění kódu MSSQL a shromažďování informací.|
|942200|Detekuje MySQL komentář-/prostor-zamlžené injekce a backtick ukončení|
|942210|Detekuje zřetězené pokusy o injektáž SQL 1/2|
|942220|Hledáte útoky přetekované celé číslo, tyto jsou převzaty z skipfish, s výjimkou 3.0.00738585072|
|942230|Detekuje podmíněné pokusy o injektáž SQL.|
|942240|Detekuje přepínač znakové sady MySQL a pokusy o MSSQL DoS|
|942250|Detekuje MATCH AGAINST, MERGE a EXECUTE OKAMŽITÉ injekce|
|942251|Detekuje s injekcemi|
|942260|Detekuje základní pokusy o obejít ověřování SQL 2/3.|
|942270|Hledáte základní sql injection. Společný útočný řetězec pro mysql oracle a další|
|942280|Detekuje Postgres pg_sleep injektáž, čekat na zpoždění útoky a pokusy o vypnutí databáze|
|942290|Najde základní pokusy o injektáž MongoDB SQL.|
|942300|Detekuje MySQL komentáře, podmínky a ch(a)r injekce|
|942310|Detekuje zřetězené pokusy o injektáž SQL 2/2|
|942320|Detekuje MySQL a PostgreSQL uložené procedury / funkce injekce|
|942330|Detekuje klasické sql injection sonbings 1/2|
|942340|Detekuje základní pokusy o obejít ověřování SQL 3/3.|
|942350|Detekuje mysql UDF injekce a další pokusy o manipulaci s daty / strukturou|
|942360|Detekuje zřetězené základní injektáž SQL a pokusy SQLLFI|
|942361|Detekuje základní injektáž SQL na základě změny klíčového slova nebo sjednocení.|
|942370|Detekuje klasické sql injection sonbings 2/2|
|942380|Útok injektáží SQL|
|942390|Útok injektáží SQL|
|942400|Útok injektáží SQL|
|942410|Útok injektáží SQL|
|942420|Omezené detekce anomálií znaků SQL (soubory cookie): Počet speciálních znaků byl překročen (8)|
|942421|Omezené detekce anomálií znaků SQL (soubory cookie): Počet speciálních znaků byl překročen (3)|
|942430|Omezené detekce anomálií znaků SQL (args): Počet speciálních znaků byl překročen (12)|
|942431|Omezené detekce anomálií znaků SQL (args): Počet speciálních znaků překročen (6)|
|942432|Omezené detekce anomálií znaků SQL (args): Počet speciálních znaků překročen (2)|
|942440|Byla zjištěna sekvence komentářů SQL.|
|942450|Sql Hex kódování identifikováno|
|942460|Výstraha detekce anomálií metaznaků – opakující se neslovní znaky|
|942470|Útok injektáží SQL|
|942480|Útok injektáží SQL|
|942490|Detekuje klasické sql injection sonbings 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Popis|
|---|---|
|943100|Možný útok fixace relace = nastavení hodnot souborů cookie v HTML|
|943110|Možný útok fixace relace = název parametru SessionID s odkazovačem mimo doménu|
|943120|Možný útok fixace relace = název parametru SessionID bez odkazování|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA</p>

|RuleId|Popis|
|---|---|
|944120|Možné spuštění datové části a vzdálené spuštění příkazu|
|944130|Podezřelé třídy Java|
|944200|Využití java deserializace Apache Commons|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a>Sady pravidel

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Obecné</p>

|RuleId|Popis|
|---|---|
|200004|Možná vícedílná neodpovídající hranice.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Popis|
|---|---|
|911100|Metoda není povolena zásadami.|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Popis|
|---|---|
|913100|Byl nalezen user-agent přidružený ke skeneru zabezpečení.|
|913110|Hlavička požadavku nalezena přidružená ke skeneru zabezpečení|
|913120|Byl nalezen název souboru/argument požadavku přidružený ke skeneru zabezpečení.|
|913101|Byl nalezen user-agent přidružený ke skriptovacímu/obecnému klientovi HTTP|
|913102|Byl nalezen user-agent přidružený k webovému prohledávači/botovi|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Popis|
|---|---|
|920100|Neplatný řádek požadavku HTTP|
|920130|Nepodařilo se analyzovat tělo požadavku.|
|920140|Tělo vícedílného požadavku selhalo striktně validací|
|920160|Hlavička HTTP o délce obsahu není číselná.|
|920170|GET nebo HEAD Request with Body Content.|
|920180|Požadavek POST chybí záhlaví délky obsahu.|
|920190|Rozsah = Neplatná hodnota posledního bajtu.|
|920210|Bylo nalezeno více nebo konfliktních dat záhlaví připojení.|
|920220|Pokus o zneužití zneužití kódování adresy URL|
|920240|Pokus o zneužití zneužití kódování adresy URL|
|920250|Pokus o zneužití zneužití uUTF8|
|920260|Pokus o zneužití unicode s úplnou/poloviční šířkou|
|920270|Neplatný znak v požadavku (prázdný znak)|
|920280|Požadavek na chybějící záhlaví hostitele|
|920290|Prázdné záhlaví hostitele|
|920310|Požadavek má prázdné záhlaví přijmout|
|920311|Požadavek má prázdné záhlaví přijmout|
|920330|Prázdné záhlaví uživatelského agenta|
|920340|Požadavek obsahující obsah, ale chybějící záhlaví typu obsahu|
|920350|Hlavička hostitele je číselná adresa IP.|
|920380|Příliš mnoho argumentů v požadavku|
|920360|Název argumentu je příliš dlouhý.|
|920370|Hodnota argumentu je příliš dlouhá.|
|920390|Celková velikost argumentů byla překročena.|
|920400|Příliš velká velikost nahraného souboru|
|920410|Celková velikost nahraných souborů je příliš velká.|
|920420|Typ obsahu požadavku není povolen zásadami.|
|920430|Verze protokolu HTTP není povolena zásadami.|
|920440|Přípona souboru URL je omezena zásadami.|
|920450|Hlavička protokolu HTTP je omezena zásadami (%@{MATCHED_VAR})|
|920200|Rozsah = Příliš mnoho polí (6 nebo více)|
|920201|Rozsah = Příliš mnoho polí pro požadavek pdf (35 nebo více)|
|920230|Bylo zjištěno více násobné kódování adres URL.|
|920300|Požadavek na chybějící hlavičku přijmout|
|920271|Neplatný znak v požadavku (netisknutelné znaky)|
|920320|Chybějící záhlaví uživatelského agenta|
|920272|Neplatný znak v požadavku (mimo tisknutelné znaky pod ascii 127)|
|920202|Rozsah = Příliš mnoho polí pro požadavek pdf (6 nebo více)|
|920273|Neplatný znak v požadavku (mimo velmi přísnou sadu)|
|920274|Neplatný znak v hlavičkách požadavku (mimo velmi přísnou sadu)|
|920460|Abnormální řídicí znaky|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Popis|
|---|---|
|921100|Http požadavek pašování útoku.|
|921110|Http žádost pašování útok|
|921120|Útok rozdělení odpovědi HTTP|
|921130|Útok rozdělení odpovědi HTTP|
|921140|Útok injektáží hlavičky HTTP prostřednictvím záhlaví|
|921150|Útok vkládáním hlavičky HTTP prostřednictvím datové části (zjištěncr/LF)|
|921160|Útok injektáží hlavičky HTTP prostřednictvím datové části (zjištěncr/LF a název záhlaví)|
|921151|Útok vkládáním hlavičky HTTP prostřednictvím datové části (zjištěncr/LF)|
|921170|Znečištění parametrů HTTP|
|921180|Znečištění parametru HTTP (%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Popis|
|---|---|
|930100|Cesta Traversal Attack (/. /)|
|930110|Cesta Traversal Attack (/. /)|
|930120|Pokus o přístup k souboru operačního systému|
|930130|Pokus o přístup k souborům s omezeným přístupem|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Popis|
|---|---|
|931100|Možný útok vzdáleného zařazení souborů (RFI) = parametr URL pomocí IP adresy|
|931110|Možný útok vzdáleného zahrnutí souborů (RFI) = společný název ohroženého parametru RFI použitý s datovou ženou w/URL|
|931120|Možné vzdálené zahrnutí souboru (RFI) Útok = URL užitečné zatížení Použité w / Koncové otazník znak (?)|
|931130|Možný útok vzdáleného zahrnutí souborů (RFI) = odkaz/odkaz mimo doménu|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Popis|
|---|---|
|932120|Vzdálené spuštění příkazu = Byl nalezen příkaz prostředí Windows PowerShell|
|932130|Vzdálené spuštění příkazu = byl nalezen výraz unixového prostředí|
|932140|Vzdálené spuštění příkazu = byl nalezen příkaz Windows FOR/IF|
|932160|Vzdálené spuštění příkazu = byl nalezen kód unixového prostředí|
|932170|Vzdálené spuštění příkazu = Shellshock (CVE-2014-6271)|
|932171|Vzdálené spuštění příkazu = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Popis|
|---|---|
|933100|PHP Injection Attack = Otevření / Uzavření Tag Nalezeno|
|933110|PHP Injection Attack = PHP Script File Upload Nalezeno|
|933120|PHP Injection Attack = Konfigurace směrnice nalezeno|
|933130|PHP Injection Attack = Nalezené proměnné|
|933150|PHP Injection Attack = Nalezen název funkce PHP s vysokým rizikem|
|933160|PHP Injection Attack = High-Risk PHP funkce volání nalezeno|
|933180|PHP Injection Attack = Nalezeno volání proměnné funkce|
|933151|PHP Injection Attack = Středně rizikový název funkce PHP nalezeno|
|933131|PHP Injection Attack = Nalezené proměnné|
|933161|PHP Injection Attack = Low-Value PHP volání funkce nalezeno|
|933111|PHP Injection Attack = PHP Script File Upload Nalezeno|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Popis|
|---|---|
|941100|XSS Útok zjištěn pomocí libinjection|
|941110|Filtr XSS - kategorie 1 = Vektor značky skriptu|
|941130|Filtr XSS - kategorie 3 = Vektor atributu|
|941140|Filtr XSS – kategorie 4 = vektor identifikátoru URI javascriptu|
|941150|Filtr XSS – kategorie 5 = Nepovolené atributy HTML|
|941180|Klíčová slova blacklist node-Validator|
|941190|XSS pomocí šablon stylů|
|941200|XSS pomocí snímků VML|
|941210|XSS pomocí zamlženého Javascriptu|
|941220|XSS pomocí zamlženého skriptu VB|
|941230|XSS pomocí značky 'vložit'|
|941240|XSS pomocí atributu "import" nebo 'implementation'|
|941260|XSS pomocí značky 'meta'|
|941270|XSS pomocí 'link' href|
|941280|XSS pomocí značky 'base'|
|941290|XSS pomocí značky 'applet'|
|941300|XSS pomocí značky 'objekt'|
|941310|US-ASCII Poškozený kódkódovací filtr XSS - byl zjištěn útok.|
|941330|IE XSS Filtry - Útok zjištěn.|
|941340|IE XSS Filtry - Útok zjištěn.|
|941350|UTF-7 Kódování IE XSS - Útok zjištěn.|
|941320|Možný útok XSS zjištěn - obslužná rutina značky HTML|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Popis|
|---|---|
|942100|Sql injection útok zjištěn prostřednictvím libinjection|
|942110|Útok vstřikování SQL: Bylo zjištěno běžné testování vstřikování|
|942130|Útok injektáží SQL: Byla zjištěna tautologie SQL.|
|942140|Útok injektáží SQL = byly zjištěny běžné názvy DB|
|942160|Detekuje slepé sqli testy pomocí sleep() nebo benchmark().|
|942170|Detekuje sql benchmark a pokusy o vkládání spánku včetně podmíněných dotazů|
|942190|Detekuje pokusy o spuštění kódu MSSQL a shromažďování informací.|
|942200|Detekuje MySQL komentář-/prostor-zamlžené injekce a backtick ukončení|
|942230|Detekuje podmíněné pokusy o injektáž SQL.|
|942260|Detekuje základní pokusy o obejít ověřování SQL 2/3.|
|942270|Hledáte základní sql injection. Společný útočný řetězec pro mysql oracle a další.|
|942290|Najde základní pokusy o injektáž MongoDB SQL.|
|942300|Detekuje MySQL komentáře, podmínky a ch(a)r injekce|
|942310|Detekuje zřetězené pokusy o injektáž SQL 2/2|
|942320|Detekuje MySQL a PostgreSQL uložené procedury / funkce injekce|
|942330|Detekuje klasické sql injection sonbings 1/2|
|942340|Detekuje základní pokusy o obejít ověřování SQL 3/3.|
|942350|Detekuje mysql UDF injekce a další pokusy o manipulaci s daty / strukturou|
|942360|Detekuje zřetězené základní injektáž SQL a pokusy SQLLFI|
|942370|Detekuje klasické sql injection sonbings 2/2|
|942150|Útok injektáží SQL|
|942410|Útok injektáží SQL|
|942430|Omezené detekce anomálií znaků SQL (args): Počet speciálních znaků byl překročen (12)|
|942440|Byla zjištěna sekvence komentářů SQL.|
|942450|Sql Hex kódování identifikováno|
|942251|Detekuje s injekcemi|
|942460|Výstraha detekce anomálií metaznaků – opakující se neslovní znaky|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Popis|
|---|---|
|943100|Možný útok fixace relace = nastavení hodnot souborů cookie v HTML|
|943110|Možný útok fixace relace = název parametru SessionID s odkazovačem mimo doménu|
|943120|Možný útok fixace relace = název parametru SessionID bez odkazování|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a>Sady pravidel

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a>crs_20_protocol_violations

|RuleId|Popis|
|---|---|
|960911|Neplatný řádek požadavku HTTP|
|981227|Chyba Apache = Neplatný identifikátor URI v požadavku.|
|960912|Nepodařilo se analyzovat tělo požadavku.|
|960914|Tělo vícedílného požadavku selhalo striktně validací|
|960915|Vícedílný analyzátor zjistil možnou nesrovnatelnou hranici.|
|960016|Hlavička HTTP o délce obsahu není číselná.|
|960011|GET nebo HEAD Request with Body Content.|
|960012|Požadavek POST chybí záhlaví délky obsahu.|
|960902|Neplatné použití kódování identity.|
|960022|Očekávejte, že záhlaví není povoleno pro protokol HTTP 1.0.|
|960020|Pragma Header vyžaduje hlavičku cache-control pro požadavky HTTP/1.1.|
|958291|Rozsah = pole existuje a začíná na 0.|
|958230|Rozsah = Neplatná hodnota posledního bajtu.|
|958295|Bylo nalezeno více nebo konfliktních dat záhlaví připojení.|
|950107|Pokus o zneužití zneužití kódování adresy URL|
|950109|Bylo zjištěno více násobné kódování adres URL.|
|950108|Pokus o zneužití zneužití kódování adresy URL|
|950801|Pokus o zneužití zneužití uUTF8|
|950116|Pokus o zneužití unicode s úplnou/poloviční šířkou|
|960901|Neplatný znak v požadavku|
|960018|Neplatný znak v požadavku|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a>crs_21_protocol_anomalies

|RuleId|Popis|
|---|---|
|960008|Požadavek na chybějící záhlaví hostitele|
|960007|Prázdné záhlaví hostitele|
|960015|Požadavek na chybějící hlavičku přijmout|
|960021|Požadavek má prázdné záhlaví přijmout|
|960009|Požadavek na chybějící záhlaví uživatelského agenta|
|960006|Prázdné záhlaví uživatelského agenta|
|960904|Požadavek obsahující obsah, ale chybějící záhlaví typu obsahu|
|960017|Hlavička hostitele je číselná adresa IP.|

### <a name="crs_23_request_limits"></a><a name="crs23"></a>crs_23_request_limits

|RuleId|Popis|
|---|---|
|960209|Název argumentu je příliš dlouhý.|
|960208|Hodnota argumentu je příliš dlouhá.|
|960335|Příliš mnoho argumentů v požadavku|
|960341|Celková velikost argumentů byla překročena.|
|960342|Příliš velká velikost nahraného souboru|
|960343|Celková velikost nahraných souborů je příliš velká.|

### <a name="crs_30_http_policy"></a><a name="crs30"></a>crs_30_http_policy

|RuleId|Popis|
|---|---|
|960032|Metoda není povolena zásadami.|
|960010|Typ obsahu požadavku není povolen zásadami.|
|960034|Verze protokolu HTTP není povolena zásadami.|
|960035|Přípona souboru URL je omezena zásadami.|
|960038|Hlavička protokolu HTTP je omezena zásadami.|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a>crs_35_bad_robots

|RuleId|Popis|
|---|---|
|990002|Požadavek Označuje bezpečnostní skener prohledávaný web.|
|990901|Požadavek Označuje bezpečnostní skener prohledávaný web.|
|990902|Požadavek Označuje bezpečnostní skener prohledávaný web.|
|990012|Rogue webových stránek prohledávač|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a>crs_40_generic_attacks

|RuleId|Popis|
|---|---|
|960024|Výstraha detekce anomálií metaznaků – opakující se neslovní znaky|
|950008|Injekce nedokumentovaných ColdFusion Tagy|
|950010|Útok injekcí LDAP|
|950011|SSI injekce útok|
|950018|Byla zjištěna univerzální adresa URL PDF XSS.|
|950019|Útok e-mailovým vkládáním|
|950012|Http požadavek pašování útoku.|
|950910|Útok rozdělení odpovědi HTTP|
|950911|Útok rozdělení odpovědi HTTP|
|950117|Útok vzdáleného zahrnutí souborů|
|950118|Útok vzdáleného zahrnutí souborů|
|950119|Útok vzdáleného zahrnutí souborů|
|950120|Možný útok vzdáleného zahrnutí souborů (RFI) = odkaz/odkaz mimo doménu|
|981133|Článek 981133|
|981134|Článek 981134|
|950009|Útok fixace relace|
|950003|Fixace relace|
|950000|Fixace relace|
|950005|Pokus o vzdálený přístup k souborům|
|950002|Přístup k systémovému příkazu|
|950006|Vstřikování systémového příkazu|
|959151|PHP injekce útok|
|958976|PHP injekce útok|
|958977|PHP injekce útok|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a>crs_41_sql_injection_attacks

|RuleId|Popis|
|---|---|
|981231|Byla zjištěna sekvence komentářů SQL.|
|981260|Sql Hex kódování identifikováno|
|981320|Útok injektáží SQL = byly zjištěny běžné názvy DB|
|981300|Článek 981300|
|981301|Článek 981301|
|981302|Článek 981302|
|981303|Článek 981303|
|981304|Článek 981304|
|981305|Článek 981305|
|981306|Článek 981306|
|981307|Článek 981307|
|981308|Článek 981308|
|981309|Článek 981309|
|981310|Článek 981310|
|981311|Článek 981311|
|981312|Článek 981312|
|981313|Článek 981313|
|981314|Článek 981314|
|981315|Článek 981315|
|981316|Článek 981316|
|981317|Výstraha detekce anomálií příkazu SQL SELECT|
|950007|Slepý útok injektáží SQL|
|950001|Útok injektáží SQL|
|950908|Útok injektáží SQL.|
|959073|Útok injektáží SQL|
|981272|Detekuje slepé sqli testy pomocí sleep() nebo benchmark().|
|981250|Detekuje sql benchmark a pokusy o vkládání spánku včetně podmíněných dotazů|
|981241|Detekuje podmíněné pokusy o injektáž SQL.|
|981276|Hledáte základní sql injection. Společný útočný řetězec pro mysql oracle a další.|
|981270|Najde základní pokusy o injektáž MongoDB SQL.|
|981253|Detekuje MySQL a PostgreSQL uložené procedury / funkce injekce|
|981251|Detekuje mysql UDF injekce a další pokusy o manipulaci s daty / strukturou|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a>crs_41_xss_attacks

|RuleId|Popis|
|---|---|
|973336|Filtr XSS - kategorie 1 = Vektor značky skriptu|
|973338|Filtr XSS – kategorie 3 = vektor identifikátoru URI javascriptu|
|981136|Článek 981136|
|981018|Článek 981018|
|958016|Útok skriptováním napříč pracemi (XSS)|
|958414|Útok skriptováním napříč pracemi (XSS)|
|958032|Útok skriptováním napříč pracemi (XSS)|
|958026|Útok skriptováním napříč pracemi (XSS)|
|958027|Útok skriptováním napříč pracemi (XSS)|
|958054|Útok skriptováním napříč pracemi (XSS)|
|958418|Útok skriptováním napříč pracemi (XSS)|
|958034|Útok skriptováním napříč pracemi (XSS)|
|958019|Útok skriptováním napříč pracemi (XSS)|
|958013|Útok skriptováním napříč pracemi (XSS)|
|958408|Útok skriptováním napříč pracemi (XSS)|
|958012|Útok skriptováním napříč pracemi (XSS)|
|958423|Útok skriptováním napříč pracemi (XSS)|
|958002|Útok skriptováním napříč pracemi (XSS)|
|958017|Útok skriptováním napříč pracemi (XSS)|
|958007|Útok skriptováním napříč pracemi (XSS)|
|958047|Útok skriptováním napříč pracemi (XSS)|
|958410|Útok skriptováním napříč pracemi (XSS)|
|958415|Útok skriptováním napříč pracemi (XSS)|
|958022|Útok skriptováním napříč pracemi (XSS)|
|958405|Útok skriptováním napříč pracemi (XSS)|
|958419|Útok skriptováním napříč pracemi (XSS)|
|958028|Útok skriptováním napříč pracemi (XSS)|
|958057|Útok skriptováním napříč pracemi (XSS)|
|958031|Útok skriptováním napříč pracemi (XSS)|
|958006|Útok skriptováním napříč pracemi (XSS)|
|958033|Útok skriptováním napříč pracemi (XSS)|
|958038|Útok skriptováním napříč pracemi (XSS)|
|958409|Útok skriptováním napříč pracemi (XSS)|
|958001|Útok skriptováním napříč pracemi (XSS)|
|958005|Útok skriptováním napříč pracemi (XSS)|
|958404|Útok skriptováním napříč pracemi (XSS)|
|958023|Útok skriptováním napříč pracemi (XSS)|
|958010|Útok skriptováním napříč pracemi (XSS)|
|958411|Útok skriptováním napříč pracemi (XSS)|
|958422|Útok skriptováním napříč pracemi (XSS)|
|958036|Útok skriptováním napříč pracemi (XSS)|
|958000|Útok skriptováním napříč pracemi (XSS)|
|958018|Útok skriptováním napříč pracemi (XSS)|
|958406|Útok skriptováním napříč pracemi (XSS)|
|958040|Útok skriptováním napříč pracemi (XSS)|
|958052|Útok skriptováním napříč pracemi (XSS)|
|958037|Útok skriptováním napříč pracemi (XSS)|
|958049|Útok skriptováním napříč pracemi (XSS)|
|958030|Útok skriptováním napříč pracemi (XSS)|
|958041|Útok skriptováním napříč pracemi (XSS)|
|958416|Útok skriptováním napříč pracemi (XSS)|
|958024|Útok skriptováním napříč pracemi (XSS)|
|958059|Útok skriptováním napříč pracemi (XSS)|
|958417|Útok skriptováním napříč pracemi (XSS)|
|958020|Útok skriptováním napříč pracemi (XSS)|
|958045|Útok skriptováním napříč pracemi (XSS)|
|958004|Útok skriptováním napříč pracemi (XSS)|
|958421|Útok skriptováním napříč pracemi (XSS)|
|958009|Útok skriptováním napříč pracemi (XSS)|
|958025|Útok skriptováním napříč pracemi (XSS)|
|958413|Útok skriptováním napříč pracemi (XSS)|
|958051|Útok skriptováním napříč pracemi (XSS)|
|958420|Útok skriptováním napříč pracemi (XSS)|
|958407|Útok skriptováním napříč pracemi (XSS)|
|958056|Útok skriptováním napříč pracemi (XSS)|
|958011|Útok skriptováním napříč pracemi (XSS)|
|958412|Útok skriptováním napříč pracemi (XSS)|
|958008|Útok skriptováním napříč pracemi (XSS)|
|958046|Útok skriptováním napříč pracemi (XSS)|
|958039|Útok skriptováním napříč pracemi (XSS)|
|958003|Útok skriptováním napříč pracemi (XSS)|
|973300|Možný útok XSS zjištěn - obslužná rutina značky HTML|
|973301|Byl zjištěn útok XSS.|
|973302|Byl zjištěn útok XSS.|
|973303|Byl zjištěn útok XSS.|
|973304|Byl zjištěn útok XSS.|
|973305|Byl zjištěn útok XSS.|
|973306|Byl zjištěn útok XSS.|
|973307|Byl zjištěn útok XSS.|
|973308|Byl zjištěn útok XSS.|
|973309|Byl zjištěn útok XSS.|
|973311|Byl zjištěn útok XSS.|
|973313|Byl zjištěn útok XSS.|
|973314|Byl zjištěn útok XSS.|
|973331|IE XSS Filtry - Útok zjištěn.|
|973315|IE XSS Filtry - Útok zjištěn.|
|973330|IE XSS Filtry - Útok zjištěn.|
|973327|IE XSS Filtry - Útok zjištěn.|
|973326|IE XSS Filtry - Útok zjištěn.|
|973346|IE XSS Filtry - Útok zjištěn.|
|973345|IE XSS Filtry - Útok zjištěn.|
|973324|IE XSS Filtry - Útok zjištěn.|
|973323|IE XSS Filtry - Útok zjištěn.|
|973348|IE XSS Filtry - Útok zjištěn.|
|973321|IE XSS Filtry - Útok zjištěn.|
|973320|IE XSS Filtry - Útok zjištěn.|
|973318|IE XSS Filtry - Útok zjištěn.|
|973317|IE XSS Filtry - Útok zjištěn.|
|973329|IE XSS Filtry - Útok zjištěn.|
|973328|IE XSS Filtry - Útok zjištěn.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a>crs_42_tight_security

|RuleId|Popis|
|---|---|
|950103|Cesta Traversal Útok|

### <a name="crs_45_trojans"></a><a name="crs45"></a>crs_45_trojans

|RuleId|Popis|
|---|---|
|950110|Zadní vrátka|
|950921|Zadní vrátka|
|950922|Zadní vrátka|

---

## <a name="next-steps"></a>Další kroky

- [Přizpůsobení pravidel brány firewall webových aplikací pomocí portálu Azure](application-gateway-customize-waf-rules-portal.md)
