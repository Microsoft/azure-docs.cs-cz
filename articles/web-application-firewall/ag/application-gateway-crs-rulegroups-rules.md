---
title: Skupiny pravidel a pravidla pro počítačový počítač
titleSuffix: Azure Web Application Firewall
description: Tato stránka poskytuje informace o skupinách pravidel a pravidlech pro Firewall webových aplikací.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 37e09612491d41887c5945920488569d3620bf0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85052024"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Skupiny pravidel a pravidla pro počítačový rezervační Firewall webových aplikací

Brána Firewall webových aplikací Application Gateway (WAF) chrání webové aplikace před běžnými chybami zabezpečení a zneužitím. To se provádí prostřednictvím pravidel, která jsou definovaná na základě sad pravidel OWASP Core 3,1, 3,0 nebo 2.2.9. Tato pravidla je možné zakázat na základě pravidla podle pravidel. Tento článek obsahuje nabízená současná pravidla a sady pravidel.

## <a name="core-rule-sets"></a>Základní sady pravidel

Ve výchozím nastavení je Application Gateway WAF předem nakonfigurovaný pomocí počítačového rezervačního systému 3,0. Místo toho se ale můžete rozhodnout pro použití počítačového rezervačního systému 3,1 nebo 2.2.9. POČÍTAČOVÝ systém souborů 3,1 nabízí nové sady pravidel, které brání napadení Java, počáteční sada kontrol odesílání souborů, pevná falešně pozitivní a další. Počítačový rezervační 3,0 nabízí nižší falešně pozitivní výsledky v porovnání s 2.2.9em počítačových testů. Můžete také [přizpůsobit pravidla tak, aby vyhovovala vašim potřebám](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![Spravuje pravidla](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF chrání před následujícími webovými ohroženími zabezpečení:

- Útoky prostřednictvím injektáže SQL
- Útoky skriptování mezi weby
- Další běžné útoky, jako je vkládání příkazů, podkládání požadavků HTTP, rozdělování odpovědí HTTP a zahrnutí vzdálených souborů
- Porušení protokolu HTTP
- Anomálie protokolu HTTP, jako například chybějící uživatel – agent a potvrzovací hlavičky
- Roboty, prohledávací moduly a skenery
- Běžné konfigurace neobvyklých aplikací (například Apache a IIS)

### <a name="owasp-crs-31"></a>OWASP POČÍTAČOVÝ POČÍTAČ 3,1

POČÍTAČOVÝ systém 3,1 obsahuje 13 skupin pravidel, jak je znázorněno v následující tabulce. Každá skupina obsahuje více pravidel, která je možné zakázat.

> [!NOTE]
> Počítačový rezervační 3,1 je k dispozici pouze pro WAF_v2 SKU.

|Skupina pravidla|Description|
|---|---|
|**[Obecné](#general-31)**|Obecná skupina|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Metody uzamčení (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Ochrana před skenery portů a prostředí|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Ochrana proti problémům s protokolem a kódováním|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Ochrana proti vkládání hlaviček, vkládání požadavků a rozdělování odpovědí|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Ochrana proti útokům pomocí souborů a cest|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Ochrana proti útokům na vzdálené zahrnutí souborů (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Ochrana před útoky vzdáleného spuštění kódu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Ochrana proti útokům prostřednictvím injektáže PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Ochrana proti útokům prostřednictvím skriptování mezi weby|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Ochrana proti útokům prostřednictvím injektáže SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Ochrana proti útokům při fixace relací|
|**[POŽADAVEK – 944 – APLIKAČNÍ – ÚTOK – SESSION-JAVA](#crs944-31)**|Ochrana proti útokům v jazyce JAVA|

### <a name="owasp-crs-30"></a>OWASP POČÍTAČOVÝ POČÍTAČ 3,0

POČÍTAČOVÝ systém 3,0 zahrnuje 12 skupin pravidel, jak je znázorněno v následující tabulce. Každá skupina obsahuje více pravidel, která je možné zakázat.

|Skupina pravidla|Description|
|---|---|
|**[Obecné](#general-30)**|Obecná skupina|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Metody uzamčení (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Ochrana před skenery portů a prostředí|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Ochrana proti problémům s protokolem a kódováním|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Ochrana proti vkládání hlaviček, vkládání požadavků a rozdělování odpovědí|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Ochrana proti útokům pomocí souborů a cest|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Ochrana proti útokům na vzdálené zahrnutí souborů (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Ochrana před útoky vzdáleného spuštění kódu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Ochrana proti útokům prostřednictvím injektáže PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Ochrana proti útokům prostřednictvím skriptování mezi weby|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Ochrana proti útokům prostřednictvím injektáže SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Ochrana proti útokům při fixace relací|

### <a name="owasp-crs-229"></a>OWASP POČÍTAČOVÝ 2.2.9

POČÍTAČOVÝ 2.2.9 zahrnuje 10 skupin pravidel, jak je znázorněno v následující tabulce. Každá skupina obsahuje více pravidel, která je možné zakázat.

|Skupina pravidla|Description|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Ochrana proti narušením protokolu (například neplatné znaky nebo zpráva GET s textem žádosti)|
|**[crs_21_protocol_anomalies](#crs21)**|Ochrana před nesprávnými informacemi v hlavičce|
|**[crs_23_request_limits](#crs23)**|Ochrana před argumenty nebo soubory, které překračují omezení|
|**[crs_30_http_policy](#crs30)**|Ochrana proti neomezeným metodám, hlavičkám a typům souborů|
|**[crs_35_bad_robots](#crs35)**|Ochrana proti webovým prohledávacím modulům a skenerům|
|**[crs_40_generic_attacks](#crs40)**|Ochrana proti obecným útokům (jako je třeba fixace relace, zahrnutí vzdáleného souboru a vkládání PHP)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Ochrana proti útokům prostřednictvím injektáže SQL|
|**[crs_41_xss_attacks](#crs41xss)**|Ochrana proti útokům prostřednictvím skriptování mezi weby|
|**[crs_42_tight_security](#crs42)**|Ochrana proti útokům pomocí cest – procházení|
|**[crs_45_trojans](#crs45)**|Ochrana před trojskými koni zadní vrátka|

Následující skupiny pravidel a pravidla jsou k dispozici při použití firewallu webových aplikací v Application Gateway.

# <a name="owasp-31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a> Sady pravidel

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Obecné</p>

|RuleId|Description|
|---|---|
|200004|Možná nespárovaná hranice na více částí.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Description|
|---|---|
|911100|Metoda není u zásad povolená.|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Description|
|---|---|
|913100|Nalezené User-Agent přidružené ke skeneru zabezpečení|
|913101|Nalezené User-Agent přidružené ke skriptování nebo obecnému klientovi HTTP|
|913102|Nalezené User-Agent přidružené k webovému crawleru/bot|
|913110|Našla se Hlavička požadavku přidružená ke skeneru zabezpečení.|
|913120|Našel se název souboru/argument pro požadavek přidružený ke skeneru zabezpečení.|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Description|
|---|---|
|920100|Neplatný řádek požadavku HTTP|
|920120|Došlo k pokusu o vynechání dat v částech/formuláři.|
|920121|Došlo k pokusu o vynechání dat v částech/formuláři.|
|920130|Nepovedlo se analyzovat text požadavku.|
|920140|Tělo žádosti s více částmi se nepodařilo striktní ověření.|
|920160|Hlavička Content-Length protokolu HTTP není číselná.|
|920170|Získá nebo vrátí požadavek HEAD s obsahem textu.|
|920171|GET nebo HEAD Request s přenosovým kódováním.|
|920180|V požadavku POST chybí hlavička Content-Length.|
|920190|Range = neplatná hodnota posledního bajtu|
|920200|Range = příliš mnoho polí (6 nebo více)|
|920201|Range = příliš mnoho polí pro požadavek PDF (35 nebo více)|
|920202|Range = příliš mnoho polí pro požadavek PDF (6 nebo více)|
|920210|Bylo nalezeno více nebo konfliktních dat záhlaví připojení.|
|920220|Pokus o útok zneužití kódování adresy URL|
|920230|Bylo zjištěno více kódování URL.|
|920240|Pokus o útok zneužití kódování adresy URL|
|920250|Pokus o útok zneužití kódování UTF8|
|920260|Pokus o útok proti zneužití Unicode s plnou/poloviční šířkou|
|920270|Neplatný znak v žádosti (znak null)|
|920271|Neplatný znak v žádosti (netisknutelné znaky)|
|920272|Neplatný znak v žádosti (mimo tisknutelné znaky pod ASCII 127)|
|920273|Neplatný znak v žádosti (mimo velmi striktní sadu)|
|920274|Neplatný znak v záhlavích žádostí (mimo velmi striktní sadu)|
|920280|V požadavku chybí Hlavička hostitele.|
|920290|Prázdná Hlavička hostitele|
|920300|Požadavek neobsahuje hlavičku Accept.|
|920310|Požadavek má prázdnou hlavičku Accept.|
|920311|Požadavek má prázdnou hlavičku Accept.|
|920320|Chybí záhlaví uživatelského agenta.|
|920330|Prázdné záhlaví uživatelského agenta|
|920340|Požadavek obsahující obsah, ale chybějící záhlaví Content-Type|
|920341|Požadavek obsahující obsah vyžaduje hlavičku Content-Type.|
|920350|Hlavička hostitele je numerická IP adresa.|
|920360|Název argumentu je příliš dlouhý.|
|920370|Hodnota argumentu je moc dlouhá.|
|920380|Příliš mnoho argumentů v žádosti|
|920390|Celková velikost argumentů byla překročena.|
|920400|Velikost nahraného souboru je moc velká.|
|920410|Celkový počet nahraných souborů je moc velký.|
|920420|Typ obsahu žádosti není povolený zásadou.|
|920430|Verze protokolu HTTP není v zásadě povolená.|
|920440|Přípona souboru adresy URL je omezená zásadami.|
|920450|Záhlaví HTTP je omezené zásadami (% @ {MATCHED_VAR}).|
|920460|Neobvyklé řídicí znaky|
|920470|Neplatné záhlaví Content-Type|
|920480|Omezení parametru charset v hlavičce Content-Type|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Description|
|---|---|
|921110|Útok proti podvržení požadavku HTTP|
|921120|Útok na rozdělení HTTP odpovědi|
|921130|Útok na rozdělení HTTP odpovědi|
|921140|Útok prostřednictvím injektáže záhlaví HTTP prostřednictvím hlaviček|
|921150|Útok injektáže záhlaví HTTP prostřednictvím datové části (počet zjištěných znaků CR/LF)|
|921151|Útok injektáže záhlaví HTTP prostřednictvím datové části (počet zjištěných znaků CR/LF)|
|921160|Útok na injektáže záhlaví HTTP prostřednictvím datové části (CR/LF a záhlaví-název byl zjištěn)|
|921170|Znečištění parametru HTTP|
|921180|Znečišťování parametru HTTP (% {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Description|
|---|---|
|930100|Útok procházením cest (/.. /)|
|930110|Útok procházením cest (/.. /)|
|930120|Pokus o přístup k souboru operačního systému|
|930130|Omezený pokus o přístup k souboru|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Description|
|---|---|
|931100|Možný útok RFI (Remote File začlenění) = parametr URL s použitím IP adresy|
|931110|Možný útok na vzdálený soubor k zahrnutí (RFI) = běžný název parametru s chybou RFI, který používal datovou část adresy URL|
|931120|Možný útok na vzdálené zahrnutí souborů (RFI) = datová část adresy URL použité pro znak otazníku (?)|
|931130|Možný útok na RFI (Remote File zařazování) = odkaz na Off-Domain/odkaz|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Description|
|---|---|
|932100|Vzdálené spuštění příkazu: vkládání příkazů ze systému UNIX|
|932105|Vzdálené spuštění příkazu: vkládání příkazů ze systému UNIX|
|932106|Vzdálené spuštění příkazu: vkládání příkazů ze systému UNIX|
|932110|Vzdálené spuštění příkazu: vkládání příkazů systému Windows|
|932115|Vzdálené spuštění příkazu: vkládání příkazů systému Windows|
|932120|Vzdálené spuštění příkazu = nalezen příkaz prostředí Windows PowerShell|
|932130|Vzdálené spuštění příkazu = Nalezeno výraz prostředí systému UNIX|
|932140|Vzdálené spuštění příkazu = Windows pro/Pokud se našel příkaz|
|932150|Vzdálené spuštění příkazu: přímé spuštění příkazu systému UNIX|
|932160|Vzdálené spuštění příkazu = nalezen kód prostředí systému UNIX|
|932170|Vzdálené spuštění příkazu = Shellshock (CVE-2014-6271)|
|932171|Vzdálené spuštění příkazu = Shellshock (CVE-2014-6271)|
|932180|Pokus o nahrání souboru s omezeným přístupem|
|932190|Vzdálené spuštění příkazu: pokus o použití zástupného znaku obejít|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Description|
|---|---|
|933100|Útok injektáže PHP = našla se počáteční nebo uzavírací značka.|
|933110|Útok injektáže PHP = nahrání souboru skriptu PHP se našlo.|
|933111|Útok injektáže PHP: nahrání souboru skriptu PHP se našlo.|
|933120|Útok injektáže PHP = nalezená direktiva konfigurace|
|933130|Útok injektáže PHP = nalezené proměnné|
|933131|Útok injektáže PHP: nalezené proměnné|
|933140|Útok injektáže PHP: našel se vstupně-výstupní Stream.|
|933150|Útok injektáže PHP = High-Risk nalezen název funkce PHP|
|933151|Útok injektáže PHP: našel se Medium-Risk název funkce PHP.|
|933160|Útok injektáže PHP = High-Risk nalezené volání funkce PHP|
|933161|Útok injektáže PHP: našla se Low-Value volání funkce PHP.|
|933170|Útok injektáže PHP: vkládání serializovaných objektů|
|933180|Útok injektáže PHP = Nalezeno volání proměnné funkce|
|933190|Útok injektáže PHP: našla se koncová značka PHP.|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Description|
|---|---|
|941100|Útok XSS zjištěný prostřednictvím libinjection|
|941101|Útok XSS zjištěný prostřednictvím libinjection|
|941110|Filtr XSS – kategorie 1 = vektor značky skriptu|
|941130|Filtr XSS – kategorie 3 = vektor atributu|
|941140|Filtr XSS – kategorie 4 = vektor URI JavaScriptu|
|941150|Filtr XSS – kategorie 5 = nepovolené atributy HTML|
|941160|NoScript XSS InjectionChecker: vkládání HTML|
|941170|NoScript XSS InjectionChecker: injektáže atributu|
|941180|Node-Validator zakázaná klíčová slova|
|941190|XSS pomocí šablon stylů|
|941200|XSS pomocí snímků VML|
|941210|XSS pomocí nepoužívaného JavaScriptu|
|941220|XSS pomocí nepoužívaného skriptu VB|
|941230|XSS pomocí tagu embed|
|941240|XSS s použitím atributu import nebo Implement|
|941250|Filtry pro IE XSS – zjištěn útok|
|941260|XSS s použitím značky meta|
|941270|XSS používající odkaz href|
|941280|XSS pomocí tagu Base|
|941290|XSS s použitím značky applet|
|941300|XSS s použitím značky Object|
|941310|Byl zjištěn útok se špatným kódováním pro kódování XSS typu US-ASCII.|
|941320|Byl zjištěn možný útok XSS – obslužná rutina značky HTML|
|941330|Filtry v IE XSS – byl zjištěn útok.|
|941340|Filtry v IE XSS – byl zjištěn útok.|
|941350|Bylo zjištěno útoku kódování UTF-7 v IE XSS.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Description|
|---|---|
|942100|Byl zjištěn útok prostřednictvím injektáže SQL prostřednictvím libinjection|
|942110|Útok injektáže SQL: byl zjištěn společný test injektáže.|
|942120|Útok injektáže SQL: byl zjištěn operátor SQL|
|942130|Útok injektáže SQL: byl zjištěn SQL Tautology.|
|942140|Útok injektáže SQL = zjištěny běžné názvy databází|
|942150|Útok injektáže SQL|
|942160|Detekuje nevidomé SQLi testy pomocí režimu spánku () nebo srovnávacího testu ().|
|942170|Detekuje srovnávací testy SQL a operace vkládání do režimu spánku včetně podmíněných dotazů.|
|942180|Detekuje základní pokusy o obejití ověřování SQL 1/3|
|942190|Detekuje provádění kódu MSSQL a pokusy o shromažďování informací|
|942200|Detekuje komentáře MySQL – vkládání/Space-obfuscated a ukončení zaškrtnutí.|
|942210|Detekuje zřetězené pokusy o vkládání SQL 1/2|
|942220|Hledání útoků přetečení celých čísel je prováděno z skipfish s výjimkou 3.0.00738585072|
|942230|Detekuje podmíněné pokusy o injektáže SQL.|
|942240|Zjistí přepínače MySQL charset a pokusy o MSSQL DoS.|
|942250|Detekuje SHODu s, SLOUČENÍm a PROVEDENÍm OKAMŽITých vstřiků.|
|942251|Detekuje, že se mají injektáže|
|942260|Detekuje základní pokusy o obejití ověřování SQL 2/3|
|942270|Hledání základního vkládání SQL Běžný řetězec útoku pro MySQL Oracle a další|
|942280|Detekuje Postgres vkládání pg_sleep, WAITFOR zpoždění a pokusy o vypnutí databáze.|
|942290|Vyhledá základní pokusy o vkládání SQL MongoDB.|
|942300|Detekuje komentáře MySQL, podmínky a injektáže r (ch) r|
|942310|Detekuje zřetězené pokusy o vkládání SQL 2/2|
|942320|Detekuje injektáže uložených procedur/funkcí MySQL a PostgreSQL.|
|942330|Detekuje bingy pro vkládání v klasických rozhraních SQL 1/2|
|942340|Detekuje základní pokusy o obejití ověřování SQL 3/3|
|942350|Detekuje injektáže systému MySQL UDF a další pokusy o manipulaci s daty/strukturou.|
|942360|Detekuje zřetězené základní vkládání SQL a pokusy SQLLFI.|
|942361|Detekuje základní vkládání SQL na základě vlastnosti nebo sjednocení klíčového slova.|
|942370|Detekuje bingy pro vkládání v klasických rozhraních SQL 2/2|
|942380|Útok injektáže SQL|
|942390|Útok injektáže SQL|
|942400|Útok injektáže SQL|
|942410|Útok injektáže SQL|
|942420|Omezené zjištění anomálií znaků SQL (soubory cookie): počet speciálních znaků překročen (8)|
|942421|Omezené zjištění anomálií znaků SQL (soubory cookie): počet speciálních znaků překročen (3)|
|942430|Omezené zjištění anomálií znaků SQL (args): počet speciálních znaků se překročil (12).|
|942431|Omezené zjištění anomálií znaků SQL (args): počet speciálních znaků přesáhl (6)|
|942432|Omezené zjištění anomálií znaků SQL (args): počet speciálních znaků byl překročen (2).|
|942440|Zjistila se sekvence komentářů SQL.|
|942450|Identifikované kódování šestnáctkového formátu SQL|
|942460|Výstraha detekce anomálií Meta-Character – opakované znaky, které nejsou v textu|
|942470|Útok injektáže SQL|
|942480|Útok injektáže SQL|
|942490|Detekuje bingy pro vkládání v klasických rozhraních SQL 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Description|
|---|---|
|943100|Možný útok z relace = nastavení hodnot souboru cookie ve formátu HTML|
|943110|Možný útok v relaci = název parametru SessionID s Off-Domain odkazujícím|
|943120|Možný útok s možností záznamu relace = název parametru SessionID bez odkazujícího serveru|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">POŽADAVEK – 944 – APLIKAČNÍ – ÚTOK – SESSION-JAVA</p>

|RuleId|Description|
|---|---|
|944120|Možné spuštění datové části a vzdálené spuštění příkazu|
|944130|Podezřelé třídy jazyka Java|
|944200|Využití deserializace Java Apache|

# <a name="owasp-30"></a>[OWASP 3,0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a> Sady pravidel

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Obecné</p>

|RuleId|Description|
|---|---|
|200004|Možná nespárovaná hranice na více částí.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Description|
|---|---|
|911100|Metoda není u zásad povolená.|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Description|
|---|---|
|913100|Nalezené User-Agent přidružené ke skeneru zabezpečení|
|913110|Našla se Hlavička požadavku přidružená ke skeneru zabezpečení.|
|913120|Našel se název souboru/argument pro požadavek přidružený ke skeneru zabezpečení.|
|913101|Nalezené User-Agent přidružené ke skriptování nebo obecnému klientovi HTTP|
|913102|Nalezené User-Agent přidružené k webovému crawleru/bot|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Description|
|---|---|
|920100|Neplatný řádek požadavku HTTP|
|920130|Nepovedlo se analyzovat text požadavku.|
|920140|Tělo žádosti s více částmi se nepodařilo striktní ověření.|
|920160|Hlavička Content-Length protokolu HTTP není číselná.|
|920170|Získá nebo vrátí požadavek HEAD s obsahem textu.|
|920180|V požadavku POST chybí hlavička Content-Length.|
|920190|Range = neplatná hodnota posledního bajtu|
|920210|Bylo nalezeno více nebo konfliktních dat záhlaví připojení.|
|920220|Pokus o útok zneužití kódování adresy URL|
|920240|Pokus o útok zneužití kódování adresy URL|
|920250|Pokus o útok zneužití kódování UTF8|
|920260|Pokus o útok proti zneužití Unicode s plnou/poloviční šířkou|
|920270|Neplatný znak v žádosti (znak null)|
|920280|V požadavku chybí Hlavička hostitele.|
|920290|Prázdná Hlavička hostitele|
|920310|Požadavek má prázdnou hlavičku Accept.|
|920311|Požadavek má prázdnou hlavičku Accept.|
|920330|Prázdné záhlaví uživatelského agenta|
|920340|Požadavek obsahující obsah, ale chybějící záhlaví Content-Type|
|920350|Hlavička hostitele je numerická IP adresa.|
|920380|Příliš mnoho argumentů v žádosti|
|920360|Název argumentu je příliš dlouhý.|
|920370|Hodnota argumentu je moc dlouhá.|
|920390|Celková velikost argumentů byla překročena.|
|920400|Velikost nahraného souboru je moc velká.|
|920410|Celkový počet nahraných souborů je moc velký.|
|920420|Typ obsahu žádosti není povolený zásadou.|
|920430|Verze protokolu HTTP není v zásadě povolená.|
|920440|Přípona souboru adresy URL je omezená zásadami.|
|920450|Záhlaví HTTP je omezené zásadami (% @ {MATCHED_VAR}).|
|920200|Range = příliš mnoho polí (6 nebo více)|
|920201|Range = příliš mnoho polí pro požadavek PDF (35 nebo více)|
|920230|Bylo zjištěno více kódování URL.|
|920300|Požadavek neobsahuje hlavičku Accept.|
|920271|Neplatný znak v žádosti (netisknutelné znaky)|
|920320|Chybí záhlaví uživatelského agenta.|
|920272|Neplatný znak v žádosti (mimo tisknutelné znaky pod ASCII 127)|
|920202|Range = příliš mnoho polí pro požadavek PDF (6 nebo více)|
|920273|Neplatný znak v žádosti (mimo velmi striktní sadu)|
|920274|Neplatný znak v záhlavích žádostí (mimo velmi striktní sadu)|
|920460|Neobvyklé řídicí znaky|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Description|
|---|---|
|921100|Útok s podvržením požadavků HTTP|
|921110|Útok proti podvržení požadavku HTTP|
|921120|Útok na rozdělení HTTP odpovědi|
|921130|Útok na rozdělení HTTP odpovědi|
|921140|Útok prostřednictvím injektáže záhlaví HTTP prostřednictvím hlaviček|
|921150|Útok injektáže záhlaví HTTP prostřednictvím datové části (počet zjištěných znaků CR/LF)|
|921160|Útok na injektáže záhlaví HTTP prostřednictvím datové části (CR/LF a záhlaví-název byl zjištěn)|
|921151|Útok injektáže záhlaví HTTP prostřednictvím datové části (počet zjištěných znaků CR/LF)|
|921170|Znečištění parametru HTTP|
|921180|Znečišťování parametru HTTP (% @ {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Description|
|---|---|
|930100|Útok procházením cest (/.. /)|
|930110|Útok procházením cest (/.. /)|
|930120|Pokus o přístup k souboru operačního systému|
|930130|Omezený pokus o přístup k souboru|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Description|
|---|---|
|931100|Možný útok RFI (Remote File začlenění) = parametr URL s použitím IP adresy|
|931110|Možný útok na vzdálený soubor k zahrnutí (RFI) = běžný název parametru s chybou RFI, který používal datovou část adresy URL|
|931120|Možný útok na vzdálené zahrnutí souborů (RFI) = datová část adresy URL použité pro znak otazníku (?)|
|931130|Možný útok na RFI (Remote File zařazování) = odkaz na Off-Domain/odkaz|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Description|
|---|---|
|932120|Vzdálené spuštění příkazu = nalezen příkaz prostředí Windows PowerShell|
|932130|Vzdálené spuštění příkazu = Nalezeno výraz prostředí systému UNIX|
|932140|Vzdálené spuštění příkazu = Windows pro/Pokud se našel příkaz|
|932160|Vzdálené spuštění příkazu = nalezen kód prostředí systému UNIX|
|932170|Vzdálené spuštění příkazu = Shellshock (CVE-2014-6271)|
|932171|Vzdálené spuštění příkazu = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Description|
|---|---|
|933100|Útok injektáže PHP = našla se počáteční nebo uzavírací značka.|
|933110|Útok injektáže PHP = nahrání souboru skriptu PHP se našlo.|
|933120|Útok injektáže PHP = nalezená direktiva konfigurace|
|933130|Útok injektáže PHP = nalezené proměnné|
|933150|Útok injektáže PHP = High-Risk nalezen název funkce PHP|
|933160|Útok injektáže PHP = High-Risk nalezené volání funkce PHP|
|933180|Útok injektáže PHP = Nalezeno volání proměnné funkce|
|933151|Útok injektáže PHP = Medium-Risk nalezen název funkce PHP|
|933131|Útok injektáže PHP = nalezené proměnné|
|933161|Útok injektáže PHP = Low-Value nalezené volání funkce PHP|
|933111|Útok injektáže PHP = nahrání souboru skriptu PHP se našlo.|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Description|
|---|---|
|941100|Útok XSS zjištěný prostřednictvím libinjection|
|941110|Filtr XSS – kategorie 1 = vektor značky skriptu|
|941130|Filtr XSS – kategorie 3 = vektor atributu|
|941140|Filtr XSS – kategorie 4 = vektor URI JavaScriptu|
|941150|Filtr XSS – kategorie 5 = nepovolené atributy HTML|
|941180|Node-Validator zakázaná klíčová slova|
|941190|XSS pomocí šablon stylů|
|941200|XSS pomocí snímků VML|
|941210|XSS pomocí nepoužívaného JavaScriptu|
|941220|XSS pomocí nepoužívaného skriptu VB|
|941230|XSS pomocí tagu embed|
|941240|XSS s použitím atributu import nebo Implement|
|941260|XSS s použitím značky meta|
|941270|XSS používající odkaz href|
|941280|XSS pomocí tagu Base|
|941290|XSS s použitím značky applet|
|941300|XSS s použitím značky Object|
|941310|Byl zjištěn útok se špatným kódováním pro kódování XSS typu US-ASCII.|
|941330|Filtry v IE XSS – byl zjištěn útok.|
|941340|Filtry v IE XSS – byl zjištěn útok.|
|941350|Bylo zjištěno útoku kódování UTF-7 v IE XSS.|
|941320|Byl zjištěn možný útok XSS – obslužná rutina značky HTML|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Description|
|---|---|
|942100|Byl zjištěn útok prostřednictvím injektáže SQL prostřednictvím libinjection|
|942110|Útok injektáže SQL: byl zjištěn společný test injektáže.|
|942130|Útok injektáže SQL: byl zjištěn SQL Tautology.|
|942140|Útok injektáže SQL = zjištěny běžné názvy databází|
|942160|Detekuje nevidomé SQLi testy pomocí režimu spánku () nebo srovnávacího testu ().|
|942170|Detekuje srovnávací testy SQL a operace vkládání do režimu spánku včetně podmíněných dotazů.|
|942190|Detekuje provádění kódu MSSQL a pokusy o shromažďování informací|
|942200|Detekuje komentáře MySQL – vkládání/Space-obfuscated a ukončení zaškrtnutí.|
|942230|Detekuje podmíněné pokusy o injektáže SQL.|
|942260|Detekuje základní pokusy o obejití ověřování SQL 2/3|
|942270|Hledání základního vkládání SQL Běžný řetězec útoku pro MySQL Oracle a další.|
|942290|Vyhledá základní pokusy o vkládání SQL MongoDB.|
|942300|Detekuje komentáře MySQL, podmínky a injektáže r (ch) r|
|942310|Detekuje zřetězené pokusy o vkládání SQL 2/2|
|942320|Detekuje injektáže uložených procedur/funkcí MySQL a PostgreSQL.|
|942330|Detekuje bingy pro vkládání v klasických rozhraních SQL 1/2|
|942340|Detekuje základní pokusy o obejití ověřování SQL 3/3|
|942350|Detekuje injektáže systému MySQL UDF a další pokusy o manipulaci s daty/strukturou.|
|942360|Detekuje zřetězené základní vkládání SQL a pokusy SQLLFI.|
|942370|Detekuje bingy pro vkládání v klasických rozhraních SQL 2/2|
|942150|Útok injektáže SQL|
|942410|Útok injektáže SQL|
|942430|Omezené zjištění anomálií znaků SQL (args): počet speciálních znaků se překročil (12).|
|942440|Zjistila se sekvence komentářů SQL.|
|942450|Identifikované kódování šestnáctkového formátu SQL|
|942251|Detekuje, že se mají injektáže|
|942460|Výstraha detekce anomálií Meta-Character – opakované znaky, které nejsou v textu|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Description|
|---|---|
|943100|Možný útok z relace = nastavení hodnot souboru cookie ve formátu HTML|
|943110|Možný útok v relaci = název parametru SessionID s Off-Domain odkazujícím|
|943120|Možný útok s možností záznamu relace = název parametru SessionID bez odkazujícího serveru|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a> Sady pravidel

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|Description|
|---|---|
|960911|Neplatný řádek požadavku HTTP|
|981227|Chyba Apache = neplatný identifikátor URI v požadavku.|
|960912|Nepovedlo se analyzovat text požadavku.|
|960914|Tělo žádosti s více částmi se nepodařilo striktní ověření.|
|960915|Analyzátor částí zjistil možnou neshodnou hranici.|
|960016|Hlavička Content-Length protokolu HTTP není číselná.|
|960011|Získá nebo vrátí požadavek HEAD s obsahem textu.|
|960012|V požadavku POST chybí hlavička Content-Length.|
|960902|Neplatné použití kódování identity|
|960022|Očekává se, že hlavička není povolená pro HTTP 1,0.|
|960020|Hlavička pragma vyžaduje Cache-Control záhlaví pro požadavky HTTP/1.1.|
|958291|Rozsah = pole existuje a začíná na 0.|
|958230|Range = neplatná hodnota posledního bajtu|
|958295|Bylo nalezeno více nebo konfliktních dat záhlaví připojení.|
|950107|Pokus o útok zneužití kódování adresy URL|
|950109|Bylo zjištěno více kódování URL.|
|950108|Pokus o útok zneužití kódování adresy URL|
|950801|Pokus o útok zneužití kódování UTF8|
|950116|Pokus o útok proti zneužití Unicode s plnou/poloviční šířkou|
|960901|Neplatný znak v žádosti|
|960018|Neplatný znak v žádosti|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Description|
|---|---|
|960008|V požadavku chybí Hlavička hostitele.|
|960007|Prázdná Hlavička hostitele|
|960015|Požadavek neobsahuje hlavičku Accept.|
|960021|Požadavek má prázdnou hlavičku Accept.|
|960009|V požadavku chybí záhlaví uživatelského agenta.|
|960006|Prázdné záhlaví uživatelského agenta|
|960904|Požadavek obsahující obsah, ale chybějící záhlaví Content-Type|
|960017|Hlavička hostitele je numerická IP adresa.|

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|RuleId|Description|
|---|---|
|960209|Název argumentu je příliš dlouhý.|
|960208|Hodnota argumentu je moc dlouhá.|
|960335|Příliš mnoho argumentů v žádosti|
|960341|Celková velikost argumentů byla překročena.|
|960342|Velikost nahraného souboru je moc velká.|
|960343|Celkový počet nahraných souborů je moc velký.|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|RuleId|Description|
|---|---|
|960032|Metoda není u zásad povolená.|
|960010|Typ obsahu žádosti není povolený zásadou.|
|960034|Verze protokolu HTTP není v zásadě povolená.|
|960035|Přípona souboru adresy URL je omezená zásadami.|
|960038|Hlavička protokolu HTTP je omezená zásadami.|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|RuleId|Description|
|---|---|
|990002|Požadavek indikuje, že skener zabezpečení prohledal Web|
|990901|Požadavek indikuje, že skener zabezpečení prohledal Web|
|990902|Požadavek indikuje, že skener zabezpečení prohledal Web|
|990012|Prohledávací nástroj neautorizovaných webů|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|RuleId|Description|
|---|---|
|960024|Výstraha detekce anomálií Meta-Character – opakované znaky, které nejsou v textu|
|950008|Vkládání nedokumentovaných značek ColdFusion|
|950010|Útok injektáže LDAP|
|950011|Útok injektáže SSI|
|950018|Byla zjištěna adresa URL technologie Universal PDF XSS.|
|950019|Útok prostřednictvím injektáže e-mailu|
|950012|Útok s podvržením požadavků HTTP|
|950910|Útok na rozdělení HTTP odpovědi|
|950911|Útok na rozdělení HTTP odpovědi|
|950117|Útok na vzdálené zahrnutí souborů|
|950118|Útok na vzdálené zahrnutí souborů|
|950119|Útok na vzdálené zahrnutí souborů|
|950120|Možný útok na RFI (Remote File zařazování) = odkaz na Off-Domain/odkaz|
|981133|Pravidlo 981133|
|981134|Pravidlo 981134|
|950009|Útok na záznam relace|
|950003|Fixace relace|
|950000|Fixace relace|
|950005|Pokus o vzdálený přístup k souborům|
|950002|Přístup k systémovému příkazu|
|950006|Vkládání systémových příkazů|
|959151|Útok injektáže PHP|
|958976|Útok injektáže PHP|
|958977|Útok injektáže PHP|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Description|
|---|---|
|981231|Zjistila se sekvence komentářů SQL.|
|981260|Identifikované kódování šestnáctkového formátu SQL|
|981320|Útok injektáže SQL = zjištěny běžné názvy databází|
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
|981317|Výstraha detekce anomálií příkazu SELECT jazyka SQL|
|950007|Nevidomý útok injektáže SQL|
|950001|Útok injektáže SQL|
|950908|Útok prostřednictvím injektáže SQL.|
|959073|Útok injektáže SQL|
|981272|Detekuje nevidomé SQLi testy pomocí režimu spánku () nebo srovnávacího testu ().|
|981250|Detekuje srovnávací testy SQL a operace vkládání do režimu spánku včetně podmíněných dotazů.|
|981241|Detekuje podmíněné pokusy o injektáže SQL.|
|981276|Hledání základního vkládání SQL Běžný řetězec útoku pro MySQL Oracle a další.|
|981270|Vyhledá základní pokusy o vkládání SQL MongoDB.|
|981253|Detekuje injektáže uložených procedur/funkcí MySQL a PostgreSQL.|
|981251|Detekuje injektáže systému MySQL UDF a další pokusy o manipulaci s daty/strukturou.|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Description|
|---|---|
|973336|Filtr XSS – kategorie 1 = vektor značky skriptu|
|973338|Filtr XSS – kategorie 3 = vektor URI JavaScriptu|
|981136|Pravidlo 981136|
|981018|Pravidlo 981018|
|958016|Útok skriptování XSS (mezi weby)|
|958414|Útok skriptování XSS (mezi weby)|
|958032|Útok skriptování XSS (mezi weby)|
|958026|Útok skriptování XSS (mezi weby)|
|958027|Útok skriptování XSS (mezi weby)|
|958054|Útok skriptování XSS (mezi weby)|
|958418|Útok skriptování XSS (mezi weby)|
|958034|Útok skriptování XSS (mezi weby)|
|958019|Útok skriptování XSS (mezi weby)|
|958013|Útok skriptování XSS (mezi weby)|
|958408|Útok skriptování XSS (mezi weby)|
|958012|Útok skriptování XSS (mezi weby)|
|958423|Útok skriptování XSS (mezi weby)|
|958002|Útok skriptování XSS (mezi weby)|
|958017|Útok skriptování XSS (mezi weby)|
|958007|Útok skriptování XSS (mezi weby)|
|958047|Útok skriptování XSS (mezi weby)|
|958410|Útok skriptování XSS (mezi weby)|
|958415|Útok skriptování XSS (mezi weby)|
|958022|Útok skriptování XSS (mezi weby)|
|958405|Útok skriptování XSS (mezi weby)|
|958419|Útok skriptování XSS (mezi weby)|
|958028|Útok skriptování XSS (mezi weby)|
|958057|Útok skriptování XSS (mezi weby)|
|958031|Útok skriptování XSS (mezi weby)|
|958006|Útok skriptování XSS (mezi weby)|
|958033|Útok skriptování XSS (mezi weby)|
|958038|Útok skriptování XSS (mezi weby)|
|958409|Útok skriptování XSS (mezi weby)|
|958001|Útok skriptování XSS (mezi weby)|
|958005|Útok skriptování XSS (mezi weby)|
|958404|Útok skriptování XSS (mezi weby)|
|958023|Útok skriptování XSS (mezi weby)|
|958010|Útok skriptování XSS (mezi weby)|
|958411|Útok skriptování XSS (mezi weby)|
|958422|Útok skriptování XSS (mezi weby)|
|958036|Útok skriptování XSS (mezi weby)|
|958000|Útok skriptování XSS (mezi weby)|
|958018|Útok skriptování XSS (mezi weby)|
|958406|Útok skriptování XSS (mezi weby)|
|958040|Útok skriptování XSS (mezi weby)|
|958052|Útok skriptování XSS (mezi weby)|
|958037|Útok skriptování XSS (mezi weby)|
|958049|Útok skriptování XSS (mezi weby)|
|958030|Útok skriptování XSS (mezi weby)|
|958041|Útok skriptování XSS (mezi weby)|
|958416|Útok skriptování XSS (mezi weby)|
|958024|Útok skriptování XSS (mezi weby)|
|958059|Útok skriptování XSS (mezi weby)|
|958417|Útok skriptování XSS (mezi weby)|
|958020|Útok skriptování XSS (mezi weby)|
|958045|Útok skriptování XSS (mezi weby)|
|958004|Útok skriptování XSS (mezi weby)|
|958421|Útok skriptování XSS (mezi weby)|
|958009|Útok skriptování XSS (mezi weby)|
|958025|Útok skriptování XSS (mezi weby)|
|958413|Útok skriptování XSS (mezi weby)|
|958051|Útok skriptování XSS (mezi weby)|
|958420|Útok skriptování XSS (mezi weby)|
|958407|Útok skriptování XSS (mezi weby)|
|958056|Útok skriptování XSS (mezi weby)|
|958011|Útok skriptování XSS (mezi weby)|
|958412|Útok skriptování XSS (mezi weby)|
|958008|Útok skriptování XSS (mezi weby)|
|958046|Útok skriptování XSS (mezi weby)|
|958039|Útok skriptování XSS (mezi weby)|
|958003|Útok skriptování XSS (mezi weby)|
|973300|Byl zjištěn možný útok XSS – obslužná rutina značky HTML|
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
|973331|Filtry v IE XSS – byl zjištěn útok.|
|973315|Filtry v IE XSS – byl zjištěn útok.|
|973330|Filtry v IE XSS – byl zjištěn útok.|
|973327|Filtry v IE XSS – byl zjištěn útok.|
|973326|Filtry v IE XSS – byl zjištěn útok.|
|973346|Filtry v IE XSS – byl zjištěn útok.|
|973345|Filtry v IE XSS – byl zjištěn útok.|
|973324|Filtry v IE XSS – byl zjištěn útok.|
|973323|Filtry v IE XSS – byl zjištěn útok.|
|973348|Filtry v IE XSS – byl zjištěn útok.|
|973321|Filtry v IE XSS – byl zjištěn útok.|
|973320|Filtry v IE XSS – byl zjištěn útok.|
|973318|Filtry v IE XSS – byl zjištěn útok.|
|973317|Filtry v IE XSS – byl zjištěn útok.|
|973329|Filtry v IE XSS – byl zjištěn útok.|
|973328|Filtry v IE XSS – byl zjištěn útok.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|RuleId|Description|
|---|---|
|950103|Útok procházením cest|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|Description|
|---|---|
|950110|Přístup zadní vrátka|
|950921|Přístup zadní vrátka|
|950922|Přístup zadní vrátka|

---

## <a name="next-steps"></a>Další kroky

- [Přizpůsobení pravidel firewallu webových aplikací pomocí Azure Portal](application-gateway-customize-waf-rules-portal.md)
