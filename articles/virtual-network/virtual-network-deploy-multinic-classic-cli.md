---
title: Vytvoření virtuálního počítače (Classic) s několika síťovými kartami – Azure Classic CLI | Microsoft Docs
description: Naučte se vytvářet virtuální počítače (Classic) s několika síťovými kartami pomocí rozhraní příkazového řádku (CLI) Azure Classic.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 2dc437b15f73866f76361da529690eac7a10af1a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058749"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Vytvoření virtuálního počítače (Classic) s několika síťovými kartami pomocí Azure Classic CLI

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

V Azure můžete vytvářet virtuální počítače a k jednotlivým virtuálním počítačům připojit několik síťových rozhraní (nic). Více síťových adaptérů povoluje oddělení typů přenosů mezi síťovými kartami. Například jedna síťová karta může komunikovat s internetem, zatímco další komunikuje pouze s interními prostředky, které nejsou připojené k Internetu. Možnost oddělit síťový provoz mezi více síťovými kartami se vyžaduje pro mnoho síťových virtuálních zařízení, jako jsou třeba doručování aplikací a řešení pro optimalizaci sítě WAN.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi:  [Správce prostředků a klasický](../resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Naučte se provádět tyto kroky pomocí [modelu nasazení Správce prostředků](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Následující kroky používají skupinu prostředků s názvem *IaaSStory* pro webové servery a skupinu prostředků s názvem *IaaSStory-back-end* pro databázové servery.

## <a name="prerequisites"></a>Požadavky
Než budete moct vytvořit databázové servery, musíte vytvořit skupinu prostředků *IaaSStory* se všemi potřebnými prostředky pro tento scénář. Chcete-li vytvořit tyto prostředky, proveďte následující kroky. Vytvořte virtuální síť podle kroků uvedených v článku [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-cli.md) .

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Nasazení back-endu virtuálních počítačů
Back-endové virtuální počítače závisí na vytváření těchto prostředků:

* **Účet úložiště pro datové disky**. Pro lepší výkon datové disky na databázových serverech budou používat technologii SSD (Solid State Drive), která vyžaduje účet Premium Storage. Ujistěte se, že umístění Azure, které nasazujete, podporuje Premium Storage.
* **Síťové adaptéry**. Každý virtuální počítač bude mít dvě síťové karty, jeden pro přístup k databázi a jeden pro správu.
* **Skupina dostupnosti:** Všechny databázové servery budou přidány do jedné skupiny dostupnosti, aby bylo zajištěno, že alespoň jeden z virtuálních počítačů bude během údržby spuštěn.

### <a name="step-1---start-your-script"></a>Krok 1 – spuštění skriptu
Můžete si stáhnout celý skript bash, který se [tady](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)používá. Provedením následujících kroků změníte skript pro práci ve vašem prostředí:

1. Hodnoty proměnných níže změňte na základě stávající skupiny prostředků nasazené výše v části [požadavky](#prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Hodnoty proměnných můžete změnit v závislosti na hodnotách, které chcete použít pro nasazení back-endu.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2 – vytvoření nezbytných prostředků pro virtuální počítače
1. Vytvoří novou cloudovou službu pro všechny back-endové virtuální počítače. Všimněte si použití `$backendCSName` proměnné pro název skupiny prostředků a `$location` pro oblast Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Vytvořte účet Premium Storage pro operační systém a datové disky, které budou používat vaše virtuální počítače.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Krok 3 – Vytvoření virtuálních počítačů s více síťovými kartami
1. Spusťte smyčku pro vytvoření více virtuálních počítačů na základě `numberOfVMs` proměnných.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Pro každý virtuální počítač zadejte název a IP adresu každého ze dvou síťových karet.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Vytvořte virtuální počítač. Všimněte si použití `--nic-config` parametru, který obsahuje seznam všech síťových adaptérů s názvem, podsítí a IP adresou.

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

### <a name="step-4---run-the-script"></a>Krok 4 – spuštění skriptu
Teď, když jste stáhli a změnili skript na základě vašich potřeb, spusťte skript pro vytvoření virtuálních počítačů databáze back-end s více síťovými kartami.

1. Uložte skript a spusťte ho z terminálu **bash** . Zobrazí se počáteční výstup, jak je znázorněno níže.

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

2. Po několika minutách se spuštění ukončí a zobrazí se zbytek výstupu, jak je uvedeno níže.

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

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5 – konfigurace směrování v operačním systému virtuálního počítače

Azure DHCP přiřadí výchozí bránu k prvnímu (primárnímu) síťovému rozhraní připojenému k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní však můžou komunikovat s prostředky mimo vlastní podsíť. Pokud chcete nakonfigurovat směrování pro sekundární síťová rozhraní, přečtěte si téma [Směrování v operačním systému virtuálního počítače s několika síťovými rozhraními](virtual-network-network-interface-vm.md).
