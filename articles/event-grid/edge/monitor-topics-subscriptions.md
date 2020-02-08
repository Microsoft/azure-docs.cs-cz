---
title: Sledovat témata a odběry událostí – Azure Event Grid IoT Edge | Microsoft Docs
description: Sledovat témata a odběry událostí
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086672"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Sledovat témata a odběry událostí

Event Grid na Edge zpřístupňuje řadu metrik pro témata a odběry událostí ve [formátu Prometheus Exposition](https://prometheus.io/docs/instrumenting/exposition_formats/). Tento článek popisuje dostupné metriky a jejich povolení.

## <a name="enable-metrics"></a>Povolit metriky

Nakonfigurujte modul pro generování metrik nastavením proměnné prostředí `metrics__reporterType` tak, aby `prometheus` v možnostech vytvoření kontejneru:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

Metriky budou k dispozici v `5888/metrics` modulu pro protokol HTTP a `4438/metrics` pro protokol HTTPS. Například `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` pro protokol HTTP. V tomto okamžiku může modul metrik spustit dotaz na koncový bod a shromažďovat metriky jako v této [ukázkové architektuře](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Dostupné metriky

Témata a odběry událostí generují metriky, které vám poskytnou přehled o výkonu události a modulu.

### <a name="topic-metrics"></a>Metriky témat

| Metrika | Popis |
| ------ | ----------- |
| EventsReceived | Počet událostí publikovaných do tématu
| UnmatchedEvents | Počet událostí publikovaných do tématu, které nesouhlasí s odběrem události a jsou vyřazeny
| SuccessRequests | Počet příchozích žádostí o publikování přijatých v tématu
| SystemErrorRequests | Počet příchozích žádostí o publikování se nezdařil, protože došlo k vnitřní chybě systému.
| UserErrorRequests | Počet příchozích žádostí o publikování se nezdařil z důvodu chyby uživatele, jako je například nesprávně vytvořený kód JSON.
| SuccessRequestLatencyMs | Publikování latence odpovědi na žádost v milisekundách


### <a name="event-subscription-metrics"></a>Metriky odběru událostí

| Metrika | Popis |
| ------ | ----------- |
| deliverySuccessCounts | Počet událostí úspěšně doručených do nakonfigurovaného koncového bodu
| deliveryFailureCounts | Počet událostí, které se nepodařilo doručit do nakonfigurovaného koncového bodu
| deliverySuccessLatencyMs | Latence událostí úspěšně doručených v milisekundách
| deliveryFailureLatencyMs | Latence selhání při doručování událostí v milisekundách
| systemDelayForFirstAttemptMs | Zpoždění systému událostí před prvním pokusem o doručení v milisekundách
| deliveryAttemptsCount | Počet pokusů o doručení událostí – úspěch a selhání
| expiredCounts | Počet událostí, jejichž platnost vypršela a nebyly doručeny do nakonfigurovaného koncového bodu