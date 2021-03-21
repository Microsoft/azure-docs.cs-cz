---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048249"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. Přejděte na **všechny prostředky** a vyberte virtuální síť WAN, kterou jste vytvořili, a pak v nabídce na levé straně vyberte **konfigurace VPN uživatele** .
1. Na stránce **Konfigurace sítě VPN uživatele** vyberte **+ vytvořit uživatel konfigurace sítě VPN** v horní části stránky a otevřete stránku **vytvořit novou konfiguraci VPN uživatele** .

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Snímek obrazovky se stránkou konfigurace uživatelských sítí VPN.":::

1. Na kartě **základy** v části **Podrobnosti instance** zadejte **název** , který chcete přiřadit ke konfiguraci sítě VPN.
1. V poli **Typ tunelu** v rozevíracím seznamu vyberte požadovaný typ tunelového propojení. Možnosti typu tunelu jsou: **IKEV2 VPN**, **OpenVPN**, a **OpenVPN a IKEv2**.
1. Použijte následující kroky, které odpovídají typu tunelového propojení, který jste vybrali. Po zadání všech hodnot klikněte na tlačítko **zkontrolovat + vytvořit** a pak **vytvořte** konfiguraci.

   **IKEv2 VPN**

   * **Požadavky:** Když vyberete typ tunelového propojení **IKEv2** , zobrazí se vám zpráva s přímým přístupem k výběru metody ověřování. Pro IKEv2 můžete zadat jenom jednu metodu ověřování. Můžete zvolit certifikát Azure, Azure Active Directory nebo ověřování pomocí protokolu RADIUS.

   * **Vlastní parametry protokolu IPSec:** Pokud chcete přizpůsobit parametry protokolu IKE fáze 1 a IKE Phase 2, přepněte přepínač IPsec na **Custom** a vyberte hodnoty parametru. Další informace o přizpůsobitelných parametrech najdete v článku o [vlastním protokolu IPSec](../articles/virtual-wan/point-to-site-ipsec.md) .

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Snímek obrazovky s přepínačem IPsec na vlastní":::

   * **Ověřování:** Přejděte na ověřovací mechanismus, který chcete použít, kliknutím na tlačítko **Další** v dolní části stránky přejděte k metodě ověřování nebo klikněte na příslušnou kartu v horní části stránky. Přepněte přepínač na **Ano** , pokud chcete vybrat metodu.

     V tomto příkladu je vybraná možnost ověřování pomocí protokolu RADIUS. Pro ověřování pomocí protokolu RADIUS můžete zadat IP adresu sekundárního serveru RADIUS a tajný klíč serveru.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Snímek protokolu IKE.":::

   **OpenVPN**

   * **Požadavky:** Když vyberete typ tunelového propojení **OpenVPN** , zobrazí se vám zpráva s přímým přístupem k výběru mechanismu ověřování. Pokud je jako typ tunelu vybrán OpenVPN, můžete zadat více metod ověřování. Můžete zvolit jakoukoli podmnožinu certifikátu Azure, Azure Active Directory nebo ověřování založeného na protokolu RADIUS. Pro ověřování pomocí protokolu RADIUS můžete zadat IP adresu sekundárního serveru RADIUS a tajný klíč serveru.

   * **Ověřování:** Přejděte na metody ověřování, které chcete použít, kliknutím na tlačítko **Další** v dolní části stránky přejděte k metodě ověřování nebo klikněte na příslušnou kartu v horní části stránky.
   Pro každou metodu, kterou chcete vybrat, přepínejte přepínač na **Ano** a zadejte odpovídající hodnoty.

     V tomto příkladu je vybrána možnost Azure Active Directory.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Snímek stránky OpenVPN":::
