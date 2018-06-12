---
title: Scénář – aplikace logiky aktivační událost s funkcemi Azure a Azure Service Bus | Microsoft Docs
description: Vytvořit funkci pro aktivaci aplikace logiky pomocí funkce Azure a Azure Service Bus
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 89fcd88643bd793935e7476ef32641ffa5ff4713
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299789"
---
# <a name="scenario-trigger-a-logic-app-with-azure-functions-and-azure-service-bus"></a>Scénář: Aktivovat aplikaci logiky s funkcemi Azure a Azure Service Bus

Azure Functions můžete vytvořit aktivační událost pro aplikace logiky, když potřebujete nasadit dlouho běžící naslouchacího procesu nebo úlohy. Můžete například vytvořit funkci, která naslouchá na frontu a okamžitě fire aplikace logiky jako aktivační událost nabízené.

## <a name="build-the-logic-app"></a>Vytvoření aplikace logiky
V tomto příkladu máte funkci pro každou aplikaci logiky, která musí být aktivována systémem. Nejprve vytvořte aplikaci logiky, který má aktivační procedury pro požadavek HTTP. Vždy, když je přijatá zpráva fronty, zavolá funkci tohoto koncového bodu.  

1. Vytvoření aplikace logiky.
2. Vyberte **ruční – když je obdržena požadavek HTTP** aktivační události.
   Volitelně můžete zadat schématu JSON pro použití s zprávy ve frontě pomocí některého nástroje, například [jsonschema.net](http://jsonschema.net). Vložte schéma aktivační událost. Schémata pomoct pochopit obrazec vlastnosti data a toku snadněji prostřednictvím pracovního postupu návrháře.
2. Přidáte další kroky, které mají být provedeny po přijetí zprávy fronty. Například odešlete e-mailu prostřednictvím Office 365.  
3. Uložte aplikaci logiky ke generování adresy URL zpětného volání pro aktivační událost pro tuto aplikaci logiky. Adresa URL se zobrazí na kartě aktivační události.

![Zpětné volání adresy URL se zobrazí na kartě aktivační události][1]

## <a name="build-the-function"></a>Vytvoření funkce
Dále musíte vytvořit funkci, která funguje jako aktivační události a naslouchá do fronty.

1. V [portálu Azure Functions](https://functions.azure.com/signin), vyberte **novou funkci**a pak vyberte **ServiceBusQueueTrigger - C#** šablony.
   
    ![Azure Functions na portálu][2]
2. Konfigurace připojení do fronty Service Bus, která používá Azure Service Bus SDK `OnMessageReceive()` naslouchací proces.
3. Write – základní funkce k volání koncový bod aplikace logiky (vytvořenou dříve) pomocí zprávy ve frontě aktivační události. Tady je příklad úplné funkce. V příkladu se používá `application/json` obsahu typ zprávy, ale můžete změnit podle potřeby tohoto typu.
   
   ```
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

Chcete-li otestovat, přidejte zprávu fronty pomocí některého nástroje, například [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). V tématu aplikace logiky fire ihned po funkce obdrží zprávu.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png
