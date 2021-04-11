---
title: 'Rychlý Start: konfigurace Azure bastionu a připojení k virtuálnímu počítači přes privátní IP adresu a prohlížeč'
titleSuffix: Azure Bastion
description: V tomto článku rychlý Start se dozvíte, jak vytvořit hostitele Azure bastionu z virtuálního počítače a bezpečně se připojit k VIRTUÁLNÍmu počítači přes prohlížeč pomocí privátní IP adresy.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 02/18/2021
ms.author: cherylmc
ms.openlocfilehash: 53f09eed89f9667611ed4d5e0268c889609d560a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553562"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Rychlý Start: zabezpečené připojení k virtuálnímu počítači přes prohlížeč prostřednictvím privátní IP adresy

Pomocí Azure Portal a Azure bastionu se můžete připojit k virtuálnímu počítači přes prohlížeč. V tomto článku rychlý Start se dozvíte, jak nakonfigurovat Azure bastionu na základě nastavení virtuálního počítače a pak se k VIRTUÁLNÍmu počítači připojit prostřednictvím portálu. Virtuální počítač nepotřebuje veřejnou IP adresu, klientský software, agenta nebo speciální konfiguraci. Po zřízení služby je prostředí RDP/SSH dostupné všem virtuálním počítačům ve stejné virtuální síti. Další informace o Azure bastionu najdete v tématu [co je Azure bastionu?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Požadavky

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

 >[!NOTE]
 >Používání Azure bastionu se zónami Azure Privátní DNS se v tuto chvíli nepodporuje. Než začnete, ujistěte se prosím, že virtuální síť, ve které plánujete nasadit prostředek bastionu, není propojená s privátní zónou DNS.
 >

### <a name="example-values"></a><a name="values"></a>Příklady hodnot

Při vytváření této konfigurace můžete použít následující příklady hodnot, nebo můžete použít vlastní.

**Základní virtuální síť a hodnoty virtuálních počítačů:**

|**Název** | **Hodnota** |
| --- | --- |
| Virtuální počítač| TestVM |
| Skupina prostředků | TestRG1 |
| Oblast | East US |
| Virtuální síť | VNet1 |
| Adresní prostor | 10.1.0.0/16 |
| Podsítě | Front-end: 10.1.0.0/24 |

**Hodnoty bastionu Azure:**

|**Název** | **Hodnota** |
| --- | --- |
| Name | VNet1 – bastionu |
| + Název podsítě | AzureBastionSubnet |
| AzureBastionSubnet adresy | Podsíť v adresním prostoru virtuální sítě s maskou podsítě/27. Například 10.1.1.0/27.  |
| Veřejná IP adresa |  Vytvořit nový |
| Název veřejné IP adresy | VNet1-IP  |
| SKU veřejné IP adresy |  Standard  |
| Přiřazení  | Static |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Vytvoření hostitele bastionu

Existuje několik různých způsobů konfigurace hostitele bastionu. V následujících krocích vytvoříte hostitele bastionu v Azure Portal přímo z virtuálního počítače. Při vytváření hostitele z virtuálního počítače se automaticky vyplní různá nastavení odpovídající vašemu virtuálnímu počítači nebo virtuální síti.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte k virtuálnímu počítači, ke kterému se chcete připojit, a pak vyberte **připojit**.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Snímek obrazovky s nastavením virtuálního počítače" lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. V rozevíracím seznamu vyberte možnost **bastionu**.

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Snímek obrazovky rozevíracího seznamu bastionu" lightbox="./media/quickstart-host-portal/bastion.png":::
1. Na **TestVM | Stránka připojit** vyberte **použít bastionu**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Snímek obrazovky s použitím bastionu":::

1. Na stránce **připojit pomocí Azure bastionu** nakonfigurujte hodnoty.

   * **Krok 1:** Hodnoty jsou předem vyplněné, protože vytváříte hostitele bastionu přímo z virtuálního počítače.

   * **Krok 2:** Adresní prostor je předem vyplněný navrhovaným adresním prostorem. AzureBastionSubnet musí mít adresní prostor/27 nebo větší (/26,/25 atd.).

   :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Snímek obrazovky s vytvořením podsítě bastionu":::

1. Kliknutím na **vytvořit podsíť** vytvořte AzureBastionSubnet.
1. Po vytvoření podsítě se stránka automaticky přesune na **Krok 3**. V kroku 3 použijte následující hodnoty:

   * **Název:** Pojmenujte hostitele bastionu.
   * **Veřejná IP adresa:** Vyberte **Vytvořit novou**.
   * **Název veřejné IP adresy:** Název prostředku veřejné IP adresy.
   * **SKU veřejné IP adresy:** Předem nakonfigurováno jako **Standard**
   * **Přiřazení:** Předem nakonfigurovaná na **statickou**. Nemůžete použít dynamické přiřazení pro Azure bastionu.
   * **Skupina prostředků**: stejná skupina prostředků jako virtuální počítač.

   :::image type="content" source="./media/quickstart-host-portal/create-bastion.png" alt-text="Snímek obrazovky s krokem 3":::
1. Po dokončení hodnot vyberte **vytvořit Azure bastionu pomocí výchozí hodnoty**. Azure ověří vaše nastavení a pak vytvoří hostitele. Vytvoření a nasazení hostitele a jeho prostředků trvá asi 5 minut.

## <a name="connect"></a><a name="connect"></a>Síti

Po nasazení bastionu do virtuální sítě se obrazovka změní na stránku připojit.

1. Zadejte uživatelské jméno a heslo pro virtuální počítač. Pak vyberte **připojit**.

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="Snímek obrazovky se zobrazí v dialogovém okně připojit pomocí Azure bastionu.":::
1. Připojení RDP k tomuto virtuálnímu počítači se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Připojení RDP":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s virtuální sítí a virtuálními počítači hotovi, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do **vyhledávacího** pole v horní části portálu zadejte název vaší skupiny prostředků a vyberte ho z výsledků hledání.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte skupinu prostředků pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pro virtuální síť vytvořili hostitele bastionu a pak jste zabezpečeni připojeni k virtuálnímu počítači prostřednictvím bastionu. V dalším kroku můžete pokračovat následujícím krokem, pokud se chcete připojit k sadě škálování virtuálního počítače.

> [!div class="nextstepaction"]
> [Připojení k sadě škálování virtuálního počítače pomocí Azure bastionu](bastion-connect-vm-scale-set.md)
