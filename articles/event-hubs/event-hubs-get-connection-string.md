---
title: Získání připojovacího řetězce – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje pokyny pro získání připojovacího řetězce, který můžou klienti používat pro připojení k Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5ae6c66ddbbf4b9946e7037e1a7723043bf60507
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86537169"
---
# <a name="get-an-event-hubs-connection-string"></a>Získání připojovacího řetězce Event Hubs

Chcete-li použít Event Hubs, je nutné vytvořit Event Hubs obor názvů. Obor názvů je kontejner oboru pro více Center událostí nebo Kafka témata. Tento obor názvů poskytuje jedinečný [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po vytvoření oboru názvů můžete získat připojovací řetězec potřebný ke komunikaci s Event Hubs.

Připojovací řetězec pro Azure Event Hubs obsahuje následující komponenty, které jsou v něm vložené.

* FQDN = plně kvalifikovaný název domény EventHubs oboru názvů, který jste vytvořili (zahrnuje název oboru názvů EventHubs následovaný servicebus.windows.net)
* SharedAccessKeyName = název, který jste zvolili pro klíče SAS vaší aplikace
* SharedAccessKey = vygenerovaná hodnota klíče.

Šablona připojovacího řetězce vypadá nějak takto.
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Příklad připojovacího řetězce může vypadat takto. `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Tento článek vás provede různými způsoby získání připojovacího řetězce.

## <a name="get-connection-string-from-the-portal"></a>Získání připojovacího řetězce z portálu
1. Přihlaste se k [Azure Portal](https://portal.azure.com). 
2. V levé navigační nabídce vyberte **všechny služby** . 
3. V části **Analýza** vyberte **Event Hubs** . 
4. V seznamu Center událostí vyberte centrum událostí.
6. Na stránce **Event Hubs obor názvů** vyberte v levé nabídce **zásady sdíleného přístupu** .

    ![Položka nabídky zásady sdíleného přístupu](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. V seznamu zásad vyberte **zásadu sdíleného přístupu** . Výchozí hodnota je pojmenována: **RootManageSharedAccessPolicy**. Můžete přidat zásadu s příslušnými oprávněními (čtení, zápis) a používat tyto zásady. 

    ![Event Hubs zásady sdíleného přístupu](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Vyberte tlačítko **Kopírovat** vedle pole **připojovací řetězec – primární klíč** . 

    ![Event Hubs – získání připojovacího řetězce](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Získání připojovacího řetězce pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K získání připojovacího řetězce pro konkrétní název zásady nebo pravidla, jak je znázorněno níže, můžete použít [příkaz Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) :

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Získání připojovacího řetězce pomocí Azure CLI
K získání připojovacího řetězce pro obor názvů můžete použít následující:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

K získání připojovacího řetězce pro entitu EventHub můžete použít následující postup:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Další informace o příkazech rozhraní příkazového řádku Azure pro Event Hubs najdete v tématu [Azure CLI pro Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled Event Hubs](./event-hubs-about.md)
* [Vytvoření centra událostí](event-hubs-create.md)
