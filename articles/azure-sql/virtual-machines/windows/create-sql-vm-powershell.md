---
title: Průvodce používáním Azure PowerShell ke zřízení SQL Server na virtuálním počítači Azure
description: Poskytuje kroky a příkazy PowerShellu pro vytvoření virtuálního počítače Azure s SQL Server imagí z Galerie virtuálních počítačů.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bb3fd9136f78a332a22f973d211dec748c4fb260
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317068"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>Jak pomocí Azure PowerShell zřídit SQL Server v Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tato příručka popisuje možnosti použití prostředí PowerShell ke zřízení SQL Server v Azure Virtual Machines (VM). Zjednodušený Azure PowerShell příklad, který závisí na výchozích hodnotách, najdete v článku [rychlý Start k SQL VM Azure PowerShell](sql-vm-create-powershell-quickstart.md).

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Konfigurace předplatného

1. Otevřete PowerShell a navažte přístup ke svému účtu Azure spuštěním příkazu **Connect-AzAccount** .

   ```powershell
   Connect-AzAccount
   ```

1. Po zobrazení výzvy zadejte své přihlašovací údaje. Použijte stejný e-mail a heslo, pomocí kterých se přihlašujete na webu Azure Portal.

## <a name="define-image-variables"></a>Definovat proměnné obrázku

Chcete-li znovu použít hodnoty a zjednodušit vytváření skriptů, začněte definováním počtu proměnných. Změňte hodnoty parametrů tak, jak chcete, ale při úpravě zadaných hodnot si pamatujte na omezení pojmenování týkající se délek názvů a speciálních znaků.

### <a name="location-and-resource-group"></a>Umístění a skupina prostředků

Definujte oblast dat a skupinu prostředků, ve které chcete vytvořit další prostředky virtuálních počítačů.

Upravte podle potřeby a pak tyto rutiny spusťte pro inicializaci těchto proměnných.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Vlastnosti úložiště

Definujte účet úložiště a typ úložiště, který bude virtuální počítač používat.

Upravte podle potřeby a spusťte následující rutinu pro inicializaci těchto proměnných. Pro produkční úlohy doporučujeme používat [prémiové SSD](../../../virtual-machines/disks-types.md#premium-ssd) .

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Vlastnosti sítě

Definujte vlastnosti, které má síť na virtuálním počítači používat. 

- Síťové rozhraní
- Metoda alokace protokolu TCP/IP
- Název virtuální sítě
- Název virtuální podsítě
- Rozsah IP adres pro virtuální síť
- Rozsah IP adres pro podsíť
- Popisek názvu veřejné domény

Upravte podle potřeby a pak spuštěním této rutiny inicializujte tyto proměnné.

```powershell
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

Definujte následující vlastnosti:

- Název virtuálního počítače
- Název počítače
- Velikost virtuálního počítače
- Název disku operačního systému pro virtuální počítač

Upravte podle potřeby a pak spuštěním této rutiny inicializujte tyto proměnné.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Zvolit SQL Server obrázek

Pomocí následujících proměnných definujte SQL Server Image, která se má použít pro virtuální počítač. 

1. Nejprve vypíšete všechny nabídky SQL Server obrázků pomocí `Get-AzVMImageOffer` příkazu. Tento příkaz vypíše aktuální bitové kopie, které jsou k dispozici v Azure Portal, a také starší bitové kopie, které lze nainstalovat pouze pomocí prostředí PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Pro tento kurz použijte následující proměnné k určení SQL Server 2017 ve Windows serveru 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Dále uveďte dostupné edice pro vaši nabídku.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Pro tento kurz použijte edici SQL Server 2017 Developer Edition (**SQLDEV**). Edice Developer Edition je zdarma licencovaná pro testování a vývoj a platíte jenom za náklady na provozování virtuálního počítače.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí modelu nasazení Správce prostředků první objekt, který vytvoříte, je skupina prostředků. Pomocí rutiny [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) vytvořte skupinu prostředků Azure a její prostředky. Zadejte proměnné, které jste předtím inicializoval pro název a umístění skupiny prostředků.

Spuštěním této rutiny vytvořte novou skupinu prostředků.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Virtuální počítač vyžaduje prostředky úložiště pro disk operačního systému a SQL Server data a soubory protokolů. Pro zjednodušení vytvoříte pro obojí jeden disk. Další disky můžete připojit později pomocí rutiny [Add-Azure disk](/powershell/module/servicemanagement/azure.service/add-azuredisk) k umístění SQL serverch dat a souborů protokolu na vyhrazené disky. Pomocí rutiny [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) vytvořte v nové skupině prostředků účet úložiště úrovně Standard. Zadejte proměnné, které jste předtím inicializoval pro název účtu úložiště, název SKU úložiště a umístění.

Spuštěním této rutiny vytvoříte nový účet úložiště.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Vytvoření účtu úložiště může trvat několik minut.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Virtuální počítač vyžaduje pro připojení k síti několik síťových prostředků.

* Každý virtuální počítač vyžaduje virtuální síť.
* Virtuální síť musí mít definovanou aspoň jednu podsíť.
* Síťové rozhraní musí být definováno buď pomocí veřejné, nebo privátní IP adresy.

### <a name="create-a-virtual-network-subnet-configuration"></a>Vytvoření konfigurace podsítě virtuální sítě

Začněte vytvořením konfigurace podsítě pro virtuální síť. Pro tento kurz Vytvořte výchozí podsíť pomocí rutiny [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) . Zadejte proměnné, které jste předtím inicializoval pro název podsítě a předponu adresy.

> [!NOTE]
> Pomocí této rutiny můžete definovat další vlastnosti konfigurace podsítě virtuální sítě, ale to je nad rámec tohoto kurzu.

Spuštěním této rutiny vytvořte konfiguraci virtuální podsítě.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Potom vytvořte virtuální síť v nové skupině prostředků pomocí rutiny [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) . Zadejte proměnné, které jste předtím inicializoval pro název, umístění a předponu adresy. Použijte konfiguraci podsítě, kterou jste definovali v předchozím kroku.

Spuštěním této rutiny vytvoříte virtuální síť.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy

Teď, když je vaše virtuální síť definovaná, musíte nakonfigurovat IP adresu pro připojení k virtuálnímu počítači. Pro účely tohoto kurzu Vytvořte veřejnou IP adresu pomocí dynamického přidělování IP adres pro podporu připojení k Internetu. Pomocí rutiny [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) Vytvořte veřejnou IP adresu v nové skupině prostředků. Zadejte proměnné, které jste předtím inicializoval pro název, umístění, metodu přidělení a popisek názvu domény DNS.

> [!NOTE]
> Pomocí této rutiny můžete definovat další vlastnosti veřejné IP adresy, ale to je nad rámec tohoto úvodního kurzu. Můžete také vytvořit soukromou adresu nebo adresu se statickou adresou, ale kromě rozsahu tohoto kurzu.

Spuštěním této rutiny vytvoříte veřejnou IP adresu.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

K zabezpečení virtuálního počítače a SQL Server provozu vytvořte skupinu zabezpečení sítě.

1. Nejdřív vytvořte pravidlo skupiny zabezpečení sítě pro vzdálenou plochu (RDP), aby se povolilo připojení RDP.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Nakonfigurujte pravidlo skupiny zabezpečení sítě, které povoluje provoz na portu TCP 1433. To umožňuje připojení k SQL Server přes Internet.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Vytvořte skupinu zabezpečení sítě.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Vytvoření síťového rozhraní

Teď jste připraveni vytvořit síťové rozhraní pro virtuální počítač. Pomocí rutiny [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) vytvořte síťové rozhraní ve vaší nové skupině prostředků. Zadejte název, umístění, podsíť a veřejnou IP adresu, kterou jste definovali dříve.

Spuštěním této rutiny vytvoříte síťové rozhraní.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurace objektu virtuálního počítače

Teď, když jsou prostředky úložiště a sítě definované, jste připraveni k definování výpočetních prostředků pro virtuální počítač.

- Zadejte velikost virtuálního počítače a různé vlastnosti operačního systému.
- Zadejte síťové rozhraní, které jste dříve vytvořili.
- Definujte úložiště objektů BLOB.
- Zadejte disk s operačním systémem.

### <a name="create-the-vm-object"></a>Vytvoření objektu virtuálního počítače

Začněte zadáním velikosti virtuálního počítače. Pro tento kurz zadejte DS13. Pomocí rutiny [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) vytvořte konfigurovatelný objekt virtuálního počítače. Určete proměnné, které jste dříve inicializoval pro název a velikost.

Spuštěním této rutiny vytvoříte objekt virtuálního počítače.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Vytvořte objekt přihlašovacích údajů, který bude obsahovat jméno a heslo pro přihlašovací údaje místního správce.

Než budete moct nastavit vlastnosti operačního systému pro virtuální počítač, musíte zadat přihlašovací údaje pro účet místního správce jako zabezpečený řetězec. K tomu použijte rutinu [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Spusťte následující rutinu. Do okna žádosti o přihlašovací údaje PowerShellu musíte zadat jméno a heslo místního správce virtuálního počítače.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Nastavit vlastnosti operačního systému pro virtuální počítač

Nyní jste připraveni nastavit vlastnosti operačního systému virtuálního počítače pomocí rutiny [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) .

- Nastavte typ operačního systému jako Windows.
- Vyžaduje instalaci [agenta virtuálního počítače](../../../virtual-machines/extensions/agent-windows.md) .
- Určuje, že rutina umožňuje automatickou aktualizaci.
- Zadejte proměnné, které jste předtím inicializoval pro název virtuálního počítače, název počítače a přihlašovací údaje.

Spuštěním této rutiny nastavte vlastnosti operačního systému pro virtuální počítač.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Přidat síťové rozhraní k virtuálnímu počítači

Dále pomocí rutiny [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) přidejte síťové rozhraní pomocí proměnné, kterou jste definovali dříve.

Spuštěním této rutiny nastavte síťové rozhraní pro virtuální počítač.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Nastavte umístění úložiště objektů BLOB pro disk, který se má použít pro virtuální počítač.

V dalším kroku nastavte umístění úložiště objektů BLOB pro disk virtuálního počítače pomocí proměnných, které jste definovali dříve.

Spuštěním této rutiny nastavte umístění úložiště objektů BLOB.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Nastavit vlastnosti disku operačního systému pro virtuální počítač

Dále nastavte vlastnosti disku operačního systému pro virtuální počítač pomocí rutiny [set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) . 

- Určete, že operační systém pro virtuální počítač bude pocházet z image.
- Nastavte ukládání do mezipaměti pouze pro čtení (protože SQL Server probíhá instalace na stejný disk).
- Zadejte proměnné, které jste předtím inicializoval pro název virtuálního počítače a disk operačního systému.

Spuštěním této rutiny nastavte vlastnosti disku operačního systému pro virtuální počítač.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Zadejte image platformy pro virtuální počítač.

Posledním krokem konfigurace je zadání image platformy pro váš virtuální počítač. Pro tento kurz použijte nejnovější bitovou kopii SQL Server 2016 CTP. Pomocí rutiny [set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) použijte tuto image s proměnnými, které jste definovali dříve.

Spuštěním této rutiny určíte image platformy pro váš virtuální počítač.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Vytvoření virtuálního počítače pro SQL

Teď, když jste dokončili kroky konfigurace, jste připraveni vytvořit virtuální počítač. Pomocí rutiny [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) vytvořte virtuální počítač s použitím proměnných, které jste definovali.

> [!TIP]
> Vytvoření virtuálního počítače může trvat několik minut.

Spusťte tuto rutinu a vytvořte svůj virtuální počítač.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Virtuální počítač se vytvoří.

> [!NOTE]
> Pokud se při diagnostice spouštění zobrazí chyba, můžete ji ignorovat. Pro diagnostiku spouštění se vytvoří standardní účet úložiště, protože zadaný účet úložiště pro disk virtuálního počítače je účtem Premium Storage.

## <a name="install-the-sql-iaas-agent"></a>Instalace agenta SQL IaaS

SQL Server virtuální počítače podporují automatizované funkce správy s [rozšířením agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). Pokud chcete nainstalovat agenta na nový virtuální počítač a zaregistrovat ho u poskytovatele prostředků, spusťte po vytvoření virtuálního počítače příkaz [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) . Zadejte typ licence pro váš virtuální počítač s SQL Server, a to pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), ať už máte licenci s průběžnými platbami nebo použitím vlastní licence. Další informace o licencování najdete v tématu [licencování modelu](licensing-model-azure-hybrid-benefit-ahb-change.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Zastavení nebo odebrání virtuálního počítače

Pokud nepotřebujete, aby se virtuální počítač spouštěl nepřetržitě, můžete se vyhnout zbytečným poplatkům, když ho zastavíte, když se nepoužívá. Následující příkaz zastaví virtuální počítač, ale ponechá ho k dispozici pro budoucí použití.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Pomocí příkazu **Remove-AzResourceGroup** můžete také trvale odstranit všechny prostředky přidružené k virtuálnímu počítači. Tím se trvale odstraní i virtuální počítač, takže tento příkaz používejte opatrně.

## <a name="example-script"></a>Ukázkový skript

Následující skript obsahuje kompletní PowerShellový skript pro tento kurz. Předpokládá se, že jste už nastavili předplatné Azure pro použití s příkazy **Connect-AzAccount** a **Select-AzSubscription** .

```powershell
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
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Další kroky

Po vytvoření virtuálního počítače můžete:

- Připojení k virtuálnímu počítači pomocí protokolu RDP
- Nakonfigurujte nastavení SQL Server na portálu pro svůj virtuální počítač, včetně těchto:
   - [Nastavení úložiště](storage-configuration.md) 
   - [Automatizované úlohy správy](sql-server-iaas-agent-extension-automate-management.md)
- [Konfigurace možností připojení](ways-to-connect-to-sql.md)
- Připojit klienty a aplikace k nové instanci SQL Server
