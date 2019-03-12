---
title: Služba Azure branou – metrik a protokolování | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit různé požadované metriky a přístup k protokolům, které podporuje služba Azure branou
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: d409d451385ba1f88189b12cf372845e70a87429
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726366"
---
# <a name="monitoring-metrics-for-front-door"></a>Monitorování metrik pro branou

S použitím branou služby Azure, můžete monitorovat prostředky následujícími způsoby:
* [Protokoly](#diagnostic-logging): Protokoly umožňují výkonu, přístupu a další data k ukládání nebo spotřebované prostředků pro účely monitorování.

* [Metriky](#metrics): Služba Application Gateway nyní má hlavní sedm metriky, chcete-li zobrazit čítače výkonu.

## <a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, kde můžete zobrazit čítače výkonu na portálu. Pro přední dveře jsou dostupné následující metriky:

| Metrika | Metriky zobrazovaný název | Jednotka | Dimenze | Popis |
| --- | --- | --- | --- | --- |
| RequestCount | Počet požadavků | Počet | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet požadavků klientů obsluhuje branou.  |
| RequestSize | Velikost požadavku | B | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných na požadavky klientů na branou. |
| ResponseSize | Velikost odpovědi | B | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako odpovědi z přední dveře do klientů. |
| TotalLatency | Celková latence | Milisekund | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Čas se počítají na základě když žádost klienta byla přijata branou až do posledního bajtu odpovědi z přední dveře potvrzeny klienta. |
| BackendRequestCount | Počet požadavků back-endu | Počet | HttpStatus</br>HttpStatusGroup</br>Back-end | Počet požadavků odeslaných z přední dveře k back-endů. |
| BackendRequestLatency | Odezva back-endu požadavku | Milisekund | Back-end | Čas se počítají na základě když žádost byla odeslána branou back-endu dokud branou poslední bajt odpovědi přijaté z back-endu. |
| BackendHealthPercentage | Procento stav back-endu | Procento | Back-end</br>BackendPool | Procento úspěšných stavů testy z přední dveře k back-endů. |
| WebApplicationFirewallRequestCount | Počet požadavků brány Firewall webových aplikací | Počet | PolicyName</br>RuleName</br>Akce | Počet žádostí klienta zpracovaných rozhraním zabezpečení vrstvy aplikace branou. |

## <a name="activity-log"></a>Protokoly aktivit

Protokoly aktivit poskytují přehled o operacích provedených ve vašich branou. Pomocí protokolů aktivit můžete určit "co, kdo a kdy" veškerých operací (PUT, POST, DELETE) provedených ve vašich branou zápisu.

> [!NOTE]
> Protokoly aktivit nezahrnují operace čtení (GET) ani operace prováděné na webu Azure Portal nebo pomocí původních rozhraní API pro správu.

Můžete získat přístup k protokolům aktivit ve vaší branou nebo získat přístup ze všech vašich prostředků Azure ve službě Azure Monitor. 

Zobrazení protokolů aktivit:

1. Vyberte instanci branou.
2. Klikněte na **Protokol aktivit**.

    ![Protokol aktivit](./media/front-door-diagnostics/activity-log.png)

3. Vyberte požadovaný obor filtrování a klikněte na **Použít**.

## <a name="diagnostic-logging"></a>Diagnostické protokoly
Diagnostické protokoly poskytují spoustu informací o operacích a chybách, které jsou důležité pro audit i pro účely odstraňování potíží. Diagnostické protokoly se liší od protokolů aktivit. Protokoly aktivit poskytují přehled o operacích provedených ve vašich prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které provedly vaše prostředky. Další informace o [diagnostické protokoly Azure monitoru](..\azure-monitor\platform\diagnostic-logs-overview.md). 

Konfigurace diagnostických protokolů pro vaše vstupní brána:

1. Vyberte instanci služby APIM.
2. Klikněte na **Nastavení diagnostiky**.

    ![Diagnostické protokoly](./media/front-door-diagnostics/diagnostic-log.png)

3. Klikněte na **Zapnout diagnostiku**. Archivace diagnostických protokolů společně s metrikami na účet úložiště, Streamovat do centra událostí nebo odeslat protokoly Azure monitoru. 

Služba Azure branou v současné době poskytuje diagnostické protokoly (dávkované po hodinách) o jednotlivých API žádosti, kde má každá položka následující schéma:

| Vlastnost  | Popis |
| ------------- | ------------- |
| ClientIp | IP adresa klienta, který vytvořil požadavek. |
| ClientPort | Port IP klienta, ze kterého přišel požadavek. |
| HttpMethod | Metoda HTTP použitá v požadavku. |
| HttpStatusCode | Stavový kód HTTP vrácený z proxy serveru. |
| HttpStatusDetails | Výsledný stav žádosti. Smyslu tento řetězec hodnotu lze najít na stav referenční tabulku. |
| HttpVersion | Typ požadavku nebo připojení. |
| RequestBytes | Velikost zprávy s požadavkem HTTP v bajtech, včetně hlaviček žádostí a textu požadavku. |
| RequestUri | Identifikátor URI byl přijat požadavek. |
| ResponseBytes | Počet odeslaných bajtů podle back-end serveru jako odpověď.  |
| RoutingRuleName | Název pravidla směrování, který odpovídá požadavku. |
| SecurityProtocol | Verze protokolu TLS/SSL používá požadavku nebo hodnota null, pokud bez šifrování. |
| timeTaken | Dlouhá doba, kterou trvala daná akce v milisekundách. |
| UserAgent | Typ prohlížeče, kterou klient použil |
| TrackingReference | Odkaz na jedinečný řetězec, který identifikuje požadavek obsluhuje branou, také odeslat jako hlavičku X-Azure-Ref klientovi. Vyžaduje se pro vyhledání podrobností v přístup k protokolům pro konkrétní žádost. |

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
