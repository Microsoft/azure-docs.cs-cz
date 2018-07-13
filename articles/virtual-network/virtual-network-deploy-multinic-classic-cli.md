---
title: Vytvoření virtuálního počítače (klasické) s několika síťovými kartami – Azure CLI 1.0 | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač (klasický) s několika síťovými kartami pomocí rozhraní příkazového řádku Azure (CLI) 1.0.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651982"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Vytvoření virtuálního počítače (klasické) s několika síťovými kartami pomocí rozhraní příkazového řádku Azure CLI 1.0

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Můžete vytvořit virtuální počítače (VM) v Azure a připojit několik síťových rozhraní (NIC) ke každé z vašich virtuálních počítačů. Několik síťových adaptérů umožňuje oddělení typů přenosů napříč síťovými kartami. Například jednou síťovou KARTOU může komunikovat s Internetem, zatímco jiné komunikuje jenom s interním prostředkům, které nejsou připojené k Internetu. Schopnost oddělit síťový provoz napříč více síťových rozhraní se vyžaduje pro řadu síťových virtuálních zařízení, jako je doručování aplikací a řešení optimalizace sítě WAN.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak provést tento postup pomocí [modelu nasazení Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Následující postup použijte skupinu prostředků s názvem *IaaSStory* pro webové servery a skupinu prostředků s názvem *IaaSStory back-endu* pro servery DB.

## <a name="prerequisites"></a>Požadavky
Než budete moct vytvořit servery DB, je potřeba vytvořit *IaaSStory* skupina prostředků se všechny prostředky potřebné pro tento scénář. Pokud chcete vytvořit tyto prostředky, proveďte následující kroky. Vytvoření virtuální sítě pomocí následujících kroků v [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-cli.md) článku.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Nasazení virtuálního počítače back-end
Back endové virtuální počítače jsou závislé na vytváření z následujících prostředků:

* **Účet úložiště pro datové disky**. Pro lepší výkon datové disky v databázových serverech použije SSD (SOLID-State drive) technologii, která vyžaduje účet úložiště úrovně premium. Ujistěte se, že umístění Azure nasazujete do podporují službu premium storage.
* **Síťové adaptéry**. Každý virtuální počítač bude mít dva síťové adaptéry, jeden pro přístup k databázi a jeden pro správu.
* **Skupina dostupnosti:** Všechny databázové servery se přidají do jednoho dostupnosti nastavena na hodnotu Ujistěte se, že je alespoň jeden z virtuálních počítačů a během údržby.

### <a name="step-1---start-your-script"></a>Krok 1: Spusťte svůj skript
Můžete stáhnout skript úplné prostředí bash, které [tady](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Proveďte následující kroky, chcete-li změnit skript fungoval ve vašem prostředí:

1. Změňte hodnoty proměnných níže podle vaší existující skupinu prostředků, které jsou nasazené výše v [požadavky](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Změňte hodnoty proměnných níže podle hodnoty, které chcete použít pro vaše nasazení back-endu.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2: vytvoření potřebné prostředky pro virtuální počítače
1. Vytvořte novou cloudovou službu pro všechny virtuální počítače back-endu. Všimněte si použití `$backendCSName` proměnnou pro název skupiny prostředků a `$location` pro oblast Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Vytvoření účtu služby premium storage pro disky operačního systému a data použije jenom virtuální počítače.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Krok 3: vytvoření virtuálních počítačů s několika síťovými kartami
1. Spustit smyčka pro vytvoření několika virtuálních počítačů, na základě `numberOfVMs` proměnné.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Pro každý virtuální počítač zadejte název a IP adresy každé dva síťové adaptéry.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Vytvoření virtuálního počítače. Všimněte si, že využití `--nic-config` parametr, který obsahuje seznam všech síťových adaptérů s názvem, podsíti a IP adresu.

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

4. Pro každý virtuální počítač vytvořte dva datové disky.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Krok 4 – spustit skript
Teď, když jste stáhli a změnit skript na základě vašich potřeb, spusťte skript, který chcete vytvořit back-endu databáze virtuální počítače s několika síťovými kartami.

1. Uložte skript a spusťte jej z vašich **Bash** terminálu. Počáteční výstupu se zobrazí, jak je znázorněno níže.

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

2. Po několika minutách se ukončí provádění a zbývající část ve výstupu se zobrazí, jak je znázorněno níže.

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

Azure DHCP přiřadí výchozí bránu první (primární) síťové rozhraní připojené k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní, ale komunikovat s prostředky mimo jejich podsíť. Konfigurace směrování pro sekundární síťová rozhraní, naleznete v tématu [směrování v rámci operačního systému virtuálního počítače s několika síťovými rozhraními](virtual-network-network-interface-vm.md).
