---
title: Příklady integrace služby Azure Service Bus do služby Event Grid | Microsoft Docs
description: Tento článek obsahuje příklady integrace zasílání zpráv služby Service Bus a služby Event Grid.
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 5d0ab8cf9e87fc13b78b00dbe77ec6f9fb38c4b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189387"
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Příklady integrace služby Azure Service Bus do služby Azure Event Grid

V tomto článku zjistíte, jak nastavit funkci Azure a aplikaci logiky, které přijímají zprávy na základě přijetí události ze služby Azure Event Grid. Provedete následující:
 
* Vytvoříte jednoduchou [testovací funkci Azure](#test-function-setup) pro účely ladění a zobrazení počátečního toku událostí ze služby Event Grid. Tento krok proveďte bez ohledu na to, jestli provedete i ostatní kroky.
* Vytvoříte [funkci Azure pro příjem a zpracování zpráv služby Azure Service Bus](#receive-messages-using-azure-function) na základě událostí služby Event Grid.
* Využijete [funkci Logic Apps služby Azure App Service](#receive-messages-using-azure-logic-app).

Příklad, který vytvoříte, předpokládá, že má téma služby Service dva odběry. Příklad také předpokládá, že odběr služby Event Grid byl vytvořený k odesílání událostí pouze pro jeden odběr služby Service Bus. 

V příkladu odešlete zprávy do tématu služby Service Bus a pak ověříte vygenerování události pro tento odběr služby Service Bus. Funkce nebo aplikace logiky přijme zprávy z odběru služby Service Bus a pak je dokončí.

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že jste dokončili kroky v následujících dvou částech.

### <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus

Vytvořte obor názvů služby Service Bus úrovně Premium a téma služby Service Bus se dvěma odběry.

### <a name="send-a-message-to-the-service-bus-topic"></a>Odeslání zprávy do tématu služby Service Bus

Do tématu služby Service Bus můžete odeslat zprávu libovolnou metodou. Vzorový kód na konci tohoto postupu předpokládá, že používáte sadu Visual Studio 2017.

1. Naklonujte [úložiště GitHub azure-service-bus](https://github.com/Azure/azure-service-bus/).

2. V sadě Visual Studio přejděte do složky *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* a otevřete soubor *SBEventGridIntegration.sln*.

3. Přejděte do projektu **MessageSender** a vyberte soubor **Program.cs**.

   ![8][]

4. Zadejte název vašeho tématu a připojovací řetězec a pak spusťte následující kód konzolové aplikace:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Nastavení testovací funkce

Než začnete procházet celý scénář, nastavte alespoň malou testovací funkci, kterou můžete použít k ladění a sledování toku událostí.

1. Na webu Azure Portal vytvořte novou aplikaci Azure Functions. Základní informace o službě Azure Functions najdete v [dokumentaci ke službě Azure Functions](https://docs.microsoft.com/azure/azure-functions/).

2. V nově vytvořené funkci výběrem symbolu plus (+) přidejte funkci triggeru HTTP:

    ![2][]
    
    Otevře se okno **Get started quickly with a premade function** (Rychlý začátek pomocí předpřipravené funkce).

    ![3][]

3. Vyberte tlačítko **Webhook + API**, vyberte jazyk **CSharp** (jazyk C#) a pak vyberte **Create this function** (Vytvořit tuto funkci).
 
4. Do funkce vložte následující kód:

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

5. Vyberte **Uložit a spustit**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Propojení funkce a oboru názvů přes službu Event Grid

V této části provážete funkci s oborem názvů služby Service Bus. V tomto příkladu použijete Azure Portal. Návod k provedení tohoto postupu pomocí PowerShellu nebo Azure CLI najdete v tématu [Přehled integrace služby Azure Service Bus do služby Azure Event Grid](service-bus-to-event-grid-integration-concept.md).

Odběr služby Azure Event Grid vytvoříte následovně:
1. Na webu Azure Portal přejděte do svého oboru názvů a pak v levém podokně vyberte **Event Grid**.  
    V pravém podokně se otevře okno vašeho oboru názvů, ve kterém se zobrazí dva odběry služby Event Grid.

    ![20][]

2. Vyberte **Odběr události**.  
    Otevře se okno **Odběr události**. Následující obrázek ukazuje formulář pro přihlášení k odběru funkce Azure nebo webhooku bez použití filtrů.

    ![21][]

3. Vyplňte formulář podle obrázku a do pole **Filtr přípon** nezapomeňte zadat odpovídající filtr.

4. Vyberte **Vytvořit**.

5. Odešlete do svého tématu služby Service Bus zprávu, jak je uvedeno v části Požadavky, a pak ověřte tok událostí prostřednictvím funkce monitorování služby Azure Functions.

Dalším krokem je provázání funkce s oborem názvů služby Service Bus. V tomto příkladu použijete Azure Portal. Návod k provedení tohoto kroku pomocí PowerShellu nebo Azure CLI najdete v tématu [Přehled integrace služby Azure Service Bus do služby Azure Event Grid](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Příjem zpráv pomocí služby Azure Functions

V předchozí části jste viděli jednoduchý scénář testování a ladění a ověřili jste tok událostí. 

V této části zjistíte, jak po přijetí události přijímat a zpracovávat zprávy.

Přidáte funkci Azure, jak je znázorněno v následujícím příkladu, protože funkce služby Service Bus v rámci služby Azure Functions ještě nativně nepodporují novou integraci služby Event Grid.

1. Ve stejném řešení sady Visual Studio, které jste otevřeli v rámci požadavků, vyberte soubor **ReceiveMessagesOnEvent.cs**. 

    ![10][]

2. Do následujícího kódu zadejte svůj připojovací řetězec:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

3. Na webu Azure Portal stáhněte profil publikování pro funkci Azure, kterou jste vytvořili v části Nastavení testovací funkce.

    ![11][]

4. V sadě Visual Studio klikněte pravým tlačítkem na **SBEventGridIntegration** a vyberte **Publikovat**. 

5. V podokně **Publikovat** pro profil publikování, který jste předtím stáhli, vyberte **Importovat profil** a pak vyberte **Publikovat**.

    ![12][]

6. Po publikování nové funkce Azure vytvořte nový odběr služby Azure Event Grid, který odkazuje na tuto novou funkci Azure.  
    V poli **Končí na** nezapomeňte použít správný filtr, což by měl být název vašeho odběru služby Service Bus.

7. Do tématu služby Azure Service Bus, které jste vytvořili dříve, odešlete zprávu a pak v protokolu služby Azure Functions na webu Azure Portal monitorujte tok událostí a příjem zpráv.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Příjem zpráv pomocí Logic Apps

Následujícím způsobem propojte se službami Azure Service Bus a Azure Event Grid aplikaci logiky:

1. Na webu Azure Portal vytvořte novou aplikaci logiky a jako spouštěcí akci vyberte **Event Grid**.

    ![13][]

    Otevře se okno Návrháře pro Logic Apps.

    ![14][]

2. Přidejte své informace následujícím způsobem:

    a. Do pole **Název prostředku** zadejte název svého oboru názvů. 

    b. V části **Pokročilé možnosti** do pole **Filtr přípon** zadejte filtr pro váš odběr.

3. Přidejte akci Příjem služby Service Bus pro příjem zpráv z odběru tématu.  
    Poslední akce je znázorněná na následujícím obrázku:

    ![15][]

4. Přidejte událost dokončení, jak je znázorněno na následujícím obrázku:

    ![16][]

5. Uložte aplikaci logiky a odešlete do svého tématu služby Service Bus zprávu, jak je uvedeno v části Požadavky.  
    Sledujte spuštění aplikace logiky. Pokud chcete zobrazit více dat o spuštění, vyberte **Přehled** a data se zobrazí v části **Historie spuštění**.

    ![17][]

    ![18][]

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
