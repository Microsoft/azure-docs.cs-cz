---
title: Vytvoření virtuálního počítače s Windows a SQL Serverem pomocí Azure PowerShellu | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit virtuální počítač se systémy Windows a SQL Server 2017 v prostředí Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8994079cf18a9af5f5e1368761015bbd8b836bd9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74790911"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Rychlý start: Vytvoření virtuálního počítače s Windows a SQL Serverem pomocí Azure PowerShellu

Tento rychlý start vás provede vytvořením virtuálního počítače s SQL Serverem v prostředí Azure PowerShell.

> [!TIP]
> - Tento rychlý start obsahuje postup pro rychlé zřízení virtuálního počítače SQL a připojení k němu. Další informace o jiných možnostech vytváření virtuálních počítačů SQL pomocí Azure PowerShellu najdete v tématu [Průvodce zřizováním virtuálních počítačů s SQL Serverem pomocí Azure PowerShellu](virtual-machines-windows-ps-sql-create.md).
> - Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Získání předplatného Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="get-azure-powershell"></a><a id="powershell"></a>Získání Azure PowerShellu

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Konfigurace prostředí PowerShell

1. Otevřete PowerShell a navažte přístup ke svému účtu Azure spuštěním příkazu **Connect-AzAccount** .

   ```powershell
   Connect-AzAccount
   ```

1. Měla by se zobrazit obrazovka pro zadání vašich přihlašovacích údajů. Použijte stejný e-mail a heslo, pomocí kterých se přihlašujete na webu Azure Portal.

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

1. Vytvořte skupinu zabezpečení sítě. Konfigurujte pravidla, která povolí připojení ke vzdálené ploše (RDP) a SQL serveru.

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

1. Definujte přihlašovací údaje pro přihlášení k virtuálnímu počítači. Uživatelské jméno je "azureadmin". Před spuštěním příkazu je \<třeba změnit heslo>.

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

## <a name="install-the-sql-iaas-agent"></a>Instalace agenta SQL IaaS

Chcete-li získat funkce integrace portálu a virtuálního počítače SQL, je nutné nainstalovat [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Pokud chcete nainstalovat agenta na nový virtuální počítač, spusťte po vytvoření virtuálního počítače následující příkaz.

   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "2.0" -Location $Location
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

1. Po zobrazení výzvy k zadání přihlašovacích údajů vyberte zadání přihlašovacích údajů k jinému účtu. Zadejte uživatelské jméno s předchozím zpětným lomítkem (například `\azureadmin`) a heslo, které jste nastavili dříve v tomto rychlém startu.

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

1. Po přihlášení k relaci vzdálené plochy spusťte v nabídce Start **SQL Server Management Studio 2017** .

1. V dialogovém okně **připojit k serveru** ponechte výchozí nastavení. Název serveru je shodný s názvem virtuálního počítače. Ověřování je nastavené na **Ověřování systému Windows**. Vyberte **Connect** (Připojit).

Nyní jste připojeni k SQL Server místně. Pokud se chcete připojit vzdáleně, musíte [nakonfigurovat připojení](virtual-machines-windows-sql-connect.md) z portálu nebo ručně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nepotřebujete, aby se virtuální počítač spouštěl nepřetržitě, můžete se vyhnout zbytečným poplatkům, když ho zastavíte, když se nepoužívá. Následující příkaz zastaví virtuální počítač, ale ponechá ho k dispozici pro budoucí použití.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Pomocí příkazu **Remove-AzResourceGroup** můžete také trvale odstranit všechny prostředky přidružené k virtuálnímu počítači. Tím se trvale odstraní i virtuální počítač, takže tento příkaz používejte opatrně.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač se systémem SQL Server 2017 pomocí prostředí Azure PowerShell. Další informace o tom, jak na tento nový SQL Server přenést data, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Migrace databáze na virtuální počítač s SQL](virtual-machines-windows-migrate-sql.md)
