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
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997994"
---
# <a name="monitoring-metrics-for-front-door"></a>Monitorování metrik pro branou

Pomocí Azure branou Service můžete monitorovat klíčové metriky ověření branou konfigurace, spolu s využití, stavu a výkonu nebo přední dveře.

## <a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, kde můžete zobrazit čítače výkonu na portálu. Pro přední dveře jsou dostupné následující metriky:

| Metrika | Metriky zobrazovaný název | Jednotka | Dimenze | Popis |
| --- | --- | --- | --- | --- |
| RequestCount | Počet požadavků | Počet | Stav HTTP</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet požadavků klientů obsluhuje branou.  |
| RequestSize | Velikost požadavku | B | Stav HTTP</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných na požadavky klientů na branou. |
| ResponseSize | Velikost odpovědi | B | Stav HTTP</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako odpovědi z přední dveře do klientů. |
| TotalLatency | Celková latence | Milisekundy | Stav HTTP</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Čas se počítají na základě když žádost klienta byla přijata branou až do posledního bajtu odpovědi z přední dveře potvrzeny klienta. |
| BackendRequestCount | Počet požadavků back-endu | Počet | Stav HTTP</br>HttpStatusGroup</br>Back-end | Počet požadavků odeslaných z přední dveře k back-endů. |
| BackendRequestLatency | Odezva back-endu požadavku | Milisekundy | Back-end | Čas se počítají na základě když žádost byla odeslána branou back-endu dokud branou poslední bajt odpovědi přijaté z back-endu. |
| BackendHealthPercentage | Procento stav back-endu | Procento | Back-end</br>Bezproblémových | Procento úspěšných stavů testy z přední dveře k back-endů. |
| WebApplicationFirewallRequestCount | Počet požadavků brány Firewall webových aplikací | Počet | PolicyName</br>RuleName</br>Akce | Počet žádostí klienta zpracovaných rozhraním zabezpečení vrstvy aplikace branou. |


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořit přední dveře](quickstart-create-front-door.md).
- Přečtěte si [fungování branou](front-door-routing-architecture.md).
