---
title: Vytvoření virtuální sítě – Rychlý start – Azure Portal | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak vytvořit virtuální síť pomocí webu Azure Portal. Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 7107dc72686004141d8bea0083089cba065a9f4c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rychlý start: Vytvoření virtuální sítě pomocí webu Azure Portal

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Pak se k jednomu virtuálnímu počítači připojíte z internetu a navážete soukromou komunikaci s druhým virtuálním počítačem.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Vytvořit novou** a zadejte *myResourceGroup*.|
    |Umístění| Vyberte **USA – východ**.|

    ![Zadání základních informací o virtuální síti](./media/quick-create-portal/create-virtual-network.png)

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače:

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVm1|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění| Vyberte **USA – východ**.|

    ![Základní informace o virtuálním počítači](./media/quick-create-portal/virtual-machine-basics.png)

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení** přijměte všechny výchozí hodnoty a pak vyberte **OK**.

    ![Nastavení virtuálního počítače](./media/quick-create-portal/virtual-machine-settings.png)

6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače. Nasazení virtuálního počítače trvá několik minut. 

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Zopakujte kroky 1 až 6. ale v kroku 3 pojmenujte virtuální počítač *myVm2*.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

1. Po vytvoření virtuálního počítače *myVm1* se k němu připojte. V horní části webu Azure Portal zadejte *myVm1*. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVm1**, vyberte ho. Klikněte na tlačítko **Připojit**.

    ![Připojení k virtuálnímu počítači](./media/quick-create-portal/connect-to-virtual-machine.png)

2. Po kliknutí na tlačítko **Připojit** se vytvoří soubor protokolu RDP (.rdp) a stáhne se na váš počítač.  
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. V PowerShellu zadejte `ping myvm2`. Příkaz ping selže, protože využívá protokol ICMP (Internet Control Message Protocol), který ve výchozím nastavení nemá povolený průchod bránou Windows Firewall.
2. Pokud chcete virtuálnímu počítači *myVm2* povolit otestování virtuálního počítače *myVm1* pomocí příkazu ping v pozdějším kroku, zadejte v PowerShellu následující příkaz, který povolí příchozí provoz protokolu ICMP přes bránu Windows Firewall:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

3. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*. 

4. Zopakujte kroky uvedené v části [Připojení k virtuálnímu počítači z internetu](#connect-to-a-vm-from-the-internet), ale připojte se k virtuálnímu počítači *myVm2*. Na příkazovém řádku zadejte `ping myvm1`.

    Obdržíte odpovědi z virtuálního počítače *myVm1*, protože jste v předchozím kroku povolili průchod protokolu ICMP bránou Windows Firewall na virtuálním počítači *myVm1*.

5. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. K jednomu virtuálnímu počítači jste se připojili z internetu a navázali jste soukromou komunikaci mezi tímto a druhým virtuálním počítačem. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md).

Ve výchozím nastavení Azure umožňuje neomezenou soukromou komunikaci mezi virtuálními počítači, ale u virtuálních počítačů s Windows povoluje pouze příchozí připojení ke vzdálené ploše z internetu. Informace o povolení nebo zakázání různých typů síťové komunikace do a z virtuálních počítačů najdete v dalším kurzu [Filtrování síťového provozu](tutorial-filter-network-traffic.md).