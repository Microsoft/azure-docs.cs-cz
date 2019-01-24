---
title: Pozastavit entit pro zasílání zpráv Azure Service Bus | Dokumentace Microsoftu
description: Pozastavení a opětovná aktivace entit zpráv Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8feee92e9105259bddf45114ac48a54347266cd7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845995"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pozastavení a opětovná aktivace entit zasílání zpráv (zakázat)

Fronty, témata a odběry mohou být dočasně pozastaveno. Pozastavení vloží entitu v zakázaném stavu, ve kterém se zachovají všechny zprávy v úložišti. Ale zprávy nemůže být přidaly nebo odebraly a operace příslušného protokolu yield chyby.

Pozastavení entity se většinou děje u naléhavých důvodů správy. Jeden scénář se nutnosti nasazení vadným příjemce, který přijímá zprávy z fronty, se nezdaří zpracování a ještě nesprávně dokončení zprávy a odebere je. Pokud toto chování je zjistit, je možné do fronty zakázat obdrží, dokud je nasazená Opravený kód a další jde zakázat ztráty dat způsobené chybného kódu.

Pozastavení a opětovná aktivace provést uživatelem nebo systémem. Systém pozastaví pouze entity z důvodu závažné administrativních důvodů, jako je například narazili předplatné limit útraty. Systém zakázáno entity nelze znovu aktivován uživatelem, ale se obnoví při vyřeší příčina pozastavení.

Na portálu **vlastnosti** oddíl pro příslušný entitu umožňuje změnu stavu; následující snímek obrazovky ukazuje přepínač pro frontu:

![][1]

Na portálu povoluje jen zcela zakazuje fronty. Můžete také zakázat odesílání a příjem operace samostatně pomocí služby Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) rozhraní API v sadě SDK rozhraní .NET Framework, nebo pomocí šablony Azure Resource Manageru pomocí rozhraní příkazového řádku Azure nebo Azure Powershellu.

## <a name="suspension-states"></a>Pozastavení stavy

Stavy, které lze nastavit pro fronty jsou:

-   **Aktivní**: Fronta je aktivní.
-   **Zakázané**: Fronta je pozastavená.
-   **SendDisabled**: Fronta je částečně pozastaveno, s receive je povolené.
-   **ReceiveDisabled**: Fronta je částečně pozastavený, s odešlete povoleno.

U předplatných a témat, pouze **aktivní** a **zakázané** lze nastavit.

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) výčet také definuje sadu přechodové stavy, které lze nastavit pouze v systému. Příkaz prostředí PowerShell zakázat frontu je znázorněno v následujícím příkladu. Příkaz opětovná aktivace je ekvivalentní, nastavení `Status` k **aktivní**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

