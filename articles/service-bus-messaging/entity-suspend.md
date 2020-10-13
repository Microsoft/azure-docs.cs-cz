---
title: Azure Service Bus – pozastavení entit zasílání zpráv
description: Tento článek vysvětluje, jak dočasně pozastavit a znovu aktivovat entity Azure Service Bus zpráv (fronty, témata a odběry).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575222"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pozastavení a opětovná aktivace entit zasílání zpráv (zakázat)

Fronty, témata a odběry je možné dočasně pozastavit. Pozastavení vloží entitu do zakázaného stavu, ve kterém jsou všechny zprávy udržovány v úložišti. Zprávy však nelze odebrat ani přidat a příslušné operace protokolu zanášejí chyby.

Pozastavení entity se obvykle provádí z naléhavých důvodů správy. Jedním z scénářů je nasazení vadného přijímače, který přebírá zprávy z fronty, selhává při zpracování a ještě nesprávně dokončuje zprávy a odebírá je. Pokud je toto chování diagnostikováno, může být fronta zakázána pro příjem, dokud není nasazen opravný kód a nebudete moci zabránit úniku dat z důvodu chybného kódu.

Přerušení nebo opětovnou aktivaci může provést buď uživatel, nebo systém. Systém pozastavuje jenom entity z důvodu neznačných administrativních důvodů, jako je například dosažení limitu útraty pro předplatné. Uživatelem zakázané entity nelze znovu aktivovat, ale budou obnoveny, pokud byla příčina pozastavení vyřešena.

## <a name="queue-status"></a>Stav fronty 
Stavy, které lze nastavit pro frontu, jsou:

-   **Aktivní**: fronta je aktivní.
-   **Zakázáno**: fronta je pozastavena. Je ekvivalentní nastavení **SendDisabled** i **ReceiveDisabled**. 
-   **SendDisabled**: fronta je částečně pozastavena s povoleným přijetím.
-   **ReceiveDisabled**: fronta je částečně pozastavena s povoleným odesláním.

### <a name="change-the-queue-status-in-the-azure-portal"></a>Změňte stav fronty v Azure Portal: 

1. V Azure Portal přejděte do svého oboru názvů Service Bus. 
1. Vyberte frontu, pro kterou chcete změnit stav. Fronty se zobrazí v dolním podokně uprostřed. 
1. Na stránce **fronta Service Bus** se zobrazí aktuální stav fronty jako hypertextový odkaz. Pokud v nabídce vlevo není vybraný **Přehled** , vyberte ho a zobrazí se stav fronty. Vyberte aktuální stav fronty pro změnu. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Vyberte stav fronty.":::
4. Vyberte nový stav fronty a vyberte **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Vyberte stav fronty.":::
    
Portál povoluje pouze úplné zakazování front. Operace odesílání a přijímání můžete také zakázat samostatně pomocí Service Bus rozhraní API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) v sadě .NET Framework SDK nebo se šablonou Azure Resource Manager prostřednictvím Azure CLI nebo Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Změna stavu fronty pomocí Azure PowerShell
V následujícím příkladu se zobrazí příkaz prostředí PowerShell pro zakázání fronty. Příkaz reaktivace je ekvivalentní nastavení `Status` na **aktivní**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Stav tématu
Změna stavu tématu v Azure Portal je podobná změně stavu fronty. Když vyberete aktuální stav tématu, zobrazí se následující stránka, která vám umožní změnit stav. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Vyberte stav fronty.":::

Stavy, které lze nastavit pro téma:
- **Aktivní**: téma je aktivní.
- **Zakázáno**: téma je pozastaveno.
- **SendDisabled**: stejný efekt jako **zakázaný**.

## <a name="subscription-status"></a>Stav předplatného
Změna stavu předplatného v Azure Portal je podobná změně stavu tématu nebo fronty. Po výběru aktuálního stavu předplatného se zobrazí následující stránka, která vám umožní změnit stav. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Vyberte stav fronty.":::

Stavy, které lze nastavit pro téma:
- **Aktivní**: téma je aktivní.
- **Zakázáno**: téma je pozastaveno.
- **ReceiveDisabled**: stejný efekt jako **zakázaný**.

## <a name="other-statuses"></a>Další stavy
Výčet [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) také definuje sadu přechodných stavů, které lze nastavit pouze systémem. 


## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

