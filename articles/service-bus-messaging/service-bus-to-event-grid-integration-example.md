---
title: Zpracování událostí Service Bus přes Event Grid pomocí Azure Logic Apps
description: Tento článek popisuje kroky pro zpracování Service Busch událostí přes Event Grid pomocí Azure Logic Apps.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998979"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Kurz: reakce na události Azure Service Bus přijatých prostřednictvím Azure Event Grid pomocí Azure Logic Apps
V tomto kurzu se naučíte reagovat na události Azure Service Bus přijaté prostřednictvím Azure Event Grid pomocí Azure Logic Apps. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Příjem zpráv pomocí Logic Apps
V tomto kroku vytvoříte aplikaci logiky Azure, která přijímá Service Bus události prostřednictvím Azure Event Grid. 

1. Vytvoření aplikace logiky v Azure Portal.
    1. Vyberte **+ vytvořit prostředek**, vyberte **integrace** a pak vyberte **Aplikace logiky**. 
    2. Na stránce **Logic App-Create (vytvořit aplikaci logiky** ) zadejte **název** aplikace logiky.
    3. Vyberte své **předplatné** Azure. 
    4. Vyberte pro **skupinu prostředků** možnost **použít existující** a vyberte skupinu prostředků, kterou jste použili pro jiné prostředky (třeba funkci Azure Functions Service Bus obor názvů), kterou jste vytvořili dříve. 
    5. Vyberte **umístění** aplikace logiky. 
    6. Vyberte **Zkontrolovat a vytvořit**. 
    1. Na stránce **Revize + vytvořit** vyberte **vytvořit** a vytvořte aplikaci logiky. 
1. Na stránce **návrháře Logic Apps** v části **šablony** vyberte **prázdná aplikace logiky** . 
1. V Návrháři proveďte následující kroky:
    1. Vyhledejte **Event Grid**. 
    2. Vyberte, **kdy dojde k události prostředku – Azure Event Grid**. 

        ![Návrhář Logic Apps – výběr Event Grid triggeru](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Vyberte **Přihlásit**, zadejte svoje přihlašovací údaje Azure a vyberte možnost **Povolení přístupu**. 
5. Na stránce **když dojde k události prostředku** , proveďte následující kroky:
    1. Vyberte své předplatné Azure. 
    2. Jako **typ prostředku** vyberte **Microsoft. ServiceBus. Namespaces**. 
    3. Jako **název prostředku** vyberte obor názvů Service Bus. 
    4. Vyberte **Přidat nový parametr** a vyberte **filtr přípon**. 
    5. Jako **filtr přípon** zadejte název druhého předplatného Service Bus tématu. 
        ![Návrhář Logic Apps – konfigurace události](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. V návrháři vyberte **+ Nový krok** a proveďte následující kroky:
    1. Vyhledejte **Service Bus**.
    2. V seznamu vyberte **Service Bus** . 
    3. V seznamu **akcí** vyberte možnost **získat zprávy** . 
    4. Vyberte možnost **získat zprávy z odběru tématu (náhled – zámek)**. 

        ![Návrhář Logic Apps – akce získat zprávy](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Zadejte **název připojení**. Například: **získat zprávy z odběru tématu** a vybrat Service Bus obor názvů. 

        ![Logic Apps Designer – výběr oboru názvů Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Vyberte **RootManageSharedAccessKey** a pak vyberte **vytvořit**.

        ![Návrhář Logic Apps – výběr sdíleného přístupového klíče](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Vyberte své **téma** a **předplatné**. 
    
        ![Snímek obrazovky, který ukazuje, kde si vyberete své téma a předplatné.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Vyberte **+ Nový krok** a proveďte následující kroky: 
    1. Vyberte **Service Bus**.
    2. V seznamu akcí vyberte **Dokončit zprávu v odběru tématu** . 
    3. Vyberte Service Bus **téma**.
    4. Vyberte druhé **předplatné** tématu.
    5. Pro **token zámku zprávy** vyberte možnost **Uzamknout token** z **dynamického obsahu**. 

        ![Návrhář Logic Apps – dokončení zprávy](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Pokud chcete aplikaci logiky uložit, vyberte **Uložit** na panelu nástrojů v Návrháři Logic Apps. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Uložení aplikace logiky":::
1. Pokud jste ještě neodeslali zkušební zprávy do tématu, postupujte podle pokynů v části [posílání zpráv do tématu Service Bus](#send-messages-to-the-service-bus-topic) a odeslání zpráv do tématu. 
1. Přepněte na stránku **Přehled** vaší aplikace logiky. Zobrazí se aplikace logiky spuštěné v **historii spuštění** odesílaných zpráv. Než uvidíte, že se aplikace logiky spustí, může to trvat několik minut. Kliknutím na tlačítko **aktualizovat** na panelu nástrojů stránku aktualizujte. 

    ![Návrhář Logic Apps – spuštění aplikace logiky](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Pokud si chcete zobrazit podrobnosti, vyberte běh aplikace logiky. Všimněte si, že zpracoval 5 zpráv ve smyčce for. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Podrobnosti o běhu aplikace logiky":::    

## <a name="troubleshoot"></a>Řešení potíží
Pokud nevidíte žádné vyvolání po čekání a aktualizace na nějakou dobu, postupujte podle těchto kroků: 

1. Potvrďte, že se zprávy dostaly do Service Busho tématu. Na stránce **Service Bus tématu** se podívejte na čítač **příchozí zprávy** . V tomto případě jsem aplikaci **MessageSender** spouštěla dvakrát, takže se zobrazí 10 zpráv (5 zpráv pro každé spuštění).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Stránka tématu Service Bus – příchozí zprávy":::    
1. Ověřte, že v předplatném Service Bus nejsou **žádné aktivní zprávy** . 
    Pokud na této stránce nevidíte žádné události, ověřte, že se na stránce **Service Bus předplatné** nezobrazuje žádný **aktivní počet zpráv**. Pokud je číslo tohoto čítače větší než nula, zprávy v předplatném nejsou z nějakého důvodu předávány do funkce obslužné rutiny (obslužná rutina odběru události). Ověřte, zda jste odběr událostí nastavili správně. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Počet aktivních zpráv v předplatném Service Bus":::    
1. **Doručené události** se zobrazí také na stránce **události** oboru názvů Service Bus. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Události doručené stránky událostí" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Můžete také vidět, že se události doručují na stránce **odběr události** . Na tuto stránku se dostanete tak, že na stránce **události** vyberete odběr události. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Události doručené stránky odběru události":::
## <a name="next-steps"></a>Další kroky

* Další informace o službě [Azure Event Grid](../event-grid/index.yml).
* Další informace o službě [Azure Functions](../azure-functions/index.yml).
* Další informace o [funkci Logic Apps služby Azure App Service](../logic-apps/index.yml).
* Další informace o službě [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png