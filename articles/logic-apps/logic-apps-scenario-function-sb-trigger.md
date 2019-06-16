---
title: Volání nebo aktivační události aplikací logiky s využitím Azure Functions a Azure Service Bus
description: Vytvoření funkce Azure, které volají nebo spouštět aplikace logiky pomocí služby Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494976"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Volání nebo vyvolání aplikace logiky s využitím Azure Functions a Azure Service Bus

Můžete použít [Azure Functions](../azure-functions/functions-overview.md) aktivovat aplikaci logiky, když je potřeba nasadit dlouhotrvající naslouchací proces nebo úkolu. Například můžete vytvořit funkci Azure, které naslouchá v [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) zařadit do fronty a okamžitě aktivuje aplikace logiky jako trigger nabízených oznámení.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Obor názvů služby Azure Service Bus. Pokud nemáte k dispozici obor názvů, [nejdřív vytvořit obor názvů](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Aplikaci Azure function app, což je kontejner pro službu Azure functions. Pokud nemáte aplikaci function app, [nejprve vytvořit aplikaci function app](../azure-functions/functions-create-first-azure-function.md)a ujistěte se, že vyberete jako zásobníku modulu runtime .NET.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

V tomto scénáři máte funkci s každou aplikaci logiky, kterou chcete aktivovat. Nejprve vytvořte aplikaci logiky, která začíná textem triggeru požadavku HTTP. Funkce volání tohoto koncového bodu při doručení zprávy do fronty.  

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a vytvoření prázdné aplikace logiky.

   Pokud se službou logic Apps teprve začínáte, přečtěte si [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte "požadavek http". Ze seznamu triggerů vyberte tento trigger: **Při přijetí požadavku HTTP**

   ![Výběr triggeru](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Pomocí triggeru požadavku můžete volitelně zadat schéma JSON pro použití s zprávy fronty. Schémat JSON pomůžou seznámit se strukturou vstupních dat a usnadnění výstupy pro použití v pracovním postupu návrhář aplikace logiky.

1. Schéma, zadejte schéma v **požádat o schéma JSON hlavní části** pole, například:

   ![Zadejte schéma JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Pokud nemáte k dispozici schéma, ale budete mít ukázkovou datovou část ve formátu JSON, můžete vygenerovat schéma z této datové části.

   1. V triggeru požadavku, zvolte **k vygenerování schématu použít ukázkovou datovou část**.

   1. V části **zadejte nebo vložte ukázkovou datovou část JSON**zadejte vaši ukázkovou datovou část a klikněte na tlačítko **provádí**.

      ![Zadejte ukázkovou datovou část](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Tuto ukázkovou datovou část generuje toto schéma, které se zobrazí v aktivační události:

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

   Například můžete odesílat e-mailu s konektorem Office 365 Outlook.

1. Uložte aplikaci logiky, která generuje adresu URL zpětného volání pro trigger v této aplikaci logiky. Později použijte tuto adresu URL zpětného volání v kódu pro aktivační událost fronty Azure Service Bus.

   Adresa URL se zobrazí v zpětného volání **adresa URL operace HTTP POST** vlastnost.

   ![Adresa URL generované zpětného volání pro trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Vytvoření funkce Azure functions

Dál vytvořte funkci, která funguje jako aktivační událost a přijímá zprávy do fronty.

1. Na webu Azure Portal otevřete a rozbalte aplikaci function app, není otevřen. 

1. V části název vaší aplikace funkcí, rozbalte položku **funkce**. Na **funkce** podokně zvolte **novou funkci**.

   ![Rozbalte "Funkce" a vyberte možnost "Nová funkce"](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Vyberte tuto šablonu závislosti na tom, jestli jste vytvořili novou aplikaci funkcí, pokud jste vybrali jako zásobníku modulu runtime .NET, nebo používáte existující aplikaci function app.

   * Pro nové aplikace function App vyberte tuto šablonu: **Aktivační událost fronty Service Bus**

     ![Vyberte šablonu pro novou aplikaci function app](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Existující aplikaci function app vyberte tuto šablonu: **Aktivační událost fronty Service Bus –C#**

     ![Vyberte šablonu pro existující aplikaci function app](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Na **aktivační událost fronty Azure Service Bus** podokně, zadejte název aktivační události a nastavit **připojení k Service Bus** do fronty, který používá sadu Azure Service Bus SDK `OnMessageReceive()` naslouchací proces a zvolte **Vytvořit**.

1. Zápis základní funkce k volání koncového bodu aplikace logiky dříve vytvořeného pomocí fronty zpráv jako trigger. V tomto příkladu `application/json` typ obsahu zprávy, ale můžete změnit podle potřeby tohoto typu. Pokud je to možné opakovaně používat instanci klientům protokolu HTTP. Další informace najdete v tématu [spravovat připojení ve službě Azure Functions](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. Chcete-li funkci otestovat, přidat zprávy fronty pomocí nástroje, jako [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer).

   Triggery aplikace logiky hned po funkci obdrží zprávu.

## <a name="next-steps"></a>Další postup

[Volání, triggery nebo vnořené pracovní postupy s použitím koncových bodů HTTP](../logic-apps/logic-apps-http-endpoint.md)