---
title: Sledování témat a předplatných událostí – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Sledování témat a odběrů událostí
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086672"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Sledování témat a odběrů událostí

Event Grid na okraji zveřejňuje řadu metrik pro témata a odběry událostí ve [formátu expozice Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Tento článek popisuje dostupné metriky a jak je povolit.

## <a name="enable-metrics"></a>Povolení metrik

Nakonfigurujte modul tak, `metrics__reporterType` aby vyzařoval metriky nastavením proměnné prostředí v `prometheus` možnostech vytvoření kontejneru:

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

Metriky budou k `5888/metrics` dispozici v modulu pro http a `4438/metrics` pro https. Například `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` pro http. V tomto okamžiku modul metriky můžete dotazování koncového bodu shromažďovat metriky jako v tomto [příkladu architektury](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Dostupné metriky

Témata i odběry událostí vyzařují metriky, které vám poskytnou přehled o doručování událostí a výkonu modulu.

### <a name="topic-metrics"></a>Metriky tématu

| Metrika | Popis |
| ------ | ----------- |
| Přijaté události | Počet událostí publikovaných v tématu
| Bezkonkurenční události | Počet událostí publikovaných na téma, které neodpovídají předplatnéudálosti a jsou vynechány
| Požadavky na úspěch | Počet příchozích žádostí o publikování přijatých tématem
| SystemErrorRequests | Počet příchozích požadavků na publikování se nezdařil z důvodu vnitřní systémové chyby
| Požadavky na uživatelské chyby | Počet příchozích požadavků na publikování se nezdařil z důvodu chyby uživatele, například poškozených požadavků JSON.
| SuccessRequestLatencyMs | Publikovat latenci odezvy požadavku v milisekundách


### <a name="event-subscription-metrics"></a>Metriky předplatného událostí

| Metrika | Popis |
| ------ | ----------- |
| DeliverySuccessCounts | Počet událostí úspěšně doručených do nakonfigurovaného koncového bodu
| Počet selhání doručení | Počet událostí, které se nepodařilo doručit do nakonfigurovaného koncového bodu
| DeliverySuccessLatencyMs | Latence událostí úspěšně doručena v milisekundách
| DeliveryFailureLatencyMs | Latence selhání doručení událostí v milisekundách
| SystemDelayForFirstAttemptMs | Systémové zpoždění událostí před prvním pokusem o doručení v milisekundách
| Počet pokusů o doručení | Počet pokusů o doručení události – úspěch a neúspěch
| ExpiredCounts | Počet událostí, jejichž platnost vypršela a nebyly doručeny do nakonfigurovaného koncového bodu