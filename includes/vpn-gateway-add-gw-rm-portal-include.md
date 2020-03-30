---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331231"
---
1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Vytvořit prostředek**. 

   ![Vytvoření prostředku na webu Azure Portal](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. Do pole **Hledat na marketplace** zadejte "Brána virtuální sítě". Vyhledejte **bránu virtuální sítě** v návratu hledání a vyberte položku. Na stránce **Brána virtuální sítě** vyberte **Vytvořit**. Tím otevřete stránku **Vytvořit bránu virtuální sítě**.
3. Na kartě **Základy** vyplňte hodnoty brány virtuální sítě.

   ![Vytvoření polí stránky brány virtuální sítě](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Vytvoření polí stránky brány virtuální sítě")

   ![Vytvoření polí stránky brány virtuální sítě](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Vytvoření polí stránky brány virtuální sítě")

   **Podrobnosti o projektu**

   - **Předplatné**: V rozevíracím souboru vyberte předplatné, které chcete použít.
   - **Skupina prostředků**: Toto nastavení se automaticky vyplní, když na této stránce vyberete virtuální síť.

   **Podrobnosti instance**

   - **Název**: Zadejte pro bránu název. Pojmenování brány není stejné jako pojmenování podsítě brány. Jedná se o název objektu brány, který vytváříte.
   - **Oblast**: Vyberte oblast, ve které chcete vytvořit tento zdroj. Oblast pro bránu musí být stejná jako virtuální síť.
   - **Typ brány**: Vyberte **VPN**. Brány VPN používají bránu virtuální sítě typu **VPN**.
   - **Typ VPN**: Vyberte typ VPN určený pro vaši konfiguraci. Většina konfigurací vyžaduje trasový typ VPN.
   - **SKU**: Z rozevíracího seznamu vyberte SKU brány. SKU uvedené v rozevíracím seznamu závisí na typu VPN, který jste vybrali. Další informace o SKU brány najdete v tématu [SKU brány](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   - **Generování**: Informace o generování brány VPN naleznete [v tématu Brána SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   - **Virtuální síť**: V rozevíracím seznamu vyberte virtuální síť, do které chcete přidat tuto bránu.
   - **Rozsah adres podsítě brány**: Toto pole se zobrazí jenom v případě, že virtuální síť nemá podsíť brány. Pokud je to možné, proveďte rozsah /27 nebo větší (/26,/25 atd.). Nedoporučujeme vytvářet rozsah menší než /28. Pokud už máte podsíť brány, můžete zobrazit podrobnosti GatewaySubnet přechodem do virtuální sítě. Chcete-li oblast zobrazit, klepněte na **položku Podsítě.** Pokud chcete změnit rozsah, můžete odstranit a znovu vytvořit GatewaySubnet.

   **Veřejná IP adresa**: Toto nastavení určuje objekt veřejné IP adresy, který se přidruží k bráně VPN. Veřejná IP adresa se dynamicky přiřadí k tomuto objektu při vytvoření brány VPN. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

     - **Veřejná IP adresa**: Ponechat **vytvořit nový** vybraný.
     - **Název veřejné IP adresy**: Do textového pole zadejte název instance veřejné IP adresy.
     - **Přiřazení**: Brána VPN podporuje pouze dynamickou funkci.

   **Aktivní aktivní režim**: Režim **Povolit aktivní aktivní režim** vyberte pouze v případě, že vytváříte konfiguraci brány aktivní a aktivní. V opačném případě nechte toto nastavení nevybrané.

   Možnost **Konfigurovat číslo ASN pro protokol BGP** nechte nevybranou s výjimkou případů, kdy vaše konfigurace specificky vyžaduje toto nastavení. Pokud toto nastavení skutečně vyžadujete, výchozí ASN je 65515. Dá se ale změnit.
4. Chcete-li spustit ověření, vyberte **zkontrolovat + vytvořit.** Jakmile ověření projde, vyberte **Vytvořit** pro nasazení brány VPN. Úplné vytvoření a nasazení brány může trvat až 45 minut. Stav nasazení se zobrazí na stránce Přehled pro bránu.

Po vytvoření brány můžete zobrazením virtuální sítě na portálu zobrazit IP adresu, která jí byla přiřazena. Brána se zobrazí jako připojené zařízení.
