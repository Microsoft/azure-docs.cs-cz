---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "92328422"
---
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Do **vyhledávacích prostředků, služeb a dokumentů (G +/)** zadejte *Virtual Network*.

   ![Stránka pro vyhledání prostředku Virtual Network](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Stránka pro vyhledání prostředku virtuální sítě")
1. Z výsledků **Marketplace** vyberte **Virtual Network** .

   ![Vybrat virtuální síť](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Stránka pro vyhledání prostředku virtuální sítě")
1. Na stránce **Virtual Network** vyberte **vytvořit**.

   ![Stránka virtuální síť](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Výběr možnosti Vytvořit")
1. Jakmile vyberete **vytvořit** , otevře se stránka **vytvořit virtuální síť** .
1. Na kartě **základy** nakonfigurujte nastavení sítě VNet **a podrobnosti o** **instanci** .

   ![Karta základy](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Karta základy") Po vyplnění polí se zobrazí zelená značka zaškrtnutí, pokud jsou znaky, které zadáte do pole, ověřovány. Některé hodnoty jsou vyplněny, které můžete nahradit vlastními hodnotami:

   - **Předplatné** : Zkontrolujte, jestli je uvedeno správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
   - **Skupina prostředků** : Vyberte existující skupinu prostředků nebo vytvořte novou kliknutím na **vytvořit novou** . Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Název** : zadejte název vaší virtuální sítě.
   - **Oblast** : vyberte umístění pro virtuální síť. Umístění Určuje, kde budou prostředky, které nasadíte do této virtuální sítě, v provozu.

1. Na kartě **IP adresy** nakonfigurujte hodnoty. Hodnoty uvedené v následujících příkladech jsou pro demonstrační účely. Upravte tyto hodnoty podle nastavení, které požadujete.

   ![Karta IP adresy](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Karta IP adresy")  
   - **Adresní prostor IPv4** : ve výchozím nastavení se automaticky vytvoří adresní prostor. Kliknutím na adresní prostor se můžete přizpůsobit tak, aby odrážel vaše vlastní hodnoty. Můžete také přidat další adresní prostory.
   - **Podsíť** : Pokud použijete výchozí adresní prostor, automaticky se vytvoří výchozí podsíť. Pokud změníte adresní prostor, budete muset přidat podsíť. Vyberte **+ Přidat podsíť** a otevřete tak okno **Přidat podsíť** . Nakonfigurujte následující nastavení a pak vyberte **Přidat** a přidejte hodnoty:
      - **Název podsítě** : v tomto příkladu jsme jmenovali podsíť "front".
      - **Rozsah adres podsítě** : rozsah adres pro tuto podsíť.

1. Na kartě **zabezpečení** nechte v tuto chvíli výchozí hodnoty:

   - **DDos Protection** : Basic
   - **Brána firewall** : zakázaná
1. Vyberte **zkontrolovat + vytvořit** a ověřte nastavení virtuální sítě.
1. Po ověření nastavení vyberte **vytvořit**.
