---
title: Pozastavit entit pro zasílání zpráv Azure Service Bus | Dokumentace Microsoftu
description: Pozastavení a opětovná aktivace entit zpráv Azure Service Bus.
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
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: bd75fd5c34a3518f6da0dbd56eca2ad152d60ba9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405712"
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

-   **Aktivní**: fronty je aktivní.
-   **Zakázané**: fronta je pozastavená.
-   **SendDisabled**: fronty částečně je pozastaveno, s receive je povolené.
-   **ReceiveDisabled**: fronty částečně je pozastaveno, s odešlete povoleno.

U předplatných a témat, pouze **aktivní** a **zakázané** lze nastavit.

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) výčet také definuje sadu přechodové stavy, které lze nastavit pouze v systému. Příkaz prostředí PowerShell zakázat frontu je znázorněno v následujícím příkladu. Příkaz opětovná aktivace je ekvivalentní, nastavení `Status` k **aktivní**.

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

