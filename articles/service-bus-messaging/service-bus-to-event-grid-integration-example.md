---
title: Příklady integrace služby Azure Service Bus do služby Event Grid | Microsoft Docs
description: Tento článek obsahuje příklady integrace zasílání zpráv služby Service Bus a služby Event Grid.
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
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b7dbc7dbc0b670de81a3f4603b0d52bce7559af8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428313"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Reakce na události služby Azure Service Bus obdržené prostřednictvím služby Azure Event Grid s využitím Azure Functions a Azure Logic Apps
V tomto kurzu se dozvíte, jak reagovat na události služby Azure Service Bus, které dostávají prostřednictvím služby Azure Event Grid pomocí Azure Functions a Azure Logic Apps. Budete proveďte následující kroky:
 
- Vytvořte testovací funkci Azure pro účely ladění a zobrazení počátečního toku událostí ze služby Event Grid.
- Vytvořte funkci Azure pro příjem a zpracování zpráv služby Azure Service Bus na základě událostí služby Event Grid.
- Vytvoření aplikace logiky pro reakci na události služby Event Grid

Po vytvoření služby Service Bus, služby Event Grid, Azure Functions a Logic Apps artefakty, proveďte následující akce: 

1. Odesílání zpráv do tématu služby Service Bus. 
2. Ověřte, že tyto zprávy přijali odběry tématu
3. Ověřte, že funkce nebo aplikace logiky, která přihlášený(á) k odběru události obdržel událost. 

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus
Postupujte podle pokynů v tomto kurzu: [Rychlé zprovoznění: Pomocí webu Azure portal k vytvoření tématu služby Service Bus a odběrů na téma](service-bus-quickstart-topics-subscriptions-portal.md) a proveďte následující úlohy:

- Vytvoření **premium** oboru názvů Service Bus. 
- Získání připojovacího řetězce. 
- Vytvoření tématu služby Service Bus.
- Vytvořte dva odběry tématu. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Příprava ukázkové aplikace pro odesílání zpráv
Do tématu služby Service Bus můžete odeslat zprávu libovolnou metodou. Vzorový kód na konci tohoto postupu se předpokládá, že používáte Visual Studio 2017.

1. Naklonujte [úložiště GitHub azure-service-bus](https://github.com/Azure/azure-service-bus/).
2. V sadě Visual Studio přejděte do složky *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* a otevřete soubor *SBEventGridIntegration.sln*.
3. Přejděte do projektu **MessageSender** a vyberte soubor **Program.cs**.
4. Zadejte název vašeho tématu služby Service Bus a připojovací řetězec, který jste získali v předchozím kroku:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Sestavte a spusťte program k odesílání zkušebních zpráv do tématu Service Bus. 

## <a name="set-up-a-test-function-on-azure"></a>Nastavení testovací funkce v Azure 
Než začnete procházet celý scénář, nastavte alespoň malou testovací funkci, kterou můžete použít k ladění a sledování událostí, které tok. Postupujte podle pokynů [vytvoření první funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md) článku a proveďte následující úlohy: 

1. Vytvoření aplikace function app.
2. Vytvoření funkce aktivované protokolem HTTP. 

Proveďte následující kroky: 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions V2](#tab/v2)

1. Rozbalte **funkce** ve stromové struktuře zobrazit a vyberte vaši funkci. Nahraďte kód funkce následujícím kódem: 

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

    ![Výstup aplikace – funkce](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Vyberte **získat adresu URL funkce** a poznamenejte si adresu URL. 

    ![Získat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions V1](#tab/v1)

1. Konfigurace funkce použít **V1** verze: 
    1. Vyberte aplikaci funkcí ve stromovém zobrazení a vyberte **fungovat nastavení aplikace**. 

        ![Nastavení aplikace funkcí]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Vyberte **~ 1** pro **verze modulu Runtime**. 
2. Rozbalte **funkce** ve stromové struktuře zobrazit a vyberte vaši funkci. Nahraďte kód funkce následujícím kódem: 

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

    ![Výstup aplikace – funkce](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Vyberte **získat adresu URL funkce** a poznamenejte si adresu URL. 

    ![Získat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Propojení funkce a oboru názvů přes službu Event Grid
V této části můžete spojovat funkce a oboru názvů služby Service Bus pomocí webu Azure portal. 

Pokud chcete vytvořit odběr služby Azure Event Grid, postupujte takto:

1. Na webu Azure Portal, přejděte do svého oboru názvů a pak v levém podokně vyberte **události**. V pravém podokně se otevře okno vašeho oboru názvů, ve kterém se zobrazí dva odběry služby Event Grid. 
    
    ![Service Bus – události stránky](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Vyberte **+ odběr události** na panelu nástrojů. 
3. Na **vytvořit odběr události** stránce, proveďte následující kroky:
    1. Zadejte **název** pro předplatné. 
    2. Vyberte **Webhook** pro **typ koncového bodu**. 

        ![Service Bus – odběr služby Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Zvolili **vyberte koncový bod**, vložte adresu URL funkce a pak vyberte **potvrďte výběr**. 

        ![Funkce – vyberte koncový bod](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Přepněte **filtry** kartu, zadejte název **první předplatné** do tématu Service Bus jste vytvořili dříve a pak vyberte **vytvořit** tlačítko. 

        ![Filtr předplatných události](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Ověřte, že se odběr události v seznamu.

    ![Odběr událostí v seznamu](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Odesílání zpráv do tématu služby Service Bus
1. Spuštění rozhraní .NET C# aplikaci, která odesílá zprávy do tématu služby Service Bus. 

    ![Výstup na konzole aplikace](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na stránce pro vaši aplikaci Azure function app, rozbalte **funkce**, rozšířit vaše **funkce**a vyberte **monitorování**. 

    ![Funkce sledování](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Příjem zpráv pomocí služby Azure Functions
V předchozí části jste viděli jednoduchý scénář testování a ladění a ověřili jste tok událostí. 

V této části zjistíte, jak po přijetí události přijímat a zpracovávat zprávy.

### <a name="publish-a-function-from-visual-studio"></a>Publikování funkce ze sady Visual Studio
1. Ve stejném řešení sady Visual Studio (**SBEventGridIntegration**), které jste otevřeli, vyberte **ReceiveMessagesOnEvent.cs** v **SBEventGridIntegration** projektu. 
2. Zadejte svůj připojovací řetězec služby Service Bus v následujícím kódu:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Stáhněte si **profil publikování** pro funkci:
    1. Vyberte vaši aplikaci function app. 
    2. Vyberte **přehled** štítek, pokud ještě není vybraná. 
    3. Vyberte **získat profil publikování** na panelu nástrojů. 

        ![Získat profil pro funkci publikování](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Uložte soubor do složky vašeho projektu. 
4. V sadě Visual Studio klikněte pravým tlačítkem na **SBEventGridIntegration** a vyberte **Publikovat**. 
5. Vyberte *Start** na **publikovat** stránky. 
6. Na **vyberte cíl publikování** stránce, proveďte následující kroky, vyberte **importovat profil**. 

    ![Visual Studio – tlačítko Importovat profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Vyberte **souboru profilu publikování** jste předtím stáhli. 
8. Vyberte **publikovat** na **publikovat** stránky. 

    ![Visual Studio – publikování](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Zkontrolujte, jestli se nová funkce Azure Functions **ReceiveMessagesOnEvent**. V případě potřeby aktualizujte stránku. 

    ![Potvrďte, že se vytvoří nová funkce](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Získat adresu URL pro nové funkce a poznamenejte si ho. 

### <a name="event-grid-subscription"></a>Odběr služby Event Grid

1. Odstraňte existující odběr služby Event Grid:
    1. Na **služby Service Bus Namespace** stránce **události** v nabídce vlevo. 
    2. Vyberte předplatné, existující událost. 
    3. Na **odběr události** stránce **odstranit**.
2. Postupujte podle pokynů [propojení funkce a oboru názvů přes službu Event Grid](#connect-the-function-and-namespace-via-event-grid) část, která vytvoří odběr Event gridu pomocí nové adresy URL funkce.
3. Postupujte podle pokynů v [odesílání zpráv do tématu služby Service Bus](#send-messages-to-the-service-bus-topic) části k odeslání zprávy do tématu a monitorování funkce. 

## <a name="receive-messages-by-using-logic-apps"></a>Příjem zpráv pomocí Logic Apps
Připojení aplikace logiky s Azure Service Bus a Azure Event Grid pomocí následujících kroků:

1. Vytvoření aplikace logiky na webu Azure Portal.
    1. Vyberte **+ vytvořit prostředek**vyberte **integrace**a pak vyberte **aplikace logiky**. 
    2. Na **aplikace logiky – vytvořit** stránky, zadejte **název** pro aplikaci logiky.
    3. Vyberte své **předplatné** Azure. 
    4. Vyberte **použít existující** pro **skupiny prostředků**a vyberte skupinu prostředků, který jste použili pro jiné prostředky (třeba funkci Azure, obor názvů služby Service Bus), které jste vytvořili dříve. 
    5. Vyberte **umístění** pro aplikaci logiky. 
    6. Vyberte **vytvořit** k vytvoření aplikace logiky. 
2. Na **návrhář pro Logic Apps** stránce **prázdná aplikace logiky** pod **šablony**. 
3. V Návrháři proveďte následující kroky:
    1. Vyhledejte **služby Event Grid**. 
    2. Vyberte **při prostředků výskytu události (preview) – služby Azure Event Grid**. 

        ![Návrhář pro Logic Apps – výběr triggeru služby Event Grid](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Vyberte **přihlášení**, zadejte své přihlašovací údaje Azure a vyberte **povolit přístup**. 
5. Na **při výskytu události prostředku** stránce, proveďte následující kroky:
    1. Vyberte své předplatné Azure. 
    2. Pro **typ prostředku**vyberte **Microsoft.ServiceBus.Namespaces**. 
    3. Pro **název prostředku**, zvolte svůj obor názvů služby Service Bus. 
    4. Vyberte **přidat nový parametr**a vyberte **filtr přípon**. 
    5. Pro **filtr přípon**, zadejte název vašeho druhého předplatného tématu služby Service Bus. 
        ![Návrhář pro Logic Apps – konfigurace událostí](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Vyberte **+ nový krok** v návrháři, a proveďte následující kroky:
    1. Vyhledejte **Service Bus**.
    2. Vyberte **služby Service Bus** v seznamu. 
    3. Vyberte pro **získání zpráv** v **akce** seznamu. 
    4. Vyberte **načíst zprávy z odběru tématu (neboli peek-lock)** . 

        ![Návrhář aplikace logiky – akce get zprávy](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Zadejte **název připojení**. Příklad: **Načíst zprávy z odběru tématu**a vyberte obor názvů služby Service Bus. 

        ![Návrhář pro Logic Apps – vyberte obor názvů služby Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Vyberte **RootManageSharedAccessKey**.

        ![Návrhář pro Logic Apps – vyberte sdílený přístupový klíč](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Vyberte **Vytvořit**. 
    8. Vyberte téma a odběr. 
    
        ![Návrhář pro Logic Apps – vyberte téma služby Service Bus a předplatné](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Vyberte **+ nový krok**, a proveďte následující kroky: 
    1. Vyberte **Service Bus**.
    2. Vyberte **dokončit zprávu v odběru tématu** ze seznamu akcí. 
    3. Výběr vaší služby Service Bus **tématu**.
    4. Vyberte druhou **předplatné** do tématu.
    5. Pro **token uzamčení zprávy**vyberte **Token uzamčení** z **dynamický obsah**. 

        ![Návrhář pro Logic Apps – vyberte téma služby Service Bus a předplatné](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Vyberte **Uložit** na panelu nástrojů v návrháři pro Logic Apps se uložit aplikaci logiky. 
9. Postupujte podle pokynů v [odesílání zpráv do tématu služby Service Bus](#send-messages-to-the-service-bus-topic) oddílu pro odesílání zpráv do tématu. 
10. Přepněte **přehled** stránky aplikace logiky. Uvidíte spuštění aplikace logiky **historie běhů** pro zprávy odeslané.

    ![Návrhář pro Logic Apps – spuštění aplikace logiky](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Další postup

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
