---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f1d4c50766fea5f4c562dc6a9661945713a41f53
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654722"
---
1. V nabídce [Azure Portal](https://portal.azure.com) vyberte **vytvořit prostředek**. 

   ![Vytvoření prostředku v Azure Portal](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. Do pole **Hledat na Marketplace** zadejte ' Virtual Network Gateway '. Ve výsledcích hledání vyhledejte položku **Brána virtuální sítě** a klikněte na ni. Na stránce **Brána virtuální sítě** klikněte na **vytvořit**. Tím otevřete stránku **Vytvořit bránu virtuální sítě**.

   ![Vytvořit pole stránky brány virtuální sítě](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw.png "Vytvořit pole stránky brány virtuální sítě")

   ![Vytvořit pole stránky brány virtuální sítě](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw2.png "Vytvořit pole stránky brány virtuální sítě")
3. Na stránce **Vytvořit bránu virtuální sítě** vyplňte hodnoty pro vaši bránu virtuální sítě.

   **Podrobnosti o projektu**

   - **Předplatné**: vyberte předplatné, které chcete použít, z rozevíracího seznamu.
   - **Skupina prostředků**: Toto nastavení se vyplní při výběru virtuální sítě na této stránce.

   **Podrobnosti instance**

   - **Název**: Zadejte pro bránu název. Pojmenování brány není stejné jako pojmenování podsítě brány. Jedná se o název objektu brány, který vytváříte.
   - **Oblast**: Vyberte oblast, ve které chcete tento prostředek vytvořit. Oblast brány musí být stejná jako tato virtuální síť.
   - **Typ brány**: Vyberte **VPN**. Brány VPN používají bránu virtuální sítě typu **VPN**.
   - **Typ VPN**: Vyberte typ VPN určený pro vaši konfiguraci. Většina konfigurací vyžaduje trasový typ VPN.
   - **SKU**: Z rozevíracího seznamu vyberte SKU brány. SKU uvedené v rozevíracím seznamu závisí na typu VPN, který jste vybrali. Další informace o SKU brány najdete v tématu [SKU brány](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Virtuální síť:** Vyberte virtuální síť, do které chcete tuto bránu přidat.

      **Rozsah adres podsítě brány**: Toto pole se zobrazí jenom v případě, že virtuální síť nemá podsíť brány. Pokud je to možné, udělejte rozsah/27 nebo větší (/26,/25 atd.). Nedoporučujeme vytvářet rozsahy, které jsou menší než/28. Pokud již máte podsíť brány, můžete zobrazit podrobnosti GatewaySubnet přechodem do vaší virtuální sítě. Rozsah zobrazíte kliknutím na **podsítě** . Pokud chcete změnit rozsah, můžete GatewaySubnet odstranit a znovu vytvořit.

   **Veřejná IP adresa**: Toto nastavení určuje objekt veřejné IP adresy, který se přidruží k bráně VPN. Veřejná IP adresa se dynamicky přiřadí k tomuto objektu při vytvoření brány VPN. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

     - **Veřejná IP adresa**: ponechte vybranou možnost **vytvořit nový** .
     - **Název veřejné IP adresy**: do textového pole zadejte název vaší instance veřejné IP adresy.
     - **Přiřazení**: Brána sítě VPN podporuje jenom dynamickou.

   **Režim aktivní-aktivní**: Pokud vytváříte konfiguraci brány aktivní-aktivní, vyberte možnost **Povolit režim aktivní-aktivní** . V opačném případě nechte toto nastavení nevybrané.

   Možnost **Konfigurovat číslo ASN pro protokol BGP** nechte nevybranou s výjimkou případů, kdy vaše konfigurace specificky vyžaduje toto nastavení. Pokud toto nastavení skutečně vyžadujete, výchozí ASN je 65515. Dá se ale změnit.

4. Kliknutím na tlačítko **zkontrolovat + vytvořit** spusťte ověřování. Po úspěšném ověření kliknutím na **vytvořit** nasaďte bránu VPN. Pro úplné vytvoření a nasazení brány může trvat až 45 minut. Stav nasazení můžete zobrazit na stránce Přehled pro vaši bránu.

Po vytvoření brány můžete zobrazením virtuální sítě na portálu zobrazit IP adresu, která jí byla přiřazena. Brána se zobrazí jako připojené zařízení.
