---
title: Monitorování metrik a protokolů ve službě Azure front-dveří | Microsoft Docs
description: Tento článek popisuje různé metriky a protokoly přístupu, které služba front-dveří Azure podporuje.
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
ms.openlocfilehash: 5f76df0045fc3939392759ed0edd266380295a85
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260174"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitorování metrik a protokolů ve službě Azure front-dveří

Pomocí služby Azure front-dveří můžete monitorovat prostředky následujícími způsoby:

- **Metriky**. Application Gateway v současné době má sedm metrik pro zobrazení čítačů výkonu.
- **Protokoly**. Protokoly aktivit a diagnostiky umožňují uložit nebo spotřebovat data z prostředku pro účely monitorování.

### <a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, které umožňují zobrazit čítače výkonu na portálu. K dispozici jsou následující metriky front dveří:

| Metrika | Metriky zobrazovaný název | Jednotka | Dimenze | Popis |
| --- | --- | --- | --- | --- |
| RequestCount | Počet požadavků | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet požadavků klientů poskytovaných předními dveřmi.  |
| requestSize | Velikost požadavku | B | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako požadavek od klientů do předních dveří. |
| responseSize | Velikost odpovědi | B | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako odpověď z front-dveří klientům. |
| TotalLatency | Celková latence | Milisekundy | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Čas vypočítaný z požadavku klienta obdrženého předními dvířky, dokud klient nepotvrdí poslední bajt odpovědi z předních dveří. |
| BackendRequestCount | Počet požadavků back-endu | Count | HttpStatus</br>HttpStatusGroup</br>Back-end | Počet požadavků odeslaných z předních dveří do back-endu. |
| BackendRequestLatency | Latence žádosti back-endu | Milisekundy | Back-end | Čas vypočítaný z doby, kdy byla žádost odeslána přes dvířka do back-endu, dokud přední dveře nedostaly poslední bajt odezvy z back-endu. |
| BackendHealthPercentage | Procento stavu back-endu | Percent | Back-end</br>BackendPool | Procento úspěšných sond stavu z předních dveří do back-endu. |
| WebApplicationFirewallRequestCount | Počet požadavků firewallu webových aplikací | Count | PolicyName</br>RuleName</br>Action | Počet požadavků klientů zpracovaných zabezpečením aplikační vrstvy předních dveří. |

## <a name="activity-log"></a>Protokoly aktivit

Protokoly aktivit obsahují informace o operacích provedených u služby front-dveří. Určují taky to, kdo a kdy se u všech operací zápisu (Put, post nebo Delete) provádí na službě front-dveří.

>[!NOTE]
>Protokoly aktivit nezahrnují operace čtení (Get). Nezahrnují také operace, které provádíte pomocí Azure Portal nebo původního rozhraní API pro správu.

Přístup k protokolům aktivit ve službě front-dveří nebo ve všech protokolech prostředků Azure v Azure Monitor. Zobrazení protokolů aktivit:

1. Vyberte instanci front-dveří.
2. Vyberte **Protokol aktivit**.

    ![Protokol aktivit](./media/front-door-diagnostics/activity-log.png)

3. Zvolte rozsah filtrování a pak vyberte **použít**.

## <a name="diagnostic-logging"></a>Diagnostické protokoly
Diagnostické protokoly poskytují obsáhlé informace o operacích a chybách, které jsou důležité pro auditování a řešení potíží. Diagnostické protokoly se liší od protokolů aktivit.

Protokoly aktivit poskytují přehled o operacích provedených v prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které provedl váš prostředek. Další informace najdete v tématu [Azure monitor diagnostické protokoly](../azure-monitor/platform/resource-logs-overview.md).

![Diagnostické protokoly](./media/front-door-diagnostics/diagnostic-log.png)

Konfigurace diagnostických protokolů pro službu front-dveří:

1. Vyberte službu Azure front-dveří.

2. Vyberte **nastavení diagnostiky**.

3. Vyberte **zapnout diagnostiku**. Archivujte diagnostické protokoly spolu se metrikami do účtu úložiště, streamujte je do centra událostí nebo je odešlete do protokolů Azure Monitor.

Služba front-dveří aktuálně poskytuje diagnostické protokoly (dávkování po hodinách). Diagnostické protokoly poskytují jednotlivé požadavky rozhraní API s každou položkou, která má následující schéma:

| Vlastnost  | Popis |
| ------------- | ------------- |
| IP adresa klienta | IP adresa klienta, který odeslal požadavek. |
| clientPort | Port IP klienta, který odeslal požadavek. |
| HttpMethod | Metoda HTTP, kterou požadavek používá |
| HttpStatusCode | Stavový kód HTTP vrácený z proxy serveru. |
| HttpStatusDetails | Výsledný stav žádosti. Význam této řetězcové hodnoty lze nalézt v tabulce odkazů na stav. |
| HttpVersion | Typ požadavku nebo připojení |
| RequestBytes | Velikost zprávy požadavku HTTP v bajtech, včetně hlaviček požadavků a textu žádosti. |
| requestUri | Identifikátor URI přijatého požadavku |
| ResponseBytes | Bajty odeslané back-end serverem jako odpověď  |
| RoutingRuleName | Název pravidla směrování, na které se požadavek shodoval. |
| Tato SecurityProtocol | Verze protokolu TLS/SSL používaná požadavkem nebo hodnotou null, pokud není šifrování. |
| timeTaken | Doba, kterou trvala akce, v milisekundách. |
| UserAgent | Typ prohlížeče použitý klientem |
| TrackingReference | Jedinečný referenční řetězec, který identifikuje požadavek, který je obsluhován předními dvířky, je také odeslán jako hlavička X-Azure-ref na klienta. Vyžaduje se pro hledání podrobností v protokolech přístupu pro konkrétní požadavek. |

## <a name="next-steps"></a>Další kroky

- [Vytvoření profilu front-dveří](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)
