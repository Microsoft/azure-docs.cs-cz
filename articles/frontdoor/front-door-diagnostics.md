---
title: Monitorování metrik a protokolů ve službě Azure branou | Dokumentace Microsoftu
description: Tento článek popisuje různé požadované metriky a přístup k protokolům, které podporuje služba Azure branou
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
ms.openlocfilehash: 98aabf5330589bf80f1653bb2882c015a4bc133c
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862101"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitorování metrik a protokolů ve službě Azure branou

S použitím branou služby Azure, můžete monitorovat prostředky následujícími způsoby:

- **Metriky**. Služba Application Gateway nyní má hlavní sedm metriky, chcete-li zobrazit čítače výkonu.
- **Protokoly**. Diagnostické protokoly aktivity a umožní výkonu, přístupu a další data k ukládání nebo spotřebované prostředků pro účely monitorování.

### <a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, které umožňují zobrazit čítače výkonu na portálu. Tady jsou k dispozici branou metriky:

| Metrika | Metriky zobrazovaný název | Jednotka | Dimenze | Popis |
| --- | --- | --- | --- | --- |
| RequestCount | Počet požadavků | Počet | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet požadavků klientů obsluhuje branou.  |
| RequestSize | Velikost požadavku | B | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných na požadavky klientů na branou. |
| ResponseSize | Velikost odpovědi | B | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako odpovědi z přední dveře do klientů. |
| TotalLatency | Celková latence | Milisekund | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Čas se počítají na základě požadavku klienta přijatých branou až do posledního bajtu odpovědi z přední dveře potvrzeny klienta. |
| BackendRequestCount | Počet požadavků back-endu | Počet | HttpStatus</br>HttpStatusGroup</br>Back-end | Počet požadavků odeslaných z přední dveře k back-endů. |
| BackendRequestLatency | Odezva back-endu požadavku | Milisekund | Back-end | Čas se počítají na základě když žádost byla odeslána branou back-endu dokud branou poslední bajt odpovědi přijaté z back-endu. |
| BackendHealthPercentage | Procento stav back-endu | Procento | Back-end</br>BackendPool | Procento úspěšných stavů testy z přední dveře k back-endů. |
| WebApplicationFirewallRequestCount | Počet požadavků brány Firewall webových aplikací | Počet | PolicyName</br>RuleName</br>Akce | Počet žádostí klienta zpracovaných rozhraním zabezpečení vrstvy aplikace branou. |

## <a name="activity-log"></a>Protokoly aktivit

Protokoly aktivit poskytují informace o operacích v branou služby. Co, také určují, kdo a kdy se pro všechny operace (put, post nebo delete) provedených ve branou služby zápisu.

>[!NOTE]
>Protokoly aktivit nezahrnují operace čtení (get). Zahrnují taky, že není operace, které můžete provést pomocí webu Azure portal nebo původní rozhraní API pro správu.

Protokoly aktivit přístupu ve vaší službě branou nebo všechny protokoly za prostředky Azure ve službě Azure Monitor. Zobrazení protokolů aktivit:

1. Vyberte instanci branou.
2. Vyberte **protokolu aktivit**.

    ![Protokol aktivit](./media/front-door-diagnostics/activity-log.png)

3. Zvolte některou filtrování a pak vyberte **použít**.

## <a name="diagnostic-logging"></a>Diagnostické protokoly
Diagnostické protokoly poskytují spoustu informací o operacích a chybách, které jsou důležité pro auditování a odstraňování potíží. Diagnostické protokoly se liší od protokolů aktivit.

Protokoly aktivit poskytují přehled o operacích na prostředky Azure. Diagnostické protokoly poskytují přehled o operacích, které provedly vaše prostředky. Další informace najdete v tématu [diagnostické protokoly Azure monitoru](../azure-monitor/platform/diagnostic-logs-overview.md).

![Diagnostické protokoly](./media/front-door-diagnostics/diagnostic-log.png)

Konfigurace diagnostických protokolů pro vaši službu branou:

1. Vyberte službu Azure API Management.

2. Zvolte **nastavení diagnostiky**.

3. Vyberte **zapnout diagnostiku**. Archivace diagnostických protokolů společně s metrikami na účet úložiště, Streamovat do centra událostí nebo odeslat protokoly Azure monitoru.

Branou služby v současné době poskytuje diagnostické protokoly (dávkované po hodinách). Diagnostické protokoly poskytují jednotlivých požadavků rozhraní API, kde má každá položka následující schéma:

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

- [Vytvoření profilu branou](quickstart-create-front-door.md)
- [Jak funguje branou](front-door-routing-architecture.md)
