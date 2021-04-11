---
title: Nasazení vyhrazených hostitelů Azure pomocí Azure Portal
description: Nasaďte virtuální počítače a sady škálování na vyhrazené hostitele pomocí Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 3bc7909f38e63256d7d708ec189c628662cf8837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667335"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Nasazení virtuálních počítačů a škálování sad na vyhrazené hostitele pomocí portálu 

Tento článek vás provede procesem vytvoření [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů. 


## <a name="limitations"></a>Omezení

- Typy velikosti a hardwaru, které jsou dostupné pro vyhrazené hostitele, se v jednotlivých oblastech liší. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.

## <a name="create-a-host-group"></a>Vytvoření skupiny hostitelů

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele. Při plánování vysoké dostupnosti jsou k dispozici další možnosti. U vyhrazených hostitelů můžete použít jednu z následujících možností: 
- Rozsah napříč několika zónami dostupnosti. V takovém případě je nutné mít skupinu hostitelů v každé z zón, které chcete použít.
- Rozložit napříč několika doménami selhání, které jsou namapované na fyzické racky. 
 
V obou případech je nutné zadat počet domén selhání pro skupinu hostitelů. Pokud nechcete rozsah domén selhání ve skupině, použijte počet domén selhání 1. 

Můžete se také rozhodnout použít jak zóny dostupnosti, tak i domény selhání. 

V tomto příkladu vytvoříme skupinu hostitelů s použitím 1 zóny dostupnosti a 2 domén selhání. 


1. Otevřete Azure [Portal](https://portal.azure.com). 
1. V levém horním rohu vyberte **vytvořit prostředek** .
1. Vyhledejte **skupinu hostitelů** a pak z výsledků vyberte **skupiny hostitelů** .
1. Na stránce **skupiny hostitelů** vyberte **vytvořit**.
1. Vyberte předplatné, které chcete použít, a pak vyberte **vytvořit novou** a vytvořte novou skupinu prostředků.
1. Jako **název** zadejte *myDedicatedHostsRG* a pak vyberte **OK**.
1. Jako **název skupiny hostitelů** zadejte *myHostGroup*.
1. V **oblasti umístění** vyberte **východní USA**.
1. V **oblasti dostupnost** vyberte **1**.
1. V případě **počtu domén selhání** vyberte **2**.
1. Pokud chcete automaticky přiřadit virtuální počítače a škálovat instance sad k dostupnému hostiteli v této skupině, vyberte **automatické umístění** .
1. Vyberte **zkontrolovat + vytvořit** a potom počkejte na ověření.
1. Jakmile se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit** a vytvořte skupinu hostitelů.

Vytvoření skupiny hostitelů by nemělo chvíli trvat.


## <a name="create-a-dedicated-host"></a>Vytvoření vyhrazeného hostitele

Teď ve skupině hostitelů vytvořte vyhrazeného hostitele. Kromě názvu pro hostitele je nutné zadat SKU pro hostitele. SKU hostitele zachytí podporovanou řadu virtuálních počítačů a také generování hardwaru pro vyhrazeného hostitele.

Další informace o SKU a cenách hostitelů najdete v tématu [ceny za vyhrazené hostitele Azure](https://aka.ms/ADHPricing).

Pokud pro skupinu hostitelů nastavíte počet domén selhání, budete požádáni o zadání domény selhání pro hostitele.  

1. V levém horním rohu vyberte **vytvořit prostředek** .
1. Vyhledejte **vyhrazeného hostitele** a pak z výsledků vyberte **vyhrazené hostitele** .
1. Na stránce **vyhrazení hostitelé** vyberte **vytvořit**.
1. Vyberte předplatné, které chcete použít.
1. Jako **skupinu prostředků** vyberte *myDedicatedHostsRG* .
1. V části **Podrobnosti o instanci** zadejte *myHost* pro **název** a vyberte *východní USA* pro umístění.
1. V **části hardwarový profil** vyberte *Standard Es3 Family – typ 1* pro **rodinu velikostí** vyberte *myHostGroup* pro **skupinu hostitelů** a pak pro **doménu selhání** vyberte *1* . Pro zbývající pole ponechte výchozí hodnoty.
1. Až budete hotovi, vyberte **zkontrolovat + vytvořit** a počkejte na ověření.
1. Jakmile se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit** a vytvořte hostitele.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Ve vyhledávacím poli nad seznamem prostředků Azure Marketplace vyhledejte a vyberte obrázek, který chcete použít, a pak zvolte **vytvořit**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné, a pak jako **skupinu prostředků** vyberte *myDedicatedHostsRG* . 
1. V části **Podrobnosti o instancích** jako **Název virtuálního počítače** zadejte *myVM* a u možnosti **Umístění** zvolte *USA – východ*.
1. V části **Možnosti dostupnosti** vyberte **zóna dostupnosti**, v rozevíracím seznamu vyberte *1* .
1. V poli Velikost vyberte **změnit velikost**. V seznamu dostupných velikostí vyberte jednu z Esv3 řad, jako je **Standard E2s V3**. Možná budete muset filtr vymazat, aby se zobrazily všechny dostupné velikosti.
1. Podle potřeby dokončete zbývající pole na kartě **základy** .
1. Pokud chcete určit hostitele, který se má použít pro váš virtuální počítač, vyberte v horní části stránky kartu **Upřesnit** a v části **hostitel** vyberte *MyHostGroup* pro **skupinu hostitelů** a *myHost* pro **hostitele**. V opačném případě se váš virtuální počítač automaticky umístí na hostitele s kapacitou.
    ![Vybrat skupinu hostitelů a hostitele](./media/dedicated-hosts-portal/advanced.png)
1. Zbytek ponechte ve výchozím nastavení a potom v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.
1. Když se zobrazí zpráva, že ověření proběhlo úspěšně, vyberte **vytvořit**.

Nasazení virtuálního počítače bude několik minut trvat.

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady 

Když nasadíte sadu škálování, zadáte skupinu hostitelů.

1. Vyhledejte *sadu škálování* a v seznamu vyberte **Virtual Machine Scale Sets** .
1. Vyberte **Přidat** a vytvořte novou sadu škálování.
1. Vyplňte pole na kartě **základy** , jako byste to obvykle měli, ale vyberte velikost virtuálního počítače, která je z řady, kterou jste zvolili pro vyhrazeného hostitele, jako je třeba **Standard E2s V3**.
1. Na kartě **Upřesnit** pro **algoritmus rozprostření** vyberte **maximální rozprostření**.
1. V části **Skupina hostitelů** vyberte v rozevíracím seznamu skupinu hostitelů. Pokud jste v poslední době vytvořili skupinu, může trvat několik minut, než se do seznamu přidá.

## <a name="add-an-existing-vm"></a>Přidat existující virtuální počítač 

Do vyhrazeného hostitele můžete přidat opuštěný virtuální počítač, ale virtuální počítač musí být nejdřív Stop\Deallocated.. Před přesunutím virtuálního počítače na vyhrazeného hostitele se ujistěte, že je konfigurace virtuálních počítačů podporovaná:

- Velikost virtuálního počítače musí být ve stejné rodině velikostí jako vyhrazený hostitel. Pokud je například vyhrazený hostitel DSv3, může být velikost virtuálního počítače Standard_D4s_v3, ale nemůžete ho Standard_A4_v2. 
- Virtuální počítač musí být umístěný ve stejné oblasti jako vyhrazený hostitel.
- Virtuální počítač nemůže být součástí skupiny umístění blízkosti. Odeberte virtuální počítač ze skupiny umístění blízkosti, než ho přesunete na vyhrazeného hostitele. Další informace najdete v tématu [přesun virtuálního počítače ze skupiny umístění do blízkosti](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group) .
- Virtuální počítač nemůže být ve skupině dostupnosti.
- Pokud je virtuální počítač v zóně dostupnosti, musí se jednat o stejnou zónu dostupnosti jako skupina hostitelů. Nastavení zóny dostupnosti pro virtuální počítač a skupinu hostitelů se musí shodovat.

Přesuňte virtuální počítač na vyhrazeného hostitele pomocí [portálu](https://portal.azure.com).

1. Otevřete stránku pro virtuální počítač.
1. Vyberte **zastavit** , aby se virtuální počítač stop\deallocate.
1. V nabídce vlevo vyberte **Konfigurace** .
1. Z rozevíracích nabídek vyberte skupinu hostitelů a hostitele.
1. Po dokončení vyberte **Uložit** v horní části stránky.
1. Po přidání virtuálního počítače do hostitele vyberte v nabídce vlevo možnost **Přehled** .
1. V horní části stránky vyberte **Spustit** a restartujte virtuální počítač.

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu Přehled [vyhrazených hostitelů](dedicated-hosts.md) .

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.

- Vyhrazeného hostitele můžete nasadit taky pomocí [Azure CLI](./linux/dedicated-hosts-cli.md) nebo [PowerShellu](./windows/dedicated-hosts-powershell.md).
