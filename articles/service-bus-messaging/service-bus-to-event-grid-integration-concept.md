---
title: Přehled integrace služby Azure Service Bus do služby Event Grid | Microsoft Docs
description: Tento článek poskytuje popis způsobu, jakým se Azure Service Bus zasílání zpráv integruje s Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 621402975411afb63055a7d6a45d86d9e026e284
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007751"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Přehled integrace služby Azure Service Bus do služby Event Grid

Služba Azure Service Bus spustila novou integraci do služby Azure Event Grid. Klíčovým scénářem, který tato funkce umožňuje, je, že se příjemce nemusí neustále dotazovat na zprávy z front nebo odběrů služby Service Bus, které obsahují malé množství zpráv. 

V případě, že fronta nebo odběr obsahuje zprávy a nejsou k dispozici žádní příjemci, teď může Service Bus vysílat události do služby Event Grid. Pro obory názvů služby Service Bus můžete vytvářet odběry služby Event Grid, naslouchat těmto událostem a pak na ně reagovat spuštěním příjemce. Díky této funkci můžete službu Service Bus využívat v reaktivních programovacích modelech.

K povolení této funkce potřebujete následující položky:

* Obor názvů služby Service Bus úrovně Premium alespoň s jednou frontou služby Service Bus nebo jedním tématem služby Service Bus s alespoň jedním oděrem.
* Přístup přispěvatele k tomuto oboru názvů služby Service Bus.
* Kromě toho potřebujete odběr služby Event Grid pro tento obor názvů služby Service Bus. Tento odběr přijímá ze služby Event Grid oznámení o zprávách čekajících na vyzvednutí. Typickými odběrateli můžou být funkce Logic Apps v Azure App Service, služba Azure Functions nebo webhook kontaktující webovou aplikaci. Odběratel pak zprávy zpracuje. 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>Ověření, že máte přístup přispěvatele
Přejděte do oboru názvů Service Bus a pak vyberte možnost **řízení přístupu (IAM)** a vyberte kartu **přiřazení rolí** . Ověřte, že máte přístup přispěvatele k oboru názvů. 

### <a name="events-and-event-schemas"></a>Události a schémata událostí

Service Bus v současné době odesílá události pro dva scénáře:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#deadletter-messages-available-event)
* [ActiveMessagesAvailablePeriodicNotifications](#active-messages-available-periodic-notifications)
* [DeadletterMessagesAvailablePeriodicNotifications](#deadletter-messages-available-periodic-notifications)

Kromě toho využívá Service Bus standardní [mechanismy ověřování](../event-grid/security-authentication.md) a zabezpečení služby Event Grid.

Další informace najdete v tématu [Schémata událostí služby Azure Event Grid](../event-grid/event-schema.md).

#### <a name="active-messages-available-event"></a>Událost Active Messages Available (Jsou k dispozici aktivní zprávy)

Tato událost se generuje v případě, že ve frontě nebo odběru máte aktivní zprávy, ale žádný příjemce nenaslouchá.

Schéma této události je následující:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="deadletter-messages-available-event"></a>Událost dostupné zprávy o nedoručených zprávách

Obdržíte alespoň jednu událost za každou frontu nedoručených zpráv, která obsahuje zprávy, ale nemá žádné aktivní příjemce.

Schéma této události je následující:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Aktivní zprávy jsou k dispozici periodická oznámení

Tato událost se generuje pravidelně, pokud máte aktivní zprávy o konkrétní frontě nebo předplatném, a to i v případě, že v dané konkrétní frontě nebo předplatném existují aktivní naslouchací procesy.

Schéma pro událost je následující.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Zprávy o nedoručených zprávách k dispozici pravidelné oznámení

Tato událost se generuje pravidelně, pokud máte zprávy nedoručené pošty v konkrétní frontě nebo předplatném, a to i v případě, že existují aktivní naslouchací procesy pro entitu nedoručených zpráv dané konkrétní frontě nebo

Schéma pro událost je následující.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>V jakém množství se vysílají události a jak často?

Pokud v oboru názvů máte více front a témat nebo odběrů, obdržíte alespoň jednu událost za každou frontu a jednu událost za každý odběr. Pokud entita služby Service Bus neobsahuje žádné zprávy, události se vysílají okamžitě po přijetí nové zprávy. Případně se události vysílají každé dvě minuty, pokud služba Service Bus nedetekuje aktivního příjemce. Procházení zpráv tyto události nepřeruší.

Ve výchozím nastavení služba Service Bus vysílá události pro všechny entity v příslušném oboru názvů. Pokud chcete přijímat pouze události pro konkrétní entity, přečtěte si následující část.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Použití filtrů k omezení zdrojů událostí

Pokud chcete přijímat události například pouze z jedné fronty nebo jednoho odběru v rámci vašeho oboru názvů, můžete k tomu použít filtry *Začíná na* a *Končí na*, které poskytuje služba Event Grid. V některých rozhraních se tyto filtry označují jako *Předpona* a *Přípona*. Pokud chcete přijímat události pro více front a odběrů, ale ne pro všechny, můžete vytvořit více odběrů služby Event Grid a pro každý z nich zadat filtr.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Vytváření odběrů služby Event Grid pro obory názvů služby Service Bus

Odběry služby Event Grid pro obory názvů služby Service Bus můžete vytvořit třemi různými způsoby:

* Na webu Azure Portal
* V [Azure CLI](#azure-cli-instructions)
* V [PowerShellu](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Pokyny pro Azure Portal

Nový odběr služby Event Grid vytvoříte následovně:
1. Na webu Azure Portal přejděte do svého oboru názvů.
2. V levém podokně vyberte **Event Grid**. 
3. Vyberte **Odběr události**.  

   Následující obrázek představuje obor názvů obsahující předplatné služby Event Grid:

   ![Předplatná Event Gridu](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   Následující obrázek ukazuje vytvoření odběru funkce nebo webhooku bez konkrétního filtrování:

   ![21][]

## <a name="azure-cli-instructions"></a>Pokyny pro Azure CLI

Nejprve se ujistěte, že máte nainstalované Azure CLI verze 2.0 nebo novější. [Stáhněte instalační program](/cli/azure/install-azure-cli?view=azure-cli-latest). Vyberte **Windows + X** a pak otevřete novou konzolu PowerShellu s oprávněními správce. Případně může použít příkazové prostředí na webu Azure Portal.

Spusťte následující kód:

 ```azurecli-interactive
az login

az account set -s "<Azure subscription name>"

namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv

az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" --endpoint "<your_function_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Pokud používáte BASH 

## <a name="powershell-instructions"></a>Pokyny pro PowerShell

Ujistěte se, že máte nainstalovaný Azure PowerShell. [Stáhněte instalační program](/powershell/azure/install-Az-ps). Stiskněte **Windows + X** a pak otevřete novou konzolu PowerShellu s oprávněními správce. Případně může použít příkazové prostředí na webu Azure Portal.

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Teď můžete prozkoumat další možnosti instalace nebo otestovat tok událostí.

## <a name="next-steps"></a>Další kroky

* [Příklady](service-bus-to-event-grid-integration-example.md) pro službu Service Bus a Event Grid.
* Další informace o službě [Event Grid](../event-grid/index.yml).
* Další informace o službě [Azure Functions](../azure-functions/index.yml).
* Další informace o [Logic Apps](../logic-apps/index.yml).
* Další informace o službě [Service Bus](/azure/service-bus/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[čl]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
