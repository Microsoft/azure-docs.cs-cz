---
title: 'Scénář: aktivační událost aplikací logiky s využitím Azure Functions a Azure Service Bus | Dokumentace Microsoftu'
description: Vytvoření funkce, které aktivují logic apps s využitím Azure Functions a Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 69a4e4c59038599a7375466c46878bdd017582fa
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231606"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Scénář: Aktivace aplikací logiky s využitím Azure Functions a Azure Service Bus

Azure Functions můžete použít k vytvoření aktivační události pro aplikaci logiky, když budete chtít nasadit dlouhotrvající naslouchací proces nebo úkolu. Můžete například vytvořit funkci, která naslouchá frontě a okamžitě aktivuje aplikace logiky jako trigger nabízených oznámení.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Než budete moct vytvořit funkci Azure, [vytvořit aplikaci function app](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

V tomto příkladu máte funkci spuštění pro každou aplikaci logiky, kterou je potřeba aktivovat. Nejprve vytvořte aplikaci logiky, který má aktivační událost požadavek HTTP. Funkce volání tohoto koncového bodu při doručení zprávy do fronty.  

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a vytvoření prázdné aplikace logiky. 

   Pokud se službou logic Apps teprve začínáte, přečtěte si [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte "požadavek http". V seznamu triggerů vyberte tento trigger: **přijetí požadavku HTTP je při**

   ![Vybrat trigger](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. Pro **žádosti** aktivační událost, můžete volitelně zadat schéma JSON pro použití s zprávy fronty. Schémat JSON pomůžou seznámit se strukturou vstupní data a provede výstupy umožňující vybrat v celém pracovním postupu návrhář aplikace logiky. 

   Schéma, zadejte schéma v **požádat o schéma JSON hlavní části** pole, například: 

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

1. Přidejte všechny další akce, které byste chtěli po přijetí zprávy fronty. 

   Například můžete odesílat e-mailu s konektorem Office 365 Outlook.

1. Uložte aplikaci logiky, která generuje adresu URL zpětného volání pro trigger v této aplikaci logiky. Tato adresa URL se zobrazí v **adresa URL operace HTTP POST** vlastnost.

   ![Adresa URL generované zpětného volání pro trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Vytvoření funkce Azure functions

Dál vytvořte funkci, která funguje jako aktivační událost a přijímá zprávy do fronty. 

1. Na webu Azure Portal otevřete a rozbalte aplikaci function app, není otevřen. 

1. V části název vaší aplikace funkcí, rozbalte položku **funkce**. Na **funkce** podokně zvolte **novou funkci**. Vyberte tuto šablonu: **aktivační událost fronty Service Bus – C#**
   
   ![Vyberte portál Azure Functions](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Zadejte název aktivační události a potom nakonfigurujte připojení do fronty služby Service Bus, která používá sadu Azure Service Bus SDK `OnMessageReceive()` naslouchacího procesu.

1. Napište základní funkce k volání koncového bodu aplikace logiky dříve vytvořeného pomocí fronty zpráv jako aktivační událost, například: 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

   V tomto příkladu `application/json` typ obsahu zprávy, ale můžete změnit podle potřeby tohoto typu.

1. Chcete-li funkci otestovat, přidat zprávy fronty pomocí nástroje, jako [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer). 

   Triggery aplikace logiky hned po funkci obdrží zprávu.

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

