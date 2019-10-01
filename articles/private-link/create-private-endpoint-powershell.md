---
title: Vytvoření privátního koncového bodu Azure pomocí Azure PowerShell | Microsoft Docs
description: Informace o privátním propojení Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: db0921d709f842b004ec4c23d15a986f2e59ec23
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687082"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Vytvoření privátního koncového bodu pomocí Azure PowerShell
Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení. 

V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač v Virtual Network Azure, SQL Database Server s privátním koncovým bodem Azure pomocí Azure PowerShell. Pak můžete z virtuálního počítače bezpečně přistupovat k serveru SQL Database.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než budete moci vytvořit prostředky, je nutné vytvořit skupinu prostředků, která hostuje Virtual Network a soukromý koncový bod pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *WestUS* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Vytvoření Virtual Network
V této části vytvoříte virtuální síť a podsíť. Potom přidružíte podsíť k vašemu Virtual Network.

### <a name="create-a-virtual-network"></a>Vytvoření Virtual Network

Vytvořte virtuální síť pro privátní koncový bod pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří Virtual Network s názvem *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Přidat podsíť

Azure nasadí prostředky do podsítě v rámci Virtual Network, takže je potřeba vytvořit podsíť. Vytvořte konfiguraci podsítě s názvem *mySubnet* with [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Následující příklad vytvoří podsíť s názvem *mySubnet* s příznakem zásady sítě privátního koncového bodu nastavenou na **disabled (zakázáno**).

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Přidružte podsíť k Virtual Network

Konfiguraci podsítě můžete zapsat do Virtual Network pomocí [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Tento příkaz vytvoří podsíť:

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

Možnost `-AsJob` vytvoří virtuální počítač na pozadí. Můžete pokračovat k dalšímu kroku.

Když Azure začne vytvářet virtuální počítač na pozadí, získáte něco podobného:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Vytvoření serveru SQL Database 

Vytvořte SQL Database Server pomocí příkazu New-AzSqlServer. Pamatujte, že název vašeho serveru SQL Database musí být v rámci Azure jedinečný, proto nahraďte hodnotu zástupného symbolu v závorkách vlastní jedinečnou hodnotou:

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

Soukromý koncový bod pro server SQL Database v Virtual Network pomocí [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Konfigurovat zónu Privátní DNS 
Vytvořte privátní zónu DNS pro doménu serveru SQL Database a vytvořte propojení přidružení s virtuální sítí: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Pomocí [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) To vrátí veřejnou IP adresu virtuálního počítače. Tento příklad vrátí veřejnou IP adresu *myVM* VM:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Otevřete příkazový řádek na místním počítači. Spusťte příkaz mstsc. Nahraďte parametr @ no__t-0 @ no__t-1with veřejnou IP adresou vrácenou z posledního kroku: 


> [!NOTE]
> Pokud jste tyto příkazy spustili z příkazového řádku PowerShellu v místním počítači a používáte modul AZ PowerShell verze 1,0 nebo novější, můžete v tomto rozhraní pokračovat.
```
mstsc /v:<publicIpAddress>
```

1. Po zobrazení výzvy vyberte **připojit**. 
2. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.
  > [!NOTE]
  > Možná budete muset vybrat další volby > použít jiný účet a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
  
3. Vyberte **OK**. 
4. Může se zobrazit upozornění certifikátu. Pokud tak učiníte, vyberte **ano** or **pokračovat**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Přístup k serveru SQL Database soukromě z virtuálního počítače

1. Ve vzdálené ploše myVM otevřete PowerShell.
2. Zadejte hodnotu @ no__t-0. 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Nainstalovat SQL Server Management Studio
4. V připojení k serveru zadejte nebo vyberte tyto informace: nastavení typu hodnoty serveru vyberte možnost databázový stroj.
      Název serveru vyberte myserver.database.windows.net uživatelské jméno zadejte uživatelské jméno zadané během vytváření.
      Heslo zadejte heslo, které jste zadali při vytváření.
      Zapamatování hesla vyberte Ano.
5. Vyberte připojit.
6. Procházet databáze z levé nabídky 
7. Volitelně Vytvoření nebo dotazování informací z MyDatabase
8. Zavřete připojení ke vzdálené ploše pro *myVM*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Po dokončení používání privátního koncového bodu, SQL Database serveru a virtuálního počítače, použijte [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) a odeberte skupinu prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky
- Další informace o [privátním propojení Azure](private-link-overview.md)
