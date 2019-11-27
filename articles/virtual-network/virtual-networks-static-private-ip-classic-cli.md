---
title: Konfigurace privátních IP adres pro virtuální počítače (Classic) – CLI
titlesuffix: Azure Virtual Network
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače (Classic) pomocí klasické rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 9deaf4b1d80ad4e55e7c971998e8b1f5ea562257
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196583"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Konfigurace privátních IP adres pro virtuální počítač (Classic) pomocí Azure classic CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. [V modelu nasazení Správce prostředků můžete také spravovat statickou privátní IP adresu](virtual-networks-static-private-ip-arm-cli.md).

Ukázky Azure classic CLI příkazy, které následují očekávat jednoduché prostředí už vytvořili. Pokud chcete spustit příkazy, které se zobrazují v tomto dokumentu, nejdříve Sestavte testovací prostředí popsané v tématu [vytvoření virtuální](virtual-networks-create-vnet-classic-cli.md)sítě.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak zadat statickou privátní IP adresu při vytváření virtuálního počítače
Pokud chcete vytvořit nový virtuální počítač s názvem *DNS01* v nové cloudové službě s názvem *TestService* , která je založená na výše uvedeném scénáři, postupujte podle následujících kroků:

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přejděte na téma [Instalace a konfigurace rozhraní příkazového řádku Azure](/cli/azure/install-cli-version-1.0) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Spuštěním příkazu **Azure Service Create** vytvořte cloudovou službu.
   
        azure service create TestService --location uscentral
   
    Očekávaný výstup:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Spuštěním příkazu **Azure Create VM** vytvořte virtuální počítač. Všimněte si, že hodnota pro statickou privátní IP adresu. Seznam uvedený za výstupem vysvětluje použité parametry.
   
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
   
   * **-l (nebo --location)** . Oblasti Azure, ve kterém se vytvoří virtuální počítač. V našem scénáři je to *centralus*.
   * **-n (nebo--VM-Name)** . Název virtuálního počítače, který se má vytvořit.
   * **-w (nebo--Virtual-Network-Name)** . Název sítě VNet, ve kterém se vytvoří virtuální počítač. 
   * **-S (nebo--Static-IP)** . Statické privátní IP adresu pro virtuální počítač.
   * **TestService**. Název cloudové služby, kde se vytvoří virtuální počítač.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v 14.2**. Bitová kopie používaná k vytvoření virtuálního počítače.
   * **AdminUser**. Místní správce pro virtuální počítač Windows.
   * <strong>AdminP@ssw0rd</strong>. Heslo místního správce pro virtuální počítač Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statické privátní informace o IP adrese virtuálního počítače
Pokud chcete zobrazit informace o statických privátních IP adresách pro virtuální počítač vytvořený pomocí skriptu uvedeného výše, spusťte následující příkaz rozhraní příkazového řádku Azure a sledujte hodnotu pro *Network StaticIP*:

    azure vm static-ip show DNS01

Očekávaný výstup:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Postup odebrání statickou privátní IP adresu z virtuálního počítače
Odeberte statickou privátní IP adresu přidat do virtuálního počítače ve skriptu vyšší, spusťte následující příkaz rozhraní příkazového řádku Azure:

    azure vm static-ip remove DNS01

Očekávaný výstup:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Postup přidání statické privátní IP adresy do existujícího virtuálního počítače
Pokud chcete do virtuálního počítače, který jste vytvořili pomocí skriptu uvedeného výše, přidat statickou privátní IP adresu, spusťte následující příkaz:

    azure vm static-ip set DNS01 192.168.1.101

Očekávaný výstup:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné. Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejnou adresu jako privátní IP adresa přiřazená k virtuálnímu počítači Azure nebo ztratíte připojení k virtuálnímu počítači. Nepřiřazujte ručně veřejnou IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače.

## <a name="next-steps"></a>Další kroky
* Seznamte se s [rezervovanými veřejnými IP](virtual-networks-reserved-public-ip.md) adresami.
* Přečtěte si informace o [veřejných IP adresách na úrovni instance (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Projděte si [vyhrazená IP adresa rozhraní REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).