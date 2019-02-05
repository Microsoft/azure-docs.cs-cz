---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 32f533d06b7db0284459951e65f9c04fe0bb0285
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735841"
---
Skupinu dostupnosti pomáhá udržovat vaše virtuální počítače, které jsou k dispozici během výpadků, například při údržbě. Umístěním dvou nebo více podobně nakonfigurovaných virtuálních počítačů ve skupině dostupnosti vytvoří redundance potřebují zachovat dostupnost aplikace nebo služby, které běží ve virtuálním počítači. Podrobnosti o tom, jak to funguje, najdete v článku [Správa dostupnosti virtuálních počítačů][Manage the availability of virtual machines].

Je osvědčeným postupem je použití skupiny dostupnosti a koncových bodů s vyrovnáváním zatížení pro zajištění, že vaše aplikace je vždy k dispozici a spuštěných efektivně. Podrobnosti o koncové body s vyrovnáváním zatížení najdete v tématu [Vyrovnávání zatížení pro služby infrastruktury Azure][Load balancing for Azure infrastructure services].

Klasické virtuální počítače můžete přidat do skupiny dostupnosti pomocí jedné ze dvou možností:

* [Option 1: Vytvoření virtuálního počítače a dostupnosti ve stejnou dobu][Option 1: Create a virtual machine and an availability set at the same time]. Když vytváříte tyto virtuální počítače pak, přidávání nových virtuálních počítačů do sady.
* [Option 2: Přidat existující virtuální počítač do skupiny dostupnosti][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> V případě klasického modelu virtuálních počítačů, které chcete umístit do stejné skupiny dostupnosti patřily do stejné cloudové službě.
> 
> 

## <a id="createset"> </a>Možnost 1: Vytvoření virtuálního počítače a dostupnosti ve stejnou dobu
K tomu můžete použít příkazy prostředí Azure PowerShell nebo portálu Azure portal.

Použití webu Azure portal:

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek** > **Compute**.
3. Vyberte virtuální počítač image Marketplace, který chcete použít. Můžete k vytvoření virtuálního počítače s Linuxem nebo Windows.
4. Pro vybraný virtuální počítač, ověřte, že model nasazení nastavený na **Classic** a potom klikněte na tlačítko **Create**
   
    ![Text k alternativnímu obrázku](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Zadejte název virtuálního počítače, uživatelské jméno a heslo (pro počítače s Windows) nebo veřejný klíč SSH (pro počítače s Linuxem). 
6. Výběr velikosti virtuálního počítače a potom klikněte na tlačítko **vyberte** pokračujte.
7. Zvolte **volitelná konfigurace > dostupnosti**, a vyberte dostupnosti, které chcete přidat virtuální počítač.
   
    ![Text k alternativnímu obrázku](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Projděte si nastavení konfigurace. Jakmile budete hotovi, klikněte na tlačítko **vytvořit**.
9. Zatímco Azure vytváří virtuální počítač, můžete sledovat průběh **virtuálních počítačů** v nabídce centra.

Chcete-li použít příkazy prostředí Azure PowerShell k vytvoření virtuálního počítače Azure a přidat ho do nové nebo existující skupiny dostupnosti, přečtěte si téma [pomocí prostředí Azure PowerShell k vytvoření a předkonfigurace virtuálního počítače se systémem Windows](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>Možnost 2: Přidat existující virtuální počítač do skupiny dostupnosti
Na webu Azure Portal můžete přidat stávající klasických virtuálních počítačů k existující skupině dostupnosti nastavena nebo vytvořit nový pro ně. (Mějte na paměti, že virtuální počítače ve stejné skupině dostupnosti patřily do stejné cloudové službě.) Postup je téměř stejný. Pomocí Azure Powershellu můžete přidat virtuální počítač do existující skupiny dostupnosti.

1. Pokud jste tak již neučinili, přihlaste se k [webu Azure portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **virtuální počítače (classic)**.
   
    ![Text k alternativnímu obrázku](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Ze seznamu virtuálních počítačů vyberte název virtuálního počítače, který chcete přidat do sady.
4. Zvolte **dostupnosti** z virtuálního počítače **nastavení**.
   
    ![Text k alternativnímu obrázku](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Vyberte skupinu dostupnosti, kterou chcete přidat virtuální počítač. Virtuální počítač musí patřit do stejné cloudové službě skupinou dostupnosti.
   
    ![Text k alternativnímu obrázku](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Klikněte na **Uložit**.

Pokud chcete použít příkazy prostředí Azure PowerShell, otevřete relaci prostředí Azure PowerShell na úrovni správce a spusťte následující příkaz. Nahraďte zástupné symboly (například &lt;VmCloudServiceName&gt;), nahraďte všechno, co v uvozovkách, včetně < a > znaků správné názvy.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> Virtuální počítač pravděpodobně nutné restartovat, aby dokončit jeho přidání do skupiny dostupnosti.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

