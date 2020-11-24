---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 163df5e639f4babb0d4302819f9f5b9538a6708c
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95819411"
---
## <a name="prerequisites"></a>Požadavky
Pokud ještě nemáte [předplatné Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Busu
Postupujte podle pokynů v tomto kurzu: [rychlý Start: pomocí Azure Portal vytvořte Service Bus téma a odběry k tématu](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) , abyste mohli provádět následující úlohy:

- Vytvořte obor názvů **premium** Service Bus. 
- Získá připojovací řetězec. 
- Vytvoří Service Bus téma.
- Vytvoření odběru tématu. V tomto kurzu potřebujete jenom jedno předplatné, takže nemusíte vytvářet odběry S2 a S3. 

## <a name="send-messages-to-the-service-bus-topic"></a>Odeslání zprávy do tématu Service Bus
V tomto kroku použijete ukázkovou aplikaci k posílání zpráv do Service Busho tématu, které jste vytvořili v předchozím kroku. 

1. Naklonujte [úložiště GitHub Azure-Service-Bus](https://github.com/Azure/azure-service-bus/).
2. V aplikaci Visual Studio, vyhledejte složku *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* a otevřete soubor *SBEventGridIntegration. sln* .
3. V okně Průzkumník řešení rozbalte projekt **MessageSender** a vyberte **program.cs**.
4. Nahraďte `<SERVICE BUS NAMESPACE - CONNECTION STRING>` připojovacím řetězcem k vašemu oboru názvů Service Bus a `<TOPIC NAME>` názvem tématu. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Sestavte a spusťte program pro odeslání 5 zkušebních zpráv ( `const int numberOfMessages = 5;` ) do tématu Service Bus. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Výstup aplikace konzoly":::
    