---
title: Nakonfigurovat privátní IP adresy pro virtuální počítače (klasické) - 1.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače (klasické) pomocí rozhraní příkazového řádku Azure (CLI) 1.0.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c23419fdee7330d091d4699714f4ec08e72be2df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-cli-10"></a>Nakonfigurovat privátní IP adresy pro virtuální počítač (klasický) pomocí Azure CLI 1.0

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení Resource Manager](virtual-networks-static-private-ip-arm-cli.md).

Níže uvedené příkazy rozhraní příkazového řádku Azure ukázka očekávat jednoduché prostředí již vytvořeny. Pokud chcete ke spuštění příkazů, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Postup při vytváření virtuálního počítače zadat statickou privátní IP adresu
Chcete-li vytvořit nový virtuální počítač s názvem *DNS01* v novou cloudovou službu s názvem *TestService* závislosti na scénáři výše, postupujte takto:

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přejděte na téma [Instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Spustit **vytvoření azure služba** příkaz pro vytvoření cloudové služby.
   
        azure service create TestService --location uscentral
   
    Očekávaný výstup:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Spustit **azure vytvořit virtuální počítač** příkaz pro vytvoření virtuálního počítače. Všimněte si, hodnota privátní statické IP adresy. Seznam uvedený za výstupem vysvětluje použité parametry.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Očekávaný výstup:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (nebo --location)**. Oblast Azure, kde bude vytvořen virtuální počítač. V našem scénáři je to *centralus*.
   * **-n (nebo název virtuálního počítače –)**. Název virtuálního počítače, který se má vytvořit.
   * **-w (nebo--virtuální síťový název)**. Název sítě VNet, kde bude vytvořen virtuální počítač. 
   * **-S (nebo--statické ip)**. Statickou privátní IP adresu pro virtuální počítač.
   * **TestService**. Název cloudové služby, kde bude vytvořen virtuální počítač.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Bitová kopie používaná k vytvoření virtuálního počítače.
   * **AdminUser**. Místní správce pro virtuální počítač s Windows.
   * **AdminP@ssw0rd**. Heslo místního správce pro virtuální počítač Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statickou privátní IP adresu informace pro virtuální počítač
Chcete-li zobrazit statické soukromé informace IP adresu pro virtuální počítač vytvořený pomocí skriptu pro výše uvedené, spuštěním následujícího příkazu příkazového řádku Azure CLI a zkontrolujte, jestli hodnota pro *sítě StaticIP*:

    azure vm static-ip show DNS01

Očekávaný výstup:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Postup odebrání statickou privátní IP adresu z virtuálního počítače
Odeberte statickou privátní IP adresu přidat do virtuálního počítače ve skriptu výše, spusťte následující příkaz rozhraní příkazového řádku Azure:

    azure vm static-ip remove DNS01

Očekávaný výstup:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Postup přidání statickou privátní IP adresu do stávajícího virtuálního počítače
Chcete-li přidat statického privátní IP adresu, které se virtuální počítač vytvořený skript výše, o následující příkaz:

    azure vm static-ip set DNS01 192.168.1.101

Očekávaný výstup:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné. Pokud ručně nastavit privátní IP adresu v operačním systému, ujistěte se, že se jedná o stejnou adresu jako přiřazené k virtuálnímu počítači Azure privátní IP adresy nebo ztratíte připojení k virtuálnímu počítači. Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače.

## <a name="next-steps"></a>Další postup
* Další informace o [vyhrazené veřejné IP adresy](virtual-networks-reserved-public-ip.md) adresy.
* Další informace o [veřejné IP (splnění) na úrovni instance](virtual-networks-instance-level-public-ip.md) adresy.
* Obrátit [vyhrazené IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).

