---
title: 'Vytvoření hostitele Azure bastionu: virtuální počítač s Windows: portál'
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu a připojit se k virtuálnímu počítači s Windows.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: cherylmc
ms.openlocfilehash: e4f8bc38a79fdbb84624e126c84d679a906a701b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91980165"
---
# <a name="tutorial-create-an-azure-bastion-host-and-connect-to-a-windows-vm"></a>Kurz: vytvoření hostitele Azure bastionu a připojení k virtuálnímu počítači s Windows

K virtuálnímu počítači s Windows se můžete připojit prostřednictvím prohlížeče prostřednictvím Azure Portal. Připojte se k VIRTUÁLNÍmu počítači přímo přes prohlížeč přes protokol TLS pomocí privátní IP adresy. Nemusíte instalovat a konfigurovat klientský software lokálně.

Pokud se chcete připojit k virtuálnímu počítači pomocí bastionu, musíte zřídit službu Azure bastionu ve vaší virtuální síti (VNet). Po zřízení služby je tento typ připojení dostupný všem virtuálním počítačům, které jsou ve virtuální síti, v závislosti na konfiguraci zabezpečení virtuálního počítače. Nasazení Azure bastionu je vázané na virtuální síť, ne pro předplatné/účet nebo virtuální počítač.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření hostitele bastionu pro virtuální síť
> * Připojení k virtuálnímu počítači s Windows

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

* Virtuální síť.
* Virtuální počítač s Windows ve virtuální síti.
* Následující požadované role:
  * Role čtenáře na virtuálním počítači.
  * Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
  * Role čtecího modulu v prostředku Azure bastionu

* Porty: pro připojení k virtuálnímu počítači s Windows musíte na svém VIRTUÁLNÍm počítači s Windows otevřít následující porty:
  * Příchozí porty: RDP (3389)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Vytvoření hostitele bastionu

Tato část vám pomůže vytvořit objekt bastionu ve vaší virtuální síti. To je nutné, aby se vytvořilo zabezpečené připojení k virtuálnímu počítači ve virtuální síti.

1. Z **domovské** stránky vyberte **+ vytvořit prostředek**.
1. Na **nové** stránce zadejte do vyhledávacího pole **bastionu**a pak vyberte **ENTER** , abyste se dostali do výsledků hledání. Na výsledku pro **bastionu**ověřte, že je Vydavatel Microsoft.
1. Vyberte **Vytvořit**.
1. Na stránce **vytvořit bastionu** Nakonfigurujte nový prostředek bastionu.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Vytvoření opevněného hostitelského počítače" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Předplatné**: předplatné Azure, které chcete použít k vytvoření nového prostředku bastionu.
    * **Skupina prostředků**: Skupina prostředků Azure, ve které se vytvoří nový prostředek bastionu. Pokud nemáte existující skupinu prostředků, můžete vytvořit novou.
    * **Name**(název): název nového prostředku bastionu.
    * **Oblast**: veřejná oblast Azure, ve které se prostředek vytvoří.
    * **Virtuální síť**: virtuální síť, ve které se vytvoří prostředek bastionu. Během tohoto procesu můžete vytvořit novou virtuální síť na portálu nebo použít stávající virtuální síť. Pokud používáte existující virtuální síť, ujistěte se, že stávající virtuální síť má dostatek volného místa pro splnění požadavků na bastionu podsíť. Pokud se vaše virtuální síť v rozevíracím seznamu nezobrazuje, ujistěte se, že jste vybrali správnou skupinu prostředků.
    * **Podsíť**: po vytvoření nebo výběru virtuální sítě se zobrazí pole podsíť. Podsíť ve virtuální síti, do které bude nasazen nový hostitel bastionu Podsíť bude vyhrazená pro hostitele bastionu. Vyberte **spravovat konfiguraci podsítě** a vytvořte podsíť Azure bastionu. Vyberte **+ podsíť** a vytvořte podsíť pomocí následujících pokynů:

         * Podsíť musí mít název **AzureBastionSubnet**.
         * Podsíť musí být aspoň/27 nebo větší.

      Nemusíte vyplnit další pole. Vyberte **OK** a potom v horní části stránky vyberte **vytvořit bastionu** a vraťte se na stránku konfigurace bastionu.
    * **Veřejná IP adresa**: veřejná IP adresa prostředku bastionu, ke kterému bude přístup RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte. Tato IP adresa nemá nic k tomu, abyste mohli provádět žádné z virtuálních počítačů, ke kterým se chcete připojit. Pro prostředek hostitele bastionu je to veřejná IP adresa.
    * **Název veřejné IP adresy**: název prostředku veřejné IP adresy. Pro tento kurz můžete ponechat výchozí nastavení.
    * **SKU veřejné IP adresy**: Toto nastavení je ve výchozím nastavení předem naplněné na **Standard**. Azure bastionu používá nebo podporuje jenom standardní IP SKU Standard.
    * **Přiřazení**: Toto nastavení je ve výchozím nastavení předem naplněné na **statické**.

1. Po dokončení zadávání nastavení vyberte **zkontrolovat + vytvořit**. Tím se hodnoty ověří. Po úspěšném ověření můžete vytvořit prostředek bastionu.
1. Vyberte **Vytvořit**.
1. Zobrazí se zpráva s informacemi o tom, že probíhá nasazení. Stav se zobrazí na této stránce při vytváření prostředků. Vytváření a nasazování prostředků bastionu trvá přibližně 5 minut.

## <a name="connect-to-a-vm"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte prostředky pomocí následujících kroků:

1. Do **vyhledávacího** pole v horní části portálu zadejte název vaší skupiny prostředků. Pokud se ve výsledcích hledání zobrazí vaše skupina prostředků, vyberte ji.
1. Vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků pro **typ název skupiny prostředků:** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili hostitele bastionu a přidružíte ho k virtuální síti a připojili se k virtuálnímu počítači s Windows. Můžete se rozhodnout použít skupiny zabezpečení sítě ve vaší podsíti Azure bastionu. Postup najdete v tématu [práce s skupin zabezpečení sítě](bastion-nsg.md).
