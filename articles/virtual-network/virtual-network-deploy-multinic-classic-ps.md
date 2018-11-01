---
title: Vytvoření virtuálního počítače (klasické) s několika síťovými kartami – Azure PowerShell | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač (klasický) s několika síťovými kartami pomocí Powershellu.
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
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 087de711fdbc70a4562045f8d774f2c6ae5a0db5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417034"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Vytvoření virtuálního počítače (klasické) s několika síťovými kartami pomocí Powershellu

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Můžete vytvořit virtuální počítače (VM) v Azure a připojit několik síťových rozhraní (NIC) ke každé z vašich virtuálních počítačů. Několik síťových adaptérů umožňuje oddělení typů přenosů napříč síťovými kartami. Například jednou síťovou KARTOU může komunikovat s Internetem, zatímco jiné komunikuje jenom s interním prostředkům, které nejsou připojené k Internetu. Schopnost oddělit síťový provoz napříč více síťových rozhraní se vyžaduje pro řadu síťových virtuálních zařízení, jako je doručování aplikací a řešení optimalizace sítě WAN.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak provést tento postup pomocí [modelu nasazení Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Následující postup použijte skupinu prostředků s názvem *IaaSStory* pro webové servery a skupinu prostředků s názvem *IaaSStory back-endu* pro servery DB.

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit servery DB, je potřeba vytvořit *IaaSStory* skupina prostředků se všechny prostředky potřebné pro tento scénář. Pokud chcete vytvořit tyto prostředky, proveďte následující kroky. Vytvoření virtuální sítě pomocí následujících kroků v [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-netcfg-ps.md) článku.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Vytvoření back endové virtuální počítače
Back endové virtuální počítače jsou závislé na vytváření z následujících prostředků:

* **Podsíť back-endu**. Databázové servery budou součástí jiné podsíti, oddělit provoz. Níže uvedený skript předpokládá, že tato podsíť existuje ve virtuální síti s názvem *WTestVnet*.
* **Účet úložiště pro datové disky**. Pro lepší výkon datové disky v databázových serverech použije SSD (SOLID-State drive) technologii, která vyžaduje účet úložiště úrovně premium. Ujistěte se, že umístění Azure nasazujete do podporují službu premium storage.
* **Skupina dostupnosti:** Všechny databázové servery se přidají do jednoho dostupnosti nastavena na hodnotu Ujistěte se, že je alespoň jeden z virtuálních počítačů a během údržby.

### <a name="step-1---start-your-script"></a>Krok 1: Spusťte svůj skript
Můžete stáhnout úplná skript prostředí PowerShell použít [tady](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Postupem uvedeným níže změníte skript fungoval ve vašem prostředí.

1. Změňte hodnoty proměnných níže podle vaší existující skupinu prostředků, které jsou nasazené výše v [požadavky](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Změňte hodnoty proměnných níže podle hodnoty, které chcete použít pro vaše nasazení back-endu.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2: vytvoření potřebné prostředky pro virtuální počítače
Je potřeba vytvořit novou cloudovou službu a účet úložiště pro datové disky pro všechny virtuální počítače. Také musíte zadat bitovou kopii a účet místního správce pro virtuální počítače. Pokud chcete vytvořit tyto prostředky, proveďte následující kroky:

1. Vytvořte novou cloudovou službu.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Vytvořte nový účet úložiště úrovně premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Nastavte účet úložiště vytvořený výše jako aktuální účet úložiště pro vaše předplatné.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Vyberte image virtuálního počítače.

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

### <a name="step-3---create-vms"></a>Krok 3: vytvoření virtuálních počítačů
Budete muset pomocí cyklu můžete vytvořit libovolný počet virtuálních počítačů a vytvořit potřebná síťové karty a virtuální počítače v rámci smyčky. Pokud chcete vytvořit síťové karty a virtuální počítače, proveďte následující kroky.

1. Začněte `for` smyčky k opakování příkazů pro vytvoření virtuálního počítače a dva síťové adaptéry tolikrát, kolikrát podle potřeby, na základě hodnoty z `$numberOfVMs` proměnné.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Vytvoření `VMConfig` určující obrázku, velikost a dostupnosti pro virtuální počítač.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Zřízení virtuálního počítače jako virtuální počítač Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Nastavit výchozí síťové rozhraní a přiřaďte mu statickou IP adresu.

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
    
6. Vytvořte pro každý virtuální počítač pro datové disky.

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

7. Vytvoření každého virtuálního počítače a ukončení smyčky.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Krok 4 – spustit skript
Teď, když jste si stáhli a změnit skript na základě vašich potřeb, o tento skript k vytvoření databáze back-end virtuální počítače s několika síťovými kartami.

1. Uložte skript a spusťte jej z **PowerShell** příkazového řádku, nebo **prostředí PowerShell ISE**. Počáteční výstupu se zobrazí, jak je znázorněno níže.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Zadejte informace potřebné v řádku přihlašovací údaje a klikněte na **OK**. Vrátí se následující výstup.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5: Konfigurace směrování v rámci operačního systému Virtuálního počítače

Azure DHCP přiřadí výchozí bránu první (primární) síťové rozhraní připojené k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní, ale komunikovat s prostředky mimo jejich podsíť. Konfigurace směrování pro sekundární síťová rozhraní, naleznete v následujících článcích:

- [Konfigurace virtuálního počítače s Windows pro více síťových rozhraní](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Konfigurace virtuálního počítače s Linuxem pro více síťových rozhraní](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
