---
title: Nasazení Azure Firewall s využitím Zóny dostupnosti pomocí PowerShellu
description: V tomto článku se dozvíte, jak nasadit Azure Firewall pomocí Zóny dostupnosti pomocí Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 46ecc4754a064e26e61365ed6ca167606bef9d81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94656101"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Nasazení služby Azure Firewall se Zónami dostupnosti pomocí Azure PowerShellu

Azure Firewall se dá nakonfigurovat během nasazení, aby se zvýšila dostupnost více Zóny dostupnosti.

Tato funkce umožňuje následující scénáře:

- Dostupnost můžete zvýšit až na 99,99% dobu provozu. Další informace najdete v tématu Azure Firewall [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Smlouva SLA o 99,99% provozu se nabízí, když jsou vybrané dvě nebo víc Zóny dostupnosti.
- K určité zóně taky můžete přidružit Azure Firewall jenom z důvodů blízkosti, a to pomocí smlouvy SLA pro službu Standard 99,95%.

Další informace o Azure Firewall Zóny dostupnosti najdete v tématu [co je Azure firewall?](overview.md)

Následující příklad Azure PowerShell ukazuje, jak můžete nasadit Azure Firewall pomocí Zóny dostupnosti.

## <a name="create-a-firewall-with-availability-zones"></a>Vytvoření brány firewall pomocí Zóny dostupnosti

Tento příklad vytvoří bránu firewall v zónách 1, 2 a 3.

Když je vytvořena standardní veřejná IP adresa, není určena žádná konkrétní zóna. Ve výchozím nastavení se vytvoří IP adresa redundantní zóny. Standardní veřejné IP adresy se dají nakonfigurovat buď v všech zónách, nebo v jedné zóně.

Je důležité, abyste měli jistotu, že nemůžete mít bránu firewall v zóně 1 a IP adresu v zóně 2. Můžete ale mít bránu firewall v zóně 1 a IP adresu ve všech zónách nebo bránu firewall a IP adresu ve stejné jedné zóně pro účely blízkosti.

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
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>Další kroky

- [Kurz: Monitorování protokolů brány Azure Firewall](./firewall-diagnostics.md)