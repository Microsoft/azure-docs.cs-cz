---
title: 'Odstraňte bránu virtuální sítě: PowerShell: Azure Resource Manageru | Dokumentace Microsoftu'
description: Odstraňte bránu virtuální sítě pomocí prostředí PowerShell v modelu nasazení Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.openlocfilehash: 922aa739a42eddbe8cd7e3cabe46681c0c2c6d46
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417070"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Odstranit bránu virtuální sítě pomocí Powershellu
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Existuje několik různých přístupů, které můžete provést, pokud chcete odstranit bránu virtuální sítě pro konfiguraci brány VPN.

- Pokud chcete vymazat úplně všechno a začít znovu, jde například o testovacím prostředí, můžete odstranit skupinu prostředků. Při odstranění skupiny prostředků odstraní všechny prostředky v rámci skupiny. Toto je metoda se doporučuje jen pokud nechcete zachovat prostředky ve skupině prostředků. Nelze odstranit selektivně pouze několik prostředků, pomocí tohoto přístupu.

- Pokud chcete zachovat některé z prostředků ve vaší skupině prostředků, odstraňuje se Brána virtuální sítě se stane něco složitější. Než budete moct odstranit bránu virtuální sítě, musíte nejprve odstranit všechny prostředky, které jsou závislé na bráně. Kroky, které sledujete závisí na typu připojení, které jste vytvořili a všechny závislé prostředky pro každé připojení.

## <a name="before-beginning"></a>Před zahájením

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Stáhněte si nejnovější rutiny Powershellu pro Azure Resource Manager.

Stáhněte a nainstalujte nejnovější verzi rutin Powershellu pro Azure Resource Manager. Další informace o stažení a instalaci rutin Powershellu najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Připojte se ke svému účtu Azure.

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
```

Zkontrolujte předplatná pro příslušný účet.

```powershell
Get-AzSubscription
```

Pokud máte více než jedno předplatné, zadejte předplatné, pro kterou chcete použít.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Odstranění brány VPN typu Site-to-Site

Pokud chcete odstranit bránu virtuální sítě pro konfiguraci S2S, musíte nejprve odstranit jednotlivé prostředky, které se vztahují k bráně virtuální sítě. Musí se odstranit prostředky v určitém pořadí z důvodu závislosti. Při práci se v příkladech níže, některé hodnoty musí být zadána, zatímco jiné hodnoty nejsou výstup výsledku. V příkladech používáme následující konkrétní hodnoty pro demonstrační účely:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje k modelu nasazení Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získejte bránu virtuální sítě, kterou chcete odstranit.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Zkontrolujte, jestli Brána virtuální sítě má všechna připojení.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Odstraňte všechna připojení.

Může se zobrazit výzva k potvrzení odstranění všech připojení.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Odstraňte bránu virtuální sítě.

Může se zobrazit výzva k potvrzení odstranění brány. Pokud máte konfigurace P2S k této virtuální síti kromě konfiguraci S2S, odstraňuje se Brána virtuální sítě automaticky odpojit všechny klienty P2S bez předchozího upozornění.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku brány virtuální sítě se odstranil. Další kroky můžete odstranit všechny prostředky, které jsou již nejsou déle používány.

### <a name="5-delete-the-local-network-gateways"></a>5 odstranění brány místní sítě.

Získá seznam odpovídající brány místní sítě.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Odstranění brány místní sítě. Může se zobrazit výzva k potvrzení odstranění každé bráně místní sítě.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Odstraňte prostředky veřejné IP adresy.

Získání konfigurace protokolu IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získá seznam veřejných IP adres prostředky používané pro tuto bránu virtuální sítě. Pokud bránu virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte prostředky veřejné IP adresy.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Odstraňte podsíť brány a nastavení konfigurace.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Odstranění brány VPN typu VNet-to-VNet

Pokud chcete odstranit bránu virtuální sítě pro konfiguraci V2V, musíte nejprve odstranit jednotlivé prostředky, které se vztahují k bráně virtuální sítě. Musí se odstranit prostředky v určitém pořadí z důvodu závislosti. Při práci se v příkladech níže, některé hodnoty musí být zadána, zatímco jiné hodnoty nejsou výstup výsledku. V příkladech používáme následující konkrétní hodnoty pro demonstrační účely:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje k modelu nasazení Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získejte bránu virtuální sítě, kterou chcete odstranit.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Zkontrolujte, jestli Brána virtuální sítě má všechna připojení.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Můžou existovat další připojení k bráně virtuální sítě, které jsou součástí jiné skupiny prostředků. Zkontrolujte další připojení v každé skupině dalších prostředků. V tomto příkladu kontrolujeme pro připojení z RG2. Spustit pro každou skupinu prostředků, abyste měli, který může mít připojení k bráně virtuální sítě.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Získá seznam připojení v obou směrech.

Protože toto je konfigurace připojení typu VNet-to-VNet, je třeba seznam připojení v obou směrech.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
V tomto příkladu kontrolujeme pro připojení z RG2. Spustit pro každou skupinu prostředků, abyste měli, který může mít připojení k bráně virtuální sítě.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Odstraňte všechna připojení.

Může se zobrazit výzva k potvrzení odstranění všech připojení.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Odstraňte bránu virtuální sítě.

Může se zobrazit výzva k potvrzení odstranění brány virtuální sítě. Pokud máte konfigurace P2S do vašich virtuálních sítí kromě V2V konfigurace, odstranění brány virtuální sítě automaticky odpojit všechny klienty P2S bez předchozího upozornění.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku brány virtuální sítě se odstranil. Další kroky můžete odstranit všechny prostředky, které jsou již nejsou déle používány.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Odstranit prostředky veřejné IP adresy

Získání konfigurace protokolu IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získá seznam veřejných IP adres prostředky používané pro tuto bránu virtuální sítě. Pokud bránu virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte prostředky veřejné IP adresy. Může se zobrazit výzva k potvrzení odstranění veřejné IP adresy.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Odstraňte podsíť brány a nastavení konfigurace.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Odstranění brány VPN typu Point-to-Site

Pokud chcete odstranit bránu virtuální sítě pro konfigurace P2S, musíte nejprve odstranit jednotlivé prostředky, které se vztahují k bráně virtuální sítě. Musí se odstranit prostředky v určitém pořadí z důvodu závislosti. Při práci se v příkladech níže, některé hodnoty musí být zadána, zatímco jiné hodnoty nejsou výstup výsledku. V příkladech používáme následující konkrétní hodnoty pro demonstrační účely:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje k modelu nasazení Resource Manager.


>[!NOTE]
> Když bránu VPN odstraníte, všechny připojené klienty se odpojí z virtuální sítě bez předchozího upozornění.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získejte bránu virtuální sítě, kterou chcete odstranit.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Odstraňte bránu virtuální sítě.

Může se zobrazit výzva k potvrzení odstranění brány virtuální sítě.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tomto okamžiku brány virtuální sítě se odstranil. Další kroky můžete odstranit všechny prostředky, které jsou již nejsou déle používány.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Odstranit prostředky veřejné IP adresy

Získání konfigurace protokolu IP brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získá seznam veřejné IP adresy používané pro tuto bránu virtuální sítě. Pokud bránu virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte veřejné IP adresy. Může se zobrazit výzva k potvrzení odstranění veřejné IP adresy.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Odstraňte podsíť brány a nastavení konfigurace.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Odstranění brány VPN tak, že odstraníte skupinu prostředků

Pokud nejste obavy o zachování všech vašich prostředků ve skupině prostředků a chcete začít, můžete odstranit celou skupinu prostředků. Toto je rychlý způsob, jak odebrat všechno, co. Následující postup se vztahuje pouze na modelu nasazení Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Získání seznamu všech skupin prostředků ve vašem předplatném.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Vyhledejte skupinu prostředků, který chcete odstranit.

Vyhledejte skupinu prostředků, kterou chcete odstranit a zobrazit seznam prostředků v příslušné skupině prostředků. V tomto příkladu je název skupiny prostředků RG1. Upravte příklad tak načíst seznam všech prostředků.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Zkontrolujte prostředky v seznamu.

Pokud je vrácen seznam, projděte si ověřit, že chcete odstranit všechny prostředky ve skupině prostředků, jakož i samotnou skupinu prostředků. Pokud chcete zachovat některé z prostředků ve skupině prostředků, postupujte podle kroků v předchozí části tohoto článku se odstranit bránu.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Odstraňte skupinu prostředků a prostředky.

Pokud chcete odstranit skupinu prostředků a všech prostředků obsažených ve skupině prostředků, upravit a spustit.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Zkontrolujte stav.

Odstraňte všechny prostředky Azure nějakou dobu trvá. Pomocí této rutiny můžete zkontrolovat stav vaší skupiny prostředků.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Výsledek, který je vrácen ukazuje "Úspěšně dokončeno".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
