---
title: 'Brána Azure VPN: Odstranění brány: PowerShell'
description: Odstraňte bránu virtuální sítě pomocí prostředí PowerShell v modelu nasazení Správce prostředků.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162102"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Odstranění brány virtuální sítě pomocí PowerShellu
> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Existuje několik různých přístupů, které můžete provést, když chcete odstranit bránu virtuální sítě pro konfiguraci brány VPN.

- Pokud chcete odstranit vše a začít znovu, jako v případě testovacího prostředí, můžete odstranit skupinu prostředků. Když odstraníte skupinu prostředků, odstraní všechny prostředky v rámci skupiny. Tato metoda se doporučuje pouze v případě, že nechcete zachovat žádné prostředky ve skupině prostředků. Pomocí tohoto přístupu nelze selektivně odstranit pouze několik prostředků.

- Pokud chcete zachovat některé prostředky ve skupině prostředků, odstranění brány virtuální sítě se stane o něco složitější. Před odstraněním brány virtuální sítě je nutné nejprve odstranit všechny prostředky, které jsou na bráně závislé. Kroky, které provedete, závisí na typu připojení, které jste vytvořili, a na závislých prostředcích pro každé připojení.

## <a name="before-beginning"></a>Před zahájením



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Stáhněte si nejnovější rutiny prostředí PowerShell Azure Resource Manager.

Stáhněte a nainstalujte nejnovější verzi rutin prostředí PowerShell Azure Resource Manager. Další informace o stahování a instalaci rutin prostředí PowerShell najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Připojte se ke svému účtu Azure.

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
```

Zkontrolujte předplatná pro příslušný účet.

```powershell
Get-AzSubscription
```

Pokud máte více než jedno předplatné, zadejte předplatné, které chcete použít.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Odstranění brány VPN mezi lokalitami

Chcete-li odstranit bránu virtuální sítě pro konfiguraci S2S, musíte nejprve odstranit každý prostředek, který se týkající se brány virtuální sítě. Prostředky musí být odstraněny v určitém pořadí z důvodu závislostí. Při práci s níže uvedenými příklady musí být zadány některé hodnoty, zatímco jiné hodnoty jsou výstupním výsledkem. Pro demonstrační účely používáme následující specifické hodnoty:

Název virtuální sítě: Virtuální síť1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující kroky platí pro model nasazení Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získejte bránu virtuální sítě, kterou chcete odstranit.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Zkontrolujte, zda brána virtuální sítě má nějaká připojení.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Odstraňte všechna připojení.

Můžete být vyzváni k potvrzení odstranění každého z připojení.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Odstraňte bránu virtuální sítě.

Můžete být vyzváni k potvrzení odstranění brány. Pokud máte konfiguraci P2S do této virtuální sítě kromě konfigurace S2S, odstranění brány virtuální sítě automaticky odpojí všechny klienty P2S bez upozornění.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku byla odstraněna brána virtuální sítě. Další kroky můžete použít k odstranění všech prostředků, které se již nepoužívají.

### <a name="5-delete-the-local-network-gateways"></a>5 Odstraňte brány místní sítě.

Získejte seznam odpovídajících bran místní sítě.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Odstraňte brány místní sítě. Můžete být vyzváni k potvrzení odstranění každé brány místní sítě.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Odstraňte prostředky veřejné IP adresy.

Získejte konfigurace IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam prostředků veřejné IP adresy použitých pro tuto bránu virtuální sítě. Pokud brána virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte prostředky veřejné IP adresy.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Odstraňte podsíť brány a nastavte konfiguraci.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Odstranění brány VPN virtuální sítě k virtuální síti

Chcete-li odstranit bránu virtuální sítě pro konfiguraci V2V, musíte nejprve odstranit každý prostředek, který se týkající se brány virtuální sítě. Prostředky musí být odstraněny v určitém pořadí z důvodu závislostí. Při práci s níže uvedenými příklady musí být zadány některé hodnoty, zatímco jiné hodnoty jsou výstupním výsledkem. Pro demonstrační účely používáme následující specifické hodnoty:

Název virtuální sítě: Virtuální síť1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující kroky platí pro model nasazení Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získejte bránu virtuální sítě, kterou chcete odstranit.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Zkontrolujte, zda brána virtuální sítě má nějaká připojení.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Mohou existovat další připojení k bráně virtuální sítě, které jsou součástí jiné skupiny prostředků. Zkontrolujte další připojení v každé další skupině prostředků. V tomto příkladu kontrolujeme připojení z RG2. Spusťte tuto možnost pro každou skupinu prostředků, která může mít připojení k bráně virtuální sítě.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Získejte seznam spojů v obou směrech.

Vzhledem k tomu, že se jedná o konfiguraci virtuální sítě k virtuální síti, potřebujete seznam připojení v obou směrech.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
V tomto příkladu kontrolujeme připojení z RG2. Spusťte tuto možnost pro každou skupinu prostředků, která může mít připojení k bráně virtuální sítě.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Odstraňte všechna připojení.

Můžete být vyzváni k potvrzení odstranění každého z připojení.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Odstraňte bránu virtuální sítě.

Můžete být vyzváni k potvrzení odstranění brány virtuální sítě. Pokud máte konfigurace P2S do virtuálních sítí kromě konfigurace V2V, odstranění brány virtuální sítě automaticky odpojí všechny klienty P2S bez upozornění.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku byla odstraněna brána virtuální sítě. Další kroky můžete použít k odstranění všech prostředků, které se již nepoužívají.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Odstranit prostředky veřejné IP adresy

Získejte konfigurace IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam prostředků veřejné IP adresy použitých pro tuto bránu virtuální sítě. Pokud brána virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte prostředky veřejné IP adresy. Můžete být vyzváni k potvrzení odstranění veřejné ip adresy.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Odstraňte podsíť brány a nastavte konfiguraci.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Odstranění brány VPN bodu na web

Chcete-li odstranit bránu virtuální sítě pro konfiguraci P2S, musíte nejprve odstranit každý prostředek, který se týkající se brány virtuální sítě. Prostředky musí být odstraněny v určitém pořadí z důvodu závislostí. Při práci s níže uvedenými příklady musí být zadány některé hodnoty, zatímco jiné hodnoty jsou výstupním výsledkem. Pro demonstrační účely používáme následující specifické hodnoty:

Název virtuální sítě: Virtuální síť1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující kroky platí pro model nasazení Resource Manager.


>[!NOTE]
> Když odstraníte bránu VPN, všichni připojení klienti budou odpojeni od virtuální sítě bez upozornění.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získejte bránu virtuální sítě, kterou chcete odstranit.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Odstraňte bránu virtuální sítě.

Můžete být vyzváni k potvrzení odstranění brány virtuální sítě.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku byla odstraněna brána virtuální sítě. Další kroky můžete použít k odstranění všech prostředků, které se již nepoužívají.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Odstranit prostředky veřejné IP adresy

Získejte konfigurace IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam veřejných IP adres používaných pro tuto bránu virtuální sítě. Pokud brána virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte veřejné IP adresy. Můžete být vyzváni k potvrzení odstranění veřejné ip adresy.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Odstraňte podsíť brány a nastavte konfiguraci.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Odstranění brány VPN odstraněním skupiny prostředků

Pokud se neobáváte zachování žádného z prostředků ve skupině prostředků a chcete začít znovu, můžete odstranit celou skupinu prostředků. To je rychlý způsob, jak odstranit všechno. Následující kroky platí pouze pro model nasazení Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Získejte seznam všech skupin prostředků v předplatném.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Vyhledejte skupinu prostředků, kterou chcete odstranit.

Vyhledejte skupinu prostředků, kterou chcete odstranit, a zobrazte seznam prostředků v této skupině prostředků. V příkladu je název skupiny prostředků RG1. Upravte příklad a načtěte seznam všech prostředků.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Ověřte zdroje v seznamu.

Po vrácení seznamu jej zkontrolujte a ověřte, zda chcete odstranit všechny prostředky ve skupině prostředků i samotnou skupinu prostředků. Pokud chcete zachovat některé prostředky ve skupině prostředků, použijte kroky v předchozích částech tohoto článku k odstranění brány.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Odstraňte skupinu prostředků a prostředky.

Chcete-li odstranit skupinu prostředků a všechny prostředky obsažené ve skupině prostředků, upravte příklad a spusťte.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Zkontrolujte stav.

Trvá nějakou dobu pro Azure odstranit všechny prostředky. Stav skupiny prostředků můžete zkontrolovat pomocí této rutiny.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Výsledek, který je vrácena zobrazí 'Succeeded'.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
