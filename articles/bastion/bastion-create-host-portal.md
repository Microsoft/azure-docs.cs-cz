---
title: 'Vytvoření hostitele Azure bastionu: portál'
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu, který poskytuje připojení RDP/SSH ke všem virtuálním počítačům ve virtuální síti.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270746"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Vytvoření hostitele Azure bastionu pomocí portálu

V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu pomocí Azure Portal. Jakmile ve své virtuální síti zřídíte službu Azure bastionu, bezproblémové prostředí RDP/SSH je dostupné všem virtuálním počítačům ve stejné virtuální síti. Nasazení Azure bastionu je vázané na virtuální síť, ne pro předplatné/účet nebo virtuální počítač.

Na portálu můžete vytvořit nový prostředek hostitele bastionu, a to buď zadáním všech nastavení ručně, nebo pomocí nastavení, která odpovídají existujícímu virtuálnímu počítači. Pokud chcete vytvořit hostitele bastionu pomocí nastavení virtuálního počítače, přečtěte si článek [rychlý Start](quickstart-host-portal.md) . Volitelně můžete pomocí [Azure PowerShell](bastion-create-host-powershell.md) vytvořit hostitele Azure bastionu.

## <a name="before-you-begin"></a>Než začnete

Bastionu je k dispozici v následujících veřejných oblastech Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Vytvoření hostitele bastionu

Tato část vám pomůže vytvořit nový prostředek Azure bastionu z Azure Portal.

1. V nabídce webu [Azure Portal](https://portal.azure.com) nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na **nové** stránce v poli *Hledat na Marketplace* zadejte **bastionu**a kliknutím na **ENTER** se dostanete do výsledků hledání.

1. Z výsledků klikněte na **bastionu**. Ujistěte se, že je Vydavatel *Microsoft* a kategorie jsou *sítě*.

1. Na stránce **bastionu** klikněte na **vytvořit** a otevřete stránku **vytvořit bastionu** .

1. Na stránce **vytvořit bastionu** Nakonfigurujte nový prostředek bastionu. Zadejte nastavení konfigurace pro prostředek bastionu.

    ![Vytvoření bastionu](./media/bastion-create-host-portal/settings.png)

    * **Předplatné**: předplatné Azure, které chcete použít k vytvoření nového prostředku bastionu.
    * **Skupina prostředků**: Skupina prostředků Azure, ve které se vytvoří nový prostředek bastionu. Pokud nemáte existující skupinu prostředků, můžete vytvořit novou.
    * **Name**(název): název nového prostředku bastionu
    * **Oblast**: veřejná oblast Azure, ve které se prostředek vytvoří.
    * **Virtuální síť**: virtuální síť, ve které se prostředek bastionu vytvoří. Během tohoto procesu můžete vytvořit novou virtuální síť na portálu nebo použít stávající virtuální síť. Pokud používáte existující virtuální síť, ujistěte se, že stávající virtuální síť má dostatek volného místa pro splnění požadavků na bastionu podsíť.
    * **Podsíť**: podsíť ve vaší virtuální síti, do které se nasadí nový hostitel bastionu. Podsíť se vyhradí pro hostitele bastionu a musí se jmenovat jako **AzureBastionSubnet**. Tato podsíť musí být aspoň/27 nebo větší.
    
       **AzureBastionSubnet** nepodporuje [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md#custom-routes), ale podporuje [skupiny zabezpečení sítě](bastion-nsg.md).
    * **Veřejná IP adresa**: veřejná IP adresa prostředku bastionu, ke kterému bude přístup RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu nebo použijte existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.
    * **Název veřejné IP adresy**: název prostředku veřejné IP adresy.
    * **SKU veřejné IP adresy**: Toto nastavení je ve výchozím nastavení předem naplněné na **Standard**. Azure bastionu používá nebo podporuje jenom standardní IP SKU Standard.
    * **Přiřazení**: Toto nastavení je ve výchozím nastavení předem naplněné na **statické**.

1. Po dokončení zadávání nastavení klikněte na tlačítko **zkontrolovat + vytvořit**. Tím se hodnoty ověří. Po úspěšném ověření můžete zahájit proces vytváření.
1. Na stránce **vytvořit bastionu** klikněte na **vytvořit**.
1. Zobrazí se zpráva s informacemi o tom, že probíhá nasazení. Stav se zobrazí na této stránce při vytváření prostředků. Vytváření a nasazování prostředků bastionu trvá přibližně 5 minut.

## <a name="next-steps"></a>Další kroky

* Další informace najdete v tématu [bastionu – Nejčastější dotazy](bastion-faq.md) .

* Pokud chcete používat skupiny zabezpečení sítě s podsítí Azure bastionu, přečtěte si téma [práce s skupin zabezpečení sítě](bastion-nsg.md).
