---
title: Vytvoření tunelu IPSec do řešení Azure VMware
description: Naučte se, jak vytvořit virtuální rozbočovač WAN pro vytvoření tunelu IPSec do řešení Azure VMware.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 63318b9fdd0de5e0ce102fafe332f40f595f38f1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357840"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Vytvoření tunelu IPSec do řešení Azure VMware

V tomto článku provedeme kroky k navázání tunelového propojení site-to-site sítě VPN (IPsec IKEv1 a IKEv2), které končí ve Microsoft Azure virtuální síti WAN. Vytvoříme Azure Virtual WAN hub a bránu VPN s připojenou veřejnou IP adresou. Pak vytvoříme bránu Azure ExpressRoute a vytvoříte koncový bod řešení Azure VMware. Přečtěte si také podrobnosti o povolení místního nastavení sítě VPN založené na zásadách. 

## <a name="topology"></a>Topologie

![Architektura tunelového propojení VPN typu Site-to-site.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Virtuální rozbočovač Azure obsahuje bránu Azure VMware Solution ExpressRoute a bránu VPN typu Site-to-site. Připojuje místní zařízení VPN s koncovým bodem řešení Azure VMware.

## <a name="before-you-begin"></a>Než začnete

Pokud chcete vytvořit tunel VPN typu Site-to-site, budete muset vytvořit veřejnou IP adresu ukončující na místním zařízení VPN.

## <a name="create-a-virtual-wan-hub"></a>Vytvoření virtuálního centra sítě WAN

1. V Azure Portal vyhledejte **virtuální sítě WAN**. Vyberte **+Přidat**. Otevře se stránka pro vytvoření sítě WAN.  

2. Na stránce **vytvořit síť WAN** zadejte požadovaná pole a potom vyberte **zkontrolovat + vytvořit**.
   
   | Pole | Hodnota |
   | --- | --- |
   | **Předplatné** | Hodnota je předem vyplněná odběrem patřícím do skupiny prostředků. |
   | **Skupina prostředků** | Virtuální síť WAN je globální prostředek a není omezená na konkrétní oblast.  |
   | **Umístění skupiny prostředků** | Chcete-li vytvořit virtuální centrum sítě WAN, je třeba nastavit umístění pro skupinu prostředků.  |
   | **Name** |   |
   | **Typ** | Vyberte **Standard** , který umožní více než jenom přenosy brány VPN Gateway.  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Vytvořte síť WAN.":::

3. V Azure Portal vyberte virtuální síť WAN, kterou jste vytvořili v předchozím kroku, vyberte **vytvořit virtuální rozbočovač** , zadejte požadovaná pole a potom vyberte **Další: lokalita v lokalitě**. 

   | Pole | Hodnota |
   | --- | --- |
   | **Oblast** | Výběr oblasti je vyžadován z perspektivy správy.  |
   | **Name** |    |
   | **Privátní adresní prostor centra** | Zadejte podsíť s použitím `/24` (minimálně).  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Vytvořte virtuální rozbočovač.":::

4. Na kartě **site-to-site** definujte bránu site-to-site nastavením agregované propustnosti z rozevíracího seznamu **jednotky škálování brány** . 

   >[!TIP]
   >Jedna jednotka škálování = 500 MB/s. Jednotky škálování jsou ve dvojicích pro redundanci, přičemž každá podporuje 500 MB/s.
  
5. Na kartě **ExpressRoute** vytvořte bránu ExpressRoute. 

   >[!TIP]
   >Hodnota jednotky škálování je 2 GB/s. 

    Vytvoření každého centra trvá přibližně 30 minut. 

## <a name="create-a-vpn-site"></a>Vytvoření webu VPN 

1. V části **nedávné prostředky** v Azure Portal vyberte virtuální síť WAN, kterou jste vytvořili v předchozí části.

2. V **přehledu** virtuálního centra vyberte možnost **připojení**  >  **VPN (site-to-site)** a pak vyberte **vytvořit novou lokalitu VPN**.


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Vytvořit lokalitu VPN.":::  
 
3. Na kartě **základy** zadejte požadovaná pole a potom vyberte **Další: odkazy**. 

   | Pole | Hodnota |
   | --- | --- |
   | **Oblast** | Stejná oblast, kterou jste zadali v předchozí části.  |
   | **Name** |  |
   | **Dodavatel zařízení** |  |
   | **Border Gateway Protocol** | Nastavte na **Povolit** , aby se zajistilo, že řešení Azure VMware i místní servery budou inzerovat své trasy v rámci tunelu. Pokud je toto pole zakázané, musí se všechny podsítě, které je třeba inzerovat, spravovat ručně. Pokud nejsou podsítě vynechání, HCX se nepodaří vytvořit síť. Další informace najdete v tématu  [o protokolu BGP s Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Privátní adresní prostor**  | Zadejte místní blok CIDR.  Používá se ke směrování všech dat vázaných na místní zařízení v rámci tunelového propojení.  Blok CIDR se vyžaduje jenom v případě, že protokol BGP nepovolíte. |
   | **Připojit k** |   |

4. Na kartě **odkazy** vyplňte požadovaná pole a vyberte **zkontrolovat + vytvořit**. Zadání názvů odkazů a poskytovatelů vám umožní rozlišovat mezi libovolným počtem bran, které se můžou nakonec vytvořit v rámci centra. Protokol BGP a číslo autonomního systému (ASN) musí být v rámci vaší organizace jedinečné.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>Volitelné Definování sítě VPN pro tunely typu Site-to-Site VPN založené na zásadách

Tato část se týká jenom sítí VPN založených na zásadách. Nastavení sítě VPN na základě zásad (nebo statických, směrování) se ve většině případů řídí funkcemi pro zařízení VPN. Vyžadují, aby byly zadány místní sítě a řešení Azure VMware. Pro řešení Azure VMware se službou Azure Virtual WAN hub nemůžete vybrat *žádnou* síť. Místo toho je nutné zadat všechny relevantní místní rozsahy služby WAN hub pro řešení Azure VMware. Tyto rozsahy rozbočovačů slouží k zadání šifrovací domény pro místní koncový bod tunelového připojení VPN základní zásady. Na straně řešení Azure VMware se vyžaduje, aby byl povolený jenom indikátor výběru provozu na základě zásad. 

1. V Azure Portal přejdete na svůj virtuální web WAN hub. v části **připojení** vyberte **VPN (site-to-site)**.

2. Vyberte název sítě VPN a tři tečky (...) úplně vpravo. pak vyberte **Upravit připojení VPN k tomuto centru**.
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Upravit připojení VPN k tomuto centru" lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Upravte připojení mezi lokalitou sítě VPN a centrem a pak vyberte **Uložit**.
   - Internet Protocol zabezpečení (IPSec) vyberte možnost **vlastní**.
   - Použijte selektor provozu na základě zásad, vyberte **Povolit** .
   - Zadejte podrobnosti **protokolu IKE fáze 1** a **IKE fáze 2 (IPSec)**. 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Upravit připojení VPN"::: 
 
    Selektory přenosu nebo podsítí, které jsou součástí domény šifrování založené na zásadách, by měly být:
    
    - Virtuální síť WAN hub/24
    - Privátní cloud řešení Azure VMware/22
    - Připojená virtuální síť Azure (Pokud je k dispozici)

## <a name="connect-your-vpn-site-to-the-hub"></a>Připojení sítě VPN k centru

1. Zaškrtněte políčko vedle názvu sítě VPN (viz předchozí **lokalita sítě VPN a** snímek obrazovky) a pak vyberte **připojit weby sítě VPN**. V poli **předsdílený klíč** zadejte klíč dříve definovaný pro místní koncový bod. Pokud nemáte dříve definovaný klíč, můžete toto pole nechat prázdné a automaticky se vygeneruje klíč. 
 
    Pokud nasazujete bránu firewall v centru a jedná se o další segment směrování připojení přes toto tunelové propojení, povolte pouze **výchozí trasu šíření** .

    Vyberte **Connect** (Připojit). Obrazovka stavu připojení zobrazí stav vytvoření tunelu.

2. Přejít na přehled virtuální sítě WAN. Otevřete stránku sítě VPN a Stáhněte si konfigurační soubor sítě VPN, abyste ho mohli použít na místní koncový bod.  

3. Nyní povedeme opravu řešení Azure VMware ExpressRoute do virtuálního centra sítě WAN. (Tento krok vyžaduje nejprve vytvoření privátního cloudu.)

    Přejít do části **připojení** v privátním cloudu řešení Azure VMware. Na kartě **ExpressRoute** vyberte **+ požádat o autorizační klíč**. Pojmenujte ho a vyberte **vytvořit**. (Vytvoření klíče může trvat přibližně 30 sekund.) Zkopírujte ID ExpressRoute a autorizační klíč. 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="Zkopírujte ID a autorizační klíč Express Route.":::

    > [!NOTE]
    > Autorizační klíč zmizí po nějaké době, takže ho zkopírujte hned po jeho zobrazení.

4. Dále propojíme řešení Azure VMware a VPN Gateway ve virtuálním centru WAN. V Azure Portal otevřete virtuální síť WAN, kterou jste vytvořili dříve. Vyberte vytvořené virtuální centrum sítě WAN a v levém podokně vyberte **ExpressRoute** . Vyberte **+ uplatnit autorizační klíč**.

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Autorizační klíč pro uplatnění.":::

    Vložte autorizační klíč do pole autorizační klíč a ID ExpressRoute do pole **identifikátor URI partnerského okruhu** . Nezapomeňte vybrat **automaticky přidružit tento okruh ExpressRoute k centru.** Vyberte **Přidat** a vytvořte odkaz. 

5. K otestování připojení [vytvořte segment NSX-T](./tutorial-nsx-t-network-segment.md) a ZŘÍDÍTE virtuální počítač v síti. Otestujte pomocí příkazů pro místní i koncové body řešení Azure VMware.
