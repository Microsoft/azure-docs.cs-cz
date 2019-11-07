---
title: 'Kurz: Azure Service Bus pro Event Grid příklady integrace'
description: 'Kurz: Tento článek popisuje příklady Service Bus zasílání zpráv a Event Grid integraci.'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: d3f523bbc0236536734f21870474492a61532ada
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719021"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Kurz: reakce na události Azure Service Bus přijatých prostřednictvím Azure Event Grid pomocí Azure Functions a Azure Logic Apps
V tomto kurzu se naučíte reagovat na události Azure Service Bus přijímané prostřednictvím Azure Event Grid pomocí Azure Functions a Azure Logic Apps. Provedete následující kroky:
 
- Vytvoření testovací funkce Azure pro ladění a zobrazení počátečního toku událostí z Event Grid.
- Vytvořte funkci Azure pro příjem a zpracování zpráv Azure Service Bus na základě Event Gridch událostí.
- Vytvoření aplikace logiky, která reaguje na události Event Grid

Po vytvoření Service Bus, Event Grid, Azure Functions a artefaktů Logic Apps proveďte následující akce: 

1. Odeslání zprávy do tématu Service Bus. 
2. Ověřte, zda odběry tohoto tématu přijaly tyto zprávy.
3. Ověřte, že funkce nebo aplikace logiky, které se přihlásily k odběru události, přijaly událost. 

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus
Postupujte podle pokynů v tomto kurzu: [rychlý Start: pomocí Azure Portal vytvořte Service Bus téma a odběry k tématu](service-bus-quickstart-topics-subscriptions-portal.md) , abyste mohli provádět následující úlohy:

- Vytvořte obor názvů **premium** Service Bus. 
- Získá připojovací řetězec. 
- Vytvoří Service Bus téma.
- Vytvořte dvě předplatná pro téma. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Příprava ukázkové aplikace pro odesílání zpráv
Do tématu služby Service Bus můžete odeslat zprávu libovolnou metodou. Vzorový kód na konci tohoto postupu předpokládá, že používáte sadu Visual Studio 2017.

1. Naklonujte [úložiště GitHub azure-service-bus](https://github.com/Azure/azure-service-bus/).
2. V sadě Visual Studio přejděte do složky *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* a otevřete soubor *SBEventGridIntegration.sln*.
3. Přejděte do projektu **MessageSender** a vyberte soubor **Program.cs**.
4. Zadejte Service Bus název tématu a připojovací řetězec, který jste získali z předchozího kroku:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Sestavte a spusťte program pro odeslání zkušebních zpráv do tématu Service Bus. 

## <a name="set-up-a-test-function-on-azure"></a>Nastavení testovací funkce v Azure 
Než začnete pracovat v celém scénáři, nastavte alespoň malou testovací funkci, kterou můžete použít k ladění a sledování událostí, které jsou předávány. Postupujte podle pokynů v části [Vytvoření první funkce v Azure Portal](../azure-functions/functions-create-first-azure-function.md) článku a proveďte následující úlohy: 

1. Vytvořte aplikaci Function App.
2. Vytvoření funkce aktivované protokolem HTTP 

Pak proveďte následující kroky: 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions v2](#tab/v2)

1. Rozbalte **funkce** ve stromovém zobrazení a vyberte svou funkci. Nahraďte kód funkce následujícím kódem: 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. Vyberte **Uložit a spustit**.

    ![Výstup aplikace Function App](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Vyberte **získat adresu URL funkce** a poznamenejte si adresu URL. 

    ![Získat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions v1](#tab/v1)

1. Nakonfigurujte funkci pro použití verze **v1** : 
    1. Ve stromovém zobrazení vyberte aplikaci Function App a vyberte možnost **nastavení aplikace Function App**. 

        ![Nastavení aplikace funkcí]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Pro **verzi modulu runtime**vyberte **~ 1** . 
2. Rozbalte **funkce** ve stromovém zobrazení a vyberte svou funkci. Nahraďte kód funkce následujícím kódem: 

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Vyberte **Uložit a spustit**.

    ![Výstup aplikace Function App](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Vyberte **získat adresu URL funkce** a poznamenejte si adresu URL. 

    ![Získat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Propojení funkce a oboru názvů přes službu Event Grid
V této části spojíte funkci a obor názvů Service Bus pomocí Azure Portal. 

K vytvoření předplatného Azure Event Grid použijte následující postup:

1. V Azure Portal přejdete do svého oboru názvů a potom v levém podokně vyberete možnost **události**. V pravém podokně se otevře okno vašeho oboru názvů, ve kterém se zobrazí dva odběry služby Event Grid. 
    
    ![Stránka Service Bus – události](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Na panelu nástrojů vyberte **+ odběr události** . 
3. Na stránce **vytvořit odběr události** proveďte následující kroky:
    1. Zadejte **název** předplatného. 
    2. Vyberte **webový Hook** pro **Typ koncového bodu**. 

        ![Předplatné Service Bus-Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Zvolte **Vybrat koncový bod**, vložte adresu URL funkce a pak vyberte **potvrdit výběr**. 

        ![Funkce – Výběr koncového bodu](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Přepněte na kartu **filtry** , zadejte název **prvního odběru** do Service Busho tématu, které jste vytvořili dříve, a pak vyberte tlačítko **vytvořit** . 

        ![Filtr odběrů událostí](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Potvrďte, že se v seznamu zobrazí odběr událostí.

    ![Odběr událostí v seznamu](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Odeslání zprávy do tématu Service Bus
1. Spusťte aplikaci .NET C# , která odešle zprávy do tématu Service Bus. 

    ![Výstup aplikace konzoly](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na stránce aplikace Function App rozbalte **funkce**, rozbalte svou **funkci**a vyberte **monitor**. 

    ![Monitor – funkce](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Příjem zpráv pomocí služby Azure Functions
V předchozí části jste viděli jednoduchý scénář testování a ladění a ověřili jste tok událostí. 

V této části zjistíte, jak po přijetí události přijímat a zpracovávat zprávy.

### <a name="publish-a-function-from-visual-studio"></a>Publikování funkce ze sady Visual Studio
1. Ve stejném řešení sady Visual Studio (**SBEventGridIntegration**), které jste otevřeli, vyberte v projektu **SBEventGridIntegration** možnost **ReceiveMessagesOnEvent.cs** . 
2. Zadejte připojovací řetězec Service Bus v následujícím kódu:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Stáhněte si **profil publikování** pro funkci:
    1. Vyberte svou aplikaci Function App. 
    2. Vyberte kartu **Přehled** , pokud už není vybraná. 
    3. Na panelu nástrojů vyberte **získat profil publikování** . 

        ![Získat profil publikování pro funkci](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Uložte soubor do složky vašeho projektu. 
4. V sadě Visual Studio klikněte pravým tlačítkem na **SBEventGridIntegration** a vyberte **Publikovat**. 
5. Na stránce **publikovat** vyberte **Spustit** . 
6. Na stránce **Vyberte cíl publikování** proveďte následující kroky a vyberte **Importovat profil**. 

    ![Visual Studio – tlačítko Importovat profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Vyberte **soubor publikačního profilu** , který jste předtím stáhli. 
8. Na stránce **publikovat** vyberte **publikovat** . 

    ![Visual Studio – publikování](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Potvrďte, že se zobrazí nová funkce Azure Function **ReceiveMessagesOnEvent**. V případě potřeby aktualizujte stránku. 

    ![Potvrďte, že je nová funkce vytvořená.](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Získejte adresu URL nové funkce a poznamenejte si ji. 

### <a name="event-grid-subscription"></a>Předplatné Event Grid

1. Odstranit stávající předplatné Event Grid:
    1. Na stránce **Service Bus obor názvů** vyberte v nabídce vlevo možnost **události** . 
    2. Vyberte existující odběr události. 
    3. Na stránce **odběr události** vyberte **Odstranit**.
2. Postupujte podle pokynů v části [připojení funkce a oboru názvů prostřednictvím Event Grid](#connect-the-function-and-namespace-via-event-grid) oddílu a vytvořte Event Grid předplatné pomocí nové adresy URL funkce.
3. Postupujte podle pokynů v části [posílání zpráv do tématu Service Bus](#send-messages-to-the-service-bus-topic) a odešlete zprávy do tématu a sledujte funkci. 

## <a name="receive-messages-by-using-logic-apps"></a>Příjem zpráv pomocí Logic Apps
Pomocí následujících kroků Připojte aplikaci logiky pomocí Azure Service Bus a Azure Event Grid:

1. Vytvoření aplikace logiky v Azure Portal.
    1. Vyberte **+ vytvořit prostředek**, vyberte **integrace**a pak vyberte **Aplikace logiky**. 
    2. Na stránce **Logic App-Create (vytvořit aplikaci logiky** ) zadejte **název** aplikace logiky.
    3. Vyberte své **předplatné** Azure. 
    4. Vyberte pro **skupinu prostředků**možnost **použít existující** a vyberte skupinu prostředků, kterou jste použili pro jiné prostředky (třeba funkci Azure Functions Service Bus obor názvů), kterou jste vytvořili dříve. 
    5. Vyberte **umístění** aplikace logiky. 
    6. Vyberte **vytvořit** a vytvořte aplikaci logiky. 
2. Na stránce **návrháře Logic Apps** v části **šablony**vyberte **prázdná aplikace logiky** . 
3. V Návrháři proveďte následující kroky:
    1. Vyhledejte **Event Grid**. 
    2. Vyberte, **kdy dojde k události prostředku (Preview)-Azure Event Grid**. 

        ![Návrhář Logic Apps – výběr Event Grid triggeru](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Vyberte **Přihlásit**, zadejte svoje přihlašovací údaje Azure a vyberte možnost **Povolení přístupu**. 
5. Na stránce **když dojde k události prostředku** , proveďte následující kroky:
    1. Vyberte své předplatné Azure. 
    2. Jako **typ prostředku**vyberte **Microsoft. ServiceBus. Namespaces**. 
    3. Jako **název prostředku**vyberte obor názvů Service Bus. 
    4. Vyberte **Přidat nový parametr**a vyberte **filtr přípon**. 
    5. Jako **filtr přípon**zadejte název druhého předplatného Service Bus tématu. 
        ![Logic Apps Designer – konfigurace](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png) událostí
6. V návrháři vyberte **+ Nový krok** a proveďte následující kroky:
    1. Vyhledejte **Service Bus**.
    2. V seznamu vyberte **Service Bus** . 
    3. V seznamu **akcí** vyberte možnost **získat zprávy** . 
    4. Vyberte možnost **získat zprávy z odběru tématu (náhled – zámek)** . 

        ![Návrhář Logic Apps – akce získat zprávy](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Zadejte **název připojení**. Například: **získat zprávy z odběru tématu**a vybrat Service Bus obor názvů. 

        ![Logic Apps Designer – výběr oboru názvů Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Vyberte **RootManageSharedAccessKey**.

        ![Návrhář Logic Apps – výběr sdíleného přístupového klíče](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Vyberte **Vytvořit**. 
    8. Vyberte své téma a předplatné. 
    
        ![Logic Apps Designer – výběr Service Busho tématu a předplatného](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Vyberte **+ Nový krok**a proveďte následující kroky: 
    1. Vyberte **Service Bus**.
    2. V seznamu akcí vyberte **Dokončit zprávu v odběru tématu** . 
    3. Vyberte Service Bus **téma**.
    4. Vyberte druhé **předplatné** tématu.
    5. Pro **token zámku zprávy**vyberte možnost **Uzamknout token** z **dynamického obsahu**. 

        ![Logic Apps Designer – výběr Service Busho tématu a předplatného](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Pokud chcete aplikaci logiky uložit, vyberte **Uložit** na panelu nástrojů v Návrháři Logic Apps. 
9. Postupujte podle pokynů v části [posílání zpráv do tématu Service Bus](#send-messages-to-the-service-bus-topic) a odešlete zprávy do tématu. 
10. Přepněte na stránku **Přehled** vaší aplikace logiky. Zobrazí se aplikace logiky spuštěné v **historii spuštění** odesílaných zpráv.

    ![Návrhář Logic Apps – spuštění aplikace logiky](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Další kroky

* Další informace o službě [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Další informace o službě [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Další informace o [funkci Logic Apps služby Azure App Service](https://docs.microsoft.com/azure/logic-apps/).
* Další informace o službě [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).


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
