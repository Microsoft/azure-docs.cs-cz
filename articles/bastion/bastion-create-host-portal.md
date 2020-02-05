---
title: Vytvoření hostitele Azure bastionu | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f907dcb4427fd07a2c212e5de91ccce5e8198960
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990415"
---
# <a name="create-an-azure-bastion-host"></a>Vytvoření hostitele Azure bastionu

V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu pomocí Azure Portal. Jakmile ve své virtuální síti zřídíte službu Azure bastionu, bezproblémové prostředí RDP/SSH je dostupné všem virtuálním počítačům ve stejné virtuální síti. Nasazení Azure bastionu je vázané na virtuální síť, ne pro předplatné/účet nebo virtuální počítač.

Na portálu můžete vytvořit nový prostředek hostitele bastionu, a to buď zadáním všech nastavení ručně, nebo pomocí nastavení, která odpovídají existujícímu virtuálnímu počítači. Volitelně můžete pomocí [Azure PowerShellu](bastion-create-host-powershell.md) vytvořit hostitele Azure bastionu.

## <a name="before-you-begin"></a>Než začnete

Bastionu je k dispozici v následujících veřejných oblastech Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Vytvoření hostitele bastionu – zadejte nastavení.

Tato část vám pomůže vytvořit nový prostředek Azure bastionu z Azure Portal.

1. V nabídce [Azure Portal](https://portal.azure.com) nebo na **domovské** stránce vyberte **vytvořit prostředek**.

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
    * **Podsíť**: podsíť ve virtuální síti, do které se nasadí nový prostředek hostitele bastionu. Podsíť musíte vytvořit pomocí hodnoty název **AzureBastionSubnet**. Tato hodnota umožňuje službě Azure zjistit, do které podsítě nasadit prostředky bastionu. To se liší od podsítě brány. Je nutné použít podsíť alespoň/27 nebo větší (/27,/26 atd.).
    
       Vytvořte **AzureBastionSubnet** bez směrovacích tabulek nebo delegování. Pokud používáte skupiny zabezpečení sítě na **AzureBastionSubnet**, přečtěte si článek [práce s skupin zabezpečení sítě](bastion-nsg.md) .
    * **Veřejná IP adresa**: veřejná IP adresa prostředku bastionu, ke kterému bude přístup RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu nebo použijte existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.
    * **Název veřejné IP adresy**: název prostředku veřejné IP adresy.
    * **SKU veřejné IP adresy**: Toto nastavení je ve výchozím nastavení předem naplněné na **Standard**. Azure bastionu používá nebo podporuje jenom standardní IP SKU Standard.
    * **Přiřazení**: Toto nastavení je ve výchozím nastavení předem naplněné na **statické**.

1. Po dokončení zadávání nastavení klikněte na tlačítko **zkontrolovat + vytvořit**. Tím se hodnoty ověří. Po úspěšném ověření můžete zahájit proces vytváření.
1. Na stránce **vytvořit bastionu** klikněte na **vytvořit**.
1. Zobrazí se zpráva s informacemi o tom, že probíhá nasazení. Stav se zobrazí na této stránce při vytváření prostředků. Vytváření a nasazování prostředků bastionu trvá přibližně 5 minut.

## <a name="createvmset"></a>Vytvoření hostitele bastionu – použití nastavení virtuálního počítače

Pokud vytvoříte hostitele bastionu na portálu pomocí existujícího virtuálního počítače, budou se automaticky standardně shodovat různá nastavení, která budou odpovídat vašemu virtuálnímu počítači nebo virtuální síti.

1. Otevřete web [Azure Portal](https://portal.azure.com). Přejděte na virtuální počítač a pak klikněte na **připojit**.

   ![Připojení k virtuálnímu počítači](./media/bastion-create-host-portal/vmsettings.png)
1. Na pravém bočním panelu klikněte na **bastionu**a pak **použijte bastionu**.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. Na stránce bastionu vyplňte následující pole nastavení:

   * **Name (název**): název hostitele bastionu, který chcete vytvořit.
   * **Podsíť**: podsíť ve virtuální síti, do které se bude nasazovat prostředek bastionu. Podsíť musí být vytvořená s názvem **AzureBastionSubnet**. To umožňuje službě Azure zjistit, do které podsítě nasadit prostředek bastionu. To se liší od podsítě brány. Je nutné použít podsíť alespoň/27 nebo větší (/27,/26 atd.). Vytvořte podsíť bez skupin zabezpečení sítě, směrovacích tabulek nebo delegování. Pokud se později rozhodnete použít skupiny zabezpečení sítě na **AzureBastionSubnet**, přečtěte si téma [práce s skupin zabezpečení sítě](bastion-nsg.md).
   
     Kliknutím na **spravovat konfiguraci podsítě** vytvořte **AzureBastionSubnet**.  Kliknutím na **vytvořit** vytvořte podsíť a pak pokračujte dalším nastavením.
   * **Veřejná IP adresa**: veřejná IP adresa prostředku bastionu, ke kterému bude přístup RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu nebo použijte existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.
   * **Název veřejné IP adresy**: název prostředku veřejné IP adresy.
1. Na obrazovce ověřování klikněte na **vytvořit**. Počkejte asi 5 minut, než se bastionu prostředek vytvoří a nasadí.

## <a name="next-steps"></a>Další kroky

* Další informace najdete v tématu [bastionu – Nejčastější dotazy](bastion-faq.md) .

* Pokud chcete používat skupiny zabezpečení sítě s podsítí Azure bastionu, přečtěte si téma [práce s skupin zabezpečení sítě](bastion-nsg.md).