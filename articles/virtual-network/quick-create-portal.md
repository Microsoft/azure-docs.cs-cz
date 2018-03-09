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
ms.openlocfilehash: 3c040f677aa25656148081d533e87cc55f1e22e7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Vytvoření virtuální sítě pomocí Portálu Azure

V tomto článku zjistěte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě, nasadíte dva virtuální počítače do virtuální sítě k otestování privátní síti komunikace mezi nimi.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. Jak je znázorněno na následujícím obrázku, zadejte *myVirtualNetwork* pro **název**, *myResourceGroup* pro **skupiny prostředků**, vyberte  **Umístění** a **předplatné**, přijměte zbývající výchozí hodnoty a pak vyberte **vytvořit**. 

    ![Zadejte základní informace o vaší virtuální sítě](./media/quick-create-portal/virtual-network.png)

    **Adresní prostor** je zadat v notaci CIDR. 10.0.0.0/16 prostoru adres zahrnuje 10.0.0.0-10.0.255.254. Virtuální síť obsahuje nula nebo více podsítí. Zadaná podsíť **rozsahu adres** zahrnuje 10.0.0.0-10.0.0.255 adresy IP. Pouze 10.0.0.4-10.0.0.254 jsou však k dispozici, protože první čtyři adresy (0-3) a poslední adresa v jednotlivých podsítích si vyhrazuje Azure. Jsou dostupné IP adresy přiřazené k prostředky nasazené v rámci virtuální sítě.

## <a name="test-network-communication"></a>Test síťové komunikace

Virtuální síť umožňuje několik typů prostředků Azure, aby soukromě vzájemně komunikovat. Virtuální počítač je jeden typ prostředku, který můžete nasadit do virtuální sítě. Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit privátní komunikaci mezi nimi později.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Vyberte **+ vytvořit prostředek** najít v levém dolním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.
3. Zadejte informace o virtuálním počítači znázorněno na obrázku, který následuje dále. **Uživatelské jméno** a **heslo** zadáte se používají k přihlášení k virtuálnímu počítači v pozdější fázi. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Vyberte vaše **předplatné**, rozhodnete použít existující *myResourceGroup* prostředků skupiny a ujistěte se, že **umístění** vybrané je stejné umístění, které jste vytvořili virtuální síť v. Po dokončení vyberte **OK**.

    ![Zadejte základní informace o virtuálním počítači](./media/quick-create-portal/virtual-machine-basics.png)
4. Vyberte velikost virtuálního počítače a pak vyberte **vyberte**. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Velikostí, které se zobrazují pro vás může být jiný než v následujícím příkladu: 

    ![Vyberte velikost virtuálního počítače](./media/quick-create-portal/virtual-machine-size.png)
5. V části **nastavení**, *myVirtualNetwork* by měla být vybrána pro **virtuální síť**, ale pokud není, vyberte **virtuální síť** , pak vyberte *myVirtualNetwork*. Nechte *výchozí* vybrané pro **podsíť**a potom vyberte **OK**.

    ![Vyberte virtuální síť.](./media/quick-create-portal/virtual-machine-network-settings.png)
6. Na **Souhrn** vyberte **vytvořit** ke spuštění nasazení virtuálního počítače. 
7. Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuální počítač je připojený k řídicímu panelu portálu Azure a souhrn virtuální počítač se automaticky otevře. Vyberte **sítě**.

    ![Informace o síti virtuálního počítače](./media/quick-create-portal/virtual-machine-networking.png)

    Uvidíte, že **privátní IP** adresa je *10.0.0.4*. V kroku 5, v části **nastavení**, jste vybrali *myVirtualNetwork* virtuální sítě a akceptovány podsíť s názvem *výchozí* pro **podsíť**. Pokud jste [vytvořili virtuální síť](#create-a-virtual-network), jste přijali výchozí hodnotu 10.0.0.0/24 pro podsíť **rozsahu adres**. Azure DHCP server přiřadí první dostupnou adresu podsítě, které vyberete k virtuálnímu počítači. Vzhledem k tomu, že Azure si vyhrazuje první čtyři adresy (0-3) každou podsíť, 10.0.0.4 je první dostupná IP adresa pro podsíť k dispozici.

    **Veřejnou IP adresu** adresu přiřazenou se liší od adresy přiřazené k virtuálnímu počítači. Azure přiřadí veřejné, Internet směrovatelné IP adresy pro každý virtuální počítač, ve výchozím nastavení. Veřejná IP adresa je přiřazena k virtuálnímu počítači z [fondu adres přiřazených každé oblasti Azure](https://www.microsoft.com/download/details.aspx?id=41653). Sice Azure nezná, která veřejná IP adresa je přiřazena k virtuálnímu počítači, ale operační systém spuštěný ve virtuálním počítači nemá žádné informace o tom všechny veřejné IP adresy přiřazené.
8. Úplné kroky 1-7 znovu, ale v kroku 3, název virtuálního počítače *Můjvp2*. 
9. Po vytvoření virtuálního počítače, vyberte **sítě**, jak jste v kroku 7. Zobrazí **privátní IP** adresa je *10.0.0.5*. Vzhledem k tomu, že Azure dřív přiřazené první použitelné adresu *10.0.0.4* v podsíti *myVm1* virtuální počítač přiřazen *10.0.0.5* k  *Můjvp2* virtuální počítač, protože se jedná o další dostupnou adresu v podsíti.

### <a name="connect-to-a-virtual-machine"></a>Připojit k virtuálnímu počítači

1. Vzdáleně se připojovat k *myVm1* virtuálního počítače. V horní části portálu Azure, zadejte *myVm1*. Když **myVm1** se zobrazí ve výsledcích hledání, vyberte ho. Vyberte **Connect** tlačítko.

    ![Přehled virtuálních počítačů](./media/quick-create-portal/virtual-machine-overview.png)
2. Po výběru **Connect** tlačítko soubor Remote Desktop Protocol (.rdp) je vytvořen a stažena do počítače.  
3. Otevřete soubor stažený rdp. Po zobrazení výzvy vyberte **Connect**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače a potom vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** nebo **pokračovat** pokračovat v připojení.

### <a name="validate-communication"></a>Ověření komunikace

Probíhá pokus o příkaz ping systému Windows virtuálního počítače selže, protože ping není přes bránu Windows firewall povolena ve výchozím nastavení. Chcete-li povolit příkaz ping na *myVm1*, zadejte následující příkaz z příkazového řádku:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

K ověření komunikace s *Můjvp2*, zadejte následující příkaz z příkazového řádku *myVm1* virtuálního počítače. Zadejte přihlašovací údaje, které jste použili při vytvoření virtuálního počítače a pak dokončete připojení:

```
mstsc /v:myVm2
```

Připojení ke vzdálené ploše je úspěšné, protože oba virtuální počítače mají privátní IP adresy přiřazené z *výchozí* podsítě a protože je otevřeno přes bránu Windows firewall ve výchozím nastavení vzdálené plochy. Budete moci připojit k *Můjvp2* podle názvu hostitele protože Azure automaticky poskytuje překlad názvů DNS pro všechny hostitele v rámci virtuální sítě. Z příkazového řádku příkaz ping *myVm1*, z *Můjvp2*.

```
ping myvm1
```

Příkaz ping je úspěšné, protože je povolené prostřednictvím brány Windows firewall na *myVm1* virtuálního počítače v předchozím kroku. Pokud chcete potvrdit odchozí komunikaci k Internetu, zadejte následující příkaz:

```
ping bing.com
```

Obdržíte čtyři odpovědi od vyhledávače bing.com. Ve výchozím nastavení může komunikovat jakéhokoli virtuálního počítače ve virtuální síti odchozí k Internetu. 

Ukončete relaci vzdálené plochy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto článku jste nasadili výchozí virtuální síť s jednou podsítí. Naučte se vytvářet vlastní virtuální síť s více podsítěmi, nadále kurz pro vytvoření vlastní virtuální sítě.

> [!div class="nextstepaction"]
> [Vytvoření vlastní virtuální sítě](virtual-networks-create-vnet-arm-pportal.md)
