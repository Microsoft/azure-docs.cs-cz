---
title: Volání aplikací logiky s využitím Azure Functions
description: Vytvářejte funkce Azure, které volají nebo aktivují aplikace logiky nasloucháním službě Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428710"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Volání nebo aktivace logických aplikací pomocí Azure Functions a Azure Service Bus

[Funkce Azure](../azure-functions/functions-overview.md) můžete použít k aktivaci aplikace logiky, když potřebujete nasadit dlouhotrvající naslouchací proces nebo úlohu. Můžete například vytvořit funkci Azure, která naslouchá ve frontě [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) a okamžitě spustí aplikaci logiky jako aktivační událost push.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Obor názvů Azure Service Bus. Pokud nemáte obor názvů, [vytvořte nejprve obor názvů](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Aplikace funkce Azure, což je kontejner pro funkce Azure. Pokud nemáte aplikaci funkce, [nejprve vytvořte aplikaci funkcí](../azure-functions/functions-create-first-azure-function.md)a ujistěte se, že vyberete .NET jako zásobník za běhu.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

Pro tento scénář máte funkci spuštěnou každou aplikaci logiky, kterou chcete aktivovat. Nejprve vytvořte aplikaci logiky, která začíná aktivační událostí požadavku HTTP. Funkce volá tento koncový bod při každém přijetí zprávy fronty.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a vytvořte prázdnou aplikaci logiky.

   Pokud s aplikacemi logiky teprve začínáte, přečtěte si [úvodní příručku: Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte `http request`. Ze seznamu aktivačních událostí vyberte aktivační **událost Při přijetí požadavku HTTP.**

   ![Vybrat aktivační událost](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Pomocí aktivační události Požadavek můžete volitelně zadat schéma JSON, které se použije se zprávou fronty. JSON schémata pomoci Návrhář epoje pochopit strukturu pro vstupní data a usnadnit výstupy pro použití v pracovním postupu.

1. Chcete-li zadat schéma, zadejte schéma do pole **Schéma JSON v** požadavku, například:

   ![Určení schématu JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Pokud nemáte schéma, ale máte ukázkovou datovou část ve formátu JSON, můžete vygenerovat schéma z této datové části.

   1. V aktivační události Požadavek vyberte **použít ukázkovou datovou část ke generování schématu**.

   1. V části **Zadejte nebo vložte ukázkovou datovou část JSON**, zadejte ukázkovou datovou část a pak vyberte **Hotovo**.

      ![Zadat ukázkovou datovou část](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Tato ukázková datová část generuje toto schéma, které se zobrazí v aktivační události:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Přidejte další akce, které chcete spustit po obdržení zprávy fronty.

   Můžete například odeslat e-mail pomocí konektoru Office 365 Outlook.

1. Uložte aplikaci logiky, která generuje adresu URL zpětného volání pro aktivační událost v této aplikaci logiky. Později použijete tuto adresu URL zpětného volání v kódu pro aktivační událost fronty služby Azure Service Bus.

   Adresa URL zpětného volání se zobrazí ve vlastnosti **adresy URL HTTP POST.**

   ![Vygenerovaná adresa URL zpětného volání pro aktivační událost](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Vytvoření funkce Azure

Dále vytvořte funkci, která funguje jako aktivační událost a naslouchá frontě.

1. Na webu Azure Portal otevřete a rozbalte aplikaci funkcí, pokud už není otevřená. 

1. Pod názvem aplikace funkce rozbalte **funkce Funkce**. V podokně **Funkce** vyberte **Nová funkce**.

   ![Rozbalte "Funkce" a vyberte "Nová funkce"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Tuto šablonu vyberte na základě toho, zda jste vytvořili novou aplikaci funkcí, ve které jste jako zásobník za běhu vybrali rozhraní .NET nebo zda používáte existující aplikaci funkcí.

   * U nových aplikací funkcí vyberte tuto šablonu: **Aktivační událost fronty služby Service Bus**

     ![Výběr šablony pro novou aplikaci funkcí](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Pro existující aplikaci funkcí vyberte tuto šablonu: **Aktivační událost fronty služby Service Bus - C#**

     ![Výběr šablony pro existující aplikaci funkcí](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. V podokně **aktivační chodu fronty služby Azure Service Bus** zadejte název aktivační události a nastavte připojení **služby Service Bus** pro frontu, která používá naslouchací proces Azure Service Bus SDK, `OnMessageReceive()` a vyberte **Vytvořit**.

1. Napište základní funkci pro volání dříve vytvořeného koncového bodu aplikace logiky pomocí zprávy fronty jako aktivační události. Než napíšete svou funkci, přečtěte si tyto aspekty:

   * Tento příklad `application/json` používá typ obsahu zprávy, ale tento typ můžete podle potřeby změnit.
   
   * Z důvodu možnésouběžně spuštěné funkce, velké svazky nebo velká zatížení, vyhnout `using` se vytváření instanci [třídy HTTPClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) s příkazem a přímo vytvářet instance HTTPClient na požadavek. Další informace naleznete [v tématu HttpClientFactory k implementaci odolných požadavků HTTP](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Pokud je to možné, znovu použijte instanci klientů HTTP. Další informace najdete [v tématu Správa připojení v Azure Functions](../azure-functions/manage-connections.md).

   Tento příklad používá [ `Task.Run` metodu](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) v [asynchronním](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) režimu. Další informace naleznete [v tématu Asynchronní programování s async a await](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Chcete-li tuto funkci otestovat, přidejte zprávu fronty pomocí nástroje, jako je například [Průzkumník sběrnice](https://github.com/paolosalvatori/ServiceBusExplorer).

   Aplikace logiky se aktivuje ihned po funkci obdrží zprávu.

## <a name="next-steps"></a>Další kroky

* [Volání, aktivační událost nebo vnoření pracovních postupů pomocí koncových bodů HTTP](../logic-apps/logic-apps-http-endpoint.md)
