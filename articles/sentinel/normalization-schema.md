---
title: Referenční informace schématu normalizace dat služby Azure Sentinel | Microsoft Docs
description: Tento článek zobrazuje schéma normalizace dat služby Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806931"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Referenční informace schématu normalizace dat služby Azure Sentinel

## <a name="terminology"></a>Terminologie

V schématech Sentinel Azure se používá následující terminologie:

| Označení | Definice |
| ---- | ---------- |
| Zařízení pro vytváření sestav | Systém odesílá záznamy do Azure Sentinel. Nemusí se jednat o systém předmětu záznamu. |
| Záznam | Jednotka dat odesílaná ze zařízení pro generování sestav. Tato možnost se často označuje jako "protokol", "událost" nebo "Výstraha", ale nemusí nutně být jednou z nich. |
|

## <a name="data-types-and-formats"></a>Typy a formáty dat

Hodnoty by měly být normalizovány na základě níže uvedených pokynů. Toto je povinné pro normalizovaná pole a doporučená pro jiná pole. 

| Datový typ | Fyzický typ | Formát a hodnota |
| --------- | ------------- | ---------------- |
| **Datum a čas** | V závislosti na použití schopnosti metody ingesta ve vzestupném pořadí:<ul><li>Log Analytics integrováno v typu DateTime</li><li>Celočíselné pole pomocí Log Analytics číselné reprezentace DateTime</li><li>Pole řetězce používající Log Analytics číselné reprezentace data a času</li></ul> | Log Analytics reprezentace data a času. <br></br>Log Analytics datum & čase je podobné jako v podstatě, ale liší se od času reprezentace v systému UNIX. Přečtěte si tyto pokyny k převodu. <br></br>Datum & čas by měl být upravený podle časového pásma. |
| **Adresa MAC** | Řetězec | Colon-Hexadecimal Notation |
| **IP adresa** | IP adresa | Schéma neobsahuje samostatné adresy IPv4 a IPv6. Každé pole IP adresy může zahrnovat adresu IPv4 nebo IPv6 adresu:<ul><li>IPv4 v desítkovém zápisu s tečkou</li><li>Protokol IPv6 v 8 hextets Notation umožňující krátké popsání zde popsaných krátkých forem.</li></ul> |
| **Uživatel** | Řetězec | K dispozici jsou následující 3 Uživatelská pole:<ul><li>Uživatelské jméno</li><li>Hlavní název uživatele (UPN)</li><li>Doména uživatele</li></ul> |
| **ID uživatele** | Řetězec | V současné době jsou podporovány následující 2 ID uživatelů:<ul><li>SID uživatele</li><li>ID služby Azure Active Directory</li></ul> |
| **Zařízení** | Řetězec | Podporují se tyto 3 sloupce zařízení/hostitele:<ul><li>ID</li><li>Name</li><li>Plně kvalifikovaný název domény (FQDN)</li></ul> |
| **Země** | Řetězec | Řetězec s použitím ISO 3166-1 podle této priority:<ul><li>Kódy alfa-2 (například US pro USA)</li><li>Kódy alfa-3 (tj. USA pro USA)</li><li>Krátký název</li></ul> |
| **Oblast** | Řetězec | Název země dílčí divize s použitím ISO 3166-2 |
| **City (Město)** | Řetězec | |
| **Bodu** | dvojité | Souřadnice ISO 6709 reprezentace (desetinné číslo se znaménkem) |
| **Šířce** | dvojité | Souřadnice ISO 6709 reprezentace (desetinné číslo se znaménkem) |
| **Algoritmus hash** | Řetězec | Podporují se tyto 4 sloupce hash:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Typ souboru** | Řetězec | Typ souboru:<ul><li>Linka</li><li>Třída</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Schéma tabulky síťových relací

Níže je schéma tabulky síťových relací, 1.0.0 se správou verzí.

| Název pole | Typ hodnoty | Příklad | Description | Přidružené entity OSSEM |
|-|-|-|-|-|
| Typ události | Řetězec | Provoz | Typ shromažďované události | Událost |
| EventSubType | Řetězec | Authentication | Další popis typu, pokud je k dispozici | Událost |
| EventCount | Integer  | 10 | Počet agregovaných událostí, pokud je k dispozici. | Událost |
| EventEndTime | Datum/Čas | Viz "datové typy" | Čas ukončení události | Událost |
| EventMessage | řetězec |  přístup byl odepřen | Obecná zpráva nebo popis, buď zahrnutý do, nebo vygenerovaný ze záznamu | Událost |
| DvcIpAddr | IP adresa |  23.21.23.34 | IP adresa zařízení, které záznam vygenerovala | Zařízení<br>IP adresa |
| DvcMacAddr | Řetězec | 06:10:9F: eb: 8F: 14 | Adresa MAC síťového rozhraní zařízení pro vytváření sestav, ze kterého byla událost odeslána. | Zařízení<br>Mac |
| DvcHostname | Název zařízení (řetězec) | syslogserver1.contoso.com | Název zařízení, které zprávu vygenerovala. | Zařízení |
| EventProduct | Řetězec | OfficeSharepoint | Produkt, který událost vygeneroval. | Událost |
| EventProductVersion | řetězec | 9.0 |  Verze produktu, který událost vygeneroval. | Událost |
| EventResourceId | ID zařízení (řetězec) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | ID prostředku zařízení, které zprávu vygenerovalo. | Událost |
| EventReportUrl | Řetězec | https://192.168.1.1/repoerts/ae3-56.htm | Odkaz na úplnou sestavu vytvořenou zařízením pro vytváření sestav | Událost |
| EventVendor | Řetězec | Microsoft | Dodavatel produktu, který událost vygeneroval. | Událost |
| EventResult | Více hodnot: úspěch, částečně, chyba, [prázdné] (řetězec) | Success | Výsledek nahlášený pro aktivitu Prázdná hodnota, pokud není k dispozici. | Událost |
| EventResultDetails | Řetězec | Chybné heslo | Důvod nebo podrobnosti pro výsledek nahlášený v EventResult | Událost |
| EventSchemaVersion | Skutečné | 0.1 | Verze schématu Sentinel Azure Aktuálně 0,1. | Událost |
| EventSeverity | Řetězec | Nízká | Pokud má nahlášená aktivita dopad na zabezpečení, označuje závažnost dopadu. | Událost |
| EventOriginalUid | Řetězec | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | ID záznamu ze zařízení pro vytváření sestav. | Událost |
| EventStartTime | Datum/Čas | Viz "datové typy" | Čas, kdy je uvedena událost | Událost |
| TimeGenerated | Datum/Čas | Viz "datové typy" | Čas, kdy došlo k události, jak je uvedeno ve zdroji generování sestav. | Vlastní pole |
| EventTimeIngested | Datum/Čas | Viz "datové typy" | Čas, kdy se událost přijala do služby Azure Sentinel. Přidá se pomocí Azure Sentinel. | Událost |
| EventUid | GUID (řetězec) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Jedinečný identifikátor používaný službou Azure Sentinel k označení řádku | Událost |
| NetworkApplicationProtocol | Řetězec | HTTPS | Protokol aplikační vrstvy používaný připojením nebo relací. | Síť |
| DstBytes | int | 32455 | Počet bajtů odeslaných z cíle do zdroje pro připojení nebo relaci. | Cíl |
| SrcBytes | int | 46536 | Počet bajtů odeslaných ze zdroje do cíle pro připojení nebo relaci. | Zdroj |
| NetworkBytes | int | 78991 | Počet bajtů odeslaných v obou směrech. Pokud existují obě BytesReceived i BytesSent, BytesTotal by se měla rovnat jejich součtu. | Síť |
| NetworkDirection | Multi-hodnota: příchozí, odchozí (řetězec) | Příchozí | Směr připojení nebo relace do organizace nebo mimo ni. | Síť |
| DstGeoCity | Řetězec | Burlington | Město přidružené k cílové IP adrese | Tabulka<br>Geografická oblast |
| DstGeoCountry | Země (String) | USA | Země přidružená ke zdrojové IP adrese | Tabulka<br>Geografická oblast |
| DstDvcHostname | Název zařízení (řetězec) |  victim_pc | Název zařízení cílového zařízení | Cíl<br>Zařízení |
| DstDvcFqdn | Řetězec | victim_pc. contoso. Local | Plně kvalifikovaný název domény hostitele, ve kterém byl protokol vytvořen | Tabulka<br>Zařízení |
| DstDomainHostname | řetězec | CONTOSO | Doména cílového umístění, doména cílového hostitele (web, název domény atd.), například pro vyhledávání DNS nebo pro vyhledávání NS. | Cíl |
| DstInterfaceName | řetězec | Microsoft Hyper-V síťový adaptér | Síťové rozhraní používané pro připojení nebo relaci cílového zařízení. | Cíl |
| DstInterfaceGuid | řetězec | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | Identifikátor GUID síťového rozhraní, který se použil pro požadavek na ověření  | Cíl |
| DstIpAddr | IP adresa | 2001: db8:: FF00:42:8329 | IP adresa připojení nebo cíle relace, která se nejčastěji označuje jako cílová IP adresa v síťovém paketu | Tabulka<br>IP adresa |
| DstDvcIpAddr | IP adresa | 75.22.12.2 | Cílová IP adresa zařízení, které není přímo přidruženo k síťovému paketu | Tabulka<br>Zařízení<br>IP adresa
| DstGeoLatitude | Zeměpisná šířka (Double) | 44,475833 | Zeměpisná šířka zeměpisné souřadnice přidružené k cílové IP adrese | Tabulka<br>Geografická oblast |
| DstMacAddr | Řetězec | 06:10:9F: eb: 8F: 14 | Adresa MAC síťového rozhraní, na kterém bylo připojení nebo relace ukončeno, nejčastěji označované jako cílový počítač MAC v síťovém paketu | Tabulka<br>POČÍTAČE |
| DstDvcMacAddr | Řetězec | 06:10:9F: eb: 8F: 14 | Cílová adresa MAC zařízení, které není přímo přidruženo k síťovému paketu. | Tabulka<br>Zařízení<br>POČÍTAČE |
| DstDvcDomain | Řetězec | CONTOSO | Doména cílového zařízení. | Tabulka<br>Zařízení |
| DstPortNumber | Integer | 443 | Cílový port IP. | Tabulka<br>Port |
| DstGeoRegion | Oblast (řetězec) | Vermont | Oblast v rámci země přidružená k cílové IP adrese | Tabulka<br>Geografická oblast |
| DstResourceId | ID zařízení (řetězec) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | ID prostředku cílového zařízení. | Cíl |
| DstNatIpAddr | IP adresa | 2::1 | Pokud je nahlášená zprostředkujícím zařízením NAT, jako je brána firewall, IP adresa používaná zařízením NAT pro komunikaci se zdrojem. | Cílové NAT,<br>IP adresa |
| DstNatPortNumber | int | 443 | Pokud je nahlášená zprostředkujícím zařízením NAT, jako je brána firewall, port používaný zařízením NAT pro komunikaci se zdrojem. | Cílové NAT,<br>Port |
| DstUserSid | SID uživatele |  S-12-1445 | ID uživatele identity přidružené k cíli relace. Obvykle se jedná o identitu, která se používá k ověření serveru. Podrobnosti najdete v tématu datové typy. | Tabulka<br>User |
| DstUserAadId | Řetězec (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | ID objektu účtu Azure AD uživatele na cílovém konci relace | Tabulka<br>User |
| DstUserName | Uživatelské jméno (String) | Jan | Uživatelské jméno identity přidružené k cíli relace.  | Tabulka<br>User |
| DstUserUpn | řetězec | johnd@anon.com | Hlavní název uživatele identity přidružené k cíli relace. | Tabulka<br>User |
| DstUserDomain | řetězec | SKUPINÌ | Název domény nebo počítače účtu v cíli relace | Tabulka<br>User |
| DstZone | Řetězec | DMZ | Zóna sítě cíle, jak je definována zařízením pro vytváření sestav. | Cíl |
| DstGeoLongitude | Zeměpisná délka (Double) | -73,211944 | Zeměpisná délka zeměpisné souřadnice přidružené k cílové IP adrese | Tabulka<br>Geografická oblast |
| DvcAction | Multi-hodnota: Allow, Deny, Drop (String) | Povolit | Pokud je hlášeno zprostředkujícím zařízením, jako je brána firewall, akce provedená zařízením. | Zařízení |
| DvcInboundInterface | Řetězec | eth0 | Pokud je hlášeno zprostředkujícím zařízením, jako je brána firewall, síťové rozhraní používané pro připojení ke zdrojovému zařízení. | Zařízení |
| DvcOutboundInterface | Řetězec  | Ethernetový adaptér Ethernet 4 | Pokud je hlášeno zprostředkujícím zařízením, jako je brána firewall, síťové rozhraní používané pro připojení k cílovému zařízení. | Zařízení |
| NetworkDuration | Integer | 1 500 | Doba, po kterou se v milisekundě dokončuje síťová relace nebo připojení | Síť |
| NetworkIcmpCode | Integer | 34 | V případě zprávy ICMP je číselná hodnota typu zpráva ICMP (RFC 2780 nebo RFC 4443). | Síť |
| NetworkIcmpType | Řetězec | Nedosažitelný cíl | V případě zprávy protokolu ICMP text typu zpráva ICMP (RFC 2780 nebo RFC 4443). | Síť |
| DstPackets | int  | 446 | Počet paketů odeslaných z cíle do zdroje pro připojení nebo relaci. Význam paketu je definován zařízením pro vytváření sestav. | Cíl |
| SrcPackets | int  | 6478 | Počet paketů odeslaných ze zdroje do cíle pro připojení nebo relaci. Význam paketu je definován zařízením pro vytváření sestav. | Zdroj |
| NetworkPackets | int  | 0 | Počet paketů odeslaných v obou směrech. Pokud existují obě PacketsReceived i PacketsSent, BytesTotal by se měla rovnat jejich součtu. | Síť |
| HttpRequestTime | Integer | 700 | Čas potřebný k odeslání žádosti na server, pokud je k dispozici. | HTTP |
| HttpResponseTime | Integer | 800 | Doba, kterou trvala příjem odpovědi na serveru (je-li k dispozici). | HTTP |
| NetworkRuleName | Řetězec | AnyAnyDrop | Název nebo ID pravidla, podle kterého se DeviceAction rozhodl | Síť |
| NetworkRuleNumber | int |  23 | Spárované číslo pravidla  | Síť |
| NetworkSessionId | řetězec | 172_12_53_32_4322__123_64_207_1_80 | Identifikátor relace, který je hlášen zařízením pro vytváření sestav. Například identifikátor relace L7 pro konkrétní aplikace následující po ověření | Síť |
| SrcGeoCity | Řetězec | Burlington | Město přidružené ke zdrojové IP adrese | Zdrojová<br>Geografická oblast |
| SrcGeoCountry | Země (String) | USA | Země přidružená ke zdrojové IP adrese | Zdrojová<br>Geografická oblast |
| SrcDvcHostname | Název zařízení (řetězec) |  villain | Název zařízení zdrojového zařízení | Zdrojová<br>Zařízení |
| SrcDvcFqdn | řetězec | Villain.malicious.com | Plně kvalifikovaný název domény hostitele, ve kterém byl protokol vytvořen | Zdrojová<br>Zařízení |
| SrcDvcDomain | řetězec | EVILORG | Doména zařízení, ze kterého byla zahájena relace | Zdrojová<br>Zařízení |
| SrcDvcOs | Řetězec | iOS | Operační systém zdrojového zařízení | Zdrojová<br>Zařízení |
| SrcDvcModelName | Řetězec | Galaxy Samsung – Poznámka | Název modelu zdrojového zařízení | Zdrojová<br>Zařízení |
| SrcDvcModelNumber | Řetězec | 10.0 | Číslo modelu zdrojového zařízení | Zdrojová<br>Zařízení |
| SrcDvcType | Řetězec | Mobilní | Typ zdrojového zařízení | Zdrojová<br> Zařízení |
| SrcIntefaceName | Řetězec | eth01 | Síťové rozhraní používané pro připojení nebo relaci ze zdrojového zařízení. | Zdroj |
| SrcInterfaceGuid | Řetězec | 46ad544b-eaf0-47ef-827c-266030f545a6 | Identifikátor GUID použitého síťového rozhraní | Zdroj |
| SrcIpAddr | IP adresa | 77.138.103.108 | IP adresa, ze které pochází připojení nebo relace. | Zdrojová<br>IP adresa |
| SrcDvcIpAddr | IP adresa | 77.138.103.108 | Zdrojová IP adresa zařízení, které není přímo přidruženo k síťovému paketu (shromážděnému poskytovatelem nebo explicitně vypočítaným). | Zdrojová<br>Zařízení<br>IP adresa |
| SrcGeoLatitude | Zeměpisná šířka (Double) | 44,475833 | Zeměpisná šířka zeměpisné souřadnice přidružené ke zdrojové IP adrese | Zdrojová<br>Geografická oblast |
| SrcGeoLongitude | Zeměpisná délka (Double) | -73,211944 | Zeměpisná délka zeměpisné souřadnice přidružené ke zdrojové IP adrese | Zdrojová<br>Geografická oblast |
| SrcMacAddr | Řetězec | 06:10:9F: eb: 8F: 14 | Adresa MAC síťového rozhraní, ze kterého se vytvořilo připojení od relace. | Zdrojová<br>Mac |
| SrcDvcMacAddr | Řetězec | 06:10:9F: eb: 8F: 14 | Zdrojová adresa MAC zařízení, které není přímo přidruženo k síťovému paketu. | Zdrojová<br>Zařízení<br>Mac |
| SrcPortNumber | Integer | 2335 | Port IP, ze kterého bylo připojení vytvořeno. Nemusí být relevantní pro relaci zahrnující více připojení. | Zdrojová<br>Port |
| SrcGeoRegion | Oblast (řetězec) | Vermont | Oblast v rámci země přidružená ke zdrojové IP adrese | Zdrojová<br>Geografická oblast |
| SrcResourceId | Řetězec | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | ID prostředku zařízení, které zprávu vygenerovalo. | Zdroj |
| SrcNatIpAddr | IP adresa | 4.3.2.1 | Pokud je hlášená zprostředkujícím zařízením NAT, jako je brána firewall, IP adresa používaná zařízením NAT pro komunikaci s cílem. | Překlad zdrojového překladu dat<br>IP adresa |
| SrcNatPortNumber | Integer | 345 | Pokud je nahlášená zprostředkujícím zařízením NAT, jako je brána firewall, port používaný zařízením NAT pro komunikaci s cílem. | Překlad zdrojového překladu dat<br>Port |
| SrcUserSid | ID uživatele (řetězec) | S-15-1445 | ID uživatele identity přidružené ke zdroji relací Obvykle uživatel provádí na klientovi akci. Podrobnosti najdete v tématu datové typy. | Zdrojová<br>User |
| SrcUserAadId | Řetězec (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | ID objektu účtu Azure AD uživatele na konci relace | Zdrojová<br>User |
| SrcUserName | Uživatelské jméno (String) | Bobem | Uživatelské jméno identity přidružené ke zdroji relací. Obvykle uživatel provádí na klientovi akci. Podrobnosti najdete v tématu datové typy. | Zdroj<br>User |
| SrcUserUpn | řetězec | bob@alice.com | Hlavní název uživatele účtu, který zahajuje relaci | Zdrojová<br>User |
| SrcUserDomain | řetězec | DESKTOPU | Doména pro účet, který zahajuje relaci | Zdrojová<br>User |
| SrcZone | Řetězec | Klepnutí | Síťová zóna zdroje, jak je definována zařízením pro vytváření sestav. | Zdroj |
| NetworkProtocol | Řetězec | TCP | Protokol IP použitý připojením nebo relací. Typicky, TCP, UDP nebo ICMP | Síť |
| CloudAppName | Řetězec | Facebook | Název cílové aplikace pro aplikaci HTTP identifikovanou proxy serverem. | Cloud |
| CloudAppId | Řetězec | 124 | ID cílové aplikace pro aplikaci HTTP identifikované proxy serverem. Tato hodnota je obvykle specifická pro použitý proxy server. | Cloud |
| CloudAppOperation | Řetězec | DeleteFile | Operace, kterou uživatel provedl v kontextu cílové aplikace pro aplikaci HTTP, jak je identifikovaný proxy serverem. Tato hodnota je obvykle specifická pro použitý proxy server. | Cloud |
| CloudAppRiskLevel | Řetězec | 3 | Úroveň rizika přidružená k aplikaci HTTP zjištěná proxy serverem. Tato hodnota je obvykle specifická pro použitý proxy server. | Cloud |
| Bitmap | Řetězec | ImNotMalicious.exe | Název souboru přenášeného přes síťová připojení pro protokoly, jako jsou FTP a HTTP, které obsahují informace o názvu souboru. | Soubor |
| FilePath | Řetězec | C:\Malicious\ImNotMalicious.exe | Úplná cesta, včetně názvu souboru, souboru | Soubor |
| FileHashMd5 | Řetězec | 51BC68715FC7C109DCEA406B42D9D78F | Hodnota hash MD5 souboru přenášeného přes síťová připojení pro protokoly. | Soubor |
| FileHashSha1 | Řetězec | 491AE3... C299821476F4 | Hodnota hash SHA1 souboru přenášeného přes síťová připojení pro protokoly. | Soubor |
| FileHashSha256 | Řetězec | 9B8F8EDB... C129976F03 | Hodnota hash SHA256 souboru přenášeného přes síťová připojení pro protokoly. | Soubor |
| FileHashSha512 | Řetězec | 5E127D... F69F73F01F361 | Hodnota hash SHA512 souboru přenášeného přes síťová připojení pro protokoly. | Soubor |
| FileExtension |  Řetězec | programu | Typ souboru přenášeného přes síťová připojení pro protokoly, jako jsou FTP a HTTP. | Soubor
| MimeType | Řetězec | aplikace/MSWord | Typ MIME souboru přenášeného přes síťová připojení pro protokoly, jako jsou FTP a HTTP | Soubor |
| Velikost souboru | Integer | 23500 | Velikost souboru (v bajtech) přenášeného přes síťová připojení pro protokoly. | Soubor |
| HttpVersion | Řetězec | 2.0 | Verze požadavku HTTP pro síťová připojení HTTP/HTTPS. | HTTP |
| HttpRequestMethod | Řetězec | GET | Metoda HTTP pro síťové relace HTTP/HTTPS. | HTTP |
| HttpStatusCode | Řetězec | 404 | Stavový kód protokolu HTTP pro síťové relace HTTP/HTTPS. | HTTP |
| HttpContentType | Řetězec | multipart/form-data; hranice = něco | Hlavička Content-Type odpovědi HTTP pro síťové relace HTTP/HTTPS. | HTTP |
| HttpReferrerOriginal | Řetězec | https://developer.mozilla.org/en-US/docs/Web/JavaScript | Hlavička odkazujícího HTTP pro síťové relace HTTP/HTTPS. | HTTP |
| HttpUserAgentOriginal | Řetězec | Mozilla/5.0 (Windows NT 10,0; WOW64) AppleWebKit/537.36 (KHTML, jako je prostředí Gecko) Chrome/83.0.4103.97 Safari/537.36 | Hlavička uživatelského agenta protokolu HTTP pro síťové relace HTTP/HTTPS. | HTTP |
| HttpRequestXff | Řetězec | 120.12.41.1 | Hlavička protokolu HTTP X-for pro síťové relace HTTP/HTTPS. | HTTP |
| UrlCategory | Řetězec | Vyhledávací weby | Definované seskupení adresy URL (nebo může být založené na doméně v adrese URL) související s tím, co je (tj.: dospělý, novinky, inzerce, zaparkované domény atd.) | url |
| UrlOriginal | Řetězec | https://contoso.com/fo/?k=v&q = u # f | Adresa URL požadavku HTTP pro síťové relace HTTP/HTTPS. | URL |
| UrlHostname | Řetězec | contoso.com | Doménová část adresy URL požadavku HTTP pro síťové relace HTTP/HTTPS. | URL |
| ThreatCategory | Řetězec | Program | Kategorie hrozby identifikované systémem zabezpečení, jako je třeba webová brána zabezpečení IP adres a je přidružená k této síťové relaci. | Hrozba |
| ThreatId | Řetězec | TR. 124 | ID hrozby identifikované systémem zabezpečení, jako je třeba webová brána zabezpečení IP adres a je přidružená k této síťové relaci. | Hrozba |
| Threat | Řetězec | Testovací soubor EICAR | Název zjištěné hrozby nebo malwaru | Hrozba |
| AdditionalFields | Dynamické (kontejner JSON) | {<br>Vlastnost1: "Val1",<br>Vlastnost2: "Val2"<br>} | Pokud žádný odpovídající sloupec ve schématu neodpovídá, další pole mohou být uložena v kontejneru JSON.<br>Při analýze dotazu doporučujeme tuto metodu nepoužívat jako data balení do formátu JSON, čímž dojde ke snížení výkonu dotazů. Místo toho doporučujeme zvýšit úroveň dalších sloupců.<br>V budoucích scénářích analýzy doby přijímání dat se do tohoto sloupce kontejneru JSON shromáždí další data. | Vlastní pole |
| 
