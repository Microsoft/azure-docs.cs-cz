---
title: Přehled integrace služby Azure Service Bus do služby Event Grid | Microsoft Docs
description: Tento článek poskytuje popis způsobu, jakým se Azure Service Bus zasílání zpráv integruje s Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369658"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Přehled integrace služby Azure Service Bus do služby Event Grid
V případě, že fronta nebo odběr obsahuje zprávy a nejsou k dispozici žádní příjemci, teď může Service Bus vysílat události do služby Event Grid. Pro obory názvů služby Service Bus můžete vytvářet odběry služby Event Grid, naslouchat těmto událostem a pak na ně reagovat spuštěním příjemce. Díky této funkci můžete službu Service Bus využívat v reaktivních programovacích modelech. Klíčovým scénářem, který tato funkce umožňuje, je, že se příjemce nemusí neustále dotazovat na zprávy z front nebo odběrů služby Service Bus, které obsahují malé množství zpráv. 

K povolení této funkce potřebujete následující položky:

* Obor názvů služby Service Bus úrovně Premium alespoň s jednou frontou služby Service Bus nebo jedním tématem služby Service Bus s alespoň jedním oděrem.
* Přístup přispěvatele k tomuto oboru názvů služby Service Bus. V Azure Portal přejděte na obor názvů Service Bus a pak vyberte možnost **řízení přístupu (IAM)** a vyberte kartu **přiřazení rolí** . Ověřte, že máte přístup přispěvatele k oboru názvů. 
* Kromě toho potřebujete odběr služby Event Grid pro tento obor názvů služby Service Bus. Tento odběr přijímá ze služby Event Grid oznámení o zprávách čekajících na vyzvednutí. Typickými odběrateli můžou být funkce Logic Apps v Azure App Service, služba Azure Functions nebo webhook kontaktující webovou aplikaci. Odběratel pak zprávy zpracuje. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Event Grid předplatných pro obory názvů Service Bus
Odběry služby Event Grid pro obory názvů služby Service Bus můžete vytvořit třemi různými způsoby:

- Azure Portal. V následujících kurzech se dozvíte, jak pomocí Azure Portal vytvořit Event Grid odběry Service Busch událostí pomocí Azure Logic Apps a Azure Functions jako obslužných rutin. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI Následující příklad rozhraní příkazového řádku ukazuje, jak vytvořit předplatné Azure Functions pro [systémové téma](../event-grid/system-topics.md) vytvořené Service BUSM oborem názvů.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- Prostředí. Tady je příklad:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>V jakém množství se vysílají události a jak často?

Pokud v oboru názvů máte více front a témat nebo odběrů, obdržíte alespoň jednu událost za každou frontu a jednu událost za každý odběr. Pokud entita služby Service Bus neobsahuje žádné zprávy, události se vysílají okamžitě po přijetí nové zprávy. Případně se události vysílají každé dvě minuty, pokud služba Service Bus nedetekuje aktivního příjemce. Procházení zpráv tyto události nepřeruší.

Ve výchozím nastavení služba Service Bus vysílá události pro všechny entity v příslušném oboru názvů. Pokud chcete přijímat pouze události pro konkrétní entity, přečtěte si následující část.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Použití filtrů k omezení zdrojů událostí

Pokud chcete přijímat události například pouze z jedné fronty nebo jednoho odběru v rámci vašeho oboru názvů, můžete k tomu použít filtry *Začíná na* a *Končí na*, které poskytuje služba Event Grid. V některých rozhraních se tyto filtry označují jako *Předpona* a *Přípona*. Pokud chcete přijímat události pro více front a odběrů, ale ne pro všechny, můžete vytvořit více odběrů služby Event Grid a pro každý z nich zadat filtr.

## <a name="next-steps"></a>Další kroky
Projděte si tyto kurzy: 
- [Azure Logic Apps zpracování zpráv Service Bus přijatých prostřednictvím Event Grid](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Functions zpracování zpráv Service Bus přijatých prostřednictvím Event Grid](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[čl]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
