---
title: Zobrazení Azure Event Grid metrik a nastavení výstrah
description: Tento článek popisuje, jak pomocí Azure Portal zobrazit metriky pro Azure Event Grid témata a předplatná a jak na nich vytvářet výstrahy.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: spelluru
ms.openlocfilehash: e74d2d8982cac961aa65d6576c80a92cb53ce387
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100492"
---
# <a name="monitor-event-grid-message-delivery"></a>Sledování Event Grid doručování zpráv 
Tento článek popisuje, jak pomocí portálu zobrazit metriky pro Event Grid témata a předplatná a jak na nich vytvářet výstrahy. 

## <a name="metrics"></a>Metriky

Portál zobrazuje metriky pro stav doručování zpráv událostí.

Témata najdete tady:

* **Publikování bylo úspěšné**: událost se úspěšně odeslala do tématu a zpracovala se s odpovědí 2xx.
* **Publikování se nezdařilo**: událost byla odeslána do tématu, ale odmítnuta s kódem chyby.
* **Nespárovaná**: událost byla úspěšně publikována do tématu, ale není shodná s odběrem události. Událost byla vyřazena.

V případě předplatných jsou zde uvedeny některé metriky:

* **Doručení bylo úspěšné**: událost byla úspěšně doručena koncovému bodu předplatného a obdržela odpověď 2xx.
* **Doručení se nezdařilo**: pokaždé, když se služba pokusí o doručení, a obslužná rutina události nevrátí úspěšný 2xx kód, zvýší se hodnota čítače **doručení se nezdařilo** . Pokud se pokusíte o doručení stejné události víckrát a selhání, čítač **neúspěšných doručení** se zvýší pro každou chybu.
* **Události, jejichž platnost vypršela**: událost nebyla doručena a byly odeslány všechny pokusy o opakování. Událost byla vyřazena.
* **Spárované události**: událost v tématu se shodovala s odběrem události.

    > [!NOTE]
    > Úplný seznam metrik najdete v tématu [metriky podporované Azure Event Grid](metrics.md).

## <a name="view-custom-topic-metrics"></a>Zobrazit vlastní metriky témat

Pokud jste publikovali vlastní téma, můžete si Zobrazit metriky pro ni. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Do panelu hledání v tématu zadejte **Event Grid témata**a v rozevíracím seznamu vyberte **Event Grid témata** . 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Vyhledat a vybrat Event Grid témata":::
3. V seznamu témat vyberte vlastní téma. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Výběr vlastního tématu":::
4. Zobrazit metriky pro vlastní téma události na stránce **Event Grid tématu** . Na následujícím obrázku je minimalizována část **základy** , která zobrazuje skupinu prostředků, předplatné atd. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Zobrazit metriky událostí":::

Grafy s podporovanými metrikami můžete vytvářet pomocí karty **metriky** na stránce **Event Grid tématu** .

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Téma – stránka metriky":::

Další informace o metrikách najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md)

Podívejte se například na graf metriky pro metriku **publikovaných událostí** .

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Metrika publikovaných událostí":::


## <a name="view-subscription-metrics"></a>Zobrazit metriky předplatného
1. Pomocí postupu v předchozí části přejděte na stránku **Event Grid tématu** . 
2. V dolním podokně vyberte předplatné, jak je znázorněno v následujícím příkladu. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Vybrat odběr události":::    

    Můžete také vyhledat **Event Grid odběry** na panelu hledání v Azure Portal, vybrat **typ tématu**, **předplatné**a **umístění** a zobrazit odběr událostí. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Vybrat odběr události ze stránky Event Grid odběry":::        

    Pro vlastní témata vyberte **Event Grid témata** jako **typ tématu**. V části systémová témata vyberte typ prostředku Azure, například **účty úložiště (BLOB, GPv2)**. 
3. Prohlédněte si metriky pro předplatné na domovské stránce pro odběr v grafu. Můžete zobrazit **Obecné**, **chyby**, **latenci**a metriky **nedoručených zpráv** pro poslední 1 hodinu, 6 hodin, 12 hodin, 1 den, 7 dní nebo 30 dní. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metriky na domovské stránce předplatného":::    

## <a name="view-system-topic-metrics"></a>Zobrazit metriky tématu systému

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Do panelu hledání v tématu zadejte **Event Grid systémová témata**a v rozevíracím seznamu vyberte **Event Grid systémová témata** . 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Vyhledat a vybrat Event Grid systémová témata":::
3. V seznamu témat vyberte své systémové téma. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Vyberte své systémové téma.":::
4. Zobrazit metriky pro systémové téma na stránce **Event Gridho systému** . Na následujícím obrázku je minimalizována část **základy** , která zobrazuje skupinu prostředků, předplatné atd. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Zobrazit metriky tématu systému na stránce Přehled":::

Grafy s podporovanými metrikami můžete vytvářet pomocí karty **metriky** na stránce **Event Grid tématu** .

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Systémové téma – stránka metriky":::

Další informace o metrikách najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md)


## <a name="set-alerts"></a>Nastavení upozornění
Můžete nastavit výstrahy na metrikách pro témata a domény v Azure Portal. 

Následující postup ukazuje, jak vytvořit výstrahu pro metriku **událostí s nedoručenými zprávami** pro vlastní téma. V tomto příkladu se odešle e-mail vlastníkovi skupiny prostředků Azure, když počet nedoručených událostí pro téma překročí 10. 

1. Na stránce **Event Grid téma** pro vaše téma v nabídce vlevo vyberte **výstrahy** a pak vyberte **+ nové pravidlo výstrahy**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Stránka výstrahy – tlačítko pro pravidlo nového upozornění":::
    
    
    Výstrahy můžete také vytvořit pomocí stránky **metriky** . Postup je podobný. 

    :::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Stránka metrik – tlačítko vytvořit výstrahu":::   
    
2. Na stránce **vytvořit pravidlo výstrahy** ověřte, že je pro prostředek vybraný váš článek. Pak klikněte na **vybrat podmínku**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Stránka výstrahy – výběr podmínky":::    
3. Na stránce **Konfigurovat logiku signálu** proveďte tyto kroky:
    1. Vyberte metriku nebo položku protokolu aktivit. V tomto příkladu je vybrána možnost **nedoručené události** . 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Vybrat události s nedoručenými písmeny":::        
    2. Vyberte dimenze (volitelné). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Konfigurace logiky signálů":::        
    3. Posuňte se dolů. V části **logika výstrahy** vyberte **operátor**, **typ agregace**a zadejte **mezní hodnotu**a potom vyberte **Hotovo**. V tomto příkladu se aktivuje výstraha v případě, že celkový počet nedoručených událostí je větší než 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Logika výstrahy":::                
4. Zpátky na stránce **vytvořit pravidlo výstrahy** klikněte na **Vybrat skupinu akcí**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Tlačítko pro výběr skupiny akcí":::
5. Na panelu nástrojů vyberte **vytvořit skupinu akcí** a vytvořte novou skupinu akcí. Můžete také vybrat existující skupinu akcí.        
6. Na stránce **Přidat skupinu akcí** proveďte tyto kroky:
    1. Zadejte **název skupiny akcí**.
    1. Zadejte **krátký název** skupiny akcí.
    1. Vyberte **předplatné Azure** , ve kterém chcete vytvořit skupinu akcí.
    1. Vyberte **skupinu prostředků Azure** , ve které chcete vytvořit skupinu akcí.
    1. Zadejte **název akce**. 
    1. Vyberte **typ akce**. V tomto příkladu je vybraná **role e-mailu Azure Resource Manager** , konkrétně role **vlastníci** . 
    1. Kliknutím na **tlačítko OK** stránku zavřete. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Přidat stránku skupiny akcí":::                   
7. Zpět na stránce **vytvořit pravidlo výstrahy** zadejte název pravidla výstrahy a pak vyberte **vytvořit pravidlo upozornění**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Název pravidla výstrahy":::  
8. Nyní se na stránce **výstrahy** v tématu zobrazí odkaz na správu pravidel výstrah, pokud zatím nejsou k dispozici žádná upozornění. Pokud jsou výstrahy, vyberte na panelu nástrojů možnost **pravidla výstrah správce** .  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Správa výstrah":::

    > [!NOTE]
    > Tento článek se nezabývá všemi různými kroky a kombinacemi, které můžete použít k vytvoření výstrahy. Přehled výstrah najdete v tématu [výstrahy přehled](../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Další kroky

* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
