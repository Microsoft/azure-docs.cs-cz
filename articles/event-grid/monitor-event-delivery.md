---
title: Sledování Azure Event Grid doručování zpráv
description: Tento článek popisuje, jak použít Azure Portal k zobrazení stavu doručování Azure Event Grid zpráv.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722131"
---
# <a name="monitor-event-grid-message-delivery"></a>Sledování Event Grid doručování zpráv 

Tento článek popisuje, jak pomocí portálu zobrazit stav doručení událostí.

Event Grid poskytuje trvalé doručování. Každou zprávu pro každé předplatné zajišťuje aspoň jednou. Události se odesílají do registrovaného Webhooku každého předplatného hned. Pokud Webhook nepotvrdí přijetí události během 60 sekund od prvního pokusu o doručení, Event Grid pokusy o doručení události.

Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Metriky doručení

Portál zobrazuje metriky pro stav doručování zpráv událostí.

Pro témata jsou metriky:

* **Publikování bylo úspěšné**: událost se úspěšně odeslala do tématu a zpracovala se s odpovědí 2xx.
* **Publikování se nezdařilo**: událost byla odeslána do tématu, ale odmítnuta s kódem chyby.
* **Nespárovaná**: událost byla úspěšně publikována do tématu, ale není shodná s odběrem události. Událost byla vyřazena.

U předplatných jsou metriky:

* **Doručení bylo úspěšné**: událost byla úspěšně doručena koncovému bodu předplatného a obdržela odpověď 2xx.
* **Doručení se nezdařilo**: událost byla odeslána do koncového bodu předplatného, ale byla přijata odpověď 4xx nebo 5xx.
* **Události, jejichž platnost vypršela**: událost nebyla doručena a byly odeslány všechny pokusy o opakování. Událost byla vyřazena.
* **Spárované události**: událost v tématu se shodovala s odběrem události.

## <a name="event-subscription-status"></a>Stav odběru události

Chcete-li zobrazit metriky pro odběr události, můžete hledat buď podle typu předplatného, nebo podle předplatných pro konkrétní prostředek.

Pokud chcete hledat podle typu odběru události, vyberte **všechny služby**.

![Vyberte všechny služby](./media/monitor-event-delivery/all-services.png)

Vyhledejte **Event Grid** a vyberte **Event Grid odběry** z dostupných možností.

![Hledat odběry událostí](./media/monitor-event-delivery/search-and-select.png)

Filtrovat podle typu události, předplatného a umístění. Vyberte **metriky** pro zobrazení předplatného.

![Filtrovat odběry událostí](./media/monitor-event-delivery/filter-events.png)

Zobrazte metriky pro téma a předplatné události.

![Zobrazit metriky událostí](./media/monitor-event-delivery/subscription-metrics.png)

Chcete-li vyhledat metriky pro konkrétní prostředek, vyberte tento prostředek. Pak vyberte možnost **události**.

![Výběr událostí pro prostředek](./media/monitor-event-delivery/select-events.png)

Zobrazí se metriky pro odběry daného prostředku.

## <a name="custom-event-status"></a>Vlastní stav události

Pokud jste publikovali vlastní téma, můžete si Zobrazit metriky pro ni. Vyberte skupinu prostředků pro téma a vyberte téma.

![Výběr vlastního tématu](./media/monitor-event-delivery/select-custom-topic.png)

Zobrazit metriky pro vlastní událost tématu.

![Zobrazit metriky událostí](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Nastavení upozornění

Můžete nastavit výstrahy týkající se metrik a úrovně domény pro vlastní témata a domény událostí. V okně Přehled pro vyberte možnost **výstrahy** z nabídky prostředků vlevo, aby bylo možné zobrazit, spravovat a vytvářet pravidla výstrah. [Další informace o výstrahách Azure Monitor](../azure-monitor/platform/alerts-overview.md)

![Zobrazit metriky událostí](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Další kroky

* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku o [Event Gridu](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
