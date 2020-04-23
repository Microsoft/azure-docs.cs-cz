---
title: Nasazení vyhrazených hostitelů Azure pomocí portálu
description: Nasazujte virtuální virtuální moduly do vyhrazených hostitelů pomocí portálu.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 53a9cd33f7bd2223be1c9f199b7a6d10d876c322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082930"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Nasazení virtuálních hostitelů do vyhrazených hostitelů pomocí portálu

Tento článek vás provede, jak vytvořit [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů.This article guides through you through how to create a Azure dedicated host to host your virtual machines (VMs). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Na stránce **Nový** vyberte v části **Oblíbené** **položku Windows Server 2016 Datacenter**.
1. Na kartě **Základy** v části **Podrobnosti o projektu**zkontrolujte, zda je vybráno správné předplatné, a pak vyberte *myDedicatedHostsRG* jako **skupinu prostředků**. 
1. V části **Podrobnosti o instancích** jako **Název virtuálního počítače** zadejte *myVM* a u možnosti **Umístění** zvolte *USA – východ*.
1. V **části Možnosti dostupnosti** vyberte **zónu dostupnost**, vyberte v rozevíracím souboru *1.*
1. Pro velikost vyberte **Změnit velikost**. V seznamu dostupných velikostí vyberte jednu z řady Esv3, například **Standard E2s v3**. Chcete-li zobrazit všechny dostupné velikosti, může být nutné filtr vymazat.
1. V **Účtu správce** zadejte uživatelské jméno, například *azureuser*, a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. V části **Pravidla příchozího portu**zvolte **Povolit vybrané porty** a v rozevíracím řádu vyberte **rdp (3389).**
1. V horní části stránky vyberte kartu **Upřesnit** a v části **Host** vyberte *myHostGroup* pro **skupinu Host** a *myHost* pro **hostitele**. 
    ![Vybrat skupinu hostitelů a hostitele](./media/dedicated-hosts-portal/advanced.png)
1. Zbytek ponechte ve výchozím nastavení a potom v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.
1. Až se zobrazí zpráva, že ověření proběhlo, vyberte **Vytvořit**.

## <a name="add-an-existing-vm"></a>Přidání existujícího virtuálního virtuálního montovna 

Existující virtuální ho do vyhrazeného hostitele můžete přidat, ale virtuální modul musí být nejprve Stop\Deallocated. Než přesunete virtuální ho důťák na vyhrazeného hostitele, ujistěte se, že je podporovaná konfigurace virtuálního počítače:

- Velikost virtuálního počítače musí být ve stejné rodině velikosti jako vyhrazený hostitel. Například pokud váš vyhrazený hostitel je DSv3, pak velikost virtuálního počítače může být Standard_D4s_v3, ale nemůže to být Standard_A4_v2. 
- Virtuální modul musí být umístěn ve stejné oblasti jako vyhrazený hostitel.
- Virtuální virtuální ms nemůže být součástí skupiny umístění bezkontaktní. Před přesunutím do vyhrazeného hostitele odeberte virtuální ho ze skupiny umístění bezkontaktní komunikace. Další informace najdete [v tématu Přesunutí virtuálního virtuálního ms ze skupiny umístění bez kontaktních míst.](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- Virtuální virtuální měsíč nemůže být v sadě dostupnosti.
- Pokud je virtuální virtuální soud v zóně dostupnosti, musí se jednat o stejnou zónu dostupnosti jako hostitelská skupina. Nastavení zóny dostupnosti pro virtuální počítače a skupinu hostitelů se musí shodovat.

Přesuňte virtuální ho na vyhrazeného hostitele pomocí [portálu](https://portal.azure.com).

1. Otevřete stránku virtuálního virtuálního soudu.
1. Vyberte **Zastavit,** chcete-li zastavit\navrátit virtuální ho.
1. V levé nabídce vyberte **Konfigurace.**
1. V rozevíracích nabídkách vyberte skupinu hostitelů a hostitele.
1. Až budete hotovi, vhorní části stránky vyberte **Uložit.**
1. Po přidání virtuálního virtuálního zařízení do hostitele vyberte **přehled** z levé nabídky.
1. V horní části stránky vyberte **Start** restartovat virtuální počítač.

## <a name="next-steps"></a>Další kroky

- Další informace naleznete v přehledu [vyhrazených hostitelů.](dedicated-hosts.md) 

- Zde je [nalezena](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)ukázková šablona , která používá zóny i domény selhání pro maximální odolnost proti chybám v oblasti.

- Můžete také nasadit vyhrazeného hostitele pomocí [Azure PowerShellu](dedicated-hosts-powershell.md).
