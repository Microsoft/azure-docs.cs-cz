---
title: Příklady integrace služby Azure Service Bus do služby Event Grid | Microsoft Docs
description: Příklady integrace zasílání zpráv služby Service Bus a služby Event Grid
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
ms.openlocfilehash: 3819a274696762861fbe76a9684b8495f1724f6a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Příklady integrace služby Azure Service Bus do služby Azure Event Grid

V tomto dokumentu zjistíte, jak nastavit funkce Azure a aplikaci logiky, které přijímají zprávy na základě přijetí události ze služby Event Grid. Tento příklad předpokládá, že máte téma služby Service Bus se dvěma odběry a že odběr služby Event Grid je vytvořený tak, aby odesílal události pouze do jednoho odběru služby Service Bus. Pak do tématu služby Service Bus odešlete zprávy a ověříte vygenerování události pro tento odběr služby Service Bus. Funkce nebo aplikace logiky pak přijme zprávy z tohoto odběru služby Service Bus a dokončí je.

* Nejprve se ujistěte, že splňujete všechny [Požadavky](#prerequisites).
* Vytvoříte [jednoduchou testovací funkci Azure](#test-function-setup) pro účely ladění a zobrazení počátečního toku událostí ze služby Event Grid.  **Tento krok byste měli provést bez ohledu na to, jestli provedete krok 3 nebo 4.**
* Vytvoříte [funkci Azure pro příjem a zpracování zpráv služby Service Bus](#receive-messages-using-azure-function) na základě událostí služby Event Grid.
* Využijete službu [Logic Apps](#receive-messages-using-azure-logic-app).

## <a name="prerequisites"></a>Požadavky

### <a name="service-bus-namespace"></a>Service Bus Namespace

Vytvořte obor názvů služby Service Bus úrovně Premium. Vytvořte téma služby Service Bus se dvěma odběry.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Kód pro odeslání zprávy do tématu služby Service Bus

Do tématu služby Service můžete odeslat zprávu jakýmkoli způsobem. Případně můžete použít níže uvedenou ukázku. Vzorový kód předpokládá, že používáte sadu Visual Studio 2017.

Naklonujte [úložiště GitHub](https://github.com/Azure/azure-service-bus/).

Přejděte do následující složky:

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration a otevřete soubor SBEventGridIntegration.sln.

Pak přejděte do projektu MessageSender a otevřete soubor Program.cs.

![8][]

Zadejte název vašeho tématu a připojovací řetězec a spusťte konzolovou aplikaci:

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>Nastavení testovací funkce

Než začnete procházet celý scénář, měli byste mít alespoň malou testovací funkci, kterou můžete použít k ladění a zobrazení toku událostí.

Na portálu vytvořte novou aplikaci funkcí Azure. Základní informace o službě Azure Functions najdete na tomto [odkazu](https://docs.microsoft.com/en-us/azure/azure-functions/).

V nově vytvořené funkci kliknutím na malý symbol plus přidejte funkci triggeru HTTP:

![2][]

![3][]

Pak do funkce zkopírujte následující kód:

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

Klikněte na Uložit a spustit.

## <a name="connect-function-and-namespace-via-event-grid"></a>Propojení funkce a oboru názvů přes službu Event Grid

Dalším krokem je provázání funkce s oborem názvů služby Service Bus. V tomto příkladu použijete Azure Portal. Na stránce věnované [konceptům](service-bus-to-event-grid-integration-concept.md) najdete návod, jak to samé provést pomocí PowerShellu nebo Azure CLI.

Pokud chcete vytvořit nový odběr služby Azure Event Grid, na webu Azure Portal přejděte do vašeho oboru názvů a vyberte okno Event Grid. Klikněte na + Odběr události.

Následující snímek obrazovky ukazuje obor názvů, který již obsahuje několik odběrů služby Event Grid.

![20][]

Následující snímek obrazovky ukazuje vytvoření odběru funkce Azure nebo webhooku bez konkrétního filtrování. Nezapomeňte jako Filtr přípon přidat odpovídající filtr pro váš odběr služby Service Bus:

![21][]

Odešlete do svého tématu služby Service Bus zprávu, jak je uvedeno v požadavcích, a ověřte tok událostí prostřednictvím funkce monitorování ve funkci Azure.

![9][]

### <a name="receive-messages-using-azure-function"></a>Příjem zpráv pomocí funkce Azure

V předchozí části jste se seznámili s jednoduchým scénářem testování a ladění a ověřili jste tok událostí. V této části dokumentace zjistíte, jak po přijetí události přijmout a zpracovat zprávy.

Důvodem přidání funkce Azure následujícím způsobem je, že funkce služby Service Bus v rámci samotné služby Azure Functions ještě nativně nepodporují novou integraci služby Event Grid.

Ve stejném řešení sady Visual Studio, které jste otevřeli v rámci požadavků, vyberte soubor ReceiveMessagesOnEvent.cs. Zadejte do kódu svůj připojovací řetězec:

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Pak přejděte na web Azure Portal a stáhněte profil publikování pro funkci Azure, kterou jste vytvořili dříve v kroku [2. Nastavení testovací funkce](#2-test-function-setup).

![11][]

Pak v sadě Visual Studio klikněte pravým tlačítkem na SBEventGridIntegration a vyberte Publikovat. Použijte profil publikování, který jste stáhli dříve, vyberte Importovat profil a klikněte na Publikovat.

![12][]

Po publikování nové funkce Azure vytvořte nový odběr služby Azure Event Grid odkazující na tuto novou funkci Azure. Nezapomeňte použít správný filtr Končí na, což by měl být název vašeho odběru služby Service Bus.

Pak do tématu služby Azure Service Bus, které jste vytvořili dříve, odešlete zprávu a v protokolu funkce Azure na portálu zkontroluje tok událostí a příjem zpráv.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Příjem zpráv pomocí aplikace logiky Azure

Následující pokyny ukazují, jak se službami Azure Service Bus a Azure Event Grid propojit aplikaci logiky Azure:

Nejprve na webu Azure Portal vytvořte novou aplikaci logiky a jako spouštěcí akci vyberte službu Event Grid.

![13][]

Počáteční zobrazení v Návrháři pro Logic Apps by mělo vypadat jako na následujícím snímku obrazovky, musíte však nahradit Název prostředku názvem vlastního oboru názvů. Také nezapomeňte rozbalit pokročilé možnosti a přidat pro váš odběr filtr přípon:

![14][]

Pak přidejte akci Příjem služby Service Bus pro příjem z odběru tématu. Konečná akce by měla vypadat jako na následujícím snímku obrazovky.

![15][]

Pak přidejte událost dokončení, která by měla vypadat takto.

![16][]

Uložte aplikaci logiky a odešlete do svého tématu služby Service Bus zprávu, jak je uvedeno v požadavcích. Pak sledujte spuštění aplikace logiky. Pokud kliknete na Přehled a pak na Historie spuštění, zobrazí se další data o spuštění.

![17][]

![18][]

## <a name="next-steps"></a>Další kroky

* Další informace o službě [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Další informace o službě [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Další informace o službě [Azure Log Apps](https://docs.microsoft.com/azure/logic-apps/).
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
