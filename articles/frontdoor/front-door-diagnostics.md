---
title: Monitorování metrik a protokolů v dopředných dveřích Azure | Microsoft Docs
description: Tento článek popisuje různé metriky a protokoly přístupu, které podporuje přední dvířka Azure.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: duau
ms.openlocfilehash: 6f5051dd7dedcc49320557f17148bcdc9bf539ab
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399748"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitorování metrik a protokolů v frontách Azure na předních dveřích

Pomocí front-dveří Azure můžete monitorovat prostředky následujícími způsoby:

- **Metriky**. Přední dveře Azure mají v současné době sedm metrik pro zobrazení čítačů výkonu.
- **Protokoly**. Protokoly aktivit a diagnostiky umožňují uložit nebo spotřebovat data z prostředku pro účely monitorování.

### <a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, které umožňují zobrazit čítače výkonu na portálu. K dispozici jsou následující metriky front dveří:

| Metrika | Zobrazovaný název metriky | Jednotka | Dimenze | Popis |
| --- | --- | --- | --- | --- |
| RequestCount | Počet požadavků | Počet | Stavu protokolu http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet požadavků klientů poskytovaných předními dveřmi.  |
| RequestSize | Velikost požadavku | Bajty | Stavu protokolu http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako požadavek od klientů do předních dveří. |
| ResponseSize | Velikost odpovědi | Bajty | Stavu protokolu http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako odpověď z front-dveří klientům. |
| TotalLatency | Celková latence | Milisekund | Stavu protokolu http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Čas vypočítaný z požadavku klienta obdrženého předními dvířky, dokud klient nepotvrdí poslední bajt odpovědi z předních dveří. |
| BackendRequestCount | Počet požadavků back-endu | Počet | Stavu protokolu http</br>HttpStatusGroup</br>Back-end | Počet požadavků odeslaných z předních dveří do back-endu. |
| BackendRequestLatency | Latence žádosti back-endu | Milisekund | Back-end | Čas vypočítaný z doby, kdy byla žádost odeslána přes dvířka do back-endu, dokud přední dveře nedostaly poslední bajt odezvy z back-endu. |
| BackendHealthPercentage | Procento stavu back-endu | Procento | Back-end</br>Problémových | Procento úspěšných sond stavu z předních dveří do back-endu. |
| WebApplicationFirewallRequestCount | Počet požadavků firewallu webových aplikací | Počet | PolicyName</br>RuleName</br>Akce | Počet požadavků klientů zpracovaných zabezpečením aplikační vrstvy předních dveří. |

## <a name="activity-logs"></a><a name="activity-log"></a>Protokoly aktivit

Protokoly aktivit obsahují informace o operacích provedených na Frontních dveřích. Určují taky to, kdo a kdy se při operacích zápisu (Put, post nebo Delete) převezme na přední dveře.

>[!NOTE]
>Protokoly aktivit nezahrnují operace čtení (Get). Nezahrnují také operace, které provádíte pomocí Azure Portal nebo původního rozhraní API pro správu.

Přístup k protokolům aktivit ve front-Vratích nebo ve všech protokolech prostředků Azure v Azure Monitor. Zobrazení protokolů aktivit:

1. Vyberte instanci front-dveří.
2. Vyberte **Protokol aktivit**.

    ![Protokol aktivit](./media/front-door-diagnostics/activity-log.png)

3. Zvolte rozsah filtrování a pak vyberte **použít**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostické protokoly
Diagnostické protokoly poskytují obsáhlé informace o operacích a chybách, které jsou důležité pro auditování a řešení potíží. Diagnostické protokoly se liší od protokolů aktivit.

Protokoly aktivit poskytují přehled o operacích provedených v prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které provedl váš prostředek. Další informace najdete v tématu [Azure monitor diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md).

![Diagnostické protokoly](./media/front-door-diagnostics/diagnostic-log.png)

Konfigurace diagnostických protokolů pro vaše přední dveře:

1. Vyberte si přední dvířka Azure.

2. Vyberte **nastavení diagnostiky**.

3. Vyberte **zapnout diagnostiku**. Archivujte diagnostické protokoly spolu se metrikami do účtu úložiště, streamujte je do centra událostí nebo je odešlete do protokolů Azure Monitor.

Přední dveře aktuálně poskytují protokoly diagnostiky (v dávce po hodinách). Diagnostické protokoly poskytují jednotlivé požadavky rozhraní API s každou položkou, která má následující schéma:

| Vlastnost  | Popis |
| ------------- | ------------- |
| BackendHostname | Pokud byl požadavek předán do back-endu, toto pole představuje název hostitele back-endu. Pokud je žádost přesměrována nebo předána do místní mezipaměti (Pokud je pro pravidlo směrování povoleno ukládání do mezipaměti), bude toto pole prázdné. |
| CacheStatus | V případě scénářů pro ukládání do mezipaměti toto pole definuje počet přístupů do mezipaměti nebo neúspěšný přístup na adresu POP. |
| ClientIp | IP adresa klienta, který odeslal požadavek. Pokud v žádosti existovala hlavička X předaná-pro, pak je IP adresa klienta převzata ze stejné. |
| ClientPort | Port IP klienta, který odeslal požadavek. |
| HttpMethod | Metoda HTTP, kterou požadavek používá |
| HttpStatusCode | Stavový kód HTTP vrácený z proxy serveru. |
| HttpStatusDetails | Výsledný stav žádosti. Význam této řetězcové hodnoty lze nalézt v tabulce odkazů na stav. |
| HttpVersion | Typ požadavku nebo připojení |
| POP | Krátký název okraje, ve kterém se vyložila žádost |
| RequestBytes | Velikost zprávy požadavku HTTP v bajtech, včetně hlaviček požadavků a textu žádosti. |
| RequestUri | Identifikátor URI přijatého požadavku |
| ResponseBytes | Bajty odeslané back-end serverem jako odpověď  |
| RoutingRuleName | Název pravidla směrování, na které se požadavek shodoval. |
| RulesEngineMatchNames | Názvy pravidel, která požadavek odpovídá. |
| Tato SecurityProtocol | Verze protokolu TLS/SSL používaná požadavkem nebo hodnotou null, pokud není šifrování. |
| SentToOriginShield | Logické pole představující, zda v prvním prostředí chyběla mezipaměť a žádost byla odeslána do místní mezipaměti. Ignorujte toto pole, pokud je pravidlo směrování přesměrování nebo když nemá povoleno ukládání do mezipaměti. |
| TimeTaken | Doba od prvního bajtu žádosti do posledního bajtu odpovědi v sekundách. |
| TrackingReference | Jedinečný referenční řetězec, který identifikuje požadavek, který je obsluhován předními dvířky, je také odeslán jako hlavička X-Azure-ref na klienta. Vyžaduje se pro hledání podrobností v protokolech přístupu pro konkrétní požadavek. |
| UserAgent | Typ prohlížeče, který klient použil. |

**Poznámka:** U různých konfigurací směrování a chování provozu můžou některá pole jako backendHostname, cacheStatus, sentToOriginShield a POP reagovat pomocí různých hodnot. Následující tabulka vysvětluje různé hodnoty, tato pole budou mít různé scénáře:

| Scénáře | Počet položek protokolu | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Pravidlo směrování bez povoleného ukládání do mezipaměti | 1 | Hraniční kód POP | Back-end, kde byl požadavek předán | Nepravda | CONFIG_NOCACHE |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Přístup do mezipaměti na hraničním okně Edge | 1 | Hraniční kód POP | Obsahovat | Nepravda | KLEPNUTÍ |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Neúspěšné přístupy do mezipaměti na hraničním zařízení POP, ale přístupy do mezipaměti v nadřazené mezipaměti | 2 | 1. hraniční kód POP</br>2. kód POP nadřazené mezipaměti | 1. název hostitele POP nadřazené mezipaměti</br>2. prázdné | 1. true</br>2. false | 1. NEÚSPĚŠNÉ</br>2. PARTIAL_HIT |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Neúspěšné přístupy do mezipaměti na úrovni POP hraničních i nadřazených cache | 2 | 1. hraniční kód POP</br>2. kód POP nadřazené mezipaměti | 1. název hostitele POP nadřazené mezipaměti</br>2. back-end, který pomáhá naplnit mezipaměť | 1. true</br>2. false | 1. NEÚSPĚŠNÉ</br>2. NEÚSPĚŠNÉ |


## <a name="next-steps"></a>Další kroky

- [Vytvoření profilu front-dveří](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)
