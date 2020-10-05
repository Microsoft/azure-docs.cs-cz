---
title: 'Rychlý Start: připojení k virtuálnímu počítači pomocí privátní IP adresy: Azure bastionu'
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu z virtuálního počítače a bezpečně se připojit pomocí privátní IP adresy.
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: b0155ae92e3179918273d6a19773aa15b67949ea
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90985593"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Rychlý Start: připojení k virtuálnímu počítači pomocí privátní IP adresy a Azure bastionu

Tento článek rychlý Start ukazuje, jak se připojit k virtuálnímu počítači pomocí privátní IP adresy. Když se připojujete přes bastionu, virtuální počítače nepotřebují veřejnou IP adresu. Kroky v tomto článku vám pomůžou nasadit bastionu do virtuální sítě přes virtuální počítač na portálu. Po zřízení služby je prostředí RDP/SSH dostupné všem virtuálním počítačům ve stejné virtuální síti.

## <a name="prerequisites"></a><a name="prereq"></a>Předpoklady

* Virtuální síť Azure.
* Virtuální počítač Azure umístěný ve virtuální síti s otevřeným portem 3389.

### <a name="example-values"></a>Příklady hodnot

|**Název** | **Hodnota** |
| --- | --- |
| Name |  VNet1Bastion |
| Oblast | eastus |
| Virtuální síť |  VNet1 |
| + Název podsítě | AzureBastionSubnet |
| AzureBastionSubnet adresy |  10.1.254.0/27 |
| Veřejná IP adresa |  Vytvořit nový |
| Název veřejné IP adresy | VNet1BastionPIP  |
| SKU veřejné IP adresy |  Standard  |
| Přiřazení  | Static |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Vytvoření hostitele bastionu

Když vytvoříte hostitele bastionu na portálu pomocí existujícího virtuálního počítače, bude automaticky standardně odpovídat různým nastavením, aby odpovídal vašemu virtuálnímu počítači nebo virtuální síti.

1. Otevřete web [Azure Portal](https://portal.azure.com). Přejděte na virtuální počítač a pak klikněte na **připojit**.

   ![nastavení virtuálního počítače](./media/quickstart-host-portal/vm-settings.png)
1. V rozevíracím seznamu vyberte možnost **bastionu**.
1. Na stránce připojit vyberte **použít bastionu**.

   ![vybrat bastionu](./media/quickstart-host-portal/select-bastion.png)

1. Na stránce bastionu vyplňte následující pole nastavení:

   * **Název**: název hostitele bastionu
   * **Podsíť**: podsíť ve virtuální síti, do které se bude nasazovat prostředek bastionu. Podsíť musí být vytvořená s názvem **AzureBastionSubnet**. Název umožňuje službě Azure zjistit, do které podsítě má nasazovat prostředek bastionu. To se liší od podsítě brány. Použijte podsíť alespoň/27 nebo větší (/27,/26,/25 atd.).
   
      * Vyberte **spravovat konfiguraci podsítě**a pak vyberte **+ podsíť**.
      * Na stránce Přidat podsíť zadejte **AzureBastionSubnet**.
      * Zadejte rozsah adres v zápisu CIDR. Například 10.1.254.0/27.
      * Vyberte **OK** a vytvořte podsíť. V horní části stránky přejděte zpátky na bastionu a dokončete zbývající nastavení.

         ![Přejít na nastavení bastionu](./media/quickstart-host-portal/navigate-bastion.png)
   * **Veřejná IP adresa**: Jedná se o veřejnou IP adresu prostředku bastionu, ke kterému bude mít přístup RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu nebo použijte existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.
   * **Název veřejné IP adresy**: název prostředku veřejné IP adresy.
1. Na obrazovce ověřování klikněte na **vytvořit**. Počkejte asi 5 minut, než se bastionu prostředek vytvoří a nasadí.

   ![vytvořit hostitele bastionu](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Připojit

Po nasazení bastionu do virtuální sítě se obrazovka změní na stránku připojit.

1. Zadejte uživatelské jméno a heslo pro virtuální počítač. Pak vyberte **připojit**.

   ![Snímek obrazovky se zobrazí v dialogovém okně připojit pomocí Azure bastionu, kde se zobrazí výzva k zadání uživatelského jména a hesla.](./media/quickstart-host-portal/connect.png)
1. Připojení RDP k tomuto virtuálnímu počítači prostřednictvím bastionu se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   ![Připojení RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s virtuální sítí a virtuálními počítači hotovi, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do **vyhledávacího** pole v horní části portálu zadejte *TestRG1* a ve výsledcích hledání vyberte **TestRG1** .

2. Vyberte **Odstranit skupinu prostředků**.

3. Zadejte *TestRG1* pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pro virtuální síť vytvořili hostitele bastionu a pak jste zabezpečeni připojeni k virtuálnímu počítači prostřednictvím hostitele bastionu.

* Pokud se chcete dozvědět víc o Azure bastionu, přečtěte si [Přehled bastionu](bastion-overview.md) a [bastionu – Nejčastější dotazy](bastion-faq.md).
* Pokud chcete používat skupiny zabezpečení sítě s podsítí Azure bastionu, přečtěte si téma [práce s skupin zabezpečení sítě](bastion-nsg.md).
* Pokyny, které obsahují vysvětlení nastavení hostitele Azure bastionu, najdete v tomto [kurzu](bastion-create-host-portal.md).
* Informace o připojení k sadě škálování virtuálního počítače najdete v tématu [připojení k sadě škálování virtuálního počítače pomocí Azure bastionu](bastion-connect-vm-scale-set.md).