---
title: Konfigurace privátních IP adres pro virtuální počítače (Classic) – klasické rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače (Classic) pomocí klasické rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: genlin
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
ms.openlocfilehash: f340b9843ed5763a20d2035e3add86123a1298e8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971140"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Konfigurace privátních IP adres pro virtuální počítač (Classic) pomocí Azure classic CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení Resource Manager](virtual-networks-static-private-ip-arm-cli.md).

Ukázky Azure classic CLI příkazy, které následují očekávat jednoduché prostředí už vytvořili. Pokud chcete spustit příkazy, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvořit síť vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak zadat statickou privátní IP adresu při vytváření virtuálního počítače
Chcete-li vytvořit nový virtuální počítač s názvem *DNS01* v novou cloudovou službu s názvem *TestService* založený na výše uvedeném scénáři, postupujte podle těchto kroků:

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přejděte na téma [Instalace a konfigurace rozhraní příkazového řádku Azure](/cli/azure/install-cli-version-1.0) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Spustit **vytvoření služby azure** příkaz k vytvoření cloudové služby.
   
        azure service create TestService --location uscentral
   
    Očekávaný výstup:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Spustit **azure vytvořit virtuální počítač** příkaz pro vytvoření virtuálního počítače. Všimněte si, že hodnota pro statickou privátní IP adresu. Seznam uvedený za výstupem vysvětluje použité parametry.
   
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
   
   * **-l (nebo --location)**. Oblasti Azure, ve kterém se vytvoří virtuální počítač. V našem scénáři je to *centralus*.
   * **-n (nebo--vm-name)**. Název virtuálního počítače, který se má vytvořit.
   * **-w (nebo--virtuální síťový název)**. Název sítě VNet, ve kterém se vytvoří virtuální počítač. 
   * **-S (nebo--statické ip)**. Statické privátní IP adresu pro virtuální počítač.
   * **TestService**. Název cloudové služby, kde se vytvoří virtuální počítač.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012 R2-x64-v14.2**. Bitová kopie používaná k vytvoření virtuálního počítače.
   * **AdminUser**. Místní správce pro virtuální počítač Windows.
   * **AdminP@ssw0rd**. Heslo místního správce pro virtuální počítač Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statické privátní informace o IP adrese virtuálního počítače
Chcete-li zobrazit statické privátní informace o IP adresu pro virtuální počítač vytvořen pomocí výše uvedeného skriptu, spusťte následující příkaz rozhraní příkazového řádku Azure a podívejte se hodnota *sítě StaticIP*:

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
Přidat statickou privátní IP adresa na virtuální počítač vytvořený pomocí skriptu výše, o následující příkaz:

    azure vm static-ip set DNS01 192.168.1.101

Očekávaný výstup:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné. Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejnou adresu jako privátní IP adresa přiřazená k virtuálnímu počítači Azure nebo ztratíte připojení k virtuálnímu počítači. Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="next-steps"></a>Další postup
* Další informace o [vyhrazené veřejné IP adresy](virtual-networks-reserved-public-ip.md) adresy.
* Další informace o [veřejné IP (ILPIP) na úrovni instance](virtual-networks-instance-level-public-ip.md) adresy.
* Poraďte [vyhrazené IP rozhraní REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).