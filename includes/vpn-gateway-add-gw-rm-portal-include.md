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
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111765"
---
1. Přihlaste se k Azure portal a vyberte **vytvořit prostředek**. **Nový** otevře se stránka.

2. V **marketplace pole pro hledání**, zadejte *brány virtuální sítě*a vyberte **Brána virtuální sítě** ze seznamu hledání. 

3. Na **Brána virtuální sítě** stránce **vytvořit** otevřít **vytvořit bránu virtuální sítě** stránky.

   ![Pole na stránce Vytvořit bránu virtuální sítě](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Pole na stránce Vytvořit bránu virtuální sítě")

4. Na **vytvořit bránu virtuální sítě** stránky, zadejte hodnoty pro příslušnou bránu virtuální sítě:

   - **Název**: Zadejte název objektu brány, kterou vytváříte. Tento název se liší od název podsítě brány. 

   - **Typ brány**: vyberte **VPN** pro brány VPN Gateway. 

   - **Typ VPN**: Vyberte typ VPN určený pro vaši konfiguraci. Většina konfigurací vyžaduje **založené na trasách** typ sítě VPN.

   - **SKU**: Z rozevíracího seznamu vyberte SKU brány. SKU uvedené v rozevíracím seznamu závisí na typu VPN, který jste vybrali. Další informace o SKU brány najdete v tématu [SKU brány](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      Vybrat pouze **povolit režim aktivní – aktivní** Pokud vytváříte konfiguraci služby brány typu aktivní aktivní. V opačném případě nechte toto nastavení nevybrané.
  
   - **Umístění**: je nutné stránku posunout, aby **umístění**. Nastavte **umístění** do umístění, kde se nachází vaše virtuální síť. Například **USA – západ**. Pokud nenastavíte umístění do oblasti, ve kterém se nachází vaše virtuální síť, nebude se zobrazovat v rozevíracím seznamu při výběru virtuální sítě.

   - **Virtuální síť:** Vyberte virtuální síť, do které chcete tuto bránu přidat. Vyberte **virtuální síť** otevřít **zvolte virtuální síť** stránky a vyberte virtuální síť. Pokud se nezobrazí vaši virtuální síť, ujistěte se, **umístění** je nastaveno do oblasti, ve kterém se nachází vaše virtuální síť.

   - **Rozsah adres podsítě brány**: zobrazí pouze v toto nastavení, pokud jste nevytvořili dříve podsíť brány pro vaši virtuální síť. Pokud jste dříve vytvořili platnou podsíť brány, toto nastavení se nezobrazí.

   - **Veřejná IP adresa**: Toto nastavení určuje, objektu veřejné IP adresy, která je přidružená k bráně VPN. Veřejná IP adresa se dynamicky přiřadí k tomuto objektu při vytvoření brány VPN. VPN gateway aktuálně podporuje pouze *dynamické* veřejné přidělování IP adres. Dynamické přidělování však neznamená, že změní IP adresa po přiřazení k vaší bráně VPN. Změny veřejné IP adresy se pouze při odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.
    
      - Možnost **Vytvořit novou** nechte vybranou.

      - Do textového pole zadejte název pro svou veřejnou IP adresu.

   - **Konfigurace protokolu BGP číslo ASN**: nechte toto nastavení nevybrané, pokud vaše konfigurace konkrétně nevyžaduje. Pokud budete potřebovat toto nastavení, výchozí číslo ASN je *65515*, které můžete změnit.
     
5. Ověřte nastavení a vyberte **vytvořit** zahajte proces vytváření brány sítě VPN. Nastavení se ověří a zobrazí se vám **nasazení brány virtuální sítě** dlaždici na řídicím panelu. Vytváření brány může trvat až 45 minut. K zobrazení stavu dokončení může být nutné obnovit stránku portálu.

6. Po vytvoření brány Ověřte adresu IP, která je přiřazena k němu zobrazením virtuální sítě na portálu. Brána se zobrazí jako připojené zařízení. Můžete vybrat připojené zařízení (bránu virtuální sítě) můžete zobrazit další informace.