---
title: Vytvoření privátního koncového bodu Azure pomocí Azure PowerShellu| Dokumenty společnosti Microsoft
description: Další informace o privátním propojení Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430348"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Vytvoření privátního koncového bodu pomocí Azure PowerShellu
Privátní koncový bod je základní stavební blok pro privátní propojení v Azure. Umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikovat s prostředky privátní ho propojení. 

V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač ve virtuální síti Azure, sql database server s privátním koncovým bodem Azure pomocí Azure PowerShellu. Potom můžete bezpečně přistupovat k databázovému serveru SQL z virtuálního účtu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením prostředků je nutné vytvořit skupinu prostředků, která je hostitelem virtuální sítě a privátního koncového bodu s [new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *WestUS:*

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
V této části vytvoříte virtuální síť a podsíť. Dále přidružíte podsíť k virtuální síti.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pro svůj soukromý koncový bod pomocí [new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Přidání podsítě

Azure nasazuje prostředky do podsítě v rámci virtuální sítě, takže je potřeba vytvořit podsíť. Vytvořte konfiguraci podsítě s názvem *mySubnet* pomocí [funkce Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Následující příklad vytvoří podsíť s názvem *mySubnet* s příznakem zásad sítě privátního koncového bodu nastaveným na **Zakázáno**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> Je snadné zaměnit parametr `PrivateEndpointNetworkPoliciesFlag` s jiným příznakem k dispozici , `PrivateLinkServiceNetworkPoliciesFlag`protože jsou obě dlouhá slova a mají podobný vzhled.  Ujistěte se, že používáte ten správný, `PrivateEndpointNetworkPoliciesFlag`.

### <a name="associate-the-subnet-to-the-virtual-network"></a>Přidružení podsítě k virtuální síti

Konfiguraci podsítě můžete zapsat do virtuální sítě pomocí [programu Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Tento příkaz vytvoří podsíť:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač ve virtuální síti s [New-AzVM](/powershell/module/az.compute/new-azvm). Při spuštění dalšího příkazu budete vyzváni k zadání pověření. Zadejte uživatelské jméno a heslo pro virtuální počítače:

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

Tato `-AsJob` možnost vytvoří virtuální ho svitek na pozadí. Můžete pokračovat dalším krokem.

Když Azure začne vytvářet virtuální počítač na pozadí, dostanete něco takového zpět:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Vytvoření databázového serveru SQL 

Vytvořte databázový server SQL pomocí příkazu New-AzSqlServer. Nezapomeňte, že název serveru SQL Database musí být v azure jedinečný, proto zástupnou hodnotu v závorkách nahraďte vlastní jedinečnou hodnotou:

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

Privátní koncový bod pro databázový server SQL ve virtuální síti s [připojením New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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

## <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS 
Vytvořte privátní zónu DNS pro doménu databázového serveru SQL a vytvořte asociační propojení s virtuální sítí: 

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

Pomocí [get-azPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) vrátit veřejnou IP adresu virtuálního počítačů. Tento příklad vrátí veřejnou IP adresu virtuálního virtuálního měna *myVM:*

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Otevřete příkazový řádek v místním počítači. Spusťte příkaz mstsc. Nahradit <publicIpAddress> veřejnou IP adresou vrácenou z posledního kroku: 


> [!NOTE]
> Pokud jste tyto příkazy spouštěli z výzvy prostředí PowerShell v místním počítači a používáte modul Az PowerShell verze 1.0 nebo novější, můžete v tomto rozhraní pokračovat.
```
mstsc /v:<publicIpAddress>
```

1. Pokud se zobrazí výzva, vyberte **Připojit**. 
2. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.
  > [!NOTE]
  > Možná budete muset vybrat další volby > Použijte jiný účet, chcete-li zadat pověření, která jste zadali při vytváření virtuálního účtu. 
  
3. Vyberte **OK**. 
4. Může se zobrazit upozornění na certifikát. Pokud tak učiníte, vyberte **Ano** nebo **Pokračovat**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Přístup k databázovému serveru SQL soukromě z virtuálního serveru

1. Na vzdálené ploše myVM otevřete PowerShell.
2. Zadejte `nslookup myserver.database.windows.net`. 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Instalace aplikace SQL Server Management Studio
4. V pole Připojit k serveru zadejte nebo vyberte tyto informace: Nastavení typu hodnotového serveru Select Database Engine.
      Název serveru Vyberte myserver.database.windows.net Uživatelské jméno Zadejte uživatelské jméno poskytnuté při vytváření.
      Heslo: Zadejte heslo poskytnuté při vytváření.
      Zapamatovat heslo Vyberte Ano.
5. Vyberte Connect (Připojit).
6. Procházet databáze z levé nabídky. 
7. (Volitelně) Vytvoření nebo dotazování informací z databáze
8. Zavřete připojení ke vzdálené ploše *myVM*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Po dokončení používání privátního koncového bodu, databázového serveru SQL a virtuálního serveru [odeberte](/powershell/module/az.resources/remove-azresourcegroup) skupinu prostředků a všechny prostředky, které má:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky
- Další informace o [privátním propojení Azure](private-link-overview.md)
