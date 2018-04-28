---
title: 'Odstranit bránu virtuální sítě: prostředí PowerShell: Azure Resource Manager | Microsoft Docs'
description: Odstraňte bránu virtuální sítě pomocí prostředí PowerShell v modelu nasazení Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: cherylmc
ms.openlocfilehash: a23a969f1381e3a10c81a903793bad2870b436f6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Odstranit bránu virtuální sítě pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Existuje několik způsobů, které můžete provést, pokud chcete odstranit bránu virtuální sítě pro konfiguraci brány VPN.

- Pokud chcete odstranit vše a začít od začátku, jako v případě testovacím prostředí, můžete odstranit skupinu prostředků. Pokud odstraníte skupinu prostředků, odstraní všechny prostředky ve skupině. Toto je metoda se doporučuje jenom Pokud nechcete, aby zachovat prostředky ve skupině prostředků. Nelze odstranit selektivně pouze několik prostředků pomocí tohoto přístupu.

- Pokud chcete zachovat některé prostředky ve vaší skupině prostředků, odstraňuje se Brána virtuální sítě je něco víc složité. Než budete moct odstranit bránu virtuální sítě, musíte nejprve odstranit všechny prostředky, které jsou závislé na bráně. Kroky, které budete postupovat podle závisí na typu připojení, které jste vytvořili a závislé prostředky pro každé připojení.

## <a name="before-beginning"></a>Před zahájením

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Stáhněte si nejnovější rutiny Powershellu pro Azure Resource Manager.

Stáhněte a nainstalujte nejnovější verzi rutin Powershellu pro Azure Resource Manager. Další informace o stažení a instalaci rutin prostředí PowerShell najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Připojte k účtu Azure.

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzureRmAccount
```

Zkontrolujte předplatná pro příslušný účet.

```powershell
Get-AzureRmSubscription
```

Pokud máte více než jedno předplatné, určete předplatné, které chcete použít.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Odstranit bránu Site-to-Site VPN

Pokud chcete odstranit bránu virtuální sítě pro konfiguraci S2S, musíte nejprve odstranit všechny prostředky, které se vztahují na bránu virtuální sítě. V určitém pořadí závislé položky musí dojít k odstranění prostředků. Při práci s příklady níže, některé musí být hodnoty zadané při další hodnoty jsou výsledek výstup. Můžeme použít následující konkrétní hodnoty v příkladech pro demonstrační účely:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje k modelu nasazení Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získáte bránu virtuální sítě, který chcete odstranit.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Zkontrolujte, jestli Brána virtuální sítě má všechna připojení.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Odstraňte všechna připojení.

Zobrazí se výzva k potvrzení odstranění jednotlivých připojení.

```powershell
$Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Odstraňte bránu virtuální sítě.

Zobrazí se výzva k potvrzení odstranění brány. Pokud máte kromě konfiguraci S2S P2S konfigurace do této virtuální sítě, odstraňuje se Brána virtuální sítě automaticky odpojte všechny klienty P2S bez upozornění.


```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku je Odstraněná brány virtuální sítě. Další kroky můžete odstranit všechny prostředky, které se již nepoužívají.

### <a name="5-delete-the-local-network-gateways"></a>5 odstranit brány místní sítě.

Získejte seznam odpovídající brány místní sítě.

```powershell
$LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Odstraňte brány místní sítě. Zobrazí se výzva k potvrzení odstranění jednotlivých bránu místní sítě.

```powershell
$LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Odstraňte veřejnou IP adresu prostředky.

Získáte konfiguraci IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam veřejnou IP adresu prostředky používané pro tuto bránu virtuální sítě. Pokud brána virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte veřejnou IP adresu prostředky.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Odstraňte podsíť brány a nastavit konfiguraci.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Odstranit bránu VPN VNet-to-VNet

Pokud chcete odstranit bránu virtuální sítě pro konfiguraci V2V, musíte nejprve odstranit všechny prostředky, které se vztahují na bránu virtuální sítě. V určitém pořadí závislé položky musí dojít k odstranění prostředků. Při práci s příklady níže, některé musí být hodnoty zadané při další hodnoty jsou výsledek výstup. Můžeme použít následující konkrétní hodnoty v příkladech pro demonstrační účely:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje k modelu nasazení Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získáte bránu virtuální sítě, který chcete odstranit.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Zkontrolujte, jestli Brána virtuální sítě má všechna připojení.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Mohou existovat další připojení k bráně virtuální sítě, které jsou součástí jiné skupině prostředků. Zkontrolujte pro další připojení v každé skupině dalších prostředků. V tomto příkladu jsme se kontroluje připojení z RG2. Spustit pro každou skupinu prostředků, abyste měli, která může mít připojení k bráně virtuální sítě.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Získejte seznam připojení v obou směrech.

Protože to je konfigurace propojení VNet-to-VNet, je třeba seznam připojení v obou směrech.

```powershell
$ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
V tomto příkladu jsme se kontroluje připojení z RG2. Spustit pro každou skupinu prostředků, abyste měli, která může mít připojení k bráně virtuální sítě.

```powershell
 $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Odstraňte všechna připojení.

Zobrazí se výzva k potvrzení odstranění jednotlivých připojení.

```powershell
$ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Odstraňte bránu virtuální sítě.

Zobrazí se výzva k potvrzení odstranění brány virtuální sítě. Pokud máte P2S konfigurace do vaší virtuální sítě kromě konfiguraci V2V, odstranění brány virtuální sítě automaticky odpojte všechny klienty P2S bez upozornění.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku je Odstraněná brány virtuální sítě. Další kroky můžete odstranit všechny prostředky, které se již nepoužívají.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Odstranit veřejnou IP adresu prostředky

Získáte konfiguraci IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam veřejnou IP adresu prostředky používané pro tuto bránu virtuální sítě. Pokud brána virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte veřejnou IP adresu prostředky. Zobrazí se výzva k potvrzení odstranění veřejné IP adresy.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Odstraňte podsíť brány a nastavit konfiguraci.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Odstranit bránu VPN typu Point-to-Site

Pokud chcete odstranit bránu virtuální sítě pro konfiguraci P2S, musíte nejprve odstranit všechny prostředky, které se vztahují na bránu virtuální sítě. V určitém pořadí závislé položky musí dojít k odstranění prostředků. Při práci s příklady níže, některé musí být hodnoty zadané při další hodnoty jsou výsledek výstup. Můžeme použít následující konkrétní hodnoty v příkladech pro demonstrační účely:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje k modelu nasazení Resource Manager.


>[!NOTE]
> Pokud bránu VPN odstraníte, všechny připojené klienty bude odpojen od sítě VNet bez upozornění.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získáte bránu virtuální sítě, který chcete odstranit.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Odstraňte bránu virtuální sítě.

Zobrazí se výzva k potvrzení odstranění brány virtuální sítě.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku je Odstraněná brány virtuální sítě. Další kroky můžete odstranit všechny prostředky, které se již nepoužívají.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Odstranit veřejnou IP adresu prostředky

Získáte konfiguraci IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam veřejné IP adresy použít pro tuto bránu virtuální sítě. Pokud brána virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte veřejné IP adresy. Zobrazí se výzva k potvrzení odstranění veřejné IP adresy.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Odstraňte podsíť brány a nastavit konfiguraci.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Odstraňte bránu VPN odstraníte skupinu prostředků

Pokud si nejste zajímá zachování některé z vašich prostředků ve skupině prostředků a chcete začít znovu, můžete odstranit celé skupiny prostředků. To je rychlý způsob, jak odebrat vše. Následující postup se vztahuje pouze na modelu nasazení Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Získáte seznam všech skupin prostředků v rámci vašeho předplatného.

```powershell
Get-AzureRmResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Najděte skupinu prostředků, který chcete odstranit.

Najděte skupinu prostředků, která chcete k odstranění a zobrazení seznamu prostředků v příslušné skupině prostředků. V příkladu je název skupiny prostředků RG1. Upravte v příkladu se načíst seznam všech prostředků.

```powershell
Find-AzureRmResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Zkontrolujte prostředky v seznamu.

Pokud se vrátí seznam, zkontrolujte a ověřit, že chcete odstranit všechny prostředky ve skupině prostředků a také samotnou skupinu prostředků. Pokud chcete zachovat některé prostředky ve skupině prostředků, použijte kroky v předchozích částech tohoto článku se odstranit bránu.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Odstraňte skupinu prostředků a prostředky.

Pokud chcete odstranit skupinu prostředků a všechny prostředky, které jsou obsažené ve skupině prostředků, upravte v příkladu a spustit.

```powershell
Remove-AzureRmResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Zkontrolujte stav.

Jak dlouho trvá delší dobu pro Azure. Chcete-li odstranit všechny prostředky. Pomocí této rutiny můžete zkontrolovat stav vaší skupiny prostředků.

```powershell
Get-AzureRmResourceGroup -ResourceGroupName RG1
```

Výsledek, který je vrácen ukazuje 'Úspěch'.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
