---
title: Monitorování doručování zpráv sítě Událostí Azure
description: Tento článek popisuje, jak pomocí portálu Azure zobrazit stav doručování zpráv Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722131"
---
# <a name="monitor-event-grid-message-delivery"></a>Sledování doručování zpráv v programu Event Grid 

Tento článek popisuje, jak pomocí portálu zobrazit stav dodávek událostí.

Event Grid poskytuje trvalé doručení. Doručuje každou zprávu alespoň jednou pro každé předplatné. Události jsou odesílány do registrovaného webhooku každého předplatného okamžitě. Pokud webhookne nepotvrdí přijetí události do 60 sekund od prvního pokusu o doručení, Event Grid opakuje doručení události.

Informace o doručení události a opakování zpráv [event grid u zpráv a opakování](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Metriky doručení

Portál zobrazuje metriky stavu doručování zpráv událostí.

U témat jsou metriky:

* **Publikování proběhlo úspěšně**: Událost byla úspěšně odeslána do tématu a zpracována s odpovědí 2xx.
* **Publikování se nezdařilo**: Událost odeslaná do tématu, ale odmítnutá s kódem chyby.
* **Bezkonkurenční**: Událost byla úspěšně publikována na téma, ale nebyla spárována s odběrem události. Událost byla zrušena.

U předplatných jsou metriky:

* **Doručení proběhlo úspěšně**: Událost byla úspěšně doručena do koncového bodu předplatného a obdržela odpověď 2xx.
* **Doručení se nezdařilo**: Událost odeslána do koncového bodu předplatného, ale obdržela odpověď 4xx nebo 5xx.
* **Události s prošlou platností**: Událost nebyla doručena a byly odeslány všechny pokusy o opakování. Událost byla zrušena.
* **Odpovídající události**: Událost v tématu byla spárována s odběrem události.

## <a name="event-subscription-status"></a>Stav předplatného události

Chcete-li zobrazit metriky pro odběr událostí, můžete vyhledávat podle typu předplatného nebo podle předplatných pro konkrétní prostředek.

Chcete-li hledat podle typu odběru událostí, vyberte **možnost Všechny služby**.

![Vybrat všechny služby](./media/monitor-event-delivery/all-services.png)

Vyhledejte **mřížku událostí** a z dostupných možností vyberte **Předplatná mřížky událostí.**

![Hledání odběrů událostí](./media/monitor-event-delivery/search-and-select.png)

Filtrujte podle typu události, předplatného a umístění. Vyberte **metriky** pro předplatné k zobrazení.

![Filtrování odběrů událostí](./media/monitor-event-delivery/filter-events.png)

Podívejte se na metriky tématu události a předplatného.

![Zobrazit metriky událostí](./media/monitor-event-delivery/subscription-metrics.png)

Chcete-li najít metriky pro konkrétní prostředek, vyberte tento prostředek. Potom vyberte **Události**.

![Výběr událostí pro zdroj](./media/monitor-event-delivery/select-events.png)

Zobrazí se metriky pro odběry pro tento prostředek.

## <a name="custom-event-status"></a>Stav vlastní události

Pokud jste publikovali vlastní téma, můžete zobrazit metriky pro něj. Vyberte skupinu prostředků pro téma a vyberte téma.

![Výběr vlastního tématu](./media/monitor-event-delivery/select-custom-topic.png)

Zobrazení metrik pro vlastní téma události.

![Zobrazit metriky událostí](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Nastavení upozornění

Můžete nastavit výstrahy na metriky tématu a úrovně domény pro vlastní témata a domény událostí. V okně přehledu pro vyberte **výstrahy** zleva měl zdroj menu pro zobrazení, správu a vytvoření pravidel výstrah. [Další informace o výstrahách azure monitoru](../azure-monitor/platform/alerts-overview.md)

![Zobrazit metriky událostí](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Další kroky

* Informace o doručení události a opakování zpráv [event grid u zpráv a opakování](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
