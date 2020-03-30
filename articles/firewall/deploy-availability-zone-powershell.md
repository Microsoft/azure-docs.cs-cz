---
title: Nasazení brány Azure firewall s zónami dostupnosti pomocí PowerShellu
description: V tomto článku se dozvíte, jak nasadit bránu Azure firewall se zónami dostupnosti pomocí Azure PowerShellu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195912"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Nasazení služby Azure Firewall se Zónami dostupnosti pomocí Azure PowerShellu

Azure Firewall lze během nasazení nakonfigurovat tak, aby pro zvýšení dostupnosti přebývalo více zón dostupnosti.

Tato funkce umožňuje následující scénáře:

- Dostupnost můžete zvýšit na 99,99% dostupnost. Další informace naleznete v [tématu Azure Firewall Smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99% dostupnost sla je nabízena, když jsou vybrány dvě nebo více zón dostupnosti.
- Azure Firewall můžete také přidružit ke konkrétní zóně jen z důvodu blízkosti pomocí standardní 99,95% sla služby.

Další informace o zónách dostupnosti azure firewall najdete v tématu [Co je Azure Firewall?](overview.md)

Následující příklad Azure PowerShellu ukazuje, jak můžete nasadit bránu Azure firewall se zónami dostupnosti.

## <a name="create-a-firewall-with-availability-zones"></a>Vytvoření brány firewall se zónami dostupnosti

Tento příklad vytvoří bránu firewall v zónách 1, 2 a 3.

Při vytvoření standardní veřejné IP adresy není zadána žádná konkrétní zóna. To ve výchozím nastavení vytvoří zónovou redundantní adresu IP. Standardní veřejné IP adresy lze konfigurovat buď ve všech zónách, nebo v jedné zóně.

Je důležité vědět, protože nemůžete mít firewall v zóně 1 a IP adresu v zóně 2. Ale můžete mít firewall v zóně 1 a IP adresu ve všech zónách, nebo firewall a IP adresu ve stejné jedné zóně pro účely přiblížení.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Další kroky

- [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
