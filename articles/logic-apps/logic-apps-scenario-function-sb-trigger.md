---
title: Volání aplikací logiky s využitím Azure Functions
description: Vytvoření Azure Functions, které volají nebo spouštějí Logic Apps naslouchání Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: fcf7f1a27633c978c10f541d0a341225fbcb126d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89013771"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Volání nebo spuštění Logic Apps pomocí Azure Functions a Azure Service Bus

Můžete použít [Azure Functions](../azure-functions/functions-overview.md) pro aktivaci aplikace logiky, pokud potřebujete nasadit dlouhodobě spuštěný naslouchací proces nebo úlohu. Můžete například vytvořit funkci Azure, která naslouchá ve frontě [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) a hned spustí aplikaci logiky jako Trigger push.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Azure Service Bus obor názvů. Pokud obor názvů nemáte, [vytvořte nejprve obor názvů](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Aplikace Azure Functions, což je kontejner pro Azure Functions. Pokud nemáte aplikaci Function App, [nejprve vytvořte aplikaci Function App](../azure-functions/functions-create-first-azure-function.md)a ujistěte se, že jste vybrali .NET jako zásobník modulu runtime.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

V tomto scénáři máte funkci spouštějící jednotlivé aplikace logiky, které chcete aktivovat. Nejdřív vytvořte aplikaci logiky, která začíná triggerem požadavku HTTP. Tato funkce volá koncový bod při každém přijetí zprávy fronty.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a vytvořte prázdnou aplikaci logiky.

   Pokud s Logic Apps začínáte, Projděte si [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte `http request`. V seznamu triggery vyberte aktivační událost **při přijetí požadavku HTTP** .

   ![Vybrat aktivační událost](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Pomocí triggeru žádosti můžete volitelně zadat schéma JSON, které se bude používat ve zprávě fronty. Schémata JSON pomůžou návrháři aplikace logiky pochopit strukturu vstupních dat a usnadnit tak použití ve vašem pracovním postupu.

1. Chcete-li zadat schéma, zadejte schéma do pole **schéma JSON textu žádosti** , například:

   ![Zadat schéma JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Pokud schéma nemáte, ale máte ukázkovou datovou část ve formátu JSON, můžete z této datové části vygenerovat schéma.

   1. V triggeru žádosti vyberte **použít ukázkovou datovou část k vygenerování schématu**.

   1. V části **Zadejte nebo vložte ukázkovou datovou část JSON**zadejte datovou část ukázky a potom vyberte **Hotovo**.

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

1. Přidejte všechny další akce, které chcete spustit po přijetí zprávy fronty.

   Můžete třeba poslat e-mail s konektorem Office 365 Outlook.

1. Uložte aplikaci logiky, která generuje adresu URL zpětného volání pro Trigger v této aplikaci logiky. Později použijete tuto adresu URL zpětného volání v kódu pro aktivační událost fronty Azure Service Bus.

   Adresa URL zpětného volání se zobrazí ve vlastnosti **Adresa URL příspěvku http** .

   ![Vygenerovaná adresa URL zpětného volání pro aktivační událost](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Vytvoření funkce Azure Functions

Dále vytvořte funkci, která funguje jako Trigger a naslouchat do fronty.

1. V Azure Portal otevřete a rozbalte aplikaci Function App, pokud ještě není otevřená. 

1. V části název aplikace Function App rozbalte **funkce**. V podokně **funkce** vyberte možnost **Nová funkce**.

   ![Rozbalte položku funkce a vyberte možnost Nová funkce.](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Tuto šablonu vyberte na základě toho, jestli jste vytvořili novou aplikaci Function App, kde jste vybrali .NET jako zásobník modulu runtime, nebo používáte existující aplikaci Function App.

   * Pro nové aplikace Function App vyberte tuto šablonu: **aktivační událost Service Bus Queue** .

     ![Vybrat šablonu pro novou aplikaci Function App](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * V případě existující aplikace Function App vyberte tuto šablonu: **aktivační událost Service Bus Queue – C#**

     ![Vybrat šablonu pro existující aplikaci Function App](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. V podokně **aktivační událost fronty Azure Service Bus** zadejte název triggeru a nastavte **připojení Service Bus** pro frontu, která používá naslouchací proces sady SDK Azure Service Bus `OnMessageReceive()` a vyberte **vytvořit**.

1. Napište základní funkci pro volání dříve vytvořeného koncového bodu aplikace logiky pomocí zprávy Queue jako triggeru. Před psaním funkce si Projděte tyto požadavky:

   * V tomto příkladu se používá `application/json` typ obsahu zprávy, ale tento typ můžete podle potřeby změnit.
   
   * Z důvodu možného souběžného spouštění funkcí, velkých objemů nebo těžkých zátěží se vyhnete vytvoření instance [třídy HTTPClient](/dotnet/api/system.net.http.httpclient) pomocí `using` příkazu a přímému vytváření instancí HTTPClient na požadavek. Další informace najdete v tématu [použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Pokud je to možné, znovu použijte instanci klientů protokolu HTTP. Další informace najdete v tématu [Správa připojení v Azure Functions](../azure-functions/manage-connections.md).

   V tomto příkladu se používá [ `Task.Run` Metoda](/dotnet/api/system.threading.tasks.task.run) v [asynchronním](/dotnet/csharp/language-reference/keywords/async) režimu. Další informace naleznete v tématu [asynchronní programování s Async a await](/dotnet/csharp/programming-guide/concepts/async/).

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

1. Pokud chcete funkci otestovat, přidejte zprávu fronty pomocí nástroje, jako je [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   Aplikace logiky se spustí hned poté, co funkce obdrží zprávu.

## <a name="next-steps"></a>Další kroky

* [Volání, Trigger nebo vnoření pracovních postupů pomocí koncových bodů HTTP](../logic-apps/logic-apps-http-endpoint.md)
