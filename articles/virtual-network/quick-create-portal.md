---
title: "Vytvoření virtuální sítě ve službě Azure - Portal | Microsoft Docs"
description: "Rychle se Naučte se vytvořit virtuální síť pomocí portálu Azure. Virtuální síť umožňuje mnoho typů prostředků Azure, aby soukromě vzájemně komunikovat."
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
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 8b6a4abdb7677417462392feade0c7cfdf99246f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Vytvoření virtuální sítě pomocí Portálu Azure

V tomto článku zjistěte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě, můžete nasadit dva virtuální počítače do virtuální sítě a soukromě komunikaci mezi nimi a s Internetem.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. Zadejte, nebo vyberte následující informace a potom vyberte **vytvořit**:
    - **Name**: *myVirtualNetwork*
    - **Adresní prostor**: přijměte výchozí. Adresní prostor je zadat v notaci CIDR.
    - **Předplatné**: vyberte své předplatné.
    - **Skupina prostředků**: vyberte **vytvořit nový** a zadejte *myResourceGroup*.
    - **Umístění**: Vyberte * východní USA **.
    - **Podsíť, název**: přijměte výchozí nastavení.
    - **Podsíť, rozsah adres**: přijměte výchozí nastavení.
    - **Koncové body služby**: přijměte výchozí nastavení.

    ![Zadejte základní informace o vaší virtuální sítě](./media/quick-create-portal/virtual-network.png)

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Virtuální síť umožňuje několik typů prostředků Azure pro soukromě komunikaci mezi sebou a s Internetem. Virtuální počítač je jeden typ prostředku, který můžete nasadit do virtuální sítě.

1. Vyberte **+ vytvořit prostředek** najít v levém dolním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.
3. Zadejte, nebo vyberte následující informace a potom vyberte **OK**:
    - **Název**: *myVm1*
    - **Uživatelské jméno**: Zadejte uživatelské jméno dle vlastního výběru.
    - **Heslo**: Zadejte heslo dle vlastního výběru. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
    - **Předplatné**: vyberte své předplatné.
    - **Skupina prostředků**: vyberte **použít existující** a vyberte **myResourceGroup**.
    - **Umístění**: vyberte *východní USA*.

    ![Zadejte základní informace o virtuálním počítači](./media/quick-create-portal/virtual-machine-basics.png)
4. Vyberte velikost virtuálního počítače a pak vyberte **vyberte**.
5. V části **nastavení**, *myVirtualNetwork* by měla být vybrána pro **virtuální síť**, ale pokud není, vyberte **virtuální síť** , pak vyberte *myVirtualNetwork*. Nechte *výchozí* vybrané pro **podsíť**a potom vyberte **OK**.

    ![Vyberte virtuální síť.](./media/quick-create-portal/virtual-machine-network-settings.png)
6. Na **Souhrn** vyberte **vytvořit** ke spuštění nasazení virtuálního počítače. Virtuální počítač trvá několik minut pro nasazení. 
7. Úplné kroky 1 – 6 znovu, ale v kroku 3, název virtuálního počítače *Můjvp2*.

## <a name="connect-to-a-virtual-machine"></a>Připojit k virtuálnímu počítači

1. Po *myVm1* je vytvořen, vzdáleně připojit k němu. V horní části portálu Azure, zadejte *myVm1*. Když **myVm1** se zobrazí ve výsledcích hledání, vyberte ho. Vyberte **Connect** tlačítko.

    ![Přehled virtuálních počítačů](./media/quick-create-portal/virtual-machine-overview.png)

2. Po výběru **Connect** tlačítko soubor Remote Desktop Protocol (.rdp) je vytvořen a stažena do počítače.  
3. Otevřete soubor stažený rdp. Po zobrazení výzvy vyberte **Connect**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při zpracování je Vytvoření virtuálního počítače), zvolte OK. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** nebo **pokračovat** pokračovat v připojení.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Z příkazového řádku, zadejte `ping myvm2`. Příkaz ping nezdaří, protože používá příkazu ping ICMP a ICMP není dovoleno prostřednictvím Windows firewall ve výchozím nastavení. Chcete-li povolit *Můjvp2* na příkaz ping *myVm1* v pozdější fázi, zadejte následující příkaz z příkazového řádku:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

2. Zavřete připojení ke vzdálené ploše *myVm1*. 

3. Proveďte kroky v [připojit k virtuálnímu počítači](#connect-to-a-virtual-machine), ale připojení k *Můjvp2*. Z příkazového řádku, zadejte `ping myvm1`.

    Budete moci úspěšně odeslat příkaz ping *myVm1* virtuální počítač z *Můjvp2* virtuální počítač protože:

    - povolené ICMP přes bránu Windows firewall na *myVm1* virtuálního počítače v předchozím kroku.
    - ve výchozím nastavení Azure umožňuje veškerý síťový provoz mezi prostředky ve stejné virtuální síti.

## <a name="communicate-to-the-internet"></a>Komunikoval s Internetem

1. Při připojení k stále *Můjvp2* virtuálního počítače z příkazového řádku, zadejte `ping bing.com`.

    Obdržíte čtyři odpovědi od vyhledávače bing.com. 

    Budete moci úspěšně odeslat příkaz ping internetových prostředků z *Můjvp2* virtuální počítač, protože žádný virtuální počítač může komunikovat odchozí k Internetu, ve výchozím nastavení.

2. Ukončete relaci vzdálené plochy.

## <a name="communicate-from-the-internet"></a>Komunikaci z Internetu

1. Získat veřejnou IP adresu *myVm1* virtuálního počítače. Na obrázku, v části Krok 1 [připojit k virtuálnímu počítači](#connect-to-a-virtual-machine), najdete v části veřejnou IP adresu. Na obrázku je adresu *13.90.241.247*. Adresu pro virtuální počítač se liší. 

2. Z vašeho počítače příkazem ping otestovat veřejnou IP adresu vašeho *myVm1* virtuálního počítače. Selhání příkazu ping, i když je otevřený přes bránu Windows firewall ICMP.

    Příkaz ping nezdaří, protože všechny přenosy na virtuálních počítačích s Windows, s výjimkou připojení ke vzdálené ploše přes port 3389, byl odepřen v Azure, ve výchozím nastavení. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto článku jste nasadili výchozí virtuální síť s jednou podsítí. Naučte se vytvářet vlastní virtuální síť s více podsítěmi, nadále kurz pro vytvoření vlastní virtuální sítě.

> [!div class="nextstepaction"]
> [Vytvoření vlastní virtuální sítě](virtual-networks-create-vnet-arm-pportal.md)
