---
title: "Vytvoření virtuální sítě Azure - Portal | Microsoft Docs"
description: "Rychle se Naučte se vytvořit virtuální síť pomocí portálu Azure. Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače, pro soukromě komunikaci mezi sebou a s Internetem."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: c8f2cbe6b7377772e019a4ff90f91355ba0815ae
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Vytvoření virtuální sítě pomocí Portálu Azure

Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikaci mezi sebou a s Internetem. V tomto článku zjistěte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě, můžete nasadit dva virtuální počítače do virtuální sítě. Potom připojení jeden virtuální počítač z Internetu a soukromě komunikaci mezi dvěma virtuálními počítači.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. Zadejte, nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a potom vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **vytvořit nový** a zadejte *myResourceGroup*.|
    |Umístění| Vyberte **východní USA**.|

    ![Zadejte základní informace o vaší virtuální sítě](./media/quick-create-portal/create-virtual-network.png)

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti:

### <a name="create-the-first-vm"></a>Vytvoření první virtuální počítač

1. Vyberte **+ vytvořit prostředek** najít v levém dolním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.
3. Zadejte, nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a potom vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVm1|
    |Uživatelské jméno| Zadejte uživatelské jméno dle vlastního výběru.|
    |Heslo| Zadejte heslo dle vlastního výběru. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **použít existující** a vyberte **myResourceGroup**.|
    |Umístění| Vyberte **východní USA**|

    ![Základní informace o virtuálním počítači](./media/quick-create-portal/virtual-machine-basics.png)

4. Vyberte velikost pro virtuální počítač a potom vyberte **vyberte**.
5. V části **nastavení**přijměte všechny výchozí hodnoty a potom vyberte **OK**.

    ![Nastavení virtuálního počítače](./media/quick-create-portal/virtual-machine-settings.png)

6. V části **vytvořit** z **Souhrn**, vyberte **vytvořit** zahájíte nasazení virtuálního počítače. Virtuální počítač trvá několik minut pro nasazení. 

### <a name="create-the-second-vm"></a>Vytvořit druhý virtuální počítač

Úplné kroky 1 – 6 znovu, ale v kroku 3, název virtuálního počítače *Můjvp2*.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z Internetu

1. Po *myVm1* je vytvořen, k nim připojit. V horní části portálu Azure, zadejte *myVm1*. Když **myVm1** se zobrazí ve výsledcích hledání, vyberte ho. Vyberte **Connect** tlačítko.

    ![Připojit k virtuálnímu počítači](./media/quick-create-portal/connect-to-virtual-machine.png)

2. Po výběru **Connect** tlačítko soubor Remote Desktop Protocol (.rdp) je vytvořen a stažena do počítače.  
3. Otevřete soubor stažený rdp. Po zobrazení výzvy vyberte **Connect**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Je nutné vybrat **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat**, chcete-li pokračovat s připojením.

## <a name="communicate-privately-between-vms"></a>Soukromě komunikaci mezi virtuálními počítači

1. Z prostředí PowerShell, zadejte `ping myvm2`. Příkaz ping nezdaří, protože ping používá protokol (ICMP) a ICMP není přes bránu Windows firewall povolena ve výchozím nastavení.
2. Chcete-li povolit *Můjvp2* na příkaz ping *myVm1* v pozdější fázi, zadejte následující příkaz z prostředí PowerShell, což umožňuje ICMP příchozí komunikace přes bránu Windows firewall:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

3. Zavřete připojení ke vzdálené ploše *myVm1*. 

4. Proveďte kroky v [připojení k virtuálnímu počítači z Internetu](#connect-to-a-vm-from-the-internet) znovu, ale připojení k *Můjvp2*. Z příkazového řádku, zadejte `ping myvm1`.

    Obdržíte odpovědi z *myVm1*, protože povolené ICMP přes bránu Windows firewall na *myVm1* virtuálních počítačů v předchozím kroku.

5. Zavřete připojení ke vzdálené ploše *Můjvp2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili výchozí virtuální sítě a dva virtuální počítače. Připojený k jeden virtuální počítač z Internetu a soukromě přenášená mezi virtuálního počítače a jiným virtuálním Počítačem. Další informace o nastavení virtuální sítě najdete v tématu [spravovat virtuální sítě](manage-virtual-network.md).

Ve výchozím nastavení Azure umožňuje neomezený privátní komunikaci mezi virtuálními počítači, ale umožňuje pouze příchozí připojení ke vzdálené ploše na virtuálních počítačích Windows z Internetu. Postup povolení nebo zakázání různé typy síťové komunikace do a z virtuálních počítačů, přechodu na v dalším kurzu.

> [!div class="nextstepaction"]
> [Filtrování provozu sítě přenosů](virtual-networks-create-nsg-arm-pportal.md)