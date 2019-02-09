---
title: Vytvoření virtuálního počítače s Windows a SQL Serverem pomocí Azure PowerShellu | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit virtuální počítač se systémy Windows a SQL Server 2017 v prostředí Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 24dfc9602f7329b4ea56db2257f29f5711510d22
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977792"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Rychlý start: Vytvoření virtuálního počítače se systémy Windows a SQL Server v prostředí Azure PowerShell

Tento rychlý start vás provede vytvořením virtuálního počítače s SQL Serverem v prostředí Azure PowerShell.

> [!TIP]
> Tento rychlý start obsahuje postup pro rychlé zřízení virtuálního počítače SQL a připojení k němu. Další informace o jiných možnostech vytváření virtuálních počítačů SQL pomocí Azure PowerShellu najdete v tématu [Průvodce zřizováním virtuálních počítačů s SQL Serverem pomocí Azure PowerShellu](virtual-machines-windows-ps-sql-create.md).

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a>Získání předplatného Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a id="powershell"></a>Získání Azure PowerShellu

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Konfigurace prostředí PowerShell

1. Otevřete PowerShell a navažte přístup ke svému účtu spuštěním **připojit AzAccount** příkazu.

   ```PowerShell
   Connect-AzAccount
   ```

1. Měli byste vidět obrazovku pro zadání přihlašovacích údajů. Použijte stejný e-mail a heslo, pomocí kterých se přihlašujete na webu Azure Portal.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Definujte proměnnou s jedinečným názvem skupiny prostředků. Pro zjednodušení zbývající části tohoto rychlého startu, Zbývající příkazy tento název použít jako základ pro ostatní názvy prostředků.

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. Zadejte umístění cílové oblasti Azure pro všechny prostředky virtuálních počítačů.

   ```PowerShell
   $Location = "East US"
   ```

1. Vytvořte skupinu prostředků.

   ```PowerShell
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

1. Vytvořte skupinu zabezpečení sítě. Konfigurujte pravidla, která povolí připojení ke vzdálené ploše (RDP) a SQL serveru.

   ```PowerShell
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

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Vytvoření virtuálního počítače pro SQL

1. Definujte přihlašovací údaje pro přihlášení k virtuálnímu počítači. Uživatelské jméno bude "azureadmin". Ujistěte se, že změníte \<heslo > před spuštěním příkazu.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Vytvořte objekt konfigurace pro virtuální počítač a pak vytvořte virtuální počítač. Následující příkaz vytvoří virtuální počítač se systémem SQL Server 2017 Developer Edition na Windows Serveru 2016.

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 | `
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Vytvoření virtuálního počítače trvá několik minut.

## <a name="install-the-sql-iaas-agent"></a>Instalace agenta SQL IaaS

Chcete-li získat funkce integrace portálu a virtuálního počítače SQL, je nutné nainstalovat [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Pokud chcete nainstalovat agenta k novému virtuálnímu počítači, spusťte následující příkaz, po vytvoření virtuálního počítače.

   ```PowerShell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Připojení k virtuálnímu počítači pomocí Vzdálené plochy

1. Použijte následující příkaz pro načtení veřejnou IP adresu nového virtuálního počítače.

   ```PowerShell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Předat jako parametr příkazového řádku pro tuto IP adresu **mstsc** spustit relaci vzdálené plochy nového virtuálního počítače.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Po zobrazení výzvy k zadání přihlašovacích údajů vyberte zadání přihlašovacích údajů k jinému účtu. Zadejte uživatelské jméno se zpětným lomítkem na začátku (například `\azureadmin`) a heslo, které jste nastavili dříve v tomto rychlém startu.

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

1. Po přihlášení k relaci vzdálené plochy spusťte **SQL Server Management Studio 2017** z nabídky start.

1. V **připojit k serveru** dialogového okna nechte výchozí hodnoty. Název serveru je shodný s názvem virtuálního počítače. Ověřování je nastavené na **Ověřování systému Windows**. Vyberte **Connect** (Připojit).

Jste teď připojeni k serveru SQL Server místně. Pokud chcete připojit vzdáleně, je nutné [konfigurace připojení k](virtual-machines-windows-sql-connect.md) z portálu nebo ručně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nepotřebujete, virtuálnímu počítači spouštět nepřetržitě, byste se vyhnout zbytečným poplatkům? zastavte ho, když se nepoužívá. Následující příkaz zastaví virtuální počítač, ale ponechá ho k dispozici pro budoucí použití.

```PowerShell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Můžete také trvale odstranit všechny prostředky přidružené k virtuálnímu počítači **odebrat AzResourceGroup** příkazu. Tím trvale odstraní virtuální počítač, proto tento příkaz používejte s opatrností.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili virtuální počítač se systémem SQL Server 2017 pomocí prostředí Azure PowerShell. Další informace o tom, jak na tento nový SQL Server migrovat data, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Migrace databáze na virtuální počítač s SQL](virtual-machines-windows-migrate-sql.md)
