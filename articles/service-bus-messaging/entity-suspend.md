---
title: Azure Service Bus – pozastavení entit zasílání zpráv
description: Tento článek vysvětluje, jak dočasně pozastavit a znovu aktivovat entity Azure Service Bus zpráv (fronty, témata a odběry).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94543047"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pozastavení a opětovná aktivace entit zasílání zpráv (zakázat)

Fronty, témata a odběry je možné dočasně pozastavit. Pozastavení vloží entitu do zakázaného stavu, ve kterém jsou všechny zprávy udržovány v úložišti. Zprávy však nelze odebrat ani přidat a příslušné operace protokolu zanášejí chyby.

Můžete chtít pozastavit entitu z naléhavých důvodů správy. Vadný přijímač například přebírá zprávy z fronty, selhává při zpracování a ještě nesprávně dokončuje zprávy a odebírá je. V takovém případě může být vhodné zakázat frontu pro příjem, dokud neopravíte kód a neprovedete jeho nasazení. 

Přerušení nebo opětovnou aktivaci může provést buď uživatel, nebo systém. Systém pozastavuje pouze entity z důvodu vážného administrativního důvodů, jako je například překročení limitu útraty pro předplatné. Uživatelem zakázané entity nelze znovu aktivovat, ale budou obnoveny, pokud byla příčina pozastavení vyřešena.

## <a name="queue-status"></a>Stav fronty 
Stavy, které lze nastavit pro **frontu** , jsou:

-   **Aktivní**: fronta je aktivní. Můžete odesílat zprávy a přijímat zprávy z fronty. 
-   **Zakázáno**: fronta je pozastavena. Je ekvivalentní nastavení **SendDisabled** i **ReceiveDisabled**. 
-   **SendDisabled**: nemůžete odesílat zprávy do fronty, ale můžete z ní přijímat zprávy. Pokud se pokusíte odeslat zprávy do fronty, získáte výjimku. 
-   **ReceiveDisabled**: můžete odesílat zprávy do fronty, ale nemůžete z ní přijímat zprávy. Pokud se pokusíte přijímat zprávy do fronty, získáte výjimku.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Změňte stav fronty v Azure Portal: 

1. V Azure Portal přejděte do svého oboru názvů Service Bus. 
1. Vyberte frontu, pro kterou chcete změnit stav. Fronty se zobrazí v dolním podokně uprostřed. 
1. Na stránce **fronta Service Bus** se zobrazí aktuální stav fronty jako hypertextový odkaz. Pokud v nabídce vlevo není vybraný **Přehled** , vyberte ho a zobrazí se stav fronty. Vyberte aktuální stav fronty pro změnu. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Vyberte stav fronty.":::
4. Vyberte nový stav fronty a vyberte **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Nastavit stav fronty":::
    
Operace odesílání a přijímání můžete také zakázat pomocí rozhraní API Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) v sadě .NET SDK nebo pomocí Azure Resource Manager šablony prostřednictvím Azure CLI nebo Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Změna stavu fronty pomocí Azure PowerShell
V následujícím příkladu se zobrazí příkaz prostředí PowerShell pro zakázání fronty. Příkaz reaktivace je ekvivalentní nastavení `Status` na **aktivní**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Stav tématu
Stav tématu můžete změnit v Azure Portal. Výběrem aktuálního stavu tématu zobrazíte následující stránku, která umožňuje změnit stav. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Změnit stav tématu":::

Stavy, které lze nastavit pro **téma** :
- **Aktivní**: téma je aktivní. Můžete odeslat zprávy do tématu. 
- **Zakázáno**: téma je pozastaveno. Nemůžete odeslat zprávy do tématu. 
- **SendDisabled**: stejný efekt jako **zakázaný**. Nemůžete odeslat zprávy do tématu. Pokud se pokusíte odeslat zprávy do tématu, získáte výjimku. 

## <a name="subscription-status"></a>Stav předplatného
V Azure Portal můžete změnit stav předplatného. Výběrem aktuálního stavu předplatného zobrazíte následující stránku, která umožňuje změnit stav. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Změnit stav předplatného":::

Stavy, které je možné nastavit pro **předplatné** , jsou:
- **Aktivní**: předplatné je aktivní. Můžete přijímat zprávy FRM předplatného.
- **Zakázáno**: předplatné je pozastaveno. Z předplatného nemůžete přijímat zprávy. 
- **ReceiveDisabled**: stejný efekt jako **zakázaný**. Z předplatného nemůžete přijímat zprávy. Pokud se pokusíte dostávat zprávy do předplatného, dostanete výjimku.

| Stav tématu | Stav předplatného | Chování | 
| ------------ | ------------------- | -------- | 
| Aktivní | Aktivní | Můžete odesílat zprávy do tématu a přijímat zprávy z předplatného. | 
| Aktivní | Zakázáno nebo přijímat zakázané | Můžete posílat zprávy do tématu, ale nemůžete přijímat zprávy z předplatného. | 
| Zakázáno nebo odesláno zakázané | Aktivní | Nemůžete odeslat zprávy do tématu, ale můžete dostávat zprávy, které jsou již v předplatném. | 
| Zakázáno nebo odesláno zakázané | Zakázáno nebo přijímat zakázané | Nemůžete odeslat zprávy do tématu a nemůžete přijímat z předplatného. | 

## <a name="other-statuses"></a>Další stavy
Výčet [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) také definuje sadu přechodných stavů, které lze nastavit pouze systémem. 


## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

