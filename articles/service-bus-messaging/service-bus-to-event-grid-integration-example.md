---
title: 'Kurz: Azure Service Bus pro Event Grid příklady integrace'
description: 'Kurz: Tento článek popisuje příklady Service Bus zasílání zpráv a Event Grid integraci.'
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 6f64c8155724a1306f5074a9164bcf97391dfbf6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000341"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Kurz: reakce na události Azure Service Bus přijatých prostřednictvím Azure Event Grid pomocí Azure Functions a Azure Logic Apps
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

## <a name="prerequisites"></a>Požadavky

Před tímto kurzem se ujistěte, že máte nainstalované tyto položky:

- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější.
- [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Busu
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

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Aktualizujte `numberOfMessages` hodnotu na **5**. 
5. Sestavte a spusťte program pro odeslání zkušebních zpráv do tématu Service Bus. 

## <a name="receive-messages-by-using-logic-apps"></a>Příjem zpráv pomocí Logic Apps
Pomocí následujících kroků Připojte aplikaci logiky pomocí Azure Service Bus a Azure Event Grid:

1. Vytvoření aplikace logiky v Azure Portal.
    1. Vyberte **+ vytvořit prostředek**, vyberte **integrace**a pak vyberte **Aplikace logiky**. 
    2. Na stránce **Logic App-Create (vytvořit aplikaci logiky** ) zadejte **název** aplikace logiky.
    3. Vyberte své **předplatné**Azure. 
    4. Vyberte pro **skupinu prostředků**možnost **použít existující** a vyberte skupinu prostředků, kterou jste použili pro jiné prostředky (třeba funkci Azure Functions Service Bus obor názvů), kterou jste vytvořili dříve. 
    5. Vyberte **umístění** aplikace logiky. 
    6. Vyberte **vytvořit** a vytvořte aplikaci logiky. 
2. Na stránce **návrháře Logic Apps** v části **šablony**vyberte **prázdná aplikace logiky** . 
3. V Návrháři proveďte následující kroky:
    1. Vyhledejte **Event Grid**. 
    2. Vyberte, **kdy dojde k události prostředku – Azure Event Grid**. 

        ![Návrhář Logic Apps – výběr Event Grid triggeru](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Vyberte **Přihlásit**, zadejte svoje přihlašovací údaje Azure a vyberte možnost **Povolení přístupu**. 
5. Na stránce **když dojde k události prostředku** , proveďte následující kroky:
    1. Vyberte své předplatné Azure. 
    2. Jako **typ prostředku**vyberte **Microsoft. ServiceBus. Namespaces**. 
    3. Jako **název prostředku**vyberte obor názvů Service Bus. 
    4. Vyberte **Přidat nový parametr**a vyberte **filtr přípon**. 
    5. Jako **filtr přípon**zadejte název druhého předplatného Service Bus tématu. 
        ![Návrhář Logic Apps – konfigurace události](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. V návrháři vyberte **+ Nový krok** a proveďte následující kroky:
    1. Vyhledejte **Service Bus**.
    2. V seznamu vyberte **Service Bus** . 
    3. V seznamu **akcí** vyberte možnost **získat zprávy** . 
    4. Vyberte možnost **získat zprávy z odběru tématu (náhled – zámek)**. 

        ![Návrhář Logic Apps – akce získat zprávy](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Zadejte **název připojení**. Například: **získat zprávy z odběru tématu**a vybrat Service Bus obor názvů. 

        ![Logic Apps Designer – výběr oboru názvů Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Vyberte **RootManageSharedAccessKey**a pak vyberte **vytvořit**.

        ![Návrhář Logic Apps – výběr sdíleného přístupového klíče](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Vyberte své **téma** a **předplatné**. 
    
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

## <a name="set-up-a-test-function-on-azure"></a>Nastavení testovací funkce v Azure 
Než začnete pracovat v celém scénáři, nastavte alespoň malou testovací funkci, kterou můžete použít k ladění a sledování událostí, které jsou předávány. Postupujte podle pokynů v části [Vytvoření první funkce v Azure Portal](../azure-functions/functions-create-first-azure-function.md) článku a proveďte následující úlohy: 

1. Vytvořte aplikaci Function App.
2. Vytvoření funkce aktivované protokolem HTTP 

Pak proveďte následující kroky: 


# <a name="azure-functions-v2"></a>[Azure Functions v2](#tab/v2)

1. Rozbalte **funkce** ve stromovém zobrazení a vyberte svou funkci. Nahraďte kód funkce následujícím kódem: 

    ```csharp
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
                log.LogInformation("Validating the subscription");            
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation($"Validation code: {code}");
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
2. Na panelu nástrojů vyberte **Uložit** a uložte kód pro funkci.

    ![Uložit kód funkce](./media/service-bus-to-event-grid-integration-example/save-function-code.png)
3. Na panelu nástrojů vyberte **test/Run** a proveďte následující kroky: 
    1. Do **textu**zadejte následující kód JSON.

        ```json
        [{
          "id": "64ba80ae-9f8e-425f-8bd7-d88d2c0ba3e3",
          "topic": "/subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/spegridsbusrg/providers/Microsoft.ServiceBus/namespaces/spegridsbusns",
          "subject": "",
          "data": {
            "validationCode": "D7D825D4-BD04-4F73-BDE3-70666B149857",
            "validationUrl": "https://rp-eastus.eventgrid.azure.net:553/eventsubscriptions/spsbusegridsubscription/validate?id=D7D825D4-BD04-4F73-BDE3-70666B149857&t=2020-06-09T18:28:51.5724615Z&apiVersion=2020-04-01-preview&[Hidden Credential]"
          },
          "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
          "eventTime": "2020-06-09T18:28:51.5724615Z",
          "metadataVersion": "1",
          "dataVersion": "2"
        }]
        ```    
    2. Klikněte na **Přidat hlavičku**a přidejte hlavičku s názvem `aeg-event-type` a hodnotou `SubscriptionValidation` . 
    3. Vyberte **Run** (Spustit). 

        ![Testovací běh](./media/service-bus-to-event-grid-integration-example/test-run-function.png)
    4. Ověřte, že se v těle odpovědi zobrazí návratový kód stavu **OK** a ověřovací kód. Podívejte se také na informace zaznamenávané funkcí. 

        ![Testovací běh – odpověď](./media/service-bus-to-event-grid-integration-example/test-function-response.png)        
3. Vyberte **získat adresu URL funkce** a poznamenejte si adresu URL. 

    ![Získat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Klikněte na tlačítko **Kopírovat** vedle textu adresy URL.    
    ![Kopírovat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

# <a name="azure-functions-v1"></a>[Azure Functions v1](#tab/v1)

1. Nakonfigurujte funkci pro použití verze **v1** : 
    1. Ve stromovém zobrazení vyberte aplikaci Function App a vyberte možnost **nastavení aplikace Function App**. 
    2. Pro **verzi modulu runtime**vyberte **~ 1** . 
2. Rozbalte **funkce** ve stromovém zobrazení a vyberte svou funkci. Nahraďte kód funkce následujícím kódem: 

    ```csharp
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
4. Na panelu nástrojů vyberte **získat adresu URL funkce** . 

    ![Získat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Klikněte na tlačítko **Kopírovat** vedle textu adresy URL.    
    ![Kopírovat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Propojení funkce a oboru názvů přes službu Event Grid
V této části spojíte funkci a obor názvů Service Bus pomocí Azure Portal. 

K vytvoření předplatného Azure Event Grid použijte následující postup:

1. V Azure Portal přejdete do svého oboru názvů a potom v levém podokně vyberete možnost **události**. V pravém podokně se otevře okno vašeho oboru názvů, ve kterém se zobrazí dva odběry služby Event Grid. 
    
    ![Stránka Service Bus – události](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Na panelu nástrojů vyberte **+ odběr události** . 
3. Na stránce **vytvořit odběr události** proveďte následující kroky:
    1. Zadejte **název** předplatného. 
    2. Zadejte **název** **systémového tématu**. Systémová témata jsou témata vytvořená pro prostředky Azure, jako je Azure Storage účet a Azure Service Bus. Další informace o systémových tématech najdete v tématu [Přehled systémových témat](../event-grid/system-topics.md).
    2. Vyberte **webový Hook** pro **Typ koncového bodu**. 

        ![Předplatné Service Bus-Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Zvolte **Vybrat koncový bod**, vložte adresu URL funkce a pak vyberte **potvrdit výběr**. 

        ![Funkce – Výběr koncového bodu](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Přepněte na kartu **filtry** a proveďte následující úlohy:
        1. Vyberte **Povolit filtrování předmětu**
        2. Do tématu Service Bus, které jste vytvořili dříve, zadejte název **prvního předplatného** .
        3. Vyberte tlačítko **Vytvořit**. 

            ![Filtr odběrů událostí](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Na stránce **události** přepněte na kartu **odběry událostí** a potvrďte, že se v seznamu zobrazí odběr událostí.

    ![Odběr událostí v seznamu](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Odeslání zprávy do tématu Service Bus
1. Spusťte aplikaci .NET C#, která odešle zprávy do tématu Service Bus. 

    ![Výstup aplikace konzoly](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na stránce aplikace Function App se přepněte na kartu **monitorování** na kartě **kód + test** . Měla by se zobrazit položka pro každou zprávu zveřejněnou v Service Bus tématu. Pokud je nevidíte, aktualizujte stránku po několik minut. 

    ![Monitor – funkce](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

    Můžete také použít kartu **protokoly** stránky **monitorování** a zobrazit tak informace o protokolování, jak jsou odesílány zprávy. Mohlo dojít k prodlevě, takže si můžete zaprotokolovat zprávy několika minut. 

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
    1. Vyberte svou aplikaci funkcí. 
    2. Vyberte kartu **Přehled** , pokud už není vybraná. 
    3. Na panelu nástrojů vyberte **získat profil publikování** . 

        ![Získat profil publikování pro funkci](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Uložte soubor do složky vašeho projektu. 
4. V sadě Visual Studio klikněte pravým tlačítkem na **SBEventGridIntegration** a vyberte **Publikovat**. 
5. Na stránce **publikovat**proveďte následující kroky: 
    1. Na stránce **publikovat** vyberte **Spustit** . 
    2. Jako **cíl**vyberte **Importovat profil**. 
    3. Vyberte **Další**. 

        ![Visual Studio – tlačítko Importovat profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Vyberte **soubor profilu publikování** , který jste stáhli dříve, a vyberte **Dokončit**.

    ![Vyberte profil publikování.](./media/service-bus-to-event-grid-integration-example/select-publish-profile.png)
8. Na stránce **publikovat** vyberte **publikovat** . 

    ![Visual Studio – publikování](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Potvrďte, že se zobrazí nová funkce Azure Function **ReceiveMessagesOnEvent**. V případě potřeby aktualizujte stránku. 

    ![Potvrďte, že je nová funkce vytvořená.](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Získejte adresu URL nové funkce a poznamenejte si ji. 

### <a name="event-grid-subscription"></a>Předplatné Event Grid

1. Odstranit stávající předplatné Event Grid:
    1. Na stránce **Service Bus obor názvů** vyberte v nabídce vlevo možnost **události** . 
    2. Přepněte na kartu **odběry událostí** . 
    2. Vyberte existující odběr události. 

        ![Vybrat odběr události](./media/service-bus-to-event-grid-integration-example/select-event-subscription.png)
    3. Na stránce **odběr události** vyberte **Odstranit**. Kliknutím na **Ano** potvrďte odstranění. 
        ![Tlačítko Odstranit předplatné události](./media/service-bus-to-event-grid-integration-example/delete-subscription-button.png)
2. Postupujte podle pokynů v části [připojení funkce a oboru názvů prostřednictvím Event Grid](#connect-the-function-and-namespace-via-event-grid) oddílu a vytvořte Event Grid předplatné pomocí nové adresy URL funkce.
3. Postupujte podle pokynů v části [posílání zpráv do tématu Service Bus](#send-messages-to-the-service-bus-topic) a odešlete zprávy do tématu a sledujte funkci. 


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