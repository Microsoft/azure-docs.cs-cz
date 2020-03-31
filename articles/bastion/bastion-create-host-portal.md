---
title: 'Vytvoření hostitele Azure Bastion: portál'
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure Bastion pomocí portálu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366139"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Vytvoření hostitele Azure Bastion pomocí portálu

Tento článek ukazuje, jak vytvořit hostitele Azure Bastion pomocí portálu Azure. Jakmile zřídíte službu Azure Bastion ve vaší virtuální síti, bezproblémové prostředí RDP/SSH je k dispozici všem virtuálním počítačům ve stejné virtuální síti. Nasazení Azure Bastion se používá pro virtuální síť, ne na předplatné nebo účet nebo virtuální počítač.

Můžete vytvořit nový prostředek hostitele bastionu na portálu buď zadáním všech nastavení ručně nebo pomocí nastavení, které odpovídají existující virtuální počítač. Pokud chcete vytvořit hostitele bastionu pomocí nastavení virtuálního počítače, přečtěte si článek [o rychlém startu.](quickstart-host-portal.md) Volitelně můžete použít [Azure PowerShell](bastion-create-host-powershell.md) k vytvoření hostitele Azure Bastion.

## <a name="before-you-begin"></a>Než začnete

Bašta je dostupná v následujících veřejných oblastech Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Vytvoření hostitele bašty

Tato část vám pomůže vytvořit nový prostředek Azure Bastion z webu Azure Portal.

1. V nabídce [Portál Azure](https://portal.azure.com) nebo na **domovské** stránce vyberte **Vytvořit prostředek**.

1. Na stránce **Nový** zadejte do pole *Hledat marketplace položku* **Bastion**a kliknutím na **Enter** se dostanete k výsledkům hledání.

1. Ve výsledcích klikněte na **Bastion**. Zkontrolujte, zda je vydavatelem *společnost Microsoft* a zda je kategorie *Síťová .*

1. Na stránce **Bašta** kliknutím na **Vytvořit** otevřete **stránku Vytvořit baštu.**

1. Na stránce **Vytvořit baštu** nakonfigurujte nový prostředek bastionu. Zadejte nastavení konfigurace prostředku bastionu.

    ![vytvořit baštu](./media/bastion-create-host-portal/settings.png)

    * **Předplatné**: Předplatné Azure, které chcete použít k vytvoření nového prostředku Bastion.
    * **Skupina prostředků**Azure : Skupina prostředků Azure, ve kterém bude vytvořen nový prostředek bastionu. Pokud nemáte existující skupinu prostředků, můžete vytvořit novou.
    * **Název**: Název nového zdroje Bašty
    * **Oblast**: Veřejná oblast Azure, ve které se prostředek vytvoří.
    * **Virtuální síť**: Virtuální síť, ve které bude prostředek Bastion vytvořen. Během tohoto procesu můžete na portálu vytvořit novou virtuální síť nebo použít existující virtuální síť. Pokud používáte existující virtuální síť, ujistěte se, že existující virtuální síť má dostatek volného adresního prostoru pro požadavky podsítě Bastion.
    * **Podsíť**: Podsíť ve virtuální síti, do které bude nasazen nový prostředek hostitele Bastion. Je nutné vytvořit podsíť pomocí hodnoty názvu **AzureBastionSubnet**. Tato hodnota umožňuje Azure vědět, které podsítě nasadit prostředky Bastion. Tose liší od podsítě Brány. Je nutné použít podsíť alespoň /27 nebo větší (/27, /26 a tak dále).
    
       Vytvořte **AzureBastionSubnet** bez všech směrovacích tabulek nebo delegací. Pokud používáte skupiny zabezpečení sítě na **AzureBastionSubnet**, naleznete v článku [Práce s skupinami zabezpečení sítě.](bastion-nsg.md)
    * **Veřejná IP adresa**: Veřejná IP adresa prostředku bastionu, ke kterému bude přístup k RDP/SSH (přes port 443). Vytvořte novou veřejnou IP adresu nebo použijte existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.
    * **Název veřejné IP adresy**: Název prostředku veřejné IP adresy.
    * **Skladová položka veřejné IP adresy**: Toto nastavení je ve výchozím nastavení předem vyplněno na **standardní**. Azure Bastion používá nebo podporuje jenom standardní veřejnou IP skladovou položku.
    * **Přiřazení**: Toto nastavení je ve výchozím nastavení předem vyplněno na **Statický**.

1. Po dokončení zadávání nastavení klepněte na **tlačítko Zkontrolovat + Vytvořit**. Tím ověříte hodnoty. Jakmile ověření projde, můžete zahájit proces vytváření.
1. Na stránce **Vytvořit baštu** klepněte na tlačítko **Vytvořit**.
1. Zobrazí se zpráva s informací, že vaše nasazení probíhá. Stav se na této stránce zobrazí při vytváření prostředků. Vytvoření a nasazení prostředku bastionu trvá přibližně 5 minut.

## <a name="next-steps"></a>Další kroky

* Další informace našlápejte na časté dotazy k [baště.](bastion-faq.md)

* Informace o použití skupin zabezpečení sítě s podsítí Azure Bastion najdete v [tématu Práce se skupinami zabezpečení sítě](bastion-nsg.md).