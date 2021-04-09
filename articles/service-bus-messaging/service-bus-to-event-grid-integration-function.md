---
title: Zpracování událostí Service Bus přes Event Grid pomocí Azure Functions
description: Tento článek popisuje kroky pro zpracování Service Busch událostí přes Event Grid pomocí Azure Functions.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95819398"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Kurz: reakce na události Azure Service Bus přijatých prostřednictvím Azure Event Grid pomocí Azure Functions
V tomto kurzu se naučíte reagovat na události Azure Service Bus přijímané prostřednictvím Azure Event Grid pomocí Azure Functions a Azure Logic Apps. 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů Service Busu
> * Příprava ukázkové aplikace pro odesílání zpráv
> * Odeslání zprávy do tématu Service Bus
> * Příjem zpráv pomocí Logic Apps
> * Nastavení testovací funkce v Azure
> * Propojení funkce a oboru názvů přes službu Event Grid
> * Příjem zpráv pomocí služby Azure Functions

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Další požadavky
Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs) a přidejte úlohu **vývoj pro Azure** . Tato úloha zahrnuje **nástroje Azure Functions** , které potřebujete k vytváření, sestavování a nasazování projektů Azure Functions v aplikaci Visual Studio. 

## <a name="deploy-the-function-app"></a>Nasazení aplikace funkcí 

>[!NOTE]
> Další informace o vytváření a nasazování aplikace Azure Functions najdete v tématu [vývoj Azure Functions pomocí sady Visual Studio](../azure-functions/functions-develop-vs.md) .

1. Otevřete soubor **ReceiveMessagesOnEvent. cs** z projektu **FunctionApp1** řešení **SBEventGridIntegration. sln** . 
1. Nahraďte `<SERCICE BUS NAMESPACE - CONNECTION STRING>` připojovacím řetězcem k vašemu oboru názvů Service Bus. Měl by být stejný jako ten, který jste použili v souboru **program. cs** projektu **MessageSender** ve stejném řešení. 
1. Klikněte pravým tlačítkem na **FunctionApp1** a vyberte **publikovat**. 
1. Na stránce **publikovat** vyberte možnost **Spustit**. Tyto kroky se můžou lišit od toho, co vidíte, ale proces publikování by měl být podobný. 
1. V průvodci **publikování** na stránce **cíl** vyberte **Azure** pro **cíl**. 
1. Na stránce **konkrétní cíl** vyberte **Azure Function App (Windows)**. 
1. Na stránce **funkce instance** vyberte **vytvořit novou funkci Azure Functions**. 
1. Na stránce **Function App (Windows)** postupujte podle následujících kroků:
    1. Zadejte **název** aplikace Function App.
    1. Vyberte **předplatné** Azure.
    1. Vyberte existující **skupinu prostředků** nebo vytvořte novou skupinu prostředků. Pro tento kurz vyberte skupinu prostředků, která má obor názvů Service Bus. 
    1. Vyberte **typ plánu** pro App Service.
    1. Vyberte **umístění**. Vyberte stejné umístění jako obor názvů Service Bus. 
    1. Vyberte existující **Azure Storage** nebo vyberte **Nový** a vytvořte nový účet úložiště, který bude používat aplikace Functions. 
    1. Vyberte **vytvořit** a vytvořte aplikaci Functions. 
1. Zpátky na stránce **Function instance** průvodce **publikováním** vyberte **Dokončit**. 
1. Na stránce **publikovat** v aplikaci Visual Studio vyberte **publikovat** a publikujte aplikaci Functions do Azure. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Publikování funkcí Function App":::    
1. V okně **výstup** se podívejte na zprávy od sestavení a publikování a potvrďte, že obě jsou úspěšné. 
1. Nyní na stránce **publikovat** vyberte **spravovat v Azure Portal**. 
1. V Azure Portal na stránce **Function App** v nabídce vlevo vyberte **funkce** a potvrďte, že vidíte dvě funkce: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Dvě funkce obslužné rutiny Event Grid triggeru a triggeru HTTP":::

    > [!NOTE]
    > `EventGridTriggerFunction`Používá [aktivační událost Event Grid](../azure-functions/functions-bindings-event-grid-trigger.md) a `HTTPTriggerFunction` používá [Trigger http](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. V seznamu vyberte **EventGridTriggerFunction** . Pro použití triggeru HTTP doporučujeme použít aktivační událost Event Grid s Azure Functions, protože má několik výhod. Podrobnosti najdete v tématu [funkce Azure Functions jako obslužná rutina události pro Event Grid události](../event-grid/handler-functions.md).
1. Na stránce **funkce** pro **EventGridTriggerFunction** vyberte v nabídce vlevo možnost **monitor** . 
1. Vyberte **Konfigurovat** a nakonfigurujte Application Insights pro zachycení protokolu vyvolání. Pomocí této stránky můžete monitorovat provádění funkcí při přijímání Service Busch událostí z Event Grid. 
1. Na stránce **Application Insights** zadejte název prostředku, vyberte **umístění** prostředku a pak vyberte **OK**. 
1. Pokud chcete přejít zpět na stránku **funkce** , vyberte **EventGridTriggerFunction** funkce v horní části (v nabídce s popisem cesty). 
1. Potvrďte, že jste na stránce **monitorování** . 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Stránka monitorování pro funkci před vyvoláním funkce":::
    
    Tuto stránku nechte otevřenou ve webovém prohlížeči na kartě. Tuto stránku aktualizujete, aby se pro tuto funkci mohla později zobrazit vyvolání.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Propojení funkce a oboru názvů přes službu Event Grid
V této části spojíte funkci a obor názvů Service Bus pomocí Azure Portal. 

K vytvoření předplatného Azure Event Grid použijte následující postup:

1. V Azure Portal přejdete do svého oboru názvů a potom v levém podokně vyberete možnost **události**. V pravém podokně se otevře okno vašeho oboru názvů, ve kterém se zobrazí dva odběry služby Event Grid. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Stránka Service Bus – události":::
2. Na panelu nástrojů vyberte **+ odběr události** . 
3. Na stránce **vytvořit odběr události** proveďte následující kroky:
    1. Zadejte **název** předplatného. 
    2. Zadejte **název** **systémového tématu**. Systémová témata jsou témata vytvořená pro prostředky Azure, jako je Azure Storage účet a Azure Service Bus. Další informace o systémových tématech najdete v tématu [Přehled systémových témat](../event-grid/system-topics.md).
    2. Pro **Typ koncového bodu** vyberte **Azure Function** a klikněte na **Vybrat koncový bod**. 

        ![Předplatné Service Bus-Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Na stránce **Vybrat funkci Azure** vyberte předplatné, skupinu prostředků, aplikaci Function App, slot a funkci a pak vyberte **potvrdit výběr**. 

        ![Funkce – Výběr koncového bodu](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Na stránce **vytvořit odběr události** přepněte na kartu **filtry** a proveďte následující úlohy:
        1. Vyberte **Povolit filtrování předmětu**
        2. Zadejte název předplatného pro Service Bus téma (**S1**), které jste vytvořili dříve.
        3. Vyberte tlačítko **Vytvořit**. 

            ![Filtr odběrů událostí](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Na stránce **události** přepněte na kartu **odběry událostí** a potvrďte, že se v seznamu zobrazí odběr událostí.

    ![Odběr událostí v seznamu](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Monitorování aplikace Functions
Zprávy, které jste odeslali do výše uvedeného tématu Service Bus, se předají do předplatného (S1). Event Grid přepošle zprávy do předplatného do funkce Azure Function. V tomto kroku kurzu ověříte, že se funkce vyvolala, a zobrazí se zaznamenané informační zprávy. 

1. Na stránce aplikace Function Azure přepněte na kartu **monitorování** , pokud už není aktivní. Měla by se zobrazit položka pro každou zprávu zveřejněnou v Service Bus tématu. Pokud je nevidíte, aktualizujte stránku po několik minut. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Stránka monitorování pro funkci po vyvolání":::
2. Kliknutím na vyvolání ze seznamu zobrazíte podrobnosti. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Podrobnosti vyvolání funkce" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    Můžete také použít kartu **protokoly** stránky **monitorování** a zobrazit tak informace o protokolování, jak jsou odesílány zprávy. Mohlo dojít k prodlevě, takže si můžete zaprotokolovat zprávy několika minut. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Protokoly funkce" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Řešení potíží
Pokud nevidíte žádné vyvolání funkcí po čekání a aktualizace po nějakou dobu neaktualizujete, použijte následující postup: 

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