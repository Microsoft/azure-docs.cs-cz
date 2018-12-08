---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109219"
---
Pomocí následujících kroků můžete vytvořit virtuální síť pomocí modelu nasazení Resource Manager a webu Azure portal. Další informace o virtuálních sítích najdete v tématu [Přehled služby Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Pro virtuální sítě pro připojení k místnímu umístění zajistěte ve spolupráci se správcem vaší místní sítě a vyčlenit rozsah IP adres, který můžete použít speciálně pro tuto virtuální síť. Pokud existuje duplicitní rozsah adres na obou stranách připojení VPN, provoz se bude směrovat neočekávaným způsobem. Pokud navíc chcete připojit tuto virtuální síť k jiné virtuální síti, adresní prostor se nesmí překrývat s jinou virtuální sítí. Podle toho naplánujte konfiguraci sítě.
>
>

1. Přihlaste se k [webu Azure portal](http://portal.azure.com) a vyberte **vytvořit prostředek**. **Nový** otevře se stránka.

2. V **Hledat na marketplace** zadejte *virtuální sítě* a vyberte **virtuální síť** z vráceném seznamu. **Virtuální síť** otevře se stránka.

   ![Stránka pro vyhledání prostředku virtuální sítě](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Stránka pro vyhledání prostředku virtuální sítě")

3. Z **vybrat model nasazení** seznam v dolní části stránky vyberte **Resource Manageru**a pak vyberte **vytvořit**. **Vytvořit virtuální síť** otevře se stránka.

   ![Stránka pro vytvoření virtuální sítě](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Stránka pro vytvoření virtuální sítě")

4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Po vyplnění polí se červený vykřičník změní zelená značka zaškrtnutí, pokud jsou znaky, které zadáte v poli ověření. Některé hodnoty jsou autofilled, můžete nahradit vlastními hodnotami:

   - **Název**: Zadejte název své virtuální sítě.

   - **Adresní prostor**: Zadejte adresní prostor. Pokud máte více adresních prostorů přidat, zadejte zde první adresní prostor. Další adresní prostory můžete přidat později po vytvoření virtuální sítě.

   - **Předplatné**: Zkontrolujte, jestli je uvedeno správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.

   - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte novou zadáním názvu nové skupiny prostředků. Pokud vytváříte novou skupinu, nazvěte skupinu prostředků v souladu s hodnotami plánované konfigurace. Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Umístění**: Vyberte umístění sítě VNet. Umístění Určuje, kde bude za prostředky, které nasadíte do této virtuální síti.

   - **Podsíť**: přidejte podsíť **název** a podsítě **rozsah adres**. Další podsítě můžete přidat později po vytvoření virtuální sítě. 
     
5. Vyberte **Vytvořit**.
