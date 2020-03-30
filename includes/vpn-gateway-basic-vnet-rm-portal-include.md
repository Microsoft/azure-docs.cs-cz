---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301932"
---
Virtuální síť můžete vytvořit s modelem nasazení Správce prostředků a portálazure podle následujících kroků. Další informace o virtuálních sítích naleznete v [tématu Přehled virtuální chod sítě](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Při použití virtuální sítě jako součást architektury mezi místními, nezapomeňte koordinovat s místním správcem sítě vyčlenit rozsah IP adres, které můžete použít speciálně pro tuto virtuální síť. Pokud na obou stranách připojení VPN existuje duplicitní rozsah adres, bude provoz směrován neočekávaným způsobem. Navíc pokud chcete připojit tuto virtuální síť k jiné virtuální síti, adresní prostor se nemůže překrývat s jinou virtuální sítí. Naplánujte konfiguraci sítě odpovídajícím způsobem.
>
>

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Do **vyhledávacích prostředků, služeb a dokumentů (G+/)** zadejte *virtuální síť*.

   ![Vyhledat stránku prostředků virtuální sítě](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Vyhledání stránky prostředků virtuální sítě")
1. Z výsledků **marketplace** vyberte **Virtuální síť.**

   ![Výběr virtuální sítě](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Vyhledání stránky prostředků virtuální sítě")
1. Na stránce **Virtuální síť** vyberte **Vytvořit**.

   ![stránka virtuální sítě](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Výběr možnosti Vytvořit")
1. Jakmile vyberete **Vytvořit**, otevře se stránka **Vytvořit virtuální síť.**
1. Na kartě **Základy** nakonfigurujte **podrobnosti o projektu** a **podrobnosti instance** nastavení virtuální sítě.

   ![Karta Základy](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Karta Základy") Když pole vyplníte, zobrazí se zelená značka zaškrtnutí, když jsou znaky zadané do pole ověřeny. Některé hodnoty jsou automaticky vyplněny, které můžete nahradit vlastními hodnotami:

   - **Předplatné**: Zkontrolujte, jestli je uvedeno správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
   - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo kliknutím na **Vytvořit nový** vytvořte novou. Další informace o skupinách prostředků najdete [v tématu Přehled Správce prostředků Azure](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Název**: Zadejte název virtuální sítě.
   - **Oblast:** Vyberte umístění pro virtuální síť. Umístění určuje, kde prostředky, které nasadíte do této virtuální sítě bude žít.

1. Na kartě **IP adresy** nakonfigurujte hodnoty. Hodnoty uvedené v níže uvedených příkladech jsou pro demonstrační účely. Upravte tyto hodnoty podle požadovaného nastavení.

   ![Karta IP adresy](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Karta IP adresy")  
   - **Adresní prostor IPv4**: Ve výchozím nastavení je adresní prostor vytvořen automaticky. Můžete kliknout na adresní prostor a upravit jej tak, aby odrážel vaše vlastní hodnoty. Můžete také přidat další adresní prostory.
   - **IPv6**: Pokud vaše konfigurace vyžaduje adresní prostor IPv6, zadejte tyto informace do pole **Přidat adresní prostor IPv6.**
   - **Podsíť**: Pokud použijete výchozí adresní prostor, bude automaticky vytvořena výchozí podsíť. Pokud změníte adresní prostor, je třeba přidat podsíť. Výběrem **+ Přidáním podsítě** otevřete okno **Přidat podsíť.** Nakonfigurujte následující nastavení a pak vyberte **Přidat** a přidejte hodnoty:
      - **Název podsítě**: V tomto příkladu jsme pojmenovali podsíť FrontEnd.
      - **Rozsah adres podsítě**: Rozsah adres pro tuto podsíť.

1. Na kartě **Zabezpečení** ponechte v tomto okamžiku výchozí hodnoty:

   - **Ochrana DDoS**: Základní
   - **Brána firewall**: Zakázáno
1. Vyberte **Zkontrolovat + vytvořit,** chcete-li ověřit nastavení virtuální sítě.
1. Po ověření nastavení vyberte **vytvořit**.
