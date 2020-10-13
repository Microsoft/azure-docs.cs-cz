---
title: 'Azure VPN Gateway: odstranění brány: PowerShell'
description: Odstraňte bránu virtuální sítě pomocí prostředí PowerShell v modelu nasazení Správce prostředků.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 09/03/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 48e0998719ea19c0b360b50d8c9171d2b448ad19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440758"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Odstranění brány virtuální sítě pomocí PowerShellu
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Existuje několik různých přístupů, které můžete provést, pokud chcete odstranit bránu virtuální sítě pro konfiguraci služby VPN Gateway.

- Pokud chcete odstranit vše a začít znovu, jako v případě testovacího prostředí můžete odstranit skupinu prostředků. Když odstraníte skupinu prostředků, odstraní se všechny prostředky v rámci této skupiny. Tato metoda se doporučuje jenom v případě, že nechcete uchovávat žádné prostředky ve skupině prostředků. Pomocí tohoto přístupu nemůžete selektivně odstranit jenom pár prostředků.

- Pokud chcete zachovat některé prostředky ve vaší skupině prostředků, bude odstranění brány virtuální sítě poněkud složitější. Než budete moct odstranit bránu virtuální sítě, musíte nejdřív odstranit všechny prostředky, které jsou na bráně závislé. Postup závisí na typu připojení, která jste vytvořili, a závislých prostředcích pro každé připojení.

## <a name="before-beginning"></a>Před zahájením



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Stáhněte nejnovější rutiny prostředí PowerShell pro Azure Resource Manager.

Stáhněte si a nainstalujte nejnovější verzi rutin Azure Resource Manager PowerShellu. Další informace o stažení a instalaci rutin PowerShellu najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

### <a name="2-connect-to-your-azure-account"></a>2. Připojte se k účtu Azure.

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

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Odstranění brány VPN typu Site-to-site

Pokud chcete odstranit bránu virtuální sítě pro konfiguraci S2S, musíte nejdřív odstranit všechny prostředky, které se vztahují k bráně virtuální sítě. Prostředky je nutné z důvodu závislostí odstranit v určitém pořadí. Při práci s příklady níže je nutné zadat některé hodnoty, zatímco jiné hodnoty jsou výsledkem výstupu. V příkladech pro demonstrační účely používáme následující konkrétní hodnoty:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje na model nasazení Správce prostředků.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získejte bránu virtuální sítě, kterou chcete odstranit.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Zkontrolujte, jestli má brána virtuální sítě nějaká připojení.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Odstraňte všechna připojení.

Může se zobrazit výzva k potvrzení odstranění každého připojení.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Odstraňte bránu virtuální sítě.

Může se zobrazit výzva k potvrzení odstranění brány. Pokud k této virtuální síti kromě konfigurace S2S máte P2S konfiguraci, při odstranění brány virtuální sítě se automaticky odpojí všichni klienti P2S bez upozornění.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tuto chvíli se vaše brána virtuální sítě odstranila. Další kroky můžete použít k odstranění všech prostředků, které se už nepoužívají.

### <a name="5-delete-the-local-network-gateways"></a>5 Odstraňte brány místní sítě.

Získejte seznam odpovídajících bran místní sítě.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Odstraňte brány místní sítě. Může se zobrazit výzva k potvrzení odstranění každé brány místní sítě.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Odstraňte prostředky veřejné IP adresy.

Získejte konfigurace IP adresy brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam prostředků veřejné IP adresy, které se používají pro tuto bránu virtuální sítě. Pokud byla brána virtuální sítě aktivní – aktivní, zobrazí se dvě veřejné IP adresy.

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

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Odstranění brány VPN typu VNet-to-VNet

Pokud chcete odstranit bránu virtuální sítě pro konfiguraci V2V, musíte nejdřív odstranit všechny prostředky, které se vztahují k bráně virtuální sítě. Prostředky je nutné z důvodu závislostí odstranit v určitém pořadí. Při práci s příklady níže je nutné zadat některé hodnoty, zatímco jiné hodnoty jsou výsledkem výstupu. V příkladech pro demonstrační účely používáme následující konkrétní hodnoty:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje na model nasazení Správce prostředků.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Získejte bránu virtuální sítě, kterou chcete odstranit.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Zkontrolujte, jestli má brána virtuální sítě nějaká připojení.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
K bráně virtuální sítě můžou existovat další připojení, která jsou součástí jiné skupiny prostředků. Podívejte se na další připojení v každé další skupině prostředků. V tomto příkladu kontrolujeme připojení z RG2. Tuto rutinu spusťte pro každou skupinu prostředků, kterou máte s připojením k bráně virtuální sítě.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Získejte seznam připojení v obou směrech.

Vzhledem k tomu, že se jedná o konfiguraci VNet-to-VNet, budete potřebovat seznam připojení v obou směrech.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
V tomto příkladu kontrolujeme připojení z RG2. Tuto rutinu spusťte pro každou skupinu prostředků, kterou máte s připojením k bráně virtuální sítě.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Odstraňte všechna připojení.

Může se zobrazit výzva k potvrzení odstranění každého připojení.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Odstraňte bránu virtuální sítě.

Může se zobrazit výzva k potvrzení odstranění brány virtuální sítě. Pokud máte kromě konfigurace V2V P2S konfigurace virtuální sítě, odstranění bran virtuální sítě automaticky odpojí všechny klienty P2S bez upozornění.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

V tuto chvíli se vaše brána virtuální sítě odstranila. Další kroky můžete použít k odstranění všech prostředků, které se už nepoužívají.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Odstraňte prostředky veřejné IP adresy.

Získejte konfigurace IP adresy brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam prostředků veřejné IP adresy, které se používají pro tuto bránu virtuální sítě. Pokud byla brána virtuální sítě aktivní – aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte prostředky veřejné IP adresy. Může se zobrazit výzva k potvrzení odstranění veřejné IP adresy.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Odstraňte podsíť brány a nastavte konfiguraci.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Odstranění brány VPN typu Point-to-site

Pokud chcete odstranit bránu virtuální sítě pro konfiguraci P2S, musíte nejdřív odstranit všechny prostředky, které se vztahují k bráně virtuální sítě. Prostředky je nutné z důvodu závislostí odstranit v určitém pořadí. Při práci s příklady níže je nutné zadat některé hodnoty, zatímco jiné hodnoty jsou výsledkem výstupu. V příkladech pro demonstrační účely používáme následující konkrétní hodnoty:

Název virtuální sítě: VNet1<br>
Název skupiny prostředků: RG1<br>
Název brány virtuální sítě: GW1<br>

Následující postup se vztahuje na model nasazení Správce prostředků.


>[!NOTE]
> Když bránu VPN odstraníte, všichni připojení klienti se z virtuální sítě odpojí bez upozornění.
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

V tuto chvíli se vaše brána virtuální sítě odstranila. Další kroky můžete použít k odstranění všech prostředků, které se už nepoužívají.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Odstraňte prostředky veřejné IP adresy.

Získejte konfigurace IP adresy brány virtuální sítě.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Získejte seznam veřejných IP adres, které se používají pro tuto bránu virtuální sítě. Pokud byla brána virtuální sítě aktivní – aktivní, zobrazí se dvě veřejné IP adresy.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Odstraňte veřejné IP adresy. Může se zobrazit výzva k potvrzení odstranění veřejné IP adresy.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Odstraňte podsíť brány a nastavte konfiguraci.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Odstranění brány VPN odstraněním skupiny prostředků

Pokud si nejste jisti tím, že si z vašich prostředků ve skupině prostředků zachováte nějaké prostředky a chcete ji jenom začít znovu, můžete odstranit celou skupinu prostředků. To je rychlý způsob, jak odebrat vše. Následující postup se vztahuje pouze na model nasazení Správce prostředků.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Získejte seznam všech skupin prostředků v rámci vašeho předplatného.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Vyhledejte skupinu prostředků, kterou chcete odstranit.

Vyhledejte skupinu prostředků, kterou chcete odstranit, a zobrazte seznam prostředků v této skupině prostředků. V tomto příkladu je název skupiny prostředků RG1. Upravte příklad tak, aby se načetl seznam všech prostředků.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Ověřte prostředky v seznamu.

Až se seznam vrátí, zkontrolujte ho a ověřte, jestli chcete odstranit všechny prostředky ve skupině prostředků, a také vlastní skupinu prostředků. Pokud chcete zachovat některé z prostředků ve skupině prostředků, odstraňte bránu pomocí kroků v předchozích částech tohoto článku.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Odstraňte skupinu prostředků a prostředky.

Pokud chcete odstranit skupinu prostředků a všechny prostředky obsažené ve skupině prostředků, upravte příklad a spusťte příkaz.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Ověřte stav.

V případě, že Azure potřebuje k odstranění všech prostředků nějakou dobu. Pomocí této rutiny můžete zjistit stav skupiny prostředků.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Vrácený výsledek zobrazuje ' úspěch '.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
