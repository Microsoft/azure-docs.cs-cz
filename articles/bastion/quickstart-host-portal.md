---
title: 'Rychlý Start: vytvoření hostitele bastionu z virtuálního počítače a připojení přes privátní IP adresu'
titleSuffix: Azure Bastion
description: V tomto článku rychlý Start se dozvíte, jak vytvořit hostitele Azure bastionu z virtuálního počítače a bezpečně se připojit pomocí privátní IP adresy.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019048"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Rychlý Start: připojení k virtuálnímu počítači pomocí privátní IP adresy a Azure bastionu

Tento článek rychlý Start ukazuje, jak se připojit k virtuálnímu počítači přes prohlížeč pomocí Azure bastionu a Azure Portal. V Azure Portal můžete z virtuálního počítače Azure nasazovat bastionu do vaší virtuální sítě. Po nasazení bastionu se k virtuálnímu počítači můžete připojit přes jeho privátní IP adresu pomocí Azure Portal. Váš virtuální počítač nepotřebuje veřejnou IP adresu ani speciální software. Jednou z výhod vytváření bastionu hostitele pro virtuální síť přímo z virtuálního počítače je to, že mnoho z těchto nastavení je pro vás předem vyplněné.

Po zřízení služby je prostředí RDP/SSH dostupné všem virtuálním počítačům ve stejné virtuální síti. Další informace o Azure bastionu najdete v tématu [co je Azure bastionu?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Požadavky

* Virtuální síť.
* Virtuální počítač s Windows ve virtuální síti.
* Následující požadované role:
  * Role čtenáře na virtuálním počítači.
  * Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače

* Porty: pro připojení k virtuálnímu počítači musíte na virtuálním počítači otevřít následující porty:
  * Příchozí porty: RDP (3389)

### <a name="example-values"></a>Příklady hodnot

|**Název** | **Hodnota** |
| --- | --- |
| Name |  Virtuální sítě testvnet1 – bastionu |
| Virtuální síť |  Virtuální sítě testvnet1 (na základě virtuálního počítače) |
| + Název podsítě | AzureBastionSubnet |
| AzureBastionSubnet adresy |  10.1.254.0/27 |
| Veřejná IP adresa |  Vytvořit nový |
| Název veřejné IP adresy | VNet1BastionPIP  |
| SKU veřejné IP adresy |  Standard  |
| Přiřazení  | Static |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Vytvoření hostitele bastionu

Když v Azure Portal vytvoříte hostitele bastionu pomocí existujícího virtuálního počítače, budou se automaticky standardně odpovídat různým nastavením, aby odpovídal vašemu virtuálnímu počítači a/nebo virtuální síti.

1. Otevřete web [Azure Portal](https://portal.azure.com). Přejít na virtuální počítač a pak vyberte **připojit**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="nastavení virtuálního počítače" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. V rozevíracím seznamu vyberte možnost **bastionu**.
1. Na **TestVM | Stránka připojit**vyberte **použít bastionu**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="nastavení virtuálního počítače" border="false":::

1. Na stránce **bastionu** vyplňte následující pole nastavení:

   * **Název**: pojmenujte hostitele bastionu.
   * **Podsíť**: podsíť ve virtuální síti, do které se bude nasazovat prostředek bastionu. Podsíť musí být vytvořená s názvem **AzureBastionSubnet**. Název umožňuje službě Azure zjistit, do které podsítě má nasazovat prostředek bastionu. To se liší od podsítě brány. Použijte podsíť alespoň/27 nebo větší (/27,/26,/25 atd.).
   
      * Vyberte **spravovat konfiguraci podsítě**.
      * Vyberte **AzureBastionSubnet**.
      * V případě potřeby upravte rozsah adres v zápisu CIDR. Například 10.1.254.0/27.
      * Neupravujte žádná další nastavení. Vyberte **OK** , pokud chcete přijmout a uložit změny podsítě, nebo vyberte **x** v horní části stránky, pokud nechcete provádět žádné změny.
1. Kliknutím na tlačítko zpět v prohlížeči přejděte zpět na stránku **bastionu** a pokračujte v zadávání hodnot.
   * **Název veřejné IP adresy**: název prostředku veřejné IP adresy.
   * **Veřejná IP adresa**: Jedná se o veřejnou IP adresu prostředku bastionu, ke kterému bude mít přístup RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu.
1. Vyberte **vytvořit** a vytvořte tak hostitele bastionu. Azure ověří vaše nastavení a pak vytvoří hostitele. Vytvoření a nasazení hostitele a jeho prostředků trvá asi 5 minut.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="nastavení virtuálního počítače":::

## <a name="connect"></a><a name="connect"></a>Připojit

Po nasazení bastionu do virtuální sítě se obrazovka změní na stránku připojit.

1. Zadejte uživatelské jméno a heslo pro virtuální počítač. Pak vyberte **připojit**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="nastavení virtuálního počítače":::
1. Připojení RDP k tomuto virtuálnímu počítači prostřednictvím bastionu se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="nastavení virtuálního počítače":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s virtuální sítí a virtuálními počítači hotovi, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do **vyhledávacího** pole v horní části portálu zadejte název vaší skupiny prostředků a vyberte ho z výsledků hledání.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte skupinu prostředků pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pro virtuální síť vytvořili hostitele bastionu a pak jste zabezpečeni připojeni k virtuálnímu počítači prostřednictvím hostitele bastionu. V dalším kroku můžete pokračovat následujícím krokem, pokud se chcete připojit k sadě škálování virtuálního počítače.

> [!div class="nextstepaction"]
> [Připojení k sadě škálování virtuálního počítače pomocí Azure bastionu](bastion-connect-vm-scale-set.md)
