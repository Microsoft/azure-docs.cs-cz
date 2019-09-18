---
title: Vytvoření virtuálního počítače (klasické) s několika síťovými kartami – Azure PowerShell | Microsoft Docs
description: Naučte se vytvářet virtuální počítače (Classic) s několika síťovými kartami pomocí PowerShellu.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: af22bc43a06be74c7a4b6c869725a19fc87a0f3e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058732"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Vytvoření virtuálního počítače (Classic) s několika síťovými kartami pomocí prostředí PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

V Azure můžete vytvářet virtuální počítače a k jednotlivým virtuálním počítačům připojit několik síťových rozhraní (nic). Více síťových adaptérů povoluje oddělení typů přenosů mezi síťovými kartami. Například jedna síťová karta může komunikovat s internetem, zatímco další komunikuje pouze s interními prostředky, které nejsou připojené k Internetu. Možnost oddělit síťový provoz mezi více síťovými kartami se vyžaduje pro mnoho síťových virtuálních zařízení, jako jsou třeba doručování aplikací a řešení pro optimalizaci sítě WAN.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi:  [Správce prostředků a klasický](../resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Naučte se provádět tyto kroky pomocí [modelu nasazení Správce prostředků](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Následující kroky používají skupinu prostředků s názvem *IaaSStory* pro webové servery a skupinu prostředků s názvem *IaaSStory-back-end* pro databázové servery.

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit databázové servery, musíte vytvořit skupinu prostředků *IaaSStory* se všemi potřebnými prostředky pro tento scénář. Chcete-li vytvořit tyto prostředky, proveďte následující kroky. Vytvořte virtuální síť podle kroků uvedených v článku [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-netcfg-ps.md) .

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Vytvoření virtuálních počítačů back-endu
Back-endové virtuální počítače závisí na vytváření těchto prostředků:

* **Podsíť back-endu**. Databázové servery budou součástí samostatné podsítě pro oddělení provozu. Níže uvedený skript předpokládá, že tato podsíť existuje ve virtuální síti s názvem *WTestVnet*.
* **Účet úložiště pro datové disky**. Pro lepší výkon datové disky na databázových serverech budou používat technologii SSD (Solid State Drive), která vyžaduje účet Premium Storage. Ujistěte se, že umístění Azure, které nasazujete, podporuje Premium Storage.
* **Skupina dostupnosti:** Všechny databázové servery budou přidány do jedné skupiny dostupnosti, aby bylo zajištěno, že alespoň jeden z virtuálních počítačů bude během údržby spuštěn.

### <a name="step-1---start-your-script"></a>Krok 1 – spuštění skriptu
Můžete si stáhnout celý skript PowerShellu, který se [tady](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)používá. Použijte následující postup ke změně skriptu pro práci ve vašem prostředí.

1. Hodnoty proměnných níže změňte na základě stávající skupiny prostředků nasazené výše v části [požadavky](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Hodnoty proměnných můžete změnit v závislosti na hodnotách, které chcete použít pro nasazení back-endu.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2 – vytvoření nezbytných prostředků pro virtuální počítače
Musíte vytvořit novou cloudovou službu a účet úložiště pro datové disky pro všechny virtuální počítače. Také je nutné zadat obrázek a účet místního správce pro virtuální počítače. Chcete-li vytvořit tyto prostředky, proveďte následující kroky:

1. Vytvořte novou cloudovou službu.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Vytvořte nový účet Premium Storage.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Nastavte účet úložiště, který jste vytvořili výše, jako aktuální účet úložiště pro vaše předplatné.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Vyberte image pro virtuální počítač.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Nastavte přihlašovací údaje účtu místního správce.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Krok 3 – Vytvoření virtuálních počítačů
Pokud chcete vytvořit libovolný počet virtuálních počítačů a vytvořit potřebné síťové rozhraní a virtuální počítače v rámci smyčky, musíte použít smyčku. Pokud chcete vytvořit síťové karty a virtuální počítače, proveďte následující kroky.

1. Spusťte smyčku, která opakuje příkazy k vytvoření virtuálního počítače a dvou síťových karet tolikrát, kolik je potřeba, podle hodnoty `$numberOfVMs` proměnné. `for`

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. `VMConfig` Vytvořte objekt, který určuje obrázek, velikost a skupinu dostupnosti pro virtuální počítač.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Zřiďte virtuální počítač jako virtuální počítač s Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Nastavte výchozí síťovou kartu a přiřaďte jí statickou IP adresu.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Přidejte druhou síťovou kartu pro každý virtuální počítač.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Vytvořte na datových discích pro každý virtuální počítač.

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

7. Vytvořte každý virtuální počítač a ukončete smyčku.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Krok 4 – spuštění skriptu
Teď, když jste stáhli a změnili skript na základě vašich potřeb, Runt skript pro vytvoření virtuálních počítačů back-endu s více síťovými kartami.

1. Uložte skript a spusťte ho z příkazového řádku PowerShellu nebo **PowerShell ISE**. Zobrazí se počáteční výstup, jak je znázorněno níže.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Vyplňte informace potřebné v příkazovém řádku s přihlašovacími údaji a klikněte na **OK**. Vrátí se následující výstup.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5 – konfigurace směrování v operačním systému virtuálního počítače

Azure DHCP přiřadí výchozí bránu k prvnímu (primárnímu) síťovému rozhraní připojenému k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní však můžou komunikovat s prostředky mimo vlastní podsíť. Pokud chcete nakonfigurovat směrování pro sekundární síťová rozhraní, přečtěte si následující články:

- [Konfigurace virtuálního počítače s Windows pro více síťových karet](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Konfigurace virtuálního počítače se systémem Linux pro více síťových karet](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
