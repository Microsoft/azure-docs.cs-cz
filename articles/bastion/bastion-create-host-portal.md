---
title: Vytvoření hostitele Azure bastionu | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: 356f8d17dd194c79915600f63683e124cc481b33
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498376"
---
# <a name="create-an-azure-bastion-host"></a>Vytvoření hostitele Azure bastionu

V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu. Po zřízení služby Azure bastionu ve vaší virtuální síti bude bezproblémové prostředí RDP/SSH dostupné pro všechny vaše virtuální počítače ve stejné virtuální síti. Toto nasazení je na jednu virtuální síť, ne na předplatné/účet nebo virtuální počítač.

Existují dva způsoby, jak můžete vytvořit prostředek hostitele bastionu:

* Vytvořte prostředek bastionu pomocí Azure Portal.
* Vytvořte prostředek bastionu v Azure Portal pomocí stávajících nastavení virtuálního počítače.

## <a name="before-you-begin"></a>Než začnete

Bastionu je k dispozici v následujících veřejných oblastech Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Vytvoření hostitele bastionu

Tato část vám pomůže vytvořit nový prostředek Azure bastionu z Azure Portal.

1. Z domovské stránky v [Azure Portal](https://portal.azure.com)klikněte na **+ vytvořit prostředek**.

1. Na **nové** stránce v poli *Hledat na Marketplace* zadejte **bastionu**a kliknutím na **ENTER** se dostanete do výsledků hledání.

1. Z výsledků klikněte na **bastionu**. Ujistěte se, že je Vydavatel *Microsoft* a kategorie jsou *sítě*.

1. Na stránce **bastionu** klikněte na **vytvořit** a otevřete stránku **vytvořit bastionu** .

1. Na stránce **vytvořit bastionu** Nakonfigurujte nový prostředek bastionu. Zadejte nastavení konfigurace pro prostředek bastionu.

    ![Vytvoření bastionu](./media/bastion-create-host-portal/settings.png)

    * **Předplatné**: předplatné Azure, které chcete použít k vytvoření nového prostředku bastionu.
    * **Skupina prostředků**: Skupina prostředků Azure, ve které se vytvoří nový prostředek bastionu. Pokud nemáte existující skupinu prostředků, můžete vytvořit novou.
    * **Name**(název): název nového prostředku bastionu
    * **Oblast**: veřejná oblast Azure, ve které se prostředek vytvoří.
    * **Virtuální síť**: virtuální síť, ve které se prostředek bastionu vytvoří. Během tohoto procesu můžete vytvořit novou virtuální síť v portálu, pokud nemáte nebo nechcete používat stávající virtuální síť. Pokud používáte existující virtuální síť, ujistěte se, že stávající virtuální síť má dostatek volného místa pro splnění požadavků na bastionu podsíť.
    * **Podsíť**: podsíť ve virtuální síti, do které se nasadí nový prostředek hostitele bastionu. Podsíť musíte vytvořit pomocí hodnoty název **AzureBastionSubnet**. Tato hodnota umožňuje službě Azure zjistit, do které podsítě nasadit prostředky bastionu. To se liší od podsítě brány. Je nutné použít podsíť alespoň a/27 nebo větší podsíť (/27,/26 atd.). Vytvořte **AzureBastionSubnet** bez směrovacích tabulek nebo delegování. Pokud používáte skupiny zabezpečení sítě na **AzureBastionSubnet**, přečtěte si téma [práce s skupin zabezpečení sítě](bastion-nsg.md).
    * **Veřejná IP adresa**: veřejná IP adresa prostředku bastionu, ke kterému bude přístup RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu nebo použijte existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.
    * **Název veřejné IP adresy**: název prostředku veřejné IP adresy.
    * **SKU veřejné IP adresy**: předem vyplněné ve výchozím nastavení **Standard**. Azure bastionu používá nebo podporuje jenom standardní IP SKU Standard.
    * **Přiřazení**: předem vyplněné ve výchozím nastavení **statickou**.

1. Po dokončení zadávání nastavení klikněte na tlačítko **zkontrolovat + vytvořit**. Tím se hodnoty ověří. Po úspěšném ověření můžete zahájit proces vytváření.
1. Na stránce vytvořit bastionu klikněte na **vytvořit**.
1. Zobrazí se zpráva s informacemi o tom, že probíhá nasazení. Stav se zobrazí na této stránce při vytváření prostředků. Vytvoření a nasazení prostředku bastionu trvá přibližně 5 minut.

## <a name="createvmset"></a>Vytvoření hostitele bastionu pomocí nastavení virtuálního počítače

Pokud vytvoříte hostitele bastionu na portálu pomocí existujícího virtuálního počítače, budou automaticky nastavena výchozí nastavení, která odpovídají vašemu virtuálnímu počítači nebo virtuální síti.

1. Otevřete web [Azure Portal](https://portal.azure.com). Přejděte na virtuální počítač a pak klikněte na **připojit**.

   ![Připojení k virtuálnímu počítači](./media/bastion-create-host-portal/vmsettings.png)
1. Na pravém bočním panelu klikněte na **bastionu**a pak **použijte bastionu**.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. Na stránce bastionu vyplňte následující pole nastavení:

   * **Name (název**): název hostitele bastionu, který chcete vytvořit.
   * **Podsíť**: podsíť ve virtuální síti, do které se bude nasazovat prostředek bastionu. Podsíť musí být vytvořená s názvem **AzureBastionSubnet**. To umožňuje službě Azure zjistit, do které podsítě nasadit prostředek bastionu. To se liší od podsítě brány. Pokud chcete vytvořit podsíť Azure bastionu, klikněte na **spravovat konfiguraci podsítě** . Důrazně doporučujeme, abyste používali alespoň a/27 nebo větší podsíť (/27,/26 atd.). Vytvořte **AzureBastionSubnet** bez jakýchkoli skupin zabezpečení sítě, směrovacích tabulek nebo delegování. Kliknutím na **vytvořit** vytvořte podsíť a pak pokračujte dalším nastavením.
   * **Veřejná IP adresa**: veřejná IP adresa prostředku bastionu, ke kterému bude přístup RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu nebo použijte existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.
   * **Název veřejné IP adresy**: název prostředku veřejné IP adresy.
1. Na obrazovce ověřování klikněte na **vytvořit**. Počkejte přibližně 5 minut, než se prostředek bastionu vytvoří a nasadí.

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md)