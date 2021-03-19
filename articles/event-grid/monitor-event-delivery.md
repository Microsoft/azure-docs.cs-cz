---
title: Zobrazení Azure Event Grid metrik a nastavení výstrah
description: Tento článek popisuje, jak pomocí Azure Portal zobrazit metriky pro Azure Event Grid témata a předplatná a jak na nich vytvářet výstrahy.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f6c119c16452246ec6eeb57ab392b29608938a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598554"
---
# <a name="monitor-event-grid-message-delivery"></a>Sledování Event Grid doručování zpráv 
Tento článek popisuje, jak pomocí portálu zobrazit metriky pro Event Grid témata a předplatná a jak na nich vytvářet výstrahy. 

> [!IMPORTANT]
> Seznam podporovaných metrik Azure Event Grid najdete v tématu [metriky](metrics.md).

## <a name="view-custom-topic-metrics"></a>Zobrazit vlastní metriky témat

Pokud jste publikovali vlastní téma, můžete si Zobrazit metriky pro ni. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. Do panelu hledání v tématu zadejte **Event Grid témata** a v rozevíracím seznamu vyberte **Event Grid témata** . 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Vyhledat a vybrat Event Grid témata":::
3. V seznamu témat vyberte vlastní téma. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Výběr vlastního tématu":::
4. Zobrazit metriky pro vlastní téma události na stránce **Event Grid tématu** . Na následujícím obrázku je minimalizována část **základy** , která zobrazuje skupinu prostředků, předplatné atd. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Zobrazit metriky událostí":::

    Grafy s podporovanými metrikami můžete vytvářet pomocí karty **metriky** na stránce **Event Grid tématu** .

    :::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Téma – stránka metriky":::

    Podívejte se například na graf metriky pro metriku **publikovaných událostí** .

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Metrika publikovaných událostí":::


## <a name="view-subscription-metrics"></a>Zobrazit metriky předplatného
1. Pomocí postupu v předchozí části přejděte na stránku **Event Grid tématu** . 
2. V dolním podokně vyberte předplatné, jak je znázorněno v následujícím příkladu. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Vybrat odběr události":::    

    Můžete také vyhledat **Event Grid odběry** na panelu hledání v Azure Portal, vybrat **typ tématu**, **předplatné** a **umístění** a zobrazit odběr událostí. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Vybrat odběr události ze stránky Event Grid odběry":::        

    Pro vlastní témata vyberte **Event Grid témata** jako **typ tématu**. V části systémová témata vyberte typ prostředku Azure, například **účty úložiště (BLOB, GPv2)**. 
3. Prohlédněte si metriky pro předplatné na domovské stránce pro odběr v grafu. Můžete zobrazit metriky **Obecné**, **Chyba** a **latence** pro poslední 1 hodinu, 6 hodin, 12 hodin, 1 den, 7 dní nebo 30 dní. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metriky na domovské stránce předplatného":::    

## <a name="view-system-topic-metrics"></a>Zobrazit metriky tématu systému

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. Do panelu hledání v tématu zadejte **Event Grid systémová témata** a v rozevíracím seznamu vyberte **Event Grid systémová témata** . 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Vyhledat a vybrat Event Grid systémová témata":::
3. V seznamu témat vyberte své systémové téma. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Vyberte své systémové téma.":::
4. Zobrazit metriky pro systémové téma na stránce **Event Gridho systému** . Na následujícím obrázku je minimalizována část **základy** , která zobrazuje skupinu prostředků, předplatné atd. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Zobrazit metriky tématu systému na stránce Přehled":::

    Grafy s podporovanými metrikami můžete vytvářet pomocí karty **metriky** na stránce **Event Grid tématu** .

    :::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Systémové téma – stránka metriky":::

    > [!IMPORTANT]
    > Seznam podporovaných metrik Azure Event Grid najdete v tématu [metriky](metrics.md).

## <a name="next-steps"></a>Další kroky
Viz následující články:

- Informace o tom, jak vytvářet upozornění na metriky a operace protokolů aktivit, najdete v tématu [Nastavení výstrah](set-alerts.md).
- Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
