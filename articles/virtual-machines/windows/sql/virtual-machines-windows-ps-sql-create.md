---
title: Průvodce zřizováním virtuálních počítačů SQL Server pomocí Azure PowerShellu | Dokumenty společnosti Microsoft
description: Poskytuje kroky a příkazy Prostředí PowerShell pro vytváření virtuálního počítače Azure s ibi můlení galerie virtuálních počítačů SQL Serveru.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1578547fbca4caaecb209021569f0fbb2f1ae24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790636"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Jak zřídit virtuální počítače SQL Serveru pomocí Azure PowerShellu

Tato příručka vysvětluje možnosti vytváření virtuálních počítačů Windows SQL Server pomocí Azure PowerShellu. Zjednodušený příklad Azure PowerShellu s více výchozími hodnotami najdete v [tématu sql vm Azure PowerShell quickstart](quickstart-sql-vm-create-powershell.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Konfigurace předplatného

1. Otevřete PowerShell a vytvořte přístup ke svému účtu Azure spuštěním příkazu **Connect-AzAccount.**

   ```powershell
   Connect-AzAccount
   ```

1. Měli byste vidět obrazovku pro zadání pověření. Použijte stejný e-mail a heslo, pomocí kterých se přihlašujete na webu Azure Portal.

## <a name="define-image-variables"></a>Definování proměnných obrazu
Chcete-li znovu použít hodnoty a zjednodušit vytváření skriptů, začněte definováním řady proměnných. Změňte hodnoty parametrů podle potřeby, ale při úpravách poskytnutých hodnot mějte na paměti omezení týkající se pojmenování, která se vztahují k délce názvů a speciálním znakům.

### <a name="location-and-resource-group"></a>Umístění a skupina prostředků
Definujte oblast dat a skupinu prostředků, do které vytvoříte další prostředky virtuálního soudu.

Upravte podle potřeby a pak spusťte tyto rutiny pro inicializaci těchto proměnných.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Vlastnosti úložiště
Definujte účet úložiště a typ úložiště, které má virtuální počítač používat.

Upravte podle potřeby a spusťte následující rutinu pro inicializaci těchto proměnných. Doporučujeme používat [prémiové SSD](../disks-types.md#premium-ssd) pro produkční úlohy.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Vlastnosti sítě
Definujte vlastnosti, které má síť používat ve virtuálním počítači. 

- Síťové rozhraní
- Metoda přidělení protokolu TCP/IP
- Název virtuální sítě
- Název virtuální podsítě
- Rozsah IP adres pro virtuální síť
- Rozsah IP adres pro podsíť
- Popisek názvu veřejné domény

Upravte podle potřeby a spusťte tuto rutinu pro inicializaci těchto proměnných.

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
Definujte název virtuálního počítače, název počítače, velikost virtuálního počítače a název disku operačního systému pro virtuální počítač.

Upravte podle potřeby a spusťte tuto rutinu pro inicializaci těchto proměnných.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Výběr bitové kopie serveru SQL Server

Pomocí následujících proměnných definujte bitovou kopii serveru SQL Server, která se má použít pro virtuální počítač. 

1. Nejprve uveďte všechny nabídky bitových obrázků `Get-AzVMImageOffer` serveru SQL Server pomocí příkazu. Tento příkaz obsahuje seznam aktuálních bitových kopií, které jsou dostupné na webu Azure Portal, a také starší image, které se dá nainstalovat jenom s PowerShellem:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. V tomto kurzu použijte následující proměnné k určení SQL Serveru 2017 v systému Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Dále uveďte dostupné edice pro vaši nabídku.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. V tomto kurzu použijte SQL Server 2017 Developer edition (**SQLDEV**). Edice Developer je volně licencována pro testování a vývoj a platíte jenom za náklady na provoz virtuálního provozu.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
S modelem nasazení Resource Manager, první objekt, který vytvoříte, je skupina prostředků. Rutina [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) slouží k vytvoření skupiny prostředků Azure a jejích prostředků. Zadejte proměnné, které jste dříve inicializovali pro název a umístění skupiny prostředků.

Spusťte tuto rutinu a vytvořte novou skupinu prostředků.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Virtuální počítač vyžaduje prostředky úložiště pro disk operačního systému a pro data a soubory protokolu serveru SQL Server. Pro jednoduchost vytvoříte jeden disk pro oba. Další disky můžete připojit později pomocí rutiny [Disk Add-Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) k umístění dat serveru SQL Server a souborů protokolu na vyhrazené disky. Pomocí rutiny [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) vytvořte standardní účet úložiště v nové skupině prostředků. Zadejte proměnné, které jste dříve inicializovali pro název účtu úložiště, název sku úložiště a umístění.

Spusťte tuto rutinu a vytvořte nový účet úložiště.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Vytvoření účtu úložiště může trvat několik minut.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Virtuální počítač vyžaduje pro připojení k síti řadu síťových prostředků.

* Každý virtuální počítač vyžaduje virtuální síť.
* Virtuální síť musí mít definována alespoň jednu podsíť.
* Síťové rozhraní musí být definováno s veřejnou nebo privátní IP adresou.

### <a name="create-a-virtual-network-subnet-configuration"></a>Vytvoření konfigurace podsítě virtuální sítě
Začněte vytvořením konfigurace podsítě pro vaši virtuální síť. Pro účely tohoto kurzu vytvořte výchozí podsíť pomocí rutiny [New-AzVirtualNetworkSubnetConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Zadejte proměnné, které jste dříve inicializovali pro předponu názvu a adresy podsítě.

> [!NOTE]
> Pomocí této rutiny můžete definovat další vlastnosti konfigurace podsítě virtuální sítě, ale to je nad rámec tohoto kurzu.

Spusťte tuto rutinu a vytvořte konfiguraci virtuální podsítě.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Dále vytvořte virtuální síť v nové skupině prostředků pomocí rutiny [New-AzVirtualNetwork.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) Zadejte proměnné, které jste dříve inicializovali pro předponu názvu, umístění a adresy. Použijte konfiguraci podsítě, kterou jste definovali v předchozím kroku.

Spusťte tuto rutinu a vytvořte virtuální síť.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy
Teď, když je vaše virtuální síť definovaná, je nutné nakonfigurovat IP adresu pro připojení k virtuálnímu počítači. Pro účely tohoto kurzu vytvořte veřejnou IP adresu pomocí dynamického adresování IP pro podporu připojení k Internetu. Pomocí rutiny [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) vytvořte veřejnou IP adresu v nové skupině prostředků. Zadejte proměnné, které jste dříve inicializovali pro název, umístění, metodu přidělení a popisek názvu domény DNS.

> [!NOTE]
> Pomocí této rutiny můžete definovat další vlastnosti veřejné IP adresy, ale to je nad rámec tohoto počátečního kurzu. Můžete také vytvořit soukromou adresu nebo adresu se statickou adresou, ale to je také nad rámec tohoto kurzu.

Spusťte tuto rutinu a vytvořte veřejnou IP adresu.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Chcete-li zabezpečit provoz virtuálních modulů a serveru SQL Server, vytvořte skupinu zabezpečení sítě.

1. Nejprve vytvořte pravidlo skupiny zabezpečení sítě pro protokol RDP, které umožní připojení ke vzdálené ploše.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Nakonfigurujte pravidlo skupiny zabezpečení sítě, které umožňuje přenosy na portu TCP 1433. Tím povolíte připojení k serveru SQL Server přes internet.

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
Teď jste připraveni vytvořit síťové rozhraní pro váš virtuální počítač. Pomocí rutiny [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) vytvořte síťové rozhraní v nové skupině prostředků. Zadejte název, umístění, podsíť a veřejnou IP adresu, která byla dříve definována.

Spusťte tuto rutinu a vytvořte síťové rozhraní.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurace objektu virtuálního počítače
Teď, když jsou definovány prostředky úložiště a sítě, jste připraveni definovat výpočetní prostředky pro virtuální počítač.

- Zadejte velikost virtuálního počítače a různé vlastnosti operačního systému.
- Zadejte dříve vytvořené síťové rozhraní.
- Definujte úložiště objektů blob.
- Zadejte disk operačního systému.

### <a name="create-the-vm-object"></a>Vytvoření objektu virtuálního mísy
Začněte zadáním velikosti virtuálního počítače. Pro účely tohoto kurzu zadejte DS13. Pomocí rutiny [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) vytvořte konfigurovatelný objekt virtuálního počítače. Zadejte proměnné, které jste dříve inicializovali pro název a velikost.

Spusťte tuto rutinu a vytvořte objekt virtuálního počítače.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Vytvoření objektu pověření pro uložení názvu a hesla pověření místního správce
Před nastavením vlastností operačního systému pro virtuální počítač je nutné zadat pověření pro účet místního správce jako zabezpečený řetězec. Chcete-li to provést, použijte rutinu [Získat pověření.](https://technet.microsoft.com/library/hh849815.aspx)

Spusťte následující rutinu a v okně žádosti o pověření prostředí PowerShell zadejte název a heslo, které chcete použít pro účet místního správce ve virtuálním počítači.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Nastavení vlastností operačního systému pro virtuální počítač
Nyní jste připraveni nastavit vlastnosti operačního systému virtuálního počítače pomocí rutiny [Set-AzVMOperatingSystem.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)

- Nastavte typ operačního systému jako Windows.
- Vyžadovat instalaci [agenta virtuálního počítače.](../../extensions/agent-windows.md)
- Určete, že rutina umožňuje automatickou aktualizaci.
- Zadejte proměnné, které jste dříve inicializovali pro název virtuálního počítače, název počítače a pověření.

Spusťte tuto rutinu a nastavte vlastnosti operačního systému pro váš virtuální počítač.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Přidání síťového rozhraní do virtuálního počítače
Dále použijte rutinu [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) k přidání síťového rozhraní pomocí proměnné, kterou jste definovali dříve.

Spusťte tuto rutinu a nastavte síťové rozhraní pro váš virtuální počítač.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Nastavení umístění úložiště objektů blob pro disk, který má být používán virtuálním počítačem
Dále nastavte umístění úložiště objektů blob pro disk virtuálního počítače pomocí proměnných, které jste definovali dříve.

Spusťte tuto rutinu a nastavte umístění úložiště objektů blob.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Nastavení vlastností disku operačního systému pro virtuální počítač
Dále nastavte vlastnosti disku operačního systému pro virtuální počítač pomocí rutiny [Set-AzVMOSDisk.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 

- Určete, že operační systém pro virtuální počítač bude pocházet z bitové kopie.
- Nastavte ukládání do mezipaměti pouze pro čtení (protože SQL Server je instalován na stejném disku).
- Zadejte proměnné, které jste dříve inicializovali pro název virtuálního počítače a disk operačního systému.

Spusťte tuto rutinu a nastavte vlastnosti disku operačního systému pro váš virtuální počítač.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Určení image platformy pro virtuální počítač
Posledním krokem konfigurace je určení image platformy pro váš virtuální počítač. Pro účely tohoto kurzu použijte nejnovější bitovou kopii CTP serveru SQL Server 2016. Pomocí rutiny [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) můžete použít tento obrázek s proměnnými, které jste definovali dříve.

Spusťte tuto rutinu a určete image platformy pro váš virtuální počítač.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Vytvoření virtuálního počítače pro SQL
Teď, když jste dokončili kroky konfigurace, jste připraveni vytvořit virtuální počítač. Pomocí rutiny [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) vytvořte virtuální počítač pomocí proměnných, které jste definovali.

> [!TIP]
> Vytvoření virtuálního virtuálního mísa může trvat několik minut.

Spusťte tuto rutinu a vytvořte svůj virtuální počítač.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Virtuální počítač je vytvořen.

> [!NOTE]
> Pokud se zobrazí chyba v diagnostice spouštění, můžete ji ignorovat. Pro diagnostiku spouštění se vytvoří standardní účet úložiště, protože zadaný účet úložiště pro disk virtuálního počítače je účet úložiště premium.

## <a name="install-the-sql-iaas-agent"></a>Instalace agenta SQL IaaS
Virtuální počítače SQL Serveru podporují funkce automatické správy pomocí [rozšíření agenta SERVERU SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Chcete-li nainstalovat agenta na nový virtuální počítač a zaregistrovat jej u poskytovatele prostředků, spusťte příkaz [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) po vytvoření virtuálního počítače. Zadejte typ licence pro váš virtuální počítač SQL Server a vyberte si mezi průběžnou platbou nebo vlastní licencí prostřednictvím [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Další informace o licencování naleznete v tématu [licenční model](virtual-machines-windows-sql-ahb.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Zastavení nebo odebrání virtuálního mísy

Pokud nepotřebujete virtuální ho spustit nepřetržitě, můžete se vyhnout zbytečné poplatky zastavením, když se nepoužívá. Následující příkaz zastaví virtuální počítač, ale ponechá ho k dispozici pro budoucí použití.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Můžete také trvale odstranit všechny prostředky přidružené k virtuálnímu počítači pomocí příkazu **Remove-AzResourceGroup.** Pokud tak učiníte trvale odstraní virtuální počítač stejně, takže použijte tento příkaz opatrně.

## <a name="example-script"></a>Ukázkový skript
Následující skript obsahuje kompletní skript prostředí PowerShell pro tento kurz. Předpokládá, že jste již nastavili předplatné Azure pro použití s **příkazy Connect-AzAccount** a **Select-AzSubscription.**

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

- Připojení k virtuálnímu počítači pomocí rdp
- Konfigurace nastavení SERVERU SQL Server na portálu pro váš virtuální počítač, včetně:
   - [Nastavení úložiště](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatizované úlohy správy](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurace připojení](virtual-machines-windows-sql-connect.md)
- Připojení klientů a aplikací k nové instanci serveru SQL Server

