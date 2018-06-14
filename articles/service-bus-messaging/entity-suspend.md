---
title: Pozastavit entit pro zasílání zpráv Azure Service Bus | Microsoft Docs
description: Pozastavit a znovu aktivujte Azure Service Bus zprávu entity.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 1984b113f695107f8d4d80e5bbf25c7dc39d13f6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197022"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pozastavení a znovu aktivovat entit pro zasílání zpráv (zakázat)

Fronty, témata a odběry mohou být dočasně pozastaveno. Pozastavení vloží entitu v zakázaném stavu, ve kterém jsou všechny zprávy udržuje v úložišti. Ale zprávy nelze odebrat nebo přidat, a operace příslušných protokolu yield chyby.

Pozastavení entity se většinou děje u naléhavých důvodů správy. Jeden scénář se nutnosti nasazení vadný příjemce, která přijímá zprávy vypnout fronty, se nezdaří zpracování a ještě nesprávně dokončení zprávy a odebere je. Pokud je daná chování diagnostice, fronty je možné zakázat obdrží dokud opravené kód nasazen a dále je možné zabránit ztráty dat způsobené kód vadný.

Pozastavení nebo opětovnou aktivaci provést uživatelem nebo v systému. Systém pozastaví pouze entity vážné příčin správce například stiskne útrat předplatné. System zakázaný entity nelze znovu aktivovat uživatelem, ale budou obnoveny při příčinu pozastavení byl vyřešen.

Na portálu **vlastnosti** oddíl pro příslušné entitu umožňuje změny stavu; následující snímek obrazovky ukazuje přepínač fronty:

![][1]

Na portálu pouze umožňuje zcela zakázat fronty. Můžete také zakázat odesílání a příjem operace samostatně pomocí sběrnice služby [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) rozhraní API v rozhraní .NET Framework SDK, nebo pomocí šablony Azure Resource Manager pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell.

## <a name="suspension-states"></a>Pozastavení stavy

Stavy, které lze nastavit pro fronty jsou:

-   **Aktivní**: fronty je aktivní.
-   **Zakázané**: fronty je pozastaveno.
-   **SendDisabled**: fronty je částečně pozastaveno s receive probíhá povolené.
-   **ReceiveDisabled**: fronty je částečně pozastaveno s odesílání povoleno.

Pro předplatné a pouze témata **Active** a **zakázané** lze nastavit.

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) výčet také definuje sadu přechodové stavy, které lze nastavit pouze v systému. Příkaz prostředí PowerShell zakázat fronty je znázorněno v následujícím příkladu. Příkaz opětovné aktivace je ekvivalentní nastavení `Status` k **Active**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

