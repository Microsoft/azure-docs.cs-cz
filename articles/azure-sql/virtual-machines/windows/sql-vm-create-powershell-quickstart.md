---
title: Vytvoření SQL Server na virtuálním počítači s Windows pomocí Azure PowerShell | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí Azure PowerShell vytvořit virtuální počítač s Windows, na kterém běží SQL Server 2017.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97356816"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>Rychlý Start: vytvoření SQL Server na virtuálním počítači s Windows pomocí Azure PowerShell

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento rychlý Start prochází postup vytvořením virtuálního počítače s SQL Server pomocí Azure PowerShell.

> [!TIP]
> - Tento rychlý start obsahuje postup pro rychlé zřízení virtuálního počítače SQL a připojení k němu. Další informace o jiných možnostech vytváření virtuálních počítačů SQL pomocí Azure PowerShellu najdete v tématu [Průvodce zřizováním virtuálních počítačů s SQL Serverem pomocí Azure PowerShellu](create-sql-vm-powershell.md).
> - Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Získání předplatného Azure

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="get-azure-powershell"></a><a id="powershell"></a>Získání Azure PowerShellu

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Konfigurace prostředí PowerShell

1. Otevřete PowerShell a navažte přístup ke svému účtu Azure spuštěním příkazu **Connect-AzAccount** .

   ```powershell
   Connect-AzAccount
   ```

1. Až se zobrazí okno přihlášení, zadejte svoje přihlašovací údaje. Použijte stejný e-mail a heslo, pomocí kterých se přihlašujete na webu Azure Portal.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Definujte proměnnou s jedinečným názvem skupiny prostředků. Aby bylo možné zjednodušit zbytek rychlého startu, zbývající příkazy používají tento název jako základ pro jiné názvy prostředků.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Zadejte umístění cílové oblasti Azure pro všechny prostředky virtuálních počítačů.

   ```powershell
   $Location = "East US"
   ```

1. Vytvořte skupinu prostředků.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Konfigurace nastavení sítě

1. Vytvořte virtuální síť, podsíť a veřejnou IP adresu. Tyto prostředky slouží k zajištění síťového připojení virtuálnímu počítači a k jeho připojení k internetu.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Vytvoření skupiny zabezpečení sítě Konfigurujte pravidla, která povolí připojení ke vzdálené ploše (RDP) a SQL serveru.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Vytvořte síťové rozhraní.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Vytvoření virtuálního počítače pro SQL

1. Definujte přihlašovací údaje pro přihlášení k virtuálnímu počítači. Uživatelské jméno je "azureadmin". Před spuštěním příkazu se ujistěte, že jste provedli změnu \<password> .

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Vytvořte objekt konfigurace pro virtuální počítač a pak vytvořte virtuální počítač. Následující příkaz vytvoří virtuální počítač se systémem SQL Server 2017 Developer Edition na Windows Serveru 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Vytvoření virtuálního počítače trvá několik minut.

## <a name="register-with-sql-vm-rp"></a>Registrace s využitím SQL VM RP 

Pokud chcete získat funkce integrace portálu a SQL VM, musíte se zaregistrovat s [rozšířením agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md).

Pokud chcete získat plnou funkčnost, budete se muset zaregistrovat s rozšířením v plném režimu. Tím se ale služba SQL Server restartuje, takže doporučeným přístupem je registrace v nenáročném režimu a následná aktualizace na plnou dobu v časovém intervalu pro správu a údržbu. 

Nejdřív Zaregistrujte svůj SQL Server virtuální počítač v režimu prostého zápisu: 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

Během časového období údržby upgradujte na úplný režim: 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>Připojení k virtuálnímu počítači pomocí Vzdálené plochy

1. Pomocí následujícího příkazu načtěte veřejnou IP adresu nového virtuálního počítače.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Předat vrácenou IP adresu jako parametr příkazového řádku pro **mstsc** ke spuštění relace vzdálené plochy do nového virtuálního počítače.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Po zobrazení výzvy k zadání přihlašovacích údajů vyberte zadání přihlašovacích údajů k jinému účtu. Zadejte uživatelské jméno s předchozím zpětným lomítkem (například `\azureadmin` ) a heslo, které jste nastavili dříve v tomto rychlém startu.

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

1. Po přihlášení k relaci vzdálené plochy spusťte v nabídce Start **SQL Server Management Studio 2017** .

1. V dialogovém okně **připojit k serveru** ponechte výchozí nastavení. Název serveru je shodný s názvem virtuálního počítače. Ověřování je nastavené na **Ověřování systému Windows**. Vyberte **Connect** (Připojit).

Nyní jste připojeni k SQL Server místně. Pokud se chcete připojit vzdáleně, musíte [nakonfigurovat připojení](ways-to-connect-to-sql.md) z Azure Portal nebo ručně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nepotřebujete, aby se virtuální počítač spouštěl nepřetržitě, můžete se vyhnout zbytečným poplatkům, když ho zastavíte, když se nepoužívá. Následující příkaz zastaví virtuální počítač, ale ponechá ho k dispozici pro budoucí použití.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Pomocí příkazu **Remove-AzResourceGroup** můžete také trvale odstranit všechny prostředky přidružené k virtuálnímu počítači. Tím se trvale odstraní i virtuální počítač, takže tento příkaz používejte opatrně.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač se systémem SQL Server 2017 pomocí prostředí Azure PowerShell. Další informace o tom, jak na tento nový SQL Server přenést data, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Migrace databáze na virtuální počítač s SQL](migrate-to-vm-from-sql-server.md)
