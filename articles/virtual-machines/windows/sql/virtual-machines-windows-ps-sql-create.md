---
title: Zřizování Průvodce pro virtuální počítače SQL serveru pomocí prostředí Azure PowerShell | Microsoft Docs
description: Obsahuje kroky a příkazy prostředí PowerShell pro vytvoření virtuálního počítače Azure s obrázky Galerie virtuálního počítače systému SQL Server.
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
ms.openlocfilehash: 2f0d9c42e32f2dd1181eac8d74c324b5ff2b0c53
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Jak zřídit virtuální počítače systému SQL Server v prostředí Azure PowerShell

Tato příručka vysvětluje možnosti vytvoření virtuálních počítačů Windows SQL serveru pomocí prostředí Azure PowerShell. Moderní prostředí Azure PowerShell příklad s další výchozí hodnoty, najdete v tématu [rychlý start SQL virtuálních počítačů Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento článek vyžaduje prostředí Azure PowerShell verze modulu 3,6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Konfigurovat předplatné

1. Otevřete PowerShell a navažte přístup ke svému účtu spuštěním příkazu **Connect-AzureRmAccount**.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Měla by se objevit obrazovka pro zadání přihlašovacích údajů. Použijte stejný e-mail a heslo, pomocí kterých se přihlašujete na webu Azure Portal.

## <a name="define-image-variables"></a>Definování proměnné bitovou kopii
Pro zjednodušení vytváření opakované použití a skript, začněte definováním počet proměnných. Podle svých potřeb, ale pozor omezení týkající se názvu délky a speciální znaky, při úpravě hodnoty poskytnuté pojmenování změně hodnot parametrů.

### <a name="location-and-resource-group"></a>Umístění a skupiny prostředků
Dvě proměnné, které slouží k určení oblasti dat a skupině prostředků, do kterého můžete vytvořit další prostředky pro virtuální počítač.

Upravte požadovaným způsobem a potom spusťte následující rutiny k chybě při inicializaci tyto proměnné.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Úložiště vlastností
Použijte následující proměnné zadat účet úložiště a typ úložiště, který se má použít pro virtuální počítač.

Upravte požadovaným způsobem a potom spusťte následující rutinu k chybě při inicializaci tyto proměnné. Všimněte si, že v tomto příkladu používáme [Storage úrovně Premium](../premium-storage.md), který se doporučuje pro úlohy v produkčním prostředí.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Vlastnosti sítě
Použijte následující proměnné k definování síťového rozhraní, metoda přidělení TCP/IP, název virtuální sítě, název virtuální podsítě, rozsah IP adres pro virtuální síť, rozsah IP adres pro podsíť a popisek názvu veřejné domény pro použití sítě ve virtuálním počítači.

Upravte požadovaným způsobem a potom spusťte následující rutinu k chybě při inicializaci tyto proměnné.

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
Zadat název virtuálního počítače, název počítače, velikost virtuálního počítače a název disku operačního systému pro virtuální počítač, použijte následující proměnné.

Upravte požadovaným způsobem a potom spusťte následující rutinu k chybě při inicializaci tyto proměnné.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Vyberte bitovou kopii systému SQL Server
Chcete-li definovat bitovou kopii systému SQL Server používat pro virtuální počítač, použijte následující proměnné.

1. První, seznam se všechny nabídky bitovou kopii systému SQL Server s **Get-AzureRmVMImageOffer** příkaz:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. V tomto kurzu použijte následující proměnné určete 2017 SQL serveru na Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Další, seznam se v edicích dostupné pro vaši nabídku.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. V tomto kurzu použít SQL Server 2017 Developer edition (**SQLDEV**). Developer edition je volně licenci pro vývoj a testování a platíte jenom pro náklady na provozování virtuálního počítače.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Pomocí modelu nasazení Resource Manager je první objekt, který vytvoříte skupinu prostředků. Použití [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) vytvořte skupinu prostředků Azure a jeho prostředků se název skupiny prostředků a umístění definované proměnné, které jste dříve inicializován.

Spusťte následující rutinu k vytvoření nové skupiny prostředků.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Virtuální počítač vyžaduje prostředků úložiště pro disk operačního systému a souborů protokolu a data systému SQL Server. Pro jednoduchost vytvoříme pro obě jeden disk. Můžete připojit další disky později pomocí [disku Azure přidat](/powershell/module/azure/add-azuredisk) rutiny, aby bylo možné umístit data systému SQL Server a protokolu se soubory ve vyhrazených disků. Použití [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/new-azurermstorageaccount) vytvořte standardní účet úložiště v nové skupiny prostředků a název účtu úložiště, název Sku úložiště a umístění definovat pomocí proměnné, které jste dříve inicializován .

Spusťte následující rutinu k vytvoření nového účtu úložiště.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Vytvoření účtu úložiště může trvat několik minut.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Virtuální počítač vyžaduje počet síťovým prostředkům pro připojení k síti.

* Každý virtuální počítač vyžaduje virtuální síť.
* Virtuální síť musí mít alespoň jednu podsíť definované.
* Síťové rozhraní musí být definovány se veřejné nebo privátní IP adresy.

### <a name="create-a-virtual-network-subnet-configuration"></a>Vytvoření konfigurace podsítě virtuální sítě
Začněte vytvořením konfigurace pro naše virtuální síť s podsítí. V našem kurzu vytvoříme výchozí podsíť pomocí [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) rutiny. Název a adresu předponu podsítě definovat pomocí proměnné, které jste dříve inicializovat jsme vytvořit naše konfigurace podsítě virtuální sítě.

> [!NOTE]
> Můžete definovat další vlastnosti konfigurace podsítě virtuální sítě pomocí této rutiny, ale který je nad rámec tohoto kurzu.

Spusťte následující rutiny můžete vytvořit konfiguraci virtuální podsítě.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Dále vytvořte pomocí vaší virtuální sítě [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) rutiny. Vytvoření virtuální sítě v nové skupiny prostředků, název, umístění a adresu předpona definovaná pomocí proměnné, které jste dříve inicializovat a pomocí konfigurace podsítě, který jste zadali v předchozím kroku.

Spusťte následující rutinu pro vytvoření svojí virtuální sítě.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy
Teď, když máme naše virtuální síť definován, je potřeba nakonfigurovat IP adresu pro připojení k virtuálnímu počítači. V tomto kurzu vytvoříme veřejnou IP adresu pomocí dynamických IP adres pro podporu připojení k Internetu. Použití [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) rutiny pro vytvoření veřejné IP adresy ve skupině prostředků vytvořili dříve a název, umístění, metoda přidělení a popisek názvu domény DNS je definovat pomocí proměnné, které dříve inicializován.

> [!NOTE]
> Můžete definovat další vlastnosti veřejné IP adresy pomocí této rutiny, ale který je nad rámec tohoto počáteční kurzu. Můžete také vytvořit privátní adresu nebo adresu se statickou adresou, ale který je také nad rámec tohoto kurzu.

Spusťte následující rutiny můžete vytvořit veřejnou IP adresu.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
K zabezpečení přenosů virtuálních počítačů a SQL Server, vytvořte skupinu zabezpečení sítě.

1. Nejprve vytvořte pravidlo pro skupinu zabezpečení sítě pro protokol RDP umožňuje připojení ke vzdálené ploše.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Nakonfigurujte pravidla skupiny zabezpečení sítě, který umožňuje komunikaci na portu TCP 1433. To umožňuje připojení k SQL serveru přes internet.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Pak vytvořte skupinu zabezpečení sítě.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Vytvořit rozhraní sítě
Nyní jsme připraveni k vytvoření síťového rozhraní, které budou používat naše virtuálního počítače. Říkáme [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) předtím definovaný vytvořte naše síťové rozhraní, ve skupině prostředků vytvořili dříve a název, umístění, podsíť a veřejnou IP adresu.

Spusťte následující rutinu k vytvoření síťového rozhraní.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurace objektu virtuálního počítače
Teď, když máme úložiště a síťové prostředky, které jsou definované, jsme připraveni k definování výpočetní prostředky pro virtuální počítač. V našem kurzu jsme zadejte velikost virtuálního počítače a různé vlastnosti operačního systému, zadejte definovat úložiště objektů blob síťového rozhraní, které jsme dříve vytvořili, a pak zadejte disku operačního systému.

### <a name="create-the-vm-object"></a>Vytvořit objekt virtuálního počítače
Spustit zadáním velikost virtuálního počítače. V tomto kurzu zadáváme DS13. Říkáme [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) vytvořte objekt konfigurovat virtuální počítač s názvem a velikostí, které jsou definované pomocí proměnné, které jste dříve inicializován.

Spusťte následující rutinu k vytvoření objektu virtuálního počítače.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Vytvořit objekt přihlašovacích údajů k uchování jméno a heslo pro přihlašovací údaje místního správce
Než budeme moct nastavit vlastnosti operačního systému pro virtuální počítač, je potřeba zadat přihlašovací údaje pro účet místního správce jako zabezpečený řetězec. K tomu použít [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) rutiny.

Spusťte následující rutinu a v okně požadavku přihlašovacích údajů prostředí PowerShell, zadejte jméno a heslo pro účet místního správce ve virtuálním počítači.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Nastavit vlastnosti operačního systému pro virtuální počítač
Nyní jsme připravení nastavit vlastnosti operačního systému virtuálního počítače s [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) vyžadují rutiny nastavte typ operačního systému jako Windows, [agenta virtuálního počítače](../../extensions/agent-windows.md) k instalaci, zadejte, že rutina umožňuje automatickou aktualizaci a nastavte název virtuálního počítače, název počítače a přihlašovacích údajů pomocí proměnné, které jste dříve inicializován.

Spusťte následující rutinu a nastavte vlastnosti operačního systému pro virtuální počítač.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Přidání rozhraní sítě k virtuálnímu počítači
Potom přidáme síťového rozhraní, které jsme vytvořili dříve k virtuálnímu počítači. Volání [přidat AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) přidejte síťové rozhraní, pomocí proměnné rozhraní sítě, která jste definovali dříve.

Spusťte následující rutiny můžete nastavit rozhraní sítě pro virtuální počítač.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Nastavení umístění úložiště objektů blob pro disk, který se má použít pro virtuální počítač
Dále nastavte umístění úložiště objektů blob pro disk, který se má použít pro virtuální počítač pomocí proměnné, které jste definovali dříve.

Spusťte následující rutiny můžete nastavit umístění úložiště objektů blob.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Nastavit vlastnosti disku pro virtuální počítač operační systém
Operační systém dále nastavte vlastnosti disku pro virtuální počítač. Použití [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) rutiny k určení, že operační systém pro virtuální počítač bude pocházet z bitové kopie, nastavení ukládání do mezipaměti ke čtení, (protože SQL Server je instalován na stejném disku) a definovat virtuální počítač název a definovat pomocí proměnné, které definovaného dříve disku operačního systému.

Spusťte následující rutiny můžete nastavit vlastnosti disku pro virtuální počítač operační systém.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Zadat image platformy pro virtuální počítač
Naše poslední krok konfigurace slouží k zadání image platformy pro naše virtuální počítač. V našem kurzu se používá nejnovější bitovou kopii systému SQL Server 2016 CTP. Použít [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) rutiny k použití této image podle definice proměnné, které jste definovali dříve.

Spusťte následující rutiny můžete zadat image platformy pro virtuální počítač.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Vytvoření virtuálního počítače pro SQL
Teď, když dokončíte kroky konfigurace, jste připraveni k vytvoření virtuálního počítače. Použití [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) rutiny k vytvoření virtuálního počítače pomocí proměnné, které jsme definovali.

Spusťte následující rutinu k vytvoření virtuálního počítače.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Při vytvoření virtuálního počítače.

> [!NOTE]
> Chybu o Diagnostika spouštění můžete ignorovat. Standardní účet úložiště je pro Diagnostika spouštění, vytvořit, protože zadaný účet úložiště pro disk virtuálního počítače je prémiový účet úložiště.

## <a name="install-the-sql-iaas-agent"></a>Instalace agenta SQL IaaS
Virtuální počítače systému SQL Server podporují funkce automatizované správy s [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Agenta na nově vytvořený virtuální počítač nainstalujete spuštěním následujícího příkazu.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Odebrání testovacího virtuálního počítače

Pokud nepotřebujete, aby virtuální počítač VM běžel nepřetržitě, můžete se vyhnout zbytečným poplatkům: když počítač nepoužíváte, zastavte ho. Následující příkaz zastaví virtuální počítač, ale ponechá ho k dispozici pro budoucí použití.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Můžete také trvale odstranit všechny prostředky přidružené k virtuálnímu počítači odstraněním příslušné skupiny prostředků na portálu příkazem **Remove-AzureRmResourceGroup**. Tím trvale odstraníte i virtuální počítač, proto tento příkaz používejte opatrně.

## <a name="example-script"></a>Ukázkový skript
Následující skript obsahuje dokončení skriptu prostředí PowerShell pro účely tohoto kurzu. Se předpokládá, že máte již instalace předplatné Azure pro použití s **Connect-AzureRmAccount** a **Select-AzureRmSubscription** příkazy.

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
Po vytvoření virtuálního počítače, můžete:

- Připojte k virtuálnímu počítači pomocí vzdálené plochy (RDP).
- Konfigurovat nastavení systému SQL Server na portálu pro virtuální počítač, včetně:
   - [Nastavení úložiště](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Úlohy automatizované správy](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurace připojení](virtual-machines-windows-sql-connect.md).
- Připojte klienty a aplikace do nové instance systému SQL Server.

