---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d648b6f7727743c6d2f2c6e050778777e44ddf4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921434"
---
Pokud chcete vytvořit virtuální síť v modelu nasazení Resource Manageru pomocí webu Azure Portal, postupujte podle následujících kroků. Použijte [ukázkové hodnoty](#values), pokud tento postup používáte v rámci kurzu. Pokud se nejedná o kurz, nezapomeňte ukázkové hodnoty nahradit svými. Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Aby se tato virtuální síť připojila k místnímu umístění, budete se muset domluvit se správcem vaší místní sítě a vyčlenit rozsah IP adres, který můžete použít speciálně pro tuto virtuální síť. Pokud existuje duplicitní rozsah adres na obou stranách připojení VPN, provoz se nemusí směrovat očekávaným způsobem. Pokud navíc chcete připojit tuto virtuální síť k jiné virtuální síti, adresní prostor se nesmí překrývat s jinou virtuální sítí. Podle toho pečlivě naplánujte konfiguraci sítě.
>
>

1. V prohlížeči přejděte na web [Azure Portal](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Klikněte na **Vytvořit prostředek**. Do pole **Hledat na Marketplace** zadejte text „Virtuální síť“. Ve vráceném seznamu vyhledejte položku **Virtuální síť** a kliknutím otevřete stránku **Virtuální síť**.
3. U dolního okraje stránky Virtuální síť v seznamu **Vybrat model nasazení** vyberte **Resource Manager** a potom klikněte na **Vytvořit**. Tím otevřete stránku Vytvořit virtuální síť.

  ![Stránka Vytvořit virtuální síť](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network.png "Stránka Vytvořit virtuální síť")
1. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Po vyplnění polí se červený vykřičník změní na zelenou značku zaškrtnutí, pokud jsou znaky zadané do pole platné.

  - **Název**: Zadejte název své virtuální sítě. V tomto příkladu používáme VNet1.
  - **Adresní prostor**: Zadejte adresní prostor. Pokud chcete přidat více adresních prostorů, přidejte první adresní prostor. Další adresní prostory můžete přidat později po vytvoření virtuální sítě. Ujistěte se, že zadaný adresní prostor se nepřekrývá s adresním prostorem pro vaše místní umístění.
  - **Předplatné**: Zkontrolujte, jestli je uvedeno správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
  - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte novou zadáním názvu nové skupiny prostředků. Pokud vytváříte novou skupinu, nazvěte skupinu prostředků v souladu s hodnotami plánované konfigurace. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
  - **Umístění**: Vyberte umístění sítě VNet. Umístění určuje, kde budou uloženy prostředky nasazené v této síti VNet.
  - **Podsíť:** Přidejte název první podsítě a rozsah adres podsítě. Později po vytvoření této virtuální sítě můžete přidat další podsítě a podsíť brány. 

5. Pokud chcete mít k síti VNet snadný přístup na řídicím panelu, vyberte možnost **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Po kliknutí na **Vytvořit** se na řídicím panelu zobrazí dlaždice s informacemi o průběhu vytváření sítě VNet. Obsah dlaždice se v průběhu vytváření sítě VNet mění.