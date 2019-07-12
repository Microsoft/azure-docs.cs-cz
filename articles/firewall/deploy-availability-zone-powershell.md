---
title: Nasazení Brána Firewall služby Azure se zónami dostupnosti pomocí Azure Powershellu
description: V tomto článku se dozvíte, jak nasadit Brána Firewall služby Azure se zónami dostupnosti pomocí Azure Powershellu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704001"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Nasazení Brána Firewall služby Azure se zónami dostupnosti pomocí Azure Powershellu

Během nasazování rozložit několika zónami dostupnosti pro zajištění vyšší dostupnosti je možné nakonfigurovat brány Firewall na Azure.

Tato funkce umožňuje následující scénáře:

- Můžete zvýšit dostupnost 99,99 % dostupnost. Další informace najdete v tématu Brána Firewall Azure [smlouva o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99 % dostupnosti smlouvy SLA se nabízí, když vyberete dva nebo více zónách dostupnosti.
- Brána Firewall služby Azure můžete také přiřadit ke konkrétní zóně pouze z důvodů blízkosti pomocí služeb standard 99,95 % SLA.

Další informace o zónách dostupnosti Azure bránu Firewall, najdete v části [co je brána Firewall služby Azure?](overview.md)

Následující příklad prostředí Azure PowerShell ukazuje, jak můžete nasadit bránu Firewall Azure se zónami dostupnosti.

## <a name="create-a-firewall-with-availability-zones"></a>Vytvoření brány firewall se zónami dostupnosti

Tento příklad vytvoří bránu firewall v zóny 1, 2 a 3.

Po vytvoření standardní veřejné IP adresy je zadán žádný konkrétní zónu. Tím se vytvoří ve výchozím nastavení zónově redundantní IP adresu. Standardní veřejné IP adresy je možné nakonfigurovat ve všech zónách nebo v jedné oblasti.

Je důležité vědět, protože nemůže mít bránu firewall v zóně 1 a IP adresy v zóně 2. Ale máte brány firewall v zóně 1 a IP adresu ve všech zónách, nebo bránu firewall a IP adresu ve stejné zóně jeden pro účely blízkosti.

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

- [Kurz: Monitorujte protokoly brány Firewall na Azure](./tutorial-diagnostics.md)
