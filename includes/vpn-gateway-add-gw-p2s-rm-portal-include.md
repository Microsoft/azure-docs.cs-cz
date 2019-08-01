---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/31/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eba7dbb934bbc19ed7dc7452c15cbf22a9429bc3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706737"
---
1. Na levé straně portálu klikněte na **+ Vytvořit prostředek** a do vyhledávacího pole zadejte text „Brána virtuální sítě“. Ve výsledcích hledání vyhledejte položku **Brána virtuální sítě** a klikněte na ni. Na stránce **Brána virtuální sítě** klikněte na **vytvořit**. Tím otevřete stránku **Vytvořit bránu virtuální sítě**.

   ![Pole na stránce Vytvořit bránu virtuální sítě](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Pole na stránce Vytvořit bránu virtuální sítě")

   ![Pole na stránce Vytvořit bránu virtuální sítě](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Pole na stránce Vytvořit bránu virtuální sítě")
2. Na stránce **Vytvořit bránu virtuální sítě** vyplňte hodnoty pro vaši bránu virtuální sítě.

   **Podrobnosti o projektu**

   - **Předplatné**: Z rozevíracího seznamu vyberte předplatné, které chcete použít.
   - **Skupina prostředků**: Toto nastavení se vyplní při výběru virtuální sítě na této stránce.

   **Podrobnosti instance**

   - **Název**: Pojmenujte bránu. Pojmenování brány není stejné jako pojmenování podsítě brány. Jedná se o název objektu brány, který vytváříte.
   - **Oblast**: Vyberte oblast, ve které chcete vytvořit tento prostředek. Oblast brány musí být stejná jako tato virtuální síť.
   - **Typ brány**: Vyberte **VPN**. Brány VPN používají bránu virtuální sítě typu **VPN**. 
   - **Typ sítě VPN**: Vyberte typ sítě VPN, který je určen pro vaši konfiguraci. Většina konfigurací vyžaduje trasový typ VPN.
   - **SKU**: Z rozevíracího seznamu vyberte SKU brány. SKU uvedené v rozevíracím seznamu závisí na typu VPN, který jste vybrali. Další informace o SKU brány najdete v tématu [SKU brány](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Virtuální síť**: Zvolte virtuální síť, do které chcete tuto bránu přidat.

      **Rozsah adres podsítě brány**: Toto pole se zobrazí jenom v případě, že virtuální síť, kterou jste vybrali, nemá podsíť brány. Pokud ještě nemáte podsíť brány, vyplňte tento rozsah. Pokud je to možné, udělejte rozsah/27 nebo větší (/26,/25 atd.).

    **Veřejná IP adresa**: Toto nastavení určuje objekt veřejné IP adresy, který se přidruží k bráně VPN. Veřejná IP adresa se dynamicky přiřadí k tomuto objektu při vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

     - **Veřejná IP adresa**: Možnost **Vytvořit novou** nechte vybranou.
     - **Název veřejné IP adresy**: Do textového pole zadejte název vaší instance veřejné IP adresy.
     - **Přiřazení**: Brána sítě VPN podporuje pouze dynamické.

   **Režim aktivní-aktivní**: Možnost **Povolit režim aktivní–aktivní** povolte jenom v případě, že vytváříte konfiguraci brány typu aktivní-aktivní. V opačném případě nechte toto nastavení nevybrané.

   Možnost **Konfigurovat číslo ASN pro protokol BGP** nechte nevybranou s výjimkou případů, kdy vaše konfigurace specificky vyžaduje toto nastavení. Pokud toto nastavení skutečně vyžadujete, výchozí ASN je 65515. Dá se ale změnit.

3. Kliknutím na tlačítko **zkontrolovat + vytvořit** spusťte ověřování. Po úspěšném ověření kliknutím na **vytvořit** nasaďte bránu VPN. Pro úplné vytvoření a nasazení brány může trvat až 45 minut. Stav nasazení můžete zobrazit na stránce Přehled pro vaši bránu.

Po vytvoření brány můžete zobrazením virtuální sítě na portálu zobrazit IP adresu, která jí byla přiřazena. Brána se zobrazí jako připojené zařízení.