---
title: Vytvoření tunelu IPSec do řešení Azure VMware
description: Naučte se, jak vytvořit tunel site-to-Site VPN (IPsec IKEv1 a IKEv2) do řešení Azure VMware.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026996"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Vytvoření tunelu IPSec do řešení Azure VMware

V tomto článku provedeme kroky k navázání tunelového propojení site-to-site sítě VPN (IPsec IKEv1 a IKEv2), které končí ve Microsoft Azure virtuální síti WAN. Centrum obsahuje bránu Azure VMware Solution ExpressRoute a bránu VPN typu Site-to-site. Připojuje místní zařízení VPN s koncovým bodem řešení Azure VMware.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagram znázorňující architekturu tunelového propojení typu Site-to-site sítě VPN." border="false":::

V tomto postupu:
- Vytvořte virtuální síť Azure a službu VPN Gateway s připojenou veřejnou IP adresou. 
- Vytvořte bránu Azure ExpressRoute a vytvořte koncový bod řešení Azure VMware. 
- Povolte místní nastavení sítě VPN založené na zásadách. 

## <a name="prerequisites"></a>Požadavky
Musíte mít veřejnou IP adresu ukončující na místním zařízení VPN.

## <a name="step-1-create-an-azure-virtual-wan"></a>Krok 1. Vytvoření virtuální sítě WAN Azure

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Krok 2. Vytvoření virtuální sítě WAN a brány

>[!TIP]
>[Bránu můžete vytvořit také v existujícím centru](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Vyberte virtuální síť WAN, kterou jste vytvořili v předchozím kroku.

1. Vyberte **vytvořit virtuální rozbočovač**, zadejte požadovaná pole a potom vyberte **Další: lokalita v lokalitě**. 

   Zadejte podsíť s použitím `/24` (minimálně).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Snímek obrazovky zobrazující stránku vytvořit virtuální rozbočovač":::

4. Vyberte kartu **site-to-site** , definujte bránu site-to-site nastavením agregované propustnosti z rozevírací nabídky **jednotky škálování brány** . 

   >[!TIP]
   >Jednotky škálování jsou ve dvojicích pro redundanci. Každá podpora 500 MB/s (jedna jednotka škálování = 500 MB/s). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Snímek obrazovky s podrobnostmi o lokalitě.":::

5. Vyberte kartu **ExpressRoute** a vytvořte bránu ExpressRoute. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Snímek obrazovky s nastavením ExpressRoute":::

   >[!TIP]
   >Hodnota jednotky škálování je 2 GB/s. 

    Vytvoření každého centra trvá přibližně 30 minut. 

## <a name="step-3-create-a-site-to-site-vpn"></a>Krok 3. Vytvoření S2S (Site-to-site) VPN

1. V Azure Portal vyberte virtuální síť WAN, kterou jste vytvořili dříve.

2. V **přehledu** virtuálního centra vyberte možnost **připojení**  >  **VPN (site-to-site)**  >  **vytvořit novou lokalitu VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Snímek obrazovky se stránkou s přehledem pro virtuální rozbočovač s VPN (site-to-site) a vybraným novým webem sítě VPN.":::  
 
3. Na kartě **základy** zadejte požadovaná pole. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Snímek obrazovky s kartou základy pro nový web VPN":::  

   1. Nastavte **Border Gateway Protocol** na **Povolit**.  Když je tato možnost povolená, zajistí, aby řešení Azure VMware i místní servery inzerovaly své trasy v rámci tunelu. Pokud je toto pole zakázané, musí se všechny podsítě, které je třeba inzerovat, spravovat ručně. Pokud nejsou podsítě vynechání, HCX se nepodaří vytvořit síť. Další informace najdete v tématu  [o protokolu BGP s Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. V případě **privátního adresního prostoru** zadejte místní blok CIDR. Používá se ke směrování všech dat vázaných na místní zařízení v rámci tunelového propojení. Blok CIDR se vyžaduje jenom v případě, že protokol BGP nepovolíte.

1. Vyberte **Další: odkazy** a vyplňte požadovaná pole. Zadání názvů odkazů a poskytovatelů vám umožní rozlišovat mezi libovolným počtem bran, které se můžou nakonec vytvořit v rámci centra. Protokol BGP a číslo autonomního systému (ASN) musí být v rámci vaší organizace jedinečné.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Snímek obrazovky zobrazující podrobnosti o propojení":::

1. Vyberte **Zkontrolovat a vytvořit**. 

1. Přejděte k požadovanému virtuálnímu rozbočovači a zrušte výběr **přidružení centra** pro připojení vašeho serveru VPN k centru.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Snímek obrazovky, který zobrazuje podokno připojené lokality pro virtuální rozbočovač, který je připravený pro předsdílený klíč a přidružená nastavení.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>Krok 4: Volitelné Vytváření tunelových propojení typu Site-to-Site VPN založená na zásadách

>[!IMPORTANT]
>Tento krok je volitelný a vztahuje se pouze na sítě VPN založené na zásadách. 

Nastavení sítě VPN založené na zásadách vyžadují zadání místních a Azure VMware řešení, včetně rozsahů rozbočovačů.  Tyto rozsahy rozbočovačů určují doménu šifrování pro místní koncový bod tunelu VPN založený na zásadách.  Na straně řešení Azure VMware se vyžaduje, aby byl povolený jenom indikátor výběru provozu na základě zásad. 

1. V Azure Portal přejdete na svůj virtuální web WAN hub. V části **připojení** vyberte **VPN (site-to-site)**.

2. Vyberte název sítě VPN, tři tečky (...) úplně vpravo a potom **upravte připojení VPN k tomuto centru**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Snímek obrazovky stránky v Azure pro virtuální lokalitu WAN, na které se zobrazují tři tečky vybrané pro přístup k úpravám připojení VPN k tomuto centru." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Upravte připojení mezi lokalitou sítě VPN a centrem a pak vyberte **Uložit**.
   - Internet Protocol zabezpečení (IPSec) vyberte možnost **vlastní**.
   - Použijte selektor provozu na základě zásad, vyberte **Povolit** .
   - Zadejte podrobnosti **protokolu IKE fáze 1** a **IKE fáze 2 (IPSec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Snímek obrazovky se stránkou pro úpravu připojení VPN"::: 
 
   Selektory přenosu nebo podsítí, které jsou součástí domény šifrování založené na zásadách, by měly být:
    
   - Virtuální centrum sítě WAN `/24`
   - Privátní cloud řešení Azure VMware `/22`
   - Připojená virtuální síť Azure (Pokud je k dispozici)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>Krok 5. Připojení sítě VPN k centru

1. Vyberte název sítě VPN a pak vyberte **připojit weby sítě VPN**. 

1. V poli **předsdílený klíč** zadejte klíč dříve definovaný pro místní koncový bod. 

   >[!TIP]
   >Pokud nemáte dříve definovaný klíč, můžete toto pole nechat prázdné. Klíč se vygeneruje automaticky. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Snímek obrazovky, který zobrazuje podokno připojené lokality pro virtuální centrum připravené na předsdílený klíč a přidružená nastavení. "::: 

1. Pokud nasazujete bránu firewall v centru a jedná se o další segment směrování, nastavte možnost **rozšířit výchozí trasu** na **Povolit**. 

   Pokud je tato možnost povolená, virtuální síť WAN se rozšíří do připojení jenom v případě, že se při nasazení brány firewall v centru nebo pokud je povolený vynucené tunelování na jiném připojeném serveru. Výchozí trasa nepochází do virtuálního centra WAN.  

1. Vyberte **Connect** (Připojit). Po několika minutách se v lokalitě zobrazí stav připojení a připojení.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Snímek obrazovky zobrazující připojení typu Site-to-site a stav připojení." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Stáhněte si konfigurační soubor sítě VPN](../virtual-wan/virtual-wan-site-to-site-portal.md#device) pro místní koncový bod.  

3. Opravte ExpressRoute řešení Azure VMware ve virtuálním centru WAN. 

   >[!IMPORTANT]
   >Před tím, než budete moct tuto platformu opravit, musíte nejdřív vytvořit privátní cloud. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Propojte řešení Azure VMware a bránu VPN společně ve virtuálním centru WAN. Z předchozího kroku použijete autorizační klíč a ID ExpressRoute (identifikátor URI partnerského okruhu).

   1. Vyberte bránu ExpressRoute a pak vyberte **uplatnit autorizační klíč**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Snímek obrazovky stránky ExpressRoute pro privátní cloud s vybraným klíčem autorizace uplatnění":::

   1. Do pole **autorizační klíč** vložte autorizační klíč.
   1. Do pole **identifikátor URI rovnocenného okruhu** Vložte ID ExpressRoute. 
   1. Zaškrtněte políčko **automaticky přidružit tento okruh ExpressRoute k centrálnímu poli centra** . 
   1. Vyberte **Přidat** a vytvořte odkaz. 

5. Otestujte připojení tak, že [vytvoříte segment NSX-T](./tutorial-nsx-t-network-segment.md) a ZŘÍDÍTE virtuální počítač v síti. Otestujete místní i koncové body řešení Azure VMware.
