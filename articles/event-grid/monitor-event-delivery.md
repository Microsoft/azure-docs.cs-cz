---
title: Monitorování doručování zpráv služby Azure Event Grid
description: Popisuje, jak sledovat doručování zpráv služby Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: fdd18b833794c25cb90188ba8bc418d4785492ba
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464799"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorování doručování zpráv služby Event Grid 

Tento článek popisuje, jak pomocí portálu pro stav doručení událostí.

Event gridu poskytuje odolné doručování. Poskytuje každou zprávu alespoň jednou pro každé předplatné. Události se posílají okamžitě registrované webhooku každé předplatné. Pokud webhooku není obdržel událost během 60 sekund první pokusy o doručení, služby Event Grid zopakuje pokus o doručení události.

Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Doručování metrik

Na portálu se zobrazí metriky pro stav doručování zpráv událostí.

Metriky pro témata, jsou:

* **Publikování bylo úspěšné**: Událost úspěšně odeslaný do tématu a zpracovat odpovědí 2xx.
* **Publikování selhalo**: Události odeslané do tématu, ale odmítl s kódem chyby.
* **Bezkonkurenční**: Událost byla úspěšně publikována do tématu, ale není spárováno k odběru událostí. Událost byla zrušena.

Pro předplatná jsou metriky:

* **Doručení bylo úspěšné.**: Událost úspěšně doručit do koncového bodu předplatného a 2xx odpověď.
* **Doručení nebylo úspěšné.**: Události odeslané do koncového bodu předplatného, ale odpověď 4xx a 5xx.
* **Vypršela platnost události**: Nebylo doručeno události a všechny opakované pokusy byly odeslány. Událost byla zrušena.
* **Odpovídající události**: Odběr události se odpovídající událost v tomto tématu.

## <a name="event-subscription-status"></a>Stav odběru událostí

Pokud chcete zobrazit metriky pro odběr událostí, můžete buď hledat podle typu předplatného nebo předplatného pro určitý prostředek.

Chcete-li hledat podle typu události předplatné, vyberte **všechny služby**.

![Vyberte všechny služby](./media/monitor-event-delivery/all-services.png)

Vyhledejte **služby event grid** a vyberte **předplatná Event gridu** z dostupných možností.

![Hledat odběry událostí](./media/monitor-event-delivery/search-and-select.png)

Filtrovat podle typu události, předplatné a umístění. Vyberte **metriky** pro předplatné, které chcete zobrazit.

![Filtrovat odběry událostí](./media/monitor-event-delivery/filter-events.png)

Zobrazte metriky pro události téma a odběr.

![Zobrazit události metriky](./media/monitor-event-delivery/subscription-metrics.png)

Najít metriky pro určitý prostředek, vyberte daný prostředek. Vyberte **události**.

![Vyberte události pro určitý prostředek](./media/monitor-event-delivery/select-events.png)

Můžete zobrazit metriky pro předplatná pro daný prostředek.

## <a name="custom-event-status"></a>Stav vlastní události

Pokud jste publikovali vlastní téma, můžete zobrazit metriky pro něj. Vyberte skupinu prostředků pro téma a vyberte téma.

![Výběr vlastního tématu](./media/monitor-event-delivery/select-custom-topic.png)

Zobrazte metriky pro vlastní události tématu.

![Zobrazit události metriky](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Další postup

* Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
