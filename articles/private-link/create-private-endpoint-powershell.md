---
title: Vytvoření privátního koncového bodu Azure pomocí Azure PowerShell | Microsoft Docs
description: Informace o privátním propojení Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 0c6fc36be101679cea3a770f311005f63c3f0d66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84737372"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Vytvoření privátního koncového bodu pomocí Azure PowerShell
Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení. 

V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač v Virtual Network Azure, což je logický SQL Server s privátním koncovým bodem Azure pomocí Azure PowerShell. Pak můžete bezpečně přistupovat k SQL Database z virtuálního počítače.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než budete moci vytvořit prostředky, je nutné vytvořit skupinu prostředků, která hostuje Virtual Network a soukromý koncový bod pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *WestUS* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
V této části vytvoříte virtuální síť a podsíť. Potom přidružíte podsíť k vašemu Virtual Network.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pro privátní koncový bod pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří Virtual Network s názvem *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Přidat podsíť

Azure nasadí prostředky do podsítě v rámci Virtual Network, takže je potřeba vytvořit podsíť. Vytvořte konfiguraci podsítě s názvem *mySubnet* pomocí [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Následující příklad vytvoří podsíť s názvem *mySubnet* s příznakem zásady sítě privátního koncového bodu nastavenou na **disabled (zakázáno**).

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> Je snadné Zaměňujte `PrivateEndpointNetworkPoliciesFlag` parametr s jiným dostupným příznakem, `PrivateLinkServiceNetworkPoliciesFlag` protože jsou to dlouhá slova a mají podobný vzhled.  Ujistěte se, že používáte tu správnou `PrivateEndpointNetworkPoliciesFlag` .

### <a name="associate-the-subnet-to-the-virtual-network"></a>Přidružte podsíť k Virtual Network

Konfiguraci podsítě můžete zapsat do Virtual Network pomocí [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Tento příkaz vytvoří podsíť:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Vytvořit virtuální počítač

Vytvořte virtuální počítač v Virtual Network pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Po spuštění dalšího příkazu budete vyzváni k zadání přihlašovacích údajů. Zadejte uživatelské jméno a heslo pro virtuální počítač:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

`-AsJob`Možnost vytvoří virtuální počítač na pozadí. Můžete pokračovat k dalšímu kroku.

Když Azure začne vytvářet virtuální počítač na pozadí, získáte něco podobného:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-logical-sql-server"></a>Vytvoření logického SQL serveru 

Pomocí příkazu New-AzSqlServer vytvořte logický SQL Server. Pamatujte, že název vašeho serveru musí být v rámci Azure jedinečný, proto nahraďte hodnotu zástupného symbolu v závorkách vlastní jedinečnou hodnotou:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

Privátní koncový bod pro server v Virtual Network pomocí [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Konfigurovat zónu Privátní DNS 
Vytvořte zónu Privátní DNS pro SQL Database doménu, vytvořte propojení přidružení s Virtual Network a vytvořte skupinu zón DNS pro přidružení privátního koncového bodu k Privátní DNS zóně.

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  

$config = New-AzPrivateDnsZoneConfig -Name "privatelink.database.windows.net" -PrivateDnsZoneId $zone.ResourceId

$privateDnsZoneGroup = New-AzPrivateDnsZoneGroup -ResourceGroupName "myResourceGroup" `
 -PrivateEndpointName "myPrivateEndpoint" -name "MyZoneGroup" -PrivateDnsZoneConfig $config
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

K vrácení veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) . Tento příklad vrátí veřejnou IP adresu virtuálního počítače *myVM* :

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Otevřete příkazový řádek na místním počítači. Spusťte příkaz mstsc. Nahraďte <publicIpAddress> veřejnou IP adresou vrácenou z posledního kroku: 


> [!NOTE]
> Pokud jste tyto příkazy spustili z příkazového řádku PowerShellu v místním počítači a používáte modul AZ PowerShell verze 1,0 nebo novější, můžete v tomto rozhraní pokračovat.
```
mstsc /v:<publicIpAddress>
```

1. Pokud se zobrazí výzva, vyberte **Připojit**. 
2. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.
  > [!NOTE]
  > Možná budete muset vybrat další volby > použít jiný účet a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
  
3. Vyberte **OK**. 
4. Může se zobrazit upozornění certifikátu. Pokud ano, vyberte **Ano** nebo **pokračovat**. 

## <a name="access-sql-database-privately-from-the-vm"></a>Přístup k SQL Database soukromě z virtuálního počítače

1. Na vzdálené ploše virtuálního počítače myVM otevřete PowerShell.
2. Zadejte `nslookup myserver.database.windows.net`. Nezapomeňte nahradit `myserver` názvem SQL serveru.

    Zobrazí se zpráva podobná této:
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. Nainstalujte [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).
4. V **Connect to Server (připojit k serveru**) zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | --- | --- |
    | Typ serveru | Databázový stroj |
    | Název serveru | myserver.database.windows.net |
    | Uživatelské jméno | Zadejte uživatelské jméno, které jste zadali při vytváření. |
    | Heslo | Zadejte heslo, které jste zadali při vytváření. |
    | Zapamatovat heslo | Yes |
    
5. Vyberte **Připojit**.
6. V nabídce vlevo vyberte **databáze** . 
7. Volitelně Vytvoření nebo dotazování informací z MyDatabase.
8. Zavřete připojení ke vzdálené ploše pro *myVM*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete s použitím privátního koncového bodu SQL Database a virtuálního počítače, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky
- Další informace o [privátním propojení Azure](private-link-overview.md)
