---
title: Vytvoření virtuálního počítače (klasické) s více síťovými kartami - prostředí Azure PowerShell | Microsoft Docs
description: Naučte se vytvořit virtuální počítač (klasický) s více síťovými kartami pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09c85a9badaad45816a9160d09cb3c4a726426e8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Vytvoření virtuálního počítače (klasické) s více síťovými kartami pomocí prostředí PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Můžete vytvořit virtuální počítače (VM) v Azure a připojit více síťových rozhraní (NIC) ke každému z virtuálních počítačů. Několik síťových adaptérů povolit oddělení typů přenosů mezi síťové adaptéry. Jeden síťový adaptér může například komunikují přes Internet, zatímco jiné komunikuje jenom s interním prostředkům, které nejsou připojené k Internetu. Schopnost oddělit síťový provoz na několik síťových adaptérů je vyžadována pro mnoho síťových virtuálních zařízení, například doručení aplikace a řešení optimalizace sítě WAN.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Naučte se provádět tyto kroky, pomocí [modelu nasazení Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Následující postup použijte skupinu prostředků s názvem *IaaSStory* pro webové servery a skupinu prostředků s názvem *IaaSStory back-end* pro servery DB.

## <a name="prerequisites"></a>Požadavky

Před vytvořením servery DB, je potřeba vytvořit *IaaSStory* skupina prostředků se všechny potřebné prostředky pro tento scénář. Chcete-li vytvořit tyto prostředky, proveďte kroky, které následují. Vytvoření virtuální sítě pomocí následujících kroků v [vytvořit virtuální síť](virtual-networks-create-vnet-classic-netcfg-ps.md) článku.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Vytvořit virtuální počítače back-end
Virtuální počítače back-end závisí na vytvoření v následujících zdrojích informací:

* **Back-end podsítě**. Databázové servery budou součástí samostatnou podsíť, oddělit provoz. Níže uvedený skript předpokládá, že tuto podsíť existovat ve virtuální síti s názvem *WTestVnet*.
* **Účet úložiště pro datové disky**. Pro lepší výkon datové disky v databázových serverech použije technologii SSD jednotky (SSD Solid-State Drive), která vyžaduje účet úložiště premium. Zajistěte, aby umístění Azure, můžete nasadit pro podporu služby storage úrovně premium.
* **Skupina dostupnosti:** Všechny databázové servery se zařadí do jedné dostupnosti nastavte, ujistěte se, že nejméně jedna z virtuálních počítačů je nahoru a během údržby.

### <a name="step-1---start-your-script"></a>Krok 1 – spustit skript
Si můžete stáhnout úplné skript prostředí PowerShell použít [zde](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Postupujte podle pokynů níže změňte skript pro práci ve vašem prostředí.

1. Změňte hodnoty proměnných níže podle vaší existující skupinu prostředků, které jsou nasazené výše v [požadavky](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Změňte hodnoty proměnných níže na základě hodnot, které chcete použít pro vaše nasazení back-end.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2 – Vytvoření potřebné prostředky pro virtuální počítače
Potřebujete vytvořit novou cloudovou službu a účet úložiště pro datové disky pro všechny virtuální počítače. Také musíte zadat bitovou kopii a účet místního správce pro virtuální počítače. Pokud chcete vytvořit tyto prostředky, proveďte následující kroky:

1. Vytvořte novou cloudovou službu.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Vytvořte nový účet úložiště premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Nastavte účet úložiště vytvořili výše jako aktuální účet úložiště pro vaše předplatné.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Vyberte bitovou kopii pro virtuální počítač.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Nastavte přihlašovací údaje k účtu místního správce.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Krok 3 – vytvoření virtuálních počítačů
Budete muset použít smyčku vytvořit libovolný počet virtuálních počítačů a vytvořit potřebné síťové karty a virtuální počítače v rámci smyčky. Chcete-li vytvořit síťové karty a virtuální počítače, spusťte následující kroky.

1. Spuštění `for` cykly opakování příkazů pro vytvoření virtuálního počítače a dva síťové adaptéry tolikrát, kolikrát podle potřeby, na základě hodnoty z `$numberOfVMs` proměnné.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Vytvoření `VMConfig` objekt obrázku, velikost a sadu dostupnosti pro virtuální počítač.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Zřídit virtuální počítač jako virtuální počítač systému Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Nastavit výchozí síťový adaptér a přiřaďte mu statickou IP adresu.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Přidejte druhý síťový adaptér pro každý virtuální počítač.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Vytvoření datových disků, pro každý virtuální počítač.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Vytvořit jednotlivé virtuální počítače a ukončení smyčky.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Krok 4 – spuštění skriptu
Teď, když jste stáhli a změnit skript na základě potřeb, o skript k vytvoření databáze back-end virtuálních počítačů s více síťovými kartami.

1. Uložte skript a spusťte jej z **prostředí PowerShell** příkazového řádku, nebo **prostředí PowerShell ISE**. Počáteční výstupu uvidíte, jak je uvedeno níže.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Vyplňte informace potřebné v řádku přihlašovací údaje a klikněte na **OK**. Tento výstup se vrátí.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5: Konfigurace směrování v rámci operačního systému Virtuálního počítače

Azure DHCP přiřadí výchozí bránu na první (primární) síťové rozhraní připojené k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní může, ale komunikovat s prostředky mimo jejich podsítě. Konfigurace směrování pro sekundární síťová rozhraní najdete v tématu [směrování v rámci operačního systému virtuálního počítače s více síťovými rozhraními](virtual-network-network-interface-vm.md).
