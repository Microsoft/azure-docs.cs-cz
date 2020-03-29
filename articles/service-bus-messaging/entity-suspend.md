---
title: Azure Service Bus – pozastavit zasílání zpráv entity
description: Tento článek vysvětluje, jak dočasně pozastavit a znovu aktivovat entity zpráv Azure Service Bus (fronty, témata a předplatná).
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760381"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pozastavit a znovu aktivovat entity zasílání zpráv (zakázat)

Fronty, témata a odběry mohou být dočasně pozastaveny. Pozastavení umístí entitu do zakázaného stavu, ve kterém jsou všechny zprávy udržovány v úložišti. Zprávy však nelze odebrat ani přidat a příslušné operace protokolu poskytují chyby.

Pozastavení entity se obvykle provádí z naléhavých administrativních důvodů. Jedním scénářem je nasazení vadného příjemce, který přijímá zprávy z fronty, selže zpracování a přesto nesprávně dokončí zprávy a odebere je. Pokud je toto chování diagnostikováno, fronta může být zakázána pro příjem, dokud je nasazen opravený kód a další ztrátě dat způsobené chybný kód může být zabráněno.

Pozastavení nebo opětovnou aktivaci může provést uživatel nebo systém. Systém pozastaví pouze entity z vážných administrativních důvodů, jako je dosažení limitu útraty předplatného. Systémové zakázané entity nelze znovu aktivovat uživatelem, ale jsou obnoveny, pokud byla vyřešena příčina pozastavení.

Na portálu umožňuje oddíl **Vlastnosti** pro příslušnou entitu změnu stavu. následující snímek obrazovky ukazuje přepínač fronty:

![][1]

Portál povoluje pouze zcela zakázání front. Operace odesílání a přijímání můžete také zakázat samostatně pomocí rozhraní API Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) v sdk .NET Framework nebo pomocí šablony Azure Resource Manager prostřednictvím rozhraní API Azure nebo Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Stavy pozastavení

Stavy, které lze nastavit pro frontu jsou:

-   **Aktivní**: Fronta je aktivní.
-   **Zakázáno**: Fronta je pozastavena.
-   **SendDisabled**: Fronta je částečně pozastavena, přičemž příjem je povolen.
-   **ReceiveDisabled**: Fronta je částečně pozastavena, přičemž odesílání je povoleno.

U předplatných a témat lze nastavit pouze **aktivní** a **zakázané.**

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) výčtu také definuje sadu přechodných stavů, které lze nastavit pouze v systému. Příkaz Prostředí PowerShell pro zakázání fronty je uveden v následujícím příkladu. Příkaz pro opětovnou aktivaci je ekvivalentní, nastavení `Status` na **Aktivní**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

