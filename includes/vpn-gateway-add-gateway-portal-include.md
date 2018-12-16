---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35f987f26ce47c19e3d5eb1ca5d2bb32d0c7ad1b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444060"
---
1. Na levé straně stránky portálu klikněte na **+** a do vyhledávacího pole zadejte text „Brána virtuální sítě“. V části **Výsledky** vyhledejte položku **Brána virtuální sítě** a klikněte na ni.
2. V dolní části stránky Brána virtuální sítě klikněte na **Vytvořit**. Tím otevřete stránku **Vytvořit bránu virtuální sítě**.
3. Na stránce **Vytvořit bránu virtuální sítě** zadejte hodnoty pro příslušnou bránu virtuální sítě.

    ![Vytvoření polí stránky brány virtuální sítě](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Nová brána")

   - **Název**: Název brány. Toto není název podsítě brány. Jedná se o název objektu brány, který vytváříte.
   - **Typ brány**: Vyberte **VPN**. Brány VPN používají bránu virtuální sítě typu **VPN**. 
   - **Typ sítě VPN**: Vyberte typ VPN určený pro vaši konfiguraci. Většina konfigurací vyžaduje trasový typ VPN.
   - **SKLADOVÁ POLOŽKA**: Z rozevíracího seznamu vyberte SKU brány. SKU uvedené v rozevíracím seznamu závisí na typu VPN, který jste vybrali. Další informace o SKU brány najdete v tématu [SKU brány](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

     Možnost **Povolit režim aktivní–aktivní** povolte jenom v případě, že vytváříte konfiguraci brány typu aktivní-aktivní. V opačném případě nechte toto nastavení nevybrané.
   - **Umístění**: Budete muset posunout k zobrazení umístění. Upravte pole **Umístění**, aby odkazovalo na umístění, kde se nachází vaše virtuální síť. Například Západní USA. Pokud umístění neukazuje na oblast, ve které je vaše virtuální síť umístěná, tato síť se v dalším kroku při výběru virtuální sítě v rozevíracím seznamu nezobrazí.
   - **Virtuální síť**: Zvolte virtuální síť, do které chcete tuto bránu přidat. Kliknutím na **Virtuální síť** otevřete stránku Zvolit virtuální síť. Vyberte virtuální síť. Pokud svou virtuální síť nevidíte, zkontrolujte, že pole Umístění odkazuje na oblast, ve které se nachází vaše virtuální síť.
   - **Rozsah adres podsítě brány**: Toto nastavení se zobrazí, pouze pokud jste nevytvořili podsíť brány pro vaši virtuální síť. Pokud jste už dříve vytvořili platnou podsíť brány, toto nastavení se nezobrazí.
   - **Veřejná IP adresa**: Toto nastavení určuje veřejnou IP adresu objektu, který se přidruží k bráně VPN. Veřejná IP adresa se dynamicky přiřadí k tomuto objektu při vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

     - Možnost **Vytvořit novou** nechte vybranou.
     - Do textového pole zadejte **název** veřejné IP adresy.

4. Možnost **Konfigurovat číslo ASN pro protokol BGP** nechte nevybranou s výjimkou případů, kdy vaše konfigurace specificky vyžaduje toto nastavení. Pokud toto nastavení skutečně vyžadujete, výchozí ASN je 65515. Dá se ale změnit.
5. Ověřte nastavení. Pokud chcete, aby se brána zobrazovala na řídicím panelu, můžete v dolní části stránky vybrat **Připnout na řídicí panel**. 
6. Kliknutím na **Vytvořit** zahajte proces vytváření brány VPN. Nastavení se ověří a na řídicím panelu se zobrazí dlaždice Nasazování brány virtuální sítě. Vytváření brány může trvat až 45 minut. K zobrazení stavu dokončení může být nutné obnovit stránku portálu.
   brány.

Po vytvoření brány můžete zobrazením virtuální sítě na portálu zobrazit IP adresu, která jí byla přiřazena. Brána se zobrazí jako připojené zařízení. Kliknutím na připojené zařízení (bránu virtuální sítě) můžete zobrazit další informace.