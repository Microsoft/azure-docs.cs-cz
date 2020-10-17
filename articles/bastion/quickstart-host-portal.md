---
title: 'Rychlý Start: konfigurace Azure bastionu a připojení k virtuálnímu počítači přes privátní IP adresu a prohlížeč'
titleSuffix: Azure Bastion
description: V tomto článku rychlý Start se dozvíte, jak vytvořit hostitele Azure bastionu z virtuálního počítače a bezpečně se připojit k VIRTUÁLNÍmu počítači přes prohlížeč pomocí privátní IP adresy.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150469"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Rychlý Start: zabezpečené připojení k virtuálnímu počítači přes prohlížeč prostřednictvím privátní IP adresy

Pomocí Azure Portal a Azure bastionu se můžete připojit k virtuálnímu počítači přes prohlížeč. V tomto článku rychlý Start se dozvíte, jak nakonfigurovat Azure bastionu na základě nastavení virtuálního počítače a pak se k VIRTUÁLNÍmu počítači připojit prostřednictvím portálu. Virtuální počítač nepotřebuje veřejnou IP adresu, klientský software, agenta nebo speciální konfiguraci. Po zřízení služby je prostředí RDP/SSH dostupné všem virtuálním počítačům ve stejné virtuální síti. Další informace o Azure bastionu najdete v tématu [co je Azure bastionu?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Předpoklady

* Účet Azure s aktivním předplatným. Pokud ho ještě nemáte, [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Abyste se mohli připojit k VIRTUÁLNÍmu počítači přes prohlížeč pomocí bastionu, musíte být schopni se přihlásit k Azure Portal.

* Virtuální počítač s Windows ve virtuální síti. Pokud nemáte virtuální počítač, vytvořte ho pomocí rychlého startu [: Vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md).

  * Pokud potřebujete ukázkové hodnoty, přečtěte si uvedené [příklady hodnot](#values).
  * Pokud už máte virtuální síť, nezapomeňte ji při vytváření virtuálního počítače vybrat na kartě síť.
  * Pokud ještě nemáte virtuální síť, můžete si ji vytvořit zároveň při vytváření virtuálního počítače.
  * Pro připojení prostřednictvím Azure bastionu není nutné mít veřejnou IP adresu pro tento virtuální počítač.

* Požadované role virtuálních počítačů:
  * Role čtenáře na virtuálním počítači.
  * Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
  
* Požadované porty virtuálních počítačů:
  * Příchozí porty: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Příklady hodnot

Při vytváření této konfigurace můžete použít následující příklady hodnot, nebo můžete použít vlastní.

**Základní virtuální síť a hodnoty virtuálních počítačů:**

|**Název** | **Hodnota** |
| --- | --- |
| Virtuální počítač| TestVM |
| Skupina prostředků | TestRG |
| Oblast | East US |
| Virtuální síť | Virtuální sítě testvnet1 |
| Adresní prostor | 10.0.0.0/16 |
| Podsítě | Front-end: 10.0.0.0/24 |

**Hodnoty bastionu Azure:**

|**Název** | **Hodnota** |
| --- | --- |
| Název | Virtuální sítě testvnet1 – bastionu |
| + Název podsítě | AzureBastionSubnet |
| AzureBastionSubnet adresy | Podsíť v adresním prostoru virtuální sítě s maskou podsítě/27. Například 10.0.1.0/27.  |
| Veřejná IP adresa |  Vytvořit nový |
| Název veřejné IP adresy | VNet1BastionPIP  |
| SKU veřejné IP adresy |  Standard  |
| Přiřazení  | Static |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Vytvoření hostitele bastionu

Existuje několik různých způsobů konfigurace hostitele bastionu. V následujících krocích vytvoříte hostitele bastionu v Azure Portal přímo z virtuálního počítače. Při vytváření hostitele z virtuálního počítače se automaticky vyplní různá nastavení odpovídající vašemu virtuálnímu počítači nebo virtuální síti.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Přejděte k virtuálnímu počítači, ke kterému se chcete připojit, a pak vyberte **připojit**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="nastavení virtuálního počítače" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. V rozevíracím seznamu vyberte možnost **bastionu**.
1. Na **TestVM | Stránka připojit**vyberte **použít bastionu**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="nastavení virtuálního počítače" border="false":::

1. Na stránce **bastionu** vyplňte následující pole nastavení:

   * **Název**: pojmenujte hostitele bastionu.
   * **Podsíť**: Jedná se o adresní prostor virtuální sítě, na který se bastionu prostředek nasadí. Podsíť musí být vytvořená s názvem **AzureBastionSubnet**. Použijte podsíť alespoň/27 nebo větší (/27,/26,/25 atd.).
   * Vyberte **spravovat konfiguraci podsítě**.
1. Na stránce **podsítě** vyberte **+ podsíť**.

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="nastavení virtuálního počítače":::
    
1. Do polí **název**na stránce **Přidat podsíť** zadejte **AzureBastionSubnet**.
   * V oblasti rozsah adres podsítě vyberte adresu podsítě, která se nachází v adresním prostoru virtuální sítě.
   * Neupravujte žádná další nastavení. Vyberte **OK** a potvrďte a uložte změny podsítě.

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="nastavení virtuálního počítače":::
1. Kliknutím na tlačítko zpět v prohlížeči přejděte zpět na stránku **bastionu** a pokračujte v zadávání hodnot.
   * **Veřejná IP adresa**: ponechte jako **vytvořit nové**.
   * **Název veřejné IP adresy**: název prostředku veřejné IP adresy.
   * **Přiřazení**: výchozí hodnota je statická. Nemůžete použít dynamické přiřazení pro Azure bastionu.
   * **Skupina prostředků**: stejná skupina prostředků jako virtuální počítač.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="nastavení virtuálního počítače":::
1. Vyberte **vytvořit** a vytvořte tak hostitele bastionu. Azure ověří vaše nastavení a pak vytvoří hostitele. Vytvoření a nasazení hostitele a jeho prostředků trvá asi 5 minut.

## <a name="connect"></a><a name="connect"></a>Připojit

Po nasazení bastionu do virtuální sítě se obrazovka změní na stránku připojit.

1. Zadejte uživatelské jméno a heslo pro virtuální počítač. Pak vyberte **připojit**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="nastavení virtuálního počítače":::
1. Připojení RDP k tomuto virtuálnímu počítači se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="nastavení virtuálního počítače":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s virtuální sítí a virtuálními počítači hotovi, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do **vyhledávacího** pole v horní části portálu zadejte název vaší skupiny prostředků a vyberte ho z výsledků hledání.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte skupinu prostředků pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pro virtuální síť vytvořili hostitele bastionu a pak jste zabezpečeni připojeni k virtuálnímu počítači prostřednictvím bastionu. V dalším kroku můžete pokračovat následujícím krokem, pokud se chcete připojit k sadě škálování virtuálního počítače.

> [!div class="nextstepaction"]
> [Připojení k sadě škálování virtuálního počítače pomocí Azure bastionu](bastion-connect-vm-scale-set.md)
