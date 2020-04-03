---
title: 'Úvodní příručka: Připojení k virtuálnímu počítači pomocí privátní IP adresy: Azure Bastion'
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure Bastion z virtuálního počítače a bezpečně se připojit pomocí privátní IP adresy.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619252"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Úvodní příručka: Připojení k virtuálnímu počítači pomocí privátní IP adresy a Azure Bastion

Tento článek rychlého startu ukazuje, jak se připojit k virtuálnímu počítači pomocí privátní IP adresy. Když se připojíte přes Bastion, vaše virtuální počítače nepotřebují veřejnou IP adresu. Kroky v tomto článku vám pomůžou nasadit Baštu do virtuální sítě prostřednictvím virtuálního počítače na portálu. Po zřízení služby prostředí RDP/SSH je k dispozici pro všechny virtuální počítače ve stejné virtuální síti.

## <a name="prerequisites"></a><a name="prereq"></a>Požadavky

* Virtuální síť Azure.
* Virtuální počítač Azure umístěný ve virtuální síti s otevřeným portem 3389.

### <a name="example-values"></a>Příklady hodnot

|**Název** | **Hodnotu** |
| --- | --- |
| Name (Název) |  VNet1Bastion |
| Region (Oblast) | eastus |
| Virtuální síť |  VNet1 |
| + Název podsítě | AzureBastionSubnet |
| Adresy AzureBastionSubnet |  10.1.254.0/27 |
| Veřejná IP adresa |  Vytvořit nový |
| Název veřejné IP adresy | VNet1BastionPIP  |
| Skladová položka veřejné IP adresy |  Standard  |
| Přiřazení  | Statická |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Vytvoření hostitele typu bašta

Když na portálu vytvoříte hostitele bastionu pomocí existujícího virtuálního počítače, různá nastavení budou automaticky nastavena jako výchozí, aby odpovídala vašemu virtuálnímu počítači nebo virtuální síti.

1. Otevřete [portál Azure](https://portal.azure.com). Přejděte na virtuální počítač a klikněte na **Připojit**.

   ![Nastavení virtuálního počítače](./media/quickstart-host-portal/vm-settings.png)
1. V rozevíracím souboru vyberte **možnost Bastion**.
1. Na stránce Připojit vyberte **Použít baštu**.

   ![vybrat Bašta](./media/quickstart-host-portal/select-bastion.png)

1. Na stránce Bašta vyplňte následující pole nastavení:

   * **Název**: Název hostitele bašty
   * **Podsíť**: Podsíť uvnitř virtuální sítě, do které bude nasazen prostředek Bastion. Podsíť musí být vytvořena s názvem **AzureBastionSubnet**. Název umožňuje Azure vědět, které podsítě nasadit prostředek Bastion. Tose liší od podsítě Brány. Použijte podsíť alespoň /27 nebo větší (/27, /26, /25 a tak dále).
   
      * Vyberte **Spravovat konfiguraci podsítě**a pak vyberte **+ Podsíť**.
      * Na stránce Přidat podsíť zadejte **AzureBastionSubnet**.
      * Zadejte rozsah adres v zápisu CIDR. Například 10.1.254.0/27.
      * Chcete-li vytvořit podsíť, vyberte **OK.** V horní části stránky přejděte zpět na Bastion a dokončete zbývající nastavení.

         ![přejděte do nastavení bašty](./media/quickstart-host-portal/navigate-bastion.png)
   * **Veřejná IP adresa**: Jedná se o veřejnou IP adresu prostředku bastionu, ke kterému bude přístup k RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu nebo použijte existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.
   * **Název veřejné IP adresy**: Název prostředku veřejné IP adresy.
1. Na obrazovce ověření klikněte na **Vytvořit**. Počkejte asi 5 minut na vytvoření a nasazení prostředku bastionu.

   ![vytvořit hostitele bašty](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Připojení

Po nasazení bašty do virtuální sítě se obrazovka změní na stránku pro připojení.

1. Zadejte uživatelské jméno a heslo pro váš virtuální počítač. Potom vyberte **Připojit**.

   ![připojit](./media/quickstart-host-portal/connect.png)
1. Připojení RDP k tomuto virtuálnímu počítači přes Bastion se otevře přímo na portálu Azure (přes HTML5) pomocí portu 443 a služby Bastion.

   ![Připojení k prv](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi s používáním virtuální sítě a virtuálních počítačů, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Zadejte *TestRG1* do **vyhledávacího** pole v horní části portálu a z výsledků hledání vyberte **TestRG1.**

2. Vyberte **Odstranit skupinu prostředků**.

3. Zadejte *TestRG1* pro **typ názvu skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pro virtuální síť vytvořili hostitele bastionu a pak jste se bezpečně připojili k virtuálnímu počítači prostřednictvím hostitele Bastion.

* Další informace o Azure Bastion najdete v nejčastějších dotazech k [baště](bastion-overview.md) a v [nejčastějších dotazech k baště](bastion-faq.md).
* Informace o použití skupin zabezpečení sítě s podsítí Azure Bastion najdete v [tématu Práce se skupinami zabezpečení sítě](bastion-nsg.md).
* Pokyny, které obsahují vysvětlení nastavení hostitele Azure Bastion, naleznete v [kurzu](bastion-create-host-portal.md).
* Pokud se chcete připojit k škálovací sadě virtuálních strojů, přečtěte si informace [o připojení k škálovací sadě virtuálních strojů pomocí Azure Bastion](bastion-connect-vm-scale-set.md).