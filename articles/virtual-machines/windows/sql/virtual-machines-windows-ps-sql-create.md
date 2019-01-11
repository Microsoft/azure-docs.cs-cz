---
title: Zřizování Příručka pro virtuální počítače s SQL serverem pomocí Azure Powershellu | Dokumentace Microsoftu
description: Poskytuje příkazy prostředí PowerShell a kroky pro vytvoření virtuálního počítače Azure s využitím Image z Galerie virtuálních počítačů SQL serveru.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 059c227f9a5a5701e3fceca94b643c30d006ce67
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199948"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Jak zřídit virtuální počítače SQL serveru pomocí Azure Powershellu

Tato příručka vysvětluje možnosti k vytvoření virtuálních počítačů s Windows SQL serverem pomocí Azure Powershellu. Zjednodušený příklad prostředí Azure PowerShell s další výchozí hodnoty, najdete v článku [rychlý start SQL virtuálního počítače pomocí Azure Powershellu](quickstart-sql-vm-create-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento článek vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Konfigurace předplatného

1. Otevřete PowerShell a navažte přístup ke svému účtu spuštěním příkazu **Connect-AzureRmAccount**.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Měla by se objevit obrazovka pro zadání přihlašovacích údajů. Použijte stejný e-mail a heslo, pomocí kterých se přihlašujete na webu Azure Portal.

## <a name="define-image-variables"></a>Definujte proměnné, které image
Pro zjednodušení vytváření opakované použití a skript, začněte definováním počet proměnných. Podle svých potřeb, ale dávejte ale pozor na omezení související s názvem délky a speciální znaky při změně hodnoty poskytnuté pojmenování změně hodnot parametrů.

### <a name="location-and-resource-group"></a>Umístění a skupině prostředků
Dvě proměnné, které slouží k definování datové oblasti a skupině prostředků, do kterého můžete vytvořit další prostředky pro virtuální počítač.

Upravit podle potřeby a potom spusťte následující rutiny inicializace těchto proměnných.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Vlastnosti úložiště
Použijte následující proměnné k definování účtu úložiště a typ úložiště pro virtuální počítač.

Upravit podle potřeby a pak spusťte následující rutinu inicializace těchto proměnných. Všimněte si, že v tomto příkladu používáme [Premium Storage](../premium-storage.md), která se doporučuje pro produkční úlohy.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Vlastnosti sítě
Použijte následující proměnné k definování síťové rozhraní, metodu přidělování TCP/IP, název virtuální sítě, název virtuální podsítě, rozsah IP adres pro virtuální síť, rozsah IP adres pro podsíť a veřejnou doménu Popisek názvu Chcete-li používat sítě na virtuálním počítači.

Upravit podle potřeby a pak spusťte následující rutinu inicializace těchto proměnných.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Vlastnosti virtuálního počítače
Zadat název virtuálního počítače, název počítače, velikost virtuálního počítače a název disku operačního systému u virtuálního počítače použijte následující proměnné.

Upravit podle potřeby a pak spusťte následující rutinu inicializace těchto proměnných.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Vyberte bitovou kopii systému SQL Server
Použijte následující proměnné k definování image SQL serveru používat pro virtuální počítač.

1. Nejprve vypište všechny nabídky imagí SQL serveru s **Get-AzureRmVMImageOffer** příkaz:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Pro účely tohoto kurzu použijte následující proměnné k určení a SQL serverem 2017 na Windows serveru 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Další seznam dostupných edice pro vaši nabídku.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Pro účely tohoto kurzu použijte SQL Server 2017 Developer edition (**SQLDEV**). U verze Developer edition je volně licencované pro vývoj a testování a platíte jenom náklady na provozování virtuálního počítače.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Pomocí modelu nasazení Resource Manageru je první objekt, který vytvoříte skupinu prostředků. Použití [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) rutina pro vytvoření skupiny prostředků Azure a její prostředky pomocí názvu skupiny prostředků a umístění definované proměnné, které dřív inicializovaný.

Spusťte následující rutinu k vytvoření nové skupiny prostředků.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Virtuální počítač vyžaduje prostředky úložiště pro disk s operačním systémem a soubory protokolu a data systému SQL Server. Pro jednoduchost vytvoříme pro obě jeden disk. Další disky můžete připojit později pomocí [Azure přidat Disk](/powershell/module/servicemanagement/azure/add-azuredisk) rutiny, aby bylo možné umístit data systému SQL Server a protokolu se soubory ve vyhrazených disků. Použití [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) rutiny v nové skupiny prostředků a název účtu úložiště, název Sku úložiště a umístění, které jsou definovány pomocí proměnné, které dřív inicializovaný, vytvořte účet úložiště úrovně standard .

Spusťte následující rutinu k vytvoření nového účtu úložiště.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Vytváří se účet úložiště může trvat několik minut.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Virtuální počítač vyžaduje pro připojení k síti, počet síťových prostředků.

* Každý virtuální počítač vyžaduje virtuální síť.
* Virtuální síť musí mít definován alespoň jednu podsíť.
* Síťové rozhraní musí být definované s veřejné nebo privátní IP adresu.

### <a name="create-a-virtual-network-subnet-configuration"></a>Vytvořte konfiguraci podsítě virtuální sítě
Začněte vytvořením konfiguraci podsítě pro naše virtuální síť. V našem kurzu vytvoříme pomocí výchozí podsíť [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) rutiny. Vytvoříme naši konfiguraci podsítě virtuální sítě s název a adresu předponu podsítě definovány pomocí proměnné, které dřív inicializovaný.

> [!NOTE]
> Můžete definovat další vlastnosti konfigurace podsítě virtuální sítě pomocí této rutiny, ale to je nad rámec tohoto kurzu.

Spusťte následující rutinu, vytvořte vlastní konfiguraci virtuální podsítě.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Dále vytvořte virtuální síť používat [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) rutiny. Vytvoření virtuální sítě do nové skupiny prostředků, název, umístění a adresa předpona definovaná pomocí proměnné, které dřív inicializovaný a pomocí konfigurace podsítě, který jste definovali v předchozím kroku.

Spusťte následující rutinu k vytvoření svojí virtuální sítě.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy
Teď, když jsme naše virtuální sítě definované, musíme nakonfigurovat IP adresu pro připojení k virtuálnímu počítači. V tomto kurzu se nám vytvořit veřejnou IP adresu pomocí dynamických IP adres pro podporu připojení k Internetu. Použití [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) rutina pro vytvoření veřejné IP adresy v dříve vytvořenou skupinu prostředků a název, umístění, metodu přidělování a popisek názvu domény DNS definovány pomocí proměnné, kterou jste dřív inicializovaný.

> [!NOTE]
> Můžete definovat další vlastnosti veřejné IP adresy pomocí této rutiny, ale to je nad rámec tento úvodní kurz. Můžete také vytvořit privátní adresu nebo adresu se statickou adresou, která je však také nad rámec tohoto kurzu.

Spuštěním následující rutiny můžete vytvořit veřejnou IP adresu.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
K zabezpečení přenosů virtuálního počítače a serveru SQL Server, vytvořte skupinu zabezpečení sítě.

1. Nejprve vytvořte pravidlo skupiny zabezpečení sítě pro připojení RDP k povolení připojení ke vzdálené ploše.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurace pravidla skupiny zabezpečení sítě, které umožní provoz na portu TCP 1433. To umožňuje připojení k SQL serveru přes internet.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Vytvořte skupinu zabezpečení sítě.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Vytvořte síťové rozhraní
Nyní jsme připraveni vytvořit síťové rozhraní, které budou používat naše virtuálního počítače. Označujeme je jako [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) dříve definovaná rutina pro vytvoření naší síťové rozhraní ve skupině prostředků vytvořili dříve a s názvem, umístění, podsíť a veřejnou IP adresu.

Spusťte následující rutinu k vytvoření síťového rozhraní.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurace objektu VM
Když teď máme úložné a síťové prostředky, které jsou definovány, jsme připraveni definovat výpočetní prostředky pro virtuální počítač. V našem kurzu jsme určit různé vlastnosti operačního systému a velikosti virtuálního počítače, zadejte síťové rozhraní, které jsme předtím vytvořili, definovat úložiště objektů blob a pak zadejte disk s operačním systémem.

### <a name="create-the-vm-object"></a>Vytvoření objektu VM
Začněte tak, že určíte velikost virtuálního počítače. Pro účely tohoto kurzu zadáváme DS13. Označujeme je jako [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) rutina pro vytvoření objektu konfigurovat virtuální počítač s názvem a velikostí, které jsou definovány pomocí proměnné, které dřív inicializovaný.

Spuštěním následující rutiny můžete vytvořit objekt virtuálního počítače.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Vytvořte objekt přihlašovacích údajů pro uložení jméno a heslo pro přihlašovací údaje místního správce
Než nastavíme vlastnosti operačního systému pro virtuální počítač, musíte zadat přihlašovací údaje pro účet místního správce jako zabezpečený řetězec. Chcete-li to provést, použijte [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) rutiny.

Spusťte následující rutinu a v okně Powershellu přihlašovacích údajů požadavku zadejte jméno a heslo pro účet místního správce ve virtuálním počítači.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Nastavit vlastnosti operačního systému pro virtuální počítač
Nyní jsme připraveni nastavit vlastnosti operačního systému virtuálního počítače s [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) vyžadují rutiny jako Windows, nastavte typ operačního systému [agenta virtuálního počítače](../../extensions/agent-windows.md) k instalaci, zadejte, že rutina povolí automatickou aktualizaci a nastavte název virtuálního počítače, název počítače a přihlašovacích údajů pomocí proměnné, které dřív inicializovaný.

Spuštěním následující rutiny můžete nastavit vlastnosti operačního systému pro virtuální počítač.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Přidání síťového rozhraní k virtuálnímu počítači
V dalším kroku přidáme síťové rozhraní, které jsme vytvořili dříve pro virtuální počítač. Volání [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) rutiny pro přidání síťového rozhraní pomocí proměnné rozhraní sítě, kterou jste definovali dříve.

Spusťte následující rutinu k nastavení síťového rozhraní pro váš virtuální počítač.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Nastavte umístění úložiště objektů blob disku, který se použije pro virtuální počítač
Dále nastavte umístění úložiště objektů blob disku, který se použije pro virtuální počítač pomocí proměnné, které jste definovali dříve.

Spuštěním následující rutiny můžete nastavit umístění úložiště objektů blob.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Nastavit vlastnosti disku pro virtuální počítač operační systém
Operační systém dále nastavte vlastnosti disku pro virtuální počítač. Použití [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) rutiny k určení, že operační systém pro virtuální počítače budou přicházet z image, nastavení ukládání do mezipaměti pro čtení jenom (protože systém SQL Server je instalován na stejném disku) a definovat virtuální počítač název a disk operačního systému, které jsou definovány pomocí proměnné, které jsme definovali dříve.

Spuštěním následující rutiny můžete nastavit vlastnosti disku pro virtuální počítač operační systém.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Zadat image platformy pro virtuální počítač
Naše poslední krok konfigurace je zadání image platformy pro naše virtuální počítač. V našem kurzu se používá nejnovější image SQL Server 2016 CTP. Použít [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) rutiny k použití této image definované proměnné, které jste definovali dříve.

Spuštěním následující rutiny můžete zadat image platformy pro váš virtuální počítač.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Vytvoření virtuálního počítače pro SQL
Dokončili jste kroky konfigurace, jste připraveni k vytvoření virtuálního počítače. Použití [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) rutina pro vytvoření virtuálního počítače pomocí proměnné, které jsme definovali.

Spusťte následující rutinu k vytvoření virtuálního počítače.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Vytvoření virtuálního počítače.

> [!NOTE]
> Chybu o diagnostice spouštění můžete ignorovat. Účet úložiště úrovně standard je pro diagnostiku spouštění, vytvořit, protože zadaný účet úložiště pro disk virtuálního počítače je účtem premium storage.

## <a name="install-the-sql-iaas-agent"></a>Instalace agenta SQL IaaS
Automatizovanou správu funkce se podporují virtuální počítače s SQL serverem [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Agenta na nově vytvořený virtuální počítač nainstalujete spuštěním následujícího příkazu.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Odebrat testovacího virtuálního počítače

Pokud nepotřebujete, aby virtuální počítač VM běžel nepřetržitě, můžete se vyhnout zbytečným poplatkům: když počítač nepoužíváte, zastavte ho. Následující příkaz zastaví virtuální počítač, ale ponechá ho k dispozici pro budoucí použití.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Můžete také trvale odstranit všechny prostředky přidružené k virtuálnímu počítači odstraněním příslušné skupiny prostředků na portálu příkazem **Remove-AzureRmResourceGroup**. Tím trvale odstraníte i virtuální počítač, proto tento příkaz používejte opatrně.

## <a name="example-script"></a>Ukázkový skript
Následující skript obsahuje celý skript prostředí PowerShell pro účely tohoto kurzu. To předpokládá, že jste již nastavení předplatného Azure k použití s **Connect-AzureRmAccount** a **Select-AzureRmSubscription** příkazy.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Další postup
Po vytvoření virtuálního počítače můžete:

- Připojte se k virtuálnímu počítači pomocí vzdálené plochy (RDP).
- Konfigurace nastavení SQL serveru na portálu pro váš virtuální počítač, včetně:
   - [Nastavení úložiště](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatizované úlohy správy](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurace připojení k](virtual-machines-windows-sql-connect.md).
- Připojte klienty a aplikace na nové instanci SQL serveru.

