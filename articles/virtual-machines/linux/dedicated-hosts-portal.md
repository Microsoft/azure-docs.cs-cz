---
title: Nasazení vyhrazených hostitelů Azure pomocí Azure Portal
description: Nasaďte virtuální počítače na vyhrazené hostitele pomocí Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 068f6e85a0b2a56bb20c8581ec4fa59d50009088
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080314"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Nasazení virtuálních počítačů na vyhrazené hostitele pomocí portálu

Tento článek vás provede procesem vytvoření [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Ve vyhledávacím poli nad seznamem prostředků Azure Marketplace vyhledejte a vyberte **Ubuntu Server 16.04 LTS** od společnosti Canonical a pak zvolte **Vytvořit**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné, a pak jako **skupinu prostředků**vyberte *myDedicatedHostsRG* . 
1. V části **Podrobnosti o instancích** jako **Název virtuálního počítače** zadejte *myVM* a u možnosti **Umístění** zvolte *USA – východ*.
1. V části **Možnosti dostupnosti** vyberte **zóna dostupnosti**, v rozevíracím seznamu vyberte *1* .
1. V poli Velikost vyberte **změnit velikost**. V seznamu dostupných velikostí vyberte jednu z Esv3 řad, jako je **Standard E2s V3**. Možná budete muset filtr vymazat, aby se zobrazily všechny dostupné velikosti.
1. V části **Účet správce** vyberte **Veřejný klíč SSH**, zadejte své uživatelské jméno a potom do textového pole vložte svůj veřejný klíč. Ve veřejném klíči odeberte počáteční a koncové prázdné znaky.

    ![Účet správce](./media/quick-create-portal/administrator-account.png)

1. V části **příchozí pravidla portů**  >  **veřejné příchozí porty**zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **SSH (22)** . 
1. V horní části stránky vyberte kartu **Upřesnit** a v části **hostitel** vyberte *MyHostGroup* pro **skupinu hostitelů** a *myHost* pro **hostitele**. 
    ![Vybrat skupinu hostitelů a hostitele](./media/dedicated-hosts-portal/advanced.png)
1. Zbytek ponechte ve výchozím nastavení a potom v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.
1. Když se zobrazí zpráva, že ověření proběhlo úspěšně, vyberte **vytvořit**.

Nasazení virtuálního počítače bude několik minut trvat.

## <a name="add-an-existing-vm"></a>Přidat existující virtuální počítač 

Do vyhrazeného hostitele můžete přidat opuštěný virtuální počítač, ale virtuální počítač musí být nejdřív Stop\Deallocated.. Před přesunutím virtuálního počítače na vyhrazeného hostitele se ujistěte, že je konfigurace virtuálních počítačů podporovaná:

- Velikost virtuálního počítače musí být ve stejné rodině velikostí jako vyhrazený hostitel. Pokud je například vyhrazený hostitel DSv3, může být velikost virtuálního počítače Standard_D4s_v3, ale nemůžete ho Standard_A4_v2. 
- Virtuální počítač musí být umístěný ve stejné oblasti jako vyhrazený hostitel.
- Virtuální počítač nemůže být součástí skupiny umístění blízkosti. Odeberte virtuální počítač ze skupiny umístění blízkosti, než ho přesunete na vyhrazeného hostitele. Další informace najdete v tématu [přesun virtuálního počítače ze skupiny umístění do blízkosti](../windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group) .
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

- Vyhrazeného hostitele můžete nasadit taky pomocí [Azure CLI](dedicated-hosts-cli.md).
