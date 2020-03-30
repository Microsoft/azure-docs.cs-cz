---
title: Monitorování metrik a protokolů v Azure Front Door| Dokumenty společnosti Microsoft
description: Tento článek popisuje různé metriky a protokoly přístupu, které podporují Azure Front Door
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
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471723"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitorování metrik a protokolů v Azure Front Door

Pomocí Azure Front Door můžete sledovat prostředky následujícími způsoby:

- **Metriky**. Azure Front Door má aktuálně sedm metrik pro zobrazení čítačů výkonu.
- **Protokoly**. Protokoly aktivit a diagnostiky umožňují ukládání nebo spotřebovávaní výkonu, přístupu a dalších dat z prostředku pro účely monitorování.

### <a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, které umožňují zobrazit čítače výkonu na portálu. Níže jsou k dispozici front door metriky:

| Metrika | Zobrazovaný název metriky | Jednotka | Dimenze | Popis |
| --- | --- | --- | --- | --- |
| RequestCount | Počet požadavků | Počet | HttpStatus</br>Skupina HttpStatus</br>Klientská oblast</br>Klientská země | Počet požadavků klientů obsluhovaných frontdoor.  |
| RequestSize | Velikost požadavku | Bajty | HttpStatus</br>Skupina HttpStatus</br>Klientská oblast</br>Klientská země | Počet bajtů odeslaných jako požadavky od klientů do front door. |
| Velikost odpovědi | Velikost odpovědi | Bajty | HttpStatus</br>Skupina HttpStatus</br>Klientská oblast</br>Klientská země | Počet bajtů odeslaných jako odpovědi z front door klientům. |
| Celková latence | Celková latence | Milisekund | HttpStatus</br>Skupina HttpStatus</br>Klientská oblast</br>Klientská země | Čas vypočítaný z požadavku klienta přijatého společností Front Door, dokud klient nepotvrdil poslední bajt odpovědi od front door. |
| BackendRequestCount | Počet požadavků back-endu | Počet | HttpStatus</br>Skupina HttpStatus</br>Back-end | Počet požadavků odeslaných z front door do back-endů. |
| BackendRequestLatency | Latence back-endu | Milisekund | Back-end | Čas vypočítaný od okamžiku, kdy byl požadavek odeslán front door do back-endu, dokud přední dveře obdržely poslední bajt odpovědi z back-endu. |
| Procento backendhealth | Procento stavu back-endu | Procento | Back-end</br>BackendPool | Procento úspěšných zdravotních sond od předních dveří až po back-endy. |
| WebApplicationFirewallRequestCount | Počet požadavků brány firewall webové aplikace | Počet | PolicyName</br>Název_pravidla</br>Akce | Počet požadavků klientů zpracovaných zabezpečením aplikační vrstvy front door. |

## <a name="activity-logs"></a><a name="activity-log"></a>Protokoly aktivit

Protokoly aktivit poskytují informace o operacích provedených na frontdoor. Oni také určit, co, kdo, a kdy pro všechny operace zápisu (put, post, nebo odstranit) přijata na přední dveře.

>[!NOTE]
>Protokoly aktivit nezahrnují operace čtení (získání). Nezahrnují také operace, které provádíte pomocí portálu Azure nebo původního rozhraní API pro správu.

Přístup k protokolům aktivit ve vašich předních dveřích nebo ke všem protokolům prostředků Azure v Azure Monitoru. Zobrazení protokolů aktivit:

1. Vyberte instanci Předních dveří.
2. Vyberte **protokol aktivit**.

    ![Protokol aktivit](./media/front-door-diagnostics/activity-log.png)

3. Zvolte obor filtrování a pak vyberte **Použít**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostické protokoly
Diagnostické protokoly poskytují bohaté informace o operacích a chybách, které jsou důležité pro auditování a řešení potíží. Diagnostické protokoly se liší od protokolů aktivit.

Protokoly aktivit poskytují přehled o operacích provedených na prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které váš prostředek provedl. Další informace naleznete v [tématu Diagnostické protokoly Azure Monitor](../azure-monitor/platform/platform-logs-overview.md).

![Diagnostické protokoly](./media/front-door-diagnostics/diagnostic-log.png)

Konfigurace diagnostických protokolů pro přední dveře:

1. Vyberte přední dveře Azure.

2. Zvolte **Nastavení diagnostiky**.

3. Vyberte **možnost Zapnout diagnostiku**. Archivujte diagnostické protokoly spolu s metrikami do účtu úložiště, streamujte je do centra událostí nebo je odešlete do protokolů Azure Monitoru.

Přední dveře v současné době poskytuje diagnostické protokoly (dávkově každou hodinu). Diagnostické protokoly poskytují jednotlivé požadavky rozhraní API s každou položkou s následujícím schématem:

| Vlastnost  | Popis |
| ------------- | ------------- |
| BackendHostname | Pokud byl požadavek předáván do back-endu, toto pole představuje název hostitele back-endu. Toto pole bude prázdné, pokud byl požadavek přesměrován nebo předán do místní mezipaměti (pokud je pro pravidlo směrování povoleno ukládání do mezipaměti). |
| Stav mezipaměti | Pro scénáře ukládání do mezipaměti toto pole definuje přístup/nepřístupnou položku mezipaměti v |
| ClientIp | IP adresa klienta, který podal požadavek. Pokud byla v požadavku hlavička X-Forwarded-For, je ip adresa klienta vybrána ze stejné. |
| Klientský port | Port IP klienta, který podal požadavek. |
| Metoda http | Metoda HTTP použitá požadavkem. |
| HttpStatusCode | Stavový kód HTTP vrácený z proxy serveru. |
| HttpStatusDetails | Výsledný stav na požadavku. Význam této hodnoty řetězce lze nalézt v tabulce odkaz na stav. |
| HttpVersion | Typ požadavku nebo připojení. |
| POP | Krátký název okraje, kde byla žádost vyložena. |
| RequestBytes | Velikost zprávy požadavku HTTP v bajtů, včetně hlavičky požadavku a tělo požadavku. |
| Requesturi | Identifikátor URI přijatého požadavku. |
| Bajty odpovědí | Bajty odeslané serverem back-end jako odpověď.  |
| Název_směrovacího_státu | Název pravidla směrování, kterému požadavek odpovídá. |
| Protokol Zabezpečení | Verze protokolu TLS/SSL používaná požadavkem nebo hodnotou null, pokud není šifrování. |
| SentToOriginShield | Logické pole představující, pokud došlo k chybě mezipaměti v prvním prostředí a požadavek byl odeslán do místní mezipaměti. Toto pole ignorujte, pokud je pravidlo směrování přesměrováním nebo pokud není povoleno ukládání do mezipaměti. |
| TimeTaken | Doba, po kterou akce trvala v milisekundách. |
| TrackingReference | Jedinečný referenční řetězec, který identifikuje požadavek obsluhovaný front door, také odeslánjako x-Azure-ref záhlaví klientovi. Vyžadováno pro vyhledávání podrobností v protokolech přístupu pro konkrétní požadavek. |
| Useragent | Typ prohlížeče, který klient použil. |

**Poznámka:** Pro různé konfigurace směrování a chování provozu, některá pole, jako je backendHostname, cacheStatus, sentToOriginShield a POP pole může reagovat s různými hodnotami. Níže uvedená tabulka vysvětluje různé hodnoty, tato pole budou mít pro různé scénáře:

| Scénáře | Počet položek protokolu | POP | BackendHostname | SentToOriginShield | Stav mezipaměti |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Pravidlo směrování bez povoleného ukládání do mezipaměti | 1 | Kód PROTOKOLU POP edge | Back-end, kde byl požadavek předán | False | CONFIG_NOCACHE |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Přístup do mezipaměti na hraničním bodě POP | 1 | Kód PROTOKOLU POP edge | Prázdné | False | Hit |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Mezipaměť chybí na okraji POP, ale cache hit na mateřské mezipaměti POP | 2 | 1. Edge POP kód</br>2. Nadřazený cache POP kód | 1. Název hostitele protokolu POP nadřazené mezipaměti</br>2. Prázdné | 1. Pravda</br>2. Nepravdivé | 1. SLEČNA</br>2. PARTIAL_HIT |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Mezipaměť chybí na hraniční i nadřazené mezipaměti POP | 2 | 1. Edge POP kód</br>2. Nadřazený cache POP kód | 1. Název hostitele protokolu POP nadřazené mezipaměti</br>2. Backend, který pomáhá naplnit mezipaměť | 1. Pravda</br>2. Nepravdivé | 1. SLEČNA</br>2. SLEČNA |


## <a name="next-steps"></a>Další kroky

- [Vytvoření profilu předních dveří](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)
