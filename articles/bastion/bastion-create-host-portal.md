---
title: Vytvoření Azure Bastion host | Dokumentace Microsoftu
description: V tomto článku najdete informace o vytvoření Azure Bastion host
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: d8012fb75282d64f7d1858e02b842b1c770200f9
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191769"
---
# <a name="create-an-azure-bastion-host-preview"></a>Vytvoření Azure Bastion host (Preview)

Tento článek ukazuje, jak vytvořit Azure Bastion host. Když si zřídíte službu Azure Bastionu ve vaší virtuální síti, bezproblémové prostředí RDP/SSH je k dispozici pro všechny vaše virtuální počítače ve stejné virtuální síti. Toto nasazení je na jednu virtuální síť, ne na předplatné/účet nebo virtuální počítač.

Existují dva způsoby, které jste vytvořili prostředek Bastion host:

* Vytvořte prostředek Bastionu pomocí webu Azure portal.
* Vytvořte prostředek Bastionu na webu Azure Portal pomocí stávajícího nastavení virtuálního počítače.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Verze public preview je omezená na následující veřejných oblastech Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

Účast v této verzi preview, budete muset zaregistrovat. Pomocí těchto kroků můžete zaregistrovat verzi preview:

[!INCLUDE [register](../../includes/bastion-preview-register-include.md)]

## <a name="createhost"></a>Vytvoření hostitel typu bašta

Tato část vám pomůže vytvořit nový prostředek Bastionu Azure na webu Azure Portal.

1. Z domovské stránky v [ webu Azure portal – ve verzi preview](http://aka.ms/BastionHost), klikněte na tlačítko **+ vytvořit prostředek**. Ujistěte se, že můžete použít odkaz uvedený pro přístup k portálu pro tuto verzi preview, není regulární webu Azure portal.

1. Na **nový** stránku, *Hledat na Marketplace* zadejte **Bastionu**, pak klikněte na tlačítko **Enter** zobrazíte výsledky hledání.

1. Z výsledků, klikněte na tlačítko **Bastionu (preview)** . Ujistěte se, že vydavatel je *Microsoft* a kategorie je *sítě*.

1. Na **Bastionu (preview)** klikněte na **vytvořit** otevřít **vytvořit bastionu** stránky.

1. Na **vytvořit bastionu** stránce, konfiguraci nového prostředku Bastionu. Určení nastavení konfigurace pro váš prostředek Bastionu.

    ![Vytvoření bastionu](./media/bastion-create-host-portal/settings.png)

    * **Předplatné**: Předplatné Azure, kterou chcete použít k vytvoření nového prostředku Bastionu.
    * **Skupina prostředků**: Skupina prostředků Azure, ve kterém bude vytvořen nový prostředek Bastionu v. Pokud nemáte existující skupinu prostředků, můžete vytvořit nový.
    * **Název**: Název nového prostředku Bastionu
    * **Oblast**: Veřejné oblast Azure, který prostředek vytvořený.
    * **Virtuální síť**: Virtuální síť, ve kterém bude vytvořen prostředek Bastionu v. Nové virtuální sítě můžete vytvořit na portálu během tohoto procesu a v případě nemají nebo nechcete použít existující virtuální sítě. Pokud použijete existující virtuální síť, ujistěte se, že existující virtuální síť má dostatek volného adresní prostor pro plnění požadavků na podsíť Bastionu.
    * **Podsíť**: Podsíť ve vaší virtuální síti, ke které se nový prostředek hostitele Bastionu nasadí. Musíte vytvořit podsíť s použitím hodnoty názvu **AzureBastionSubnet**. Tato hodnota umožňuje Azure vědělo, který podsítě do Bastionu prostředky nasadíte. To se liší od podsíť brány. Důrazně doporučujeme použít aspoň/27 nebo větší podsítě (/ 27, / 26 a tak dále). Vytvořte **AzureBastionSubnet** bez jakékoli skupiny zabezpečení sítě, směrování tabulky nebo delegace.
    * **Veřejná IP adresa**: Veřejné IP adresy Bastionu prostředku, na kterém budou mít přístup RDP/SSH (přes port 443). Vytvořit novou veřejnou IP adresu, nebo použijte již existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek Bastionu, kterou vytváříte.
    * **Název veřejné IP adresy**: Název prostředku veřejné IP adresy.
    * **Veřejné IP adresy skladové položky**: Ve výchozím nastavení předem **standardní**.
    * **Přiřazení**: Ve výchozím nastavení předem **statické**.

1. Když zadáte nastavení, klikněte na tlačítko **revize + vytvořit**. Tato operace ověří hodnoty. Jakmile úspěšně proběhne ověření, můžete začít v procesu vytváření.
1. Na stránce vytvořit bastionu klikněte na tlačítko **vytvořit**.
1. Zobrazí se zpráva s informacemi, které už znáte, že probíhá nasazení. Stav se zobrazí na této stránce vytvářené prostředky. Trvá přibližně 5 minut, než Bastionu prostředek, který se dají vytvářet a nasazovat.

## <a name="createvmset"></a>Vytvoření hostitel typu bašta pomocí nastavení virtuálního počítače

Pokud hostitel typu bašta vytvoříte na portálu s použitím existujícího virtuálního počítače, různá nastavení automaticky výchozí odpovídající virtuální počítač nebo virtuální sítě.

1. V [portálu preview](https://aka.ms/BastionHost), přejděte ke svému virtuálnímu počítači a pak klikněte na tlačítko **připojit**.

    ![Připojení virtuálního počítače](./media/bastion-create-host-portal/vmsettings.png)

1. Na postranním panelu vpravo klikněte na tlačítko **Bastionu**, pak **použití Bastionu**.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. Na stránce Bastionu vyplňte následující pole nastavení:

    * **Název**: Název hostitele bastionu chcete vytvořit.
    * **Podsíť**: Podsíť ve vaší virtuální sítě, do které Bastionu se nasadí prostředků. Je potřeba vytvořit podsíť s názvem **AzureBastionSubnet**. Díky Azure vědělo, která podsíť Bastionu prostředek, který chcete nasadit. To se liší od podsíť brány. Klikněte na tlačítko **spravovat konfiguraci podsítě** vytvořit podsíť Bastionu Azure. Důrazně doporučujeme použít aspoň/27 nebo větší podsítě (/ 27, / 26, atd.). Vytvořte **AzureBastionSubnet** bez jakékoli skupiny zabezpečení sítě, směrování tabulky nebo delegace. Klikněte na tlačítko **vytvořit** vytvořte podsíť, pak pokračujte další nastavení.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Veřejná IP adresa**: Veřejné IP adresy Bastionu prostředku, na kterém budou mít přístup RDP/SSH (přes port 443). Vytvořit novou veřejnou IP adresu, nebo použijte již existující. Veřejná IP adresa musí být ve stejné oblasti jako prostředek Bastionu, kterou vytváříte.
    * **Název veřejné IP adresy**: Název prostředku veřejné IP adresy.
1. V dialogovém okně ověřování klikněte na tlačítko **vytvořit**. Počkejte přibližně 5 minut, než Bastionu prostředek, který se dají vytvářet a nasazovat.

## <a name="next-steps"></a>Další postup

Přečtěte si [Bastionu – nejčastější dotazy](bastion-faq.md)