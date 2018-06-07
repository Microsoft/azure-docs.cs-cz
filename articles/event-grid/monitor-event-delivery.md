---
title: Monitorování doručení zpráv mřížky událostí Azure
description: Popisuje, jak monitorovat doručení zpráv mřížky událostí Azure.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: tomfitz
ms.openlocfilehash: 625f3e228bb28c85e68fb592914fb2191baf3e4e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626985"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorování událostí mřížky doručení zpráv 

Tento článek popisuje, jak pomocí portálu a zjistit stav události doručení.

Událost mřížky poskytuje trvanlivý doručení. Zajišťuje každou zprávu alespoň jednou pro každé předplatné. Události se posílají okamžitě registrované webhooku každého předplatného. Webhook, jehož není potvrdil přijetí události do 60 sekund od první pokus o doručení, události mřížky opakuje doručení události.

Informace o události doručení a opakovaných pokusů [doručení zpráv událostí mřížky a zkuste to znovu](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Doručení metriky

Na portálu se zobrazí metriky pro stav doručení zprávy o událostech.

Témata jsou metriky:

* **Publikování bylo úspěšné**: událost úspěšně odeslané do tématu a zpracování s 2xx odpovědi.
* **Publikování se nezdařilo**: událost odeslané do tématu, ale odmítl s kódem chyby.
* **Neodpovídající**: událost úspěšně publikované do tématu, ale nebyla neodpovídala odběr událostí. Událost byla zrušena.

Pro předplatné jsou metriky:

* **Úspěšné doručení**: událost úspěšně doručit do odběru koncového bodu a obdržel odpověď 2xx.
* **Nezdařilo se doručení**: událostí odeslaných do koncového bodu předplatného, ale obdržel 4xx nebo 5xx odpověď.
* **Platnost události**: nebyla událostí doručit a byly odeslány všechny pokusy o opakování. Událost byla zrušena.
* **Shodná události**: událostí v tomto tématu, odpovídal událost odběru.

## <a name="event-subscription-status"></a>Stav odběru událostí

Metriky pro odběr událostí najdete můžete buď hledat typ předplatného, nebo předplatné pro konkrétní zdroje.

Chcete-li vyhledávat podle typu události předplatné, vyberte **všechny služby**.

![Vyberte všechny služby](./media/monitor-event-delivery/all-services.png)

Vyhledejte **událostí mřížky** a vyberte **odběry mřížky událostí** z dostupných možností.

![Vyhledejte odběry událostí](./media/monitor-event-delivery/search-and-select.png)

Filtrovat podle typu události, předplatné a umístění. Vyberte **metriky** pro předplatné, které chcete zobrazit.

![Filtr událostí odběrů](./media/monitor-event-delivery/filter-events.png)

Zobrazte metriky pro událost téma a odběr.

![Zobrazit události metriky](./media/monitor-event-delivery/subscription-metrics.png)

Chcete-li najít metriky pro konkrétní prostředek, vyberte tento prostředek. Pak vyberte **události**.

![Vyberte události pro prostředek](./media/monitor-event-delivery/select-events.png)

Zobrazí metriky pro předplatná pro tento prostředek.

## <a name="custom-event-status"></a>Stav vlastní události

Pokud jste publikovali vlastní téma, můžete zobrazit metriky pro ni. Vyberte skupinu prostředků pro téma a vyberte tématu.

![Vyberte vlastní tématu](./media/monitor-event-delivery/select-custom-topic.png)

Zobrazte metriky pro vlastní události tématu.

![Zobrazit události metriky](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Další postup

* Informace o události doručení a opakovaných pokusů [doručení zpráv událostí mřížky a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).
