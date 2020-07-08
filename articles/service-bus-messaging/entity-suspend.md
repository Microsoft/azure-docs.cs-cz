---
title: Azure Service Bus – pozastavení entit zasílání zpráv
description: Tento článek vysvětluje, jak dočasně pozastavit a znovu aktivovat entity Azure Service Bus zpráv (fronty, témata a odběry).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4c4e554ada8c66b56d5d466c7becfc813701402a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85340000"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pozastavení a opětovná aktivace entit zasílání zpráv (zakázat)

Fronty, témata a odběry je možné dočasně pozastavit. Pozastavení vloží entitu do zakázaného stavu, ve kterém jsou všechny zprávy udržovány v úložišti. Zprávy však nelze odebrat ani přidat a příslušné operace protokolu zanášejí chyby.

Pozastavení entity se obvykle provádí z naléhavých důvodů správy. Jedním z scénářů je nasazení vadného přijímače, který přebírá zprávy z fronty, selhává při zpracování a ještě nesprávně dokončuje zprávy a odebírá je. Pokud je toto chování diagnostikováno, může být fronta zakázána pro příjem, dokud není nasazen opravný kód a nebudete moci zabránit úniku dat z důvodu chybného kódu.

Přerušení nebo opětovnou aktivaci může provést buď uživatel, nebo systém. Systém pozastavuje jenom entity z důvodu neznačných administrativních důvodů, jako je například dosažení limitu útraty pro předplatné. Uživatelem zakázané entity nelze znovu aktivovat, ale budou obnoveny, pokud byla příčina pozastavení vyřešena.

Na portálu umožňuje oddíl **vlastnosti** příslušné entity změnit stav; na následujícím snímku obrazovky vidíte přepínač pro frontu:

![][1]

Portál povoluje pouze úplné zakazování front. Operace odesílání a přijímání můžete také zakázat samostatně pomocí Service Bus rozhraní API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) v sadě .NET Framework SDK nebo se šablonou Azure Resource Manager prostřednictvím Azure CLI nebo Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Stavy pozastavení

Stavy, které lze nastavit pro frontu, jsou:

-   **Aktivní**: fronta je aktivní.
-   **Zakázáno**: fronta je pozastavena.
-   **SendDisabled**: fronta je částečně pozastavena s povoleným přijetím.
-   **ReceiveDisabled**: fronta je částečně pozastavena s povoleným odesláním.

Pro předplatná a témata je možné nastavit pouze **aktivní** a **zakázané** .

Výčet [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) také definuje sadu přechodných stavů, které lze nastavit pouze systémem. V následujícím příkladu se zobrazí příkaz prostředí PowerShell pro zakázání fronty. Příkaz reaktivace je ekvivalentní nastavení `Status` na **aktivní**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

