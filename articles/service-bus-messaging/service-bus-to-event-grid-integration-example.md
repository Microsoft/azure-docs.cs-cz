---
title: 'Kurz: Příklady integrace Azure Service Bus do gridu událostí'
description: 'Kurz: Tento článek obsahuje příklady zasílání zpráv service bus a integrace gridu událostí.'
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
ms.openlocfilehash: fef325b67c38eda09a05dac9d74bd5b97df164cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067770"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Kurz: Reagujte na události Azure Service Bus přijaté prostřednictvím Azure Event Grid pomocí Azure Functions a Azure Logic Apps
V tomto kurzu se dozvíte, jak reagovat na události Azure Service Bus, které jsou přijímány prostřednictvím Azure Event Grid pomocí Azure Functions a Azure Logic Apps. 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů Service Bus
> * Příprava ukázkové aplikace pro odesílání zpráv
> * Nastavení testovací funkce v Azure
> * Propojení funkce a oboru názvů přes službu Event Grid
> * Odeslání zpráv tématu Service Bus
> * Příjem zpráv pomocí služby Azure Functions
> * Příjem zpráv pomocí Logic Apps

## <a name="prerequisites"></a>Požadavky

Před tímto kurzem se ujistěte, že máte nainstalované tyto položky:

- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější.
- [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus
Postupujte podle pokynů v tomto kurzu: [Úvodní příručka: Pomocí portálu Azure vytvořte téma služby Service Bus a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md) k provedení následujících úkolů:

- Vytvořte obor názvů **premium** Service Bus. 
- Získejte připojovací řetězec. 
- Vytvořte téma služby Service Bus.
- Vytvořte dvě odběry tématu. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Příprava ukázkové aplikace pro odesílání zpráv
Do tématu služby Service Bus můžete odeslat zprávu libovolnou metodou. Ukázkový kód na konci tohoto postupu předpokládá, že používáte Visual Studio 2017.

1. Naklonujte [úložiště GitHub azure-service-bus](https://github.com/Azure/azure-service-bus/).
2. V sadě Visual Studio přejděte do složky *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* a otevřete soubor *SBEventGridIntegration.sln*.
3. Přejděte do projektu **MessageSender** a vyberte soubor **Program.cs**.
4. Vyplňte název tématu služby Service Bus a připojovací řetězec, který jste získali z předchozího kroku:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Vytvořte a spusťte program a odesílejte testovací zprávy do tématu Service Bus. 

## <a name="set-up-a-test-function-on-azure"></a>Nastavení testovací funkce v Azure 
Před zahájením práce přes celý scénář, nastavte alespoň malou testovací funkci, kterou můžete použít k ladění a sledování události, které jsou tekoucí. Postupujte podle pokynů v článku Vytvořit první funkci v článku [na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md) a proveďte následující úkoly: 

1. Vytvořte aplikaci funkcí.
2. Vytvořte funkci aktivovanou protokolem HTTP. 

Potom proveďte následující kroky: 


# <a name="azure-functions-v2"></a>[Funkce Azure V2](#tab/v2)

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

    ![Výstup aplikace funkce](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Vyberte **Získat adresu URL funkce** a poznamenejte si adresu URL. 

    ![Získat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1"></a>[Funkce Azure V1](#tab/v1)

1. Nakonfigurujte funkci pro použití verze **V1:** 
    1. Ve stromovém zobrazení vyberte aplikaci funkce a vyberte **Nastavení aplikace Funkce**. 

        ![Nastavení aplikace funkcí]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Vyberte **~1** pro **verzi runtime**. 
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

    ![Výstup aplikace funkce](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Vyberte **Získat adresu URL funkce** a poznamenejte si adresu URL. 

    ![Získat adresu URL funkce](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Propojení funkce a oboru názvů přes službu Event Grid
V této části můžete spojit dohromady funkci a obor názvů Service Bus pomocí portálu Azure. 

Pokud chcete vytvořit předplatné Azure Event Grid, postupujte takto:

1. Na webu Azure Portal přejděte do oboru názvů a v levém podokně vyberte **Události**. V pravém podokně se otevře okno vašeho oboru názvů, ve kterém se zobrazí dva odběry služby Event Grid. 
    
    ![Service Bus - stránka událostí](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Na panelu nástrojů vyberte **+ Odběr událostí.** 
3. Na stránce **Vytvořit odběr událostí** postupujte takto:
    1. Zadejte **název** předplatného. 
    2. Vyberte **webový hák** pro **typ koncového bodu**. 

        ![Service Bus – předplatné Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Vyberte **Vybrat koncový bod**, vložte adresu URL funkce a pak vyberte Potvrdit **výběr**. 

        ![Funkce - výběr koncového bodu](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Přepněte na kartu **Filtry,** zadejte název **prvního předplatného** k tématu Service Bus, které jste vytvořili dříve, a pak vyberte tlačítko **Vytvořit.** 

        ![Filtr odběru událostí](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Potvrďte, že se v seznamu zobrazuje odběr událostí.

    ![Odběr událostí v seznamu](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Odeslání zpráv tématu Service Bus
1. Spusťte aplikaci .NET C#, která odesílá zprávy do tématu Service Bus. 

    ![Výstup konzolové aplikace](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na stránce aplikace Azure function rozbalte **funkce**, rozbalte **svou funkci**a vyberte **Monitor**. 

    ![Funkce monitoru](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Příjem zpráv pomocí služby Azure Functions
V předchozí části jste viděli jednoduchý scénář testování a ladění a ověřili jste tok událostí. 

V této části zjistíte, jak po přijetí události přijímat a zpracovávat zprávy.

### <a name="publish-a-function-from-visual-studio"></a>Publikování funkce z Visual Studia
1. Ve stejném řešení Visual Studio (**SBEventGridIntegration),** které jste otevřeli, vyberte **ReceiveMessagesOnEvent.cs** v projektu **SBEventGridIntegration.** 
2. Zadejte připojovací řetězec service bus v následujícím kódu:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Stáhněte si **profil publikování** funkce:
    1. Vyberte svou aplikaci funkcí. 
    2. Vyberte kartu **Přehled,** pokud ještě není vybraná. 
    3. Na panelu nástrojů vyberte **Získat profil publikování.** 

        ![Získání profilu publikování pro funkci](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Uložte soubor do složky projektu. 
4. V sadě Visual Studio klikněte pravým tlačítkem na **SBEventGridIntegration** a vyberte **Publikovat**. 
5. Na stránce **Publikovat** vyberte **Začít.** 
6. Na stránce **Vyberte cílovou** publikování postupujte takto a vyberte **importovat profil**. 

    ![Visual Studio – tlačítko Importovat profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Vyberte **dříve stažený soubor profilu publikování.** 
8. Na stránce **Publikovat** vyberte **Publikovat.** 

    ![Visual Studio - publikovat](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Potvrďte, že se zobrazí nová funkce Azure **ReceiveMessagesOnEvent**. V případě potřeby stránku aktualizujte. 

    ![Potvrzení vytvoření nové funkce](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Získejte adresu URL nové funkce a poznamenejte si ji. 

### <a name="event-grid-subscription"></a>Předplatné event gridu

1. Odstraňte existující předplatné služby Event Grid:
    1. Na stránce **Obor názvů služby Service Bus** vyberte **události** v levé nabídce. 
    2. Vyberte existující odběr událostí. 
    3. Na stránce **Odběr událostí** vyberte **Odstranit**.
2. Podle pokynů v části [Připojit funkci a obor názvů prostřednictvím](#connect-the-function-and-namespace-via-event-grid) programu Event Grid vytvořte odběr služby Event Grid pomocí nové adresy URL funkce.
3. Postupujte podle pokynů v části [Odeslat zprávy do tématu služby Service Bus,](#send-messages-to-the-service-bus-topic) abyste odeslali zprávy do tématu a sledovali funkci. 

## <a name="receive-messages-by-using-logic-apps"></a>Příjem zpráv pomocí Logic Apps
Připojte aplikaci logiky s Azure Service Bus a Azure Event Grid podle následujících kroků:

1. Vytvořte aplikaci logiky na webu Azure Portal.
    1. Vyberte **+ Vytvořit prostředek**, vyberte **Integrace**a pak vyberte **Aplikace logiky**. 
    2. Na **stránce Logika – vytvořit** zadejte **název** aplikace logiky.
    3. Vyberte **předplatné**Azure . 
    4. Vyberte **Použít existující** pro **skupinu prostředků**a vyberte skupinu prostředků, kterou jste použili pro jiné prostředky (například funkce Azure, obor názvů Service Bus), které jste vytvořili dříve. 
    5. Vyberte **umístění** aplikace logiky. 
    6. Chcete-li vytvořit aplikaci logiky, vyberte **vytvořit.** 
2. Na stránce **Návrhář aplikací logiky** vyberte v části Šablony **možnost Prázdná aplikace** **logiky**. 
3. V návrháři proveďte následující kroky:
    1. Vyhledejte **mřížku událostí**. 
    2. **Vyberte, když dojde k události prostředku (preview) - Azure Event Grid**. 

        ![Návrhář aplikací logiky – vyberte aktivační událost mřížky událostí](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Vyberte **Přihlásit**se , zadejte svoje přihlašovací údaje azure a vyberte **Povolit přístup**. 
5. Na stránce **Když dojde k události prostředku,** proveďte následující kroky:
    1. Vyberte své předplatné Azure. 
    2. V **pole Typ prostředku**vyberte položku **Microsoft.ServiceBus.Namespaces**. 
    3. V **popřípadě Název prostředku**vyberte obor názvů Service Bus. 
    4. Vyberte **Přidat nový parametr**a vyberte Filtr **přípony**. 
    5. Do **pole Filtr přípony**zadejte název druhého temanového předplatného služby Service Bus. 
        ![Návrhář aplikací logiky – konfigurace události](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. V návrháři vyberte **+ Nový krok** a proveďte následující kroky:
    1. Vyhledejte **službu Service Bus**.
    2. V seznamu vyberte **Service Bus.** 
    3. Vyberte možnost **Získat zprávy** v seznamu **Akce.** 
    4. Vyberte **Získat zprávy z tematického předplatného (peek-lock)**. 

        ![Návrhář aplikací logiky – akce získání zpráv](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Zadejte **název připojení**. Příklad: **Získejte zprávy z odběru tématu**a vyberte obor názvů Service Bus. 

        ![Návrhář aplikací logiky – vyberte obor názvů Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Vyberte **rootmanagesharedaccesskey**.

        ![Návrhář aplikací logiky – výběr sdíleného přístupového klíče](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Vyberte **Vytvořit**. 
    8. Vyberte téma a předplatné. 
    
        ![Návrhář aplikací logiky – vyberte téma a předplatné služby Service Bus](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Vyberte **+ Nový krok**a proveďte následující kroky: 
    1. Vyberte **položku Servisní sběrnice**.
    2. Ze seznamu akcí **vyberte Dokončit zprávu v odběru tématu.** 
    3. Vyberte **téma**služby Service Bus .
    4. Vyberte druhé **předplatné** tématu.
    5. Pro **token zámku zprávy**vyberte **zamknout token** z **dynamického obsahu**. 

        ![Návrhář aplikací logiky – vyberte téma a předplatné služby Service Bus](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Vyberte **Uložit** na panelu nástrojů v Návrháři aplikací logiky, abyste uložili aplikaci logiky. 
9. Postupujte podle pokynů v části [Odeslat zprávy do tématu služby Service Bus](#send-messages-to-the-service-bus-topic) a odesílat zprávy tématu. 
10. Přepněte na stránku **Přehled** aplikace logiky. Uvidíte, že aplikace logiky běží v **historii spuštění** pro odeslané zprávy.

    ![Návrhář aplikací logiky – spustí se aplikace logiky](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
