---
title: Vytvoření virtuálního počítače (klasické) s více síťovými kartami - 1.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvořit virtuální počítač (klasický) s více síťovými kartami pomocí rozhraní příkazového řádku Azure (CLI) 1.0.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0b56ab474ff23748487c50bd34487c80242c6429
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Vytvoření virtuálního počítače (klasické) s více síťovými kartami pomocí Azure CLI 1.0

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Můžete vytvořit virtuální počítače (VM) v Azure a připojit více síťových rozhraní (NIC) ke každému z virtuálních počítačů. Několik síťových adaptérů povolit oddělení typů přenosů mezi síťové adaptéry. Jeden síťový adaptér může například komunikují přes Internet, zatímco jiné komunikuje jenom s interním prostředkům, které nejsou připojené k Internetu. Schopnost oddělit síťový provoz na několik síťových adaptérů je vyžadována pro mnoho síťových virtuálních zařízení, například doručení aplikace a řešení optimalizace sítě WAN.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Naučte se provádět tyto kroky, pomocí [modelu nasazení Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Následující postup použijte skupinu prostředků s názvem *IaaSStory* pro webové servery a skupinu prostředků s názvem *IaaSStory back-end* pro servery DB.

## <a name="prerequisites"></a>Požadavky
Před vytvořením servery DB, je potřeba vytvořit *IaaSStory* skupina prostředků se všechny potřebné prostředky pro tento scénář. Chcete-li vytvořit tyto prostředky, proveďte kroky, které následují. Vytvoření virtuální sítě pomocí následujících kroků v [vytvořit virtuální síť](virtual-networks-create-vnet-classic-cli.md) článku.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Nasazení virtuálních počítačů back-end
Virtuální počítače back-end závisí na vytvoření v následujících zdrojích informací:

* **Účet úložiště pro datové disky**. Pro lepší výkon datové disky v databázových serverech použije technologii SSD jednotky (SSD Solid-State Drive), která vyžaduje účet úložiště premium. Zajistěte, aby umístění Azure, můžete nasadit pro podporu služby storage úrovně premium.
* **Síťové adaptéry**. Každý virtuální počítač bude mít dva síťové adaptéry, jeden pro přístup k databázi a jeden pro správu.
* **Skupina dostupnosti:** Všechny databázové servery se zařadí do jedné dostupnosti nastavte, ujistěte se, že nejméně jedna z virtuálních počítačů je nahoru a během údržby.

### <a name="step-1---start-your-script"></a>Krok 1 – spustit skript
Si můžete stáhnout skript úplné bash používá [zde](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Proveďte následující kroky, chcete-li změnit skript pro práci ve vašem prostředí:

1. Změňte hodnoty proměnných níže podle vaší existující skupinu prostředků, které jsou nasazené výše v [požadavky](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Změňte hodnoty proměnných níže na základě hodnot, které chcete použít pro vaše nasazení back-end.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2 – Vytvoření potřebné prostředky pro virtuální počítače
1. Vytvořte novou cloudovou službu pro všechny virtuální počítače back-end. Všimněte si použití `$backendCSName` proměnná pro název skupiny prostředků a `$location` pro oblast Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Vytvořte účet úložiště premium pro disky operačního systému a dat má být používána vaše virtuální počítače.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Krok 3 – vytvoření virtuálních počítačů s více síťovými kartami
1. Spustit smyčku vytvořit víc virtuálních počítačů, na základě `numberOfVMs` proměnné.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Pro každý virtuální počítač zadejte název a IP adresu každé dva síťové adaptéry.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Vytvoření virtuálního počítače. Všimněte si použití `--nic-config` parametr, který obsahuje seznam všech síťových adaptérů s názvem, podsíť a IP adresu.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Pro každý virtuální počítač vytvořte dva datových disků.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Krok 4 – spuštění skriptu
Teď, když jste stáhli a změnit na základě potřeb skriptu, spusťte skript pro vytvoření back-end databáze virtuálních počítačů s více síťovými kartami.

1. Uložte skript a spusťte jej z vaší **Bash** terminálu. Počáteční výstupu uvidíte, jak je uvedeno níže.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Po několika minutách se ukončí provádění a zbytek výstupu se zobrazí, jak je uvedeno níže.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5: Konfigurace směrování v rámci operačního systému Virtuálního počítače

Azure DHCP přiřadí výchozí bránu na první (primární) síťové rozhraní připojené k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní může, ale komunikovat s prostředky mimo jejich podsítě. Konfigurace směrování pro sekundární síťová rozhraní najdete v tématu [směrování v rámci operačního systému virtuálního počítače s více síťovými rozhraními](virtual-network-network-interface-vm.md).
