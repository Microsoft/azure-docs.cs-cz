---
title: Nastavení upozornění pro Azure Event Grid metriky a operace protokolu aktivit
description: Tento článek popisuje, jak vytvořit výstrahy pro Azure Event Grid metriky a operace protokolu aktivit.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 615ebef749be49822a09470eb0d47d8eb31b9a5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119136"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Nastavení upozornění na Azure Event Grid metriky a protokoly aktivit
Tento článek popisuje, jak vytvořit výstrahy pro Azure Event Grid metriky a operace protokolu aktivit. Můžete vytvářet upozornění na metriky publikování i doručování pro Azure Event Grid prostředky (témata a domény). Pro systémová témata [vytvořte výstrahy pomocí stránky **metriky** ](#create-alerts-using-the-metrics-page).

## <a name="create-alerts-on-dead-lettered-events"></a>Vytváření výstrah pro události s nedoručenými písmeny
Následující postup ukazuje, jak vytvořit výstrahu pro metriku **událostí s nedoručenými zprávami** pro vlastní téma. V tomto příkladu se odešle e-mail vlastníkovi skupiny prostředků Azure, když počet nedoručených událostí pro téma překročí 10. 

1. Na stránce **Event Grid téma** pro vaše téma v nabídce vlevo vyberte **výstrahy** a pak vyberte **+ nové pravidlo výstrahy**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::
2. Na stránce **vytvořit pravidlo výstrahy** ověřte, že je pro prostředek vybraný váš článek. Pak klikněte na **vybrat podmínku**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::    
3. Na stránce **Konfigurovat logiku signálu** proveďte tyto kroky:
    1. Vyberte metriku nebo položku protokolu aktivit. V tomto příkladu je vybrána možnost **nedoručené události** . 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::        
    2. Vyberte dimenze (volitelné). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::        

        > [!NOTE]
        > Můžete vybrat **+** tlačítko pro **EventSubscriptionName** a zadat název odběru události pro filtrování událostí. 
    3. Posuňte se dolů. V části **logika výstrahy** vyberte **operátor**, **typ agregace**a zadejte **mezní hodnotu**a potom vyberte **Hotovo**. V tomto příkladu se aktivuje výstraha v případě, že celkový počet nedoručených událostí je větší než 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::                
4. Zpátky na stránce **vytvořit pravidlo výstrahy** klikněte na **Vybrat skupinu akcí**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::
5. Na panelu nástrojů vyberte **vytvořit skupinu akcí** a vytvořte novou skupinu akcí. Můžete také vybrat existující skupinu akcí.        
6. Na stránce **Přidat skupinu akcí** proveďte tyto kroky:
    1. Zadejte **název skupiny akcí**.
    1. Zadejte **krátký název** skupiny akcí.
    1. Vyberte **předplatné Azure** , ve kterém chcete vytvořit skupinu akcí.
    1. Vyberte **skupinu prostředků Azure** , ve které chcete vytvořit skupinu akcí.
    1. Zadejte **název akce**. 
    1. Vyberte **typ akce**. V tomto příkladu je vybraná **role e-mailu Azure Resource Manager** , konkrétně role **vlastníci** . 
    1. Kliknutím na **tlačítko OK** stránku zavřete. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::                   
7. Zpět na stránce **vytvořit pravidlo výstrahy** zadejte název pravidla výstrahy a pak vyberte **vytvořit pravidlo upozornění**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::  
8. Nyní se na stránce **výstrahy** v tématu zobrazí odkaz na správu pravidel výstrah, pokud zatím nejsou k dispozici žádná upozornění. Pokud jsou výstrahy, vyberte na panelu nástrojů možnost **pravidla výstrah správce** .  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Vytváření upozornění na jiné metriky nebo operace protokolu aktivit
Předchozí část ukázala, jak vytvořit upozornění na události s nedoručenými zprávami. Postup vytvoření upozornění na jiné metriky nebo operace protokolu aktivit jsou podobné. 

Pokud například chcete vytvořit výstrahu pro událost selhání doručení, vyberte na stránce **Konfigurovat logiku signálu** **neúspěšné doručování událostí** . 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::


## <a name="create-alerts-using-the-metrics-page"></a>Vytváření výstrah pomocí stránky metriky
Výstrahy můžete také vytvořit pomocí stránky **metriky** . Postup je podobný. Pro systémová témata můžete k vytváření výstrah použít jenom stránku **metriky** , protože stránka **výstrahy** není k dispozici. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::   
    

> [!NOTE]
> Tento článek se nezabývá všemi různými kroky a kombinacemi, které můžete použít k vytvoření výstrahy. Přehled výstrah najdete v tématu [výstrahy přehled](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Další kroky

* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
