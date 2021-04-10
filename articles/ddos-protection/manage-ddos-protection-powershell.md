---
title: Vytvoření a konfigurace plánu Azure DDoS Protection pomocí Azure PowerShell
description: Naučte se vytvořit plán DDoS Protection pomocí Azure PowerShell
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 41d45b216337cc1b674a9be390f241c4e1bb062a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103126"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Rychlý Start: vytvoření a konfigurace Azure DDoS Protection Standard pomocí Azure PowerShell

Začněte používat Azure DDoS Protection Standard pomocí Azure PowerShell. 

Plán DDoS Protection definuje sadu virtuálních sítí, které mají povolený Standard DDoS Protection v rámci předplatných. Můžete nakonfigurovat jeden plán DDoS Protection pro vaši organizaci a propojit virtuální sítě s několika předplatnými do stejného plánu. 

V tomto rychlém startu vytvoříte plán DDoS Protection a propojíte ho s virtuální sítí. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>Vytvoření plánu DDoS Protection

V Azure přidělíte související prostředky skupině prostředků. Můžete použít buď existující skupinu prostředků, nebo vytvořit novou.

Pokud chcete vytvořit skupinu prostředků, použijte [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). V tomto příkladu pojmenujte naši skupinu prostředků _MyResourceGroup_ a použijeme _východní USA_ umístění:

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Teď vytvořte plán DDoS Protection s názvem _MyDdosProtectionPlan_:

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>Povolení DDoS pro virtuální síť

### <a name="enable-ddos-for-a-new-virtual-network"></a>Povolení DDoS pro novou virtuální síť

Při vytváření virtuální sítě můžete povolit DDoS Protection. V tomto příkladu pojmenujte naši virtuální síť _MyVnet_: 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>Povolení DDoS pro existující virtuální síť

Při vytváření plánu DDoS Protection můžete přidružit existující virtuální síť:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Ověřit a otestovat

Nejdřív si Projděte podrobnosti svého plánu DDoS Protection:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Ověřte, že příkaz vrací správné podrobnosti vašeho plánu DDoS Protection.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Své prostředky můžete uchovávat pro další kurz. Pokud už je nepotřebujete, odstraňte skupinu prostředků _MyResourceGroup_ . Když odstraníte skupinu prostředků, odstraní se i plán DDoS Protection a všechny související prostředky. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Zakázání ochrany DDoS pro virtuální síť: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

Pokud chcete odstranit plán DDoS Protection, musíte nejdřív z něj oddělit všechny virtuální sítě.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak zobrazit a nakonfigurovat telemetrii pro svůj plán DDoS Protection, přejděte k kurzům.

> [!div class="nextstepaction"]
> [Zobrazení a konfigurace telemetrie ochrany před útoky DDoS](telemetry.md)