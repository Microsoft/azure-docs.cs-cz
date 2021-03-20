---
title: Vytvoření skupin IP adres v Azure Firewall
description: Skupiny IP adres umožňují seskupovat a spravovat IP adresy pro pravidla Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394522"
---
# <a name="create-ip-groups"></a>Vytváření skupin IP adres

Skupiny IP adres umožňují seskupovat a spravovat IP adresy pro pravidla Azure Firewall. Můžou mít jednu IP adresu, víc IP adres nebo jeden nebo víc rozsahů IP adres.

## <a name="create-an-ip-group---azure-portal"></a>Vytvoření skupiny IP adres – Azure Portal

Vytvoření skupiny IP adres pomocí Azure Portal:

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
1. Do vyhledávacího pole zadejte **skupiny IP adres** a pak vyberte **skupiny IP adres**.
1. Vyberte **Vytvořit**.
1. Vyberte své předplatné.
1. Vyberte skupinu prostředků nebo vytvořte novou.
1. Zadejte jedinečný název pro vaši skupinu IP adres a pak vyberte oblast.
1. Vyberte **Další: IP adresy**.
1. Zadejte IP adresu, více IP adres nebo rozsahy IP adres.

   Existují dva způsoby, jak zadat IP adresy:
   - Můžete je zadat ručně.
   - Můžete je importovat ze souboru.

   Pokud chcete importovat ze souboru, vyberte **Importovat ze souboru**. Můžete buď soubor přetáhnout do pole nebo vybrat **Vyhledat soubory**. V případě potřeby můžete zkontrolovat a upravit nahrané IP adresy.

   Když zadáte IP adresu, portál ji ověří, aby zkontrolovala, jestli nedošlo k překrývání, duplicitám a formátování.

1. Po dokončení vyberte **zkontrolovat + vytvořit**.
1. Vyberte **Vytvořit**.

## <a name="create-an-ip-group---azure-powershell"></a>Vytvoření skupiny IP adres – Azure PowerShell

Tento příklad vytvoří skupinu IP adres s předponou adresy a IP adresou pomocí Azure PowerShell:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>Vytvoření skupiny IP adres – Azure CLI

Tento příklad vytvoří skupinu IP adres s předponou adresy a IP adresou pomocí Azure CLI:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Další kroky

- [Další informace o skupinách IP adres](ip-groups.md)
