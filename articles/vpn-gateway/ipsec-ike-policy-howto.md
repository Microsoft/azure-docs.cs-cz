---
title: 'Zásady IPsec/IKE pro S2S VPN & připojení VNet-to-VNet: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Nakonfigurujte zásady protokolu IPsec/IKE pro připojení S2S nebo VNet-to-VNet se službou Azure VPN Gateway pomocí Azure Resource Manager a Azure Portal.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: eda920640667abc6620c5c90ee7d04a44789353e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995314"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>Konfigurace zásad IPsec/IKE pro připojení S2S VPN nebo VNet-to-VNet: Azure Portal

Tento článek vás provede jednotlivými kroky konfigurace zásad IPsec/IKE pro VPN Gateway připojení typu Site-to-Site VPN nebo VNet-to-VNet pomocí Azure Portal. Následující části vám pomůžou vytvořit a nakonfigurovat zásady IPsec/IKE a použít tyto zásady na nové nebo existující připojení.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>Informace o parametrech zásad IPsec a IKE

Protokol IPsec a IKE standard podporuje široké spektrum kryptografických algoritmů v různých kombinacích. Další informace [o kryptografických požadavcích a branách Azure VPN](vpn-gateway-about-compliance-crypto.md) najdete v článku o tom, jak vám to může přispět k zajištění připojení mezi různými místy a VNET-to-VNet, aby splňovalo požadavky na dodržování předpisů nebo zabezpečení.

Tento článek poskytuje pokyny k vytvoření a konfiguraci zásad IPsec/IKE a použije se pro nové nebo existující připojení VPN Gateway.

### <a name="considerations"></a>Požadavky

* Zásady IPsec/IKE fungují jenom na následujících SKU brány:
  * ***VpnGw1 ~ 5 a VpnGw1AZ ~ 5AZ***
  * ***Standard*** a ***HighPerformance***
* Pro jedno připojení můžete zadat pouze ***jednu*** kombinaci zásad.
* Je nutné zadat všechny algoritmy a parametry pro protokol IKE (hlavní režim) i pro protokol IPsec (rychlý režim). Zadání částečných zásad není povoleno.
* Pokud chcete zajistit, aby se zásady na místních zařízeních VPN podporovaly, kontaktujte specifikace dodavatele zařízení VPN. Připojení S2S nebo VNet-to-VNet nelze nastavit, pokud jsou zásady nekompatibilní.

## <a name="workflow"></a><a name ="workflow"></a>Pracovní postup

Tato část popisuje pracovní postup vytvoření a aktualizace zásad IPsec/IKE na připojení S2S VPN nebo VNet-to-VNet:

1. Vytvořte virtuální síť a bránu VPN.
2. Vytvořte bránu místní sítě pro připojení mezi různými místy nebo jinou virtuální síť a bránu pro připojení typu VNet-to-VNet.
3. Vytvořte připojení (IPsec nebo VNet2VNet).
4. Nakonfigurujte nebo aktualizujte nebo odeberte zásady IPsec/IKE u prostředků připojení.

Pokyny v tomto článku vám pomůžou nastavit a nakonfigurovat zásady IPsec/IKE, jak je znázorněno v diagramu:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagram zásad IPsec/IKE" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Podporované kryptografické algoritmy & síly klíčů

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algoritmy a klíče

Následující tabulka uvádí podporované kryptografické algoritmy a síly klíče, které můžou zákazníci konfigurovat:

| **Protokol IPsec/IKE**    | **Možnosti**    |
| ---              | ---            |
| Šifrování IKE   | AES256, AES192, AES128, DES3, DES                  |
| Integrita protokolu IKE    | SHA384, SHA256, SHA1, MD5                          |
| Skupina DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Šifrování protokolem IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Žádné    |
| Integrita protokolu IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Skupina PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Žádná   |
| Doba života přidružení zabezpečení v rychlém režimu   | (**Volitelné**: použijí se výchozí hodnoty, pokud není zadaný)<br>Sekundy (integer; **min. 300** / výchozí hodnota 27 000 sekund)<br>Kilobajty (integer; **min. 1024** / výchozí hodnota 102 400 000 kilobajtů)    |
| Selektor provozu | UsePolicyBasedTrafficSelectors * * ($True/$False; **Volitelné**, výchozí $false, pokud není zadané)    |
| Časový limit DPD      | Sekundy (Integer: min. 9/max. 3600; výchozí 45 sekund) |
|  |  |

#### <a name="important-requirements"></a>Důležité požadavky

* Konfigurace vašeho místního zařízení VPN musí odpovídat zásadám brány Azure VPN Gateway nebo musí obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec/IKE Azure:
  * Šifrovací algoritmus IKE (hlavní režim/fáze 1)
  * Algoritmus integrity IKE (hlavní režim/fáze 1)
  * Skupina DH (hlavní režim/fáze 1)
  * Šifrovací algoritmus IPsec (rychlý režim/fáze 2)
  * Algoritmus integrity protokolu IPsec (rychlý režim/fáze 2)
  * Skupina PFS (rychlý režim/fáze 2) > * selektor provozu (Pokud se používá UsePolicyBasedTrafficSelectors)
  * Doby životnosti přidružení zabezpečení jsou pouze místní specifikace, nemusí se shodovat.

* Pokud se pro šifrovací algoritmus IPsec používá GCMAES, musíte vybrat stejný algoritmus GCMAES a délku klíče pro integritu protokolu IPsec. například použití GCMAES128 pro obojí.

* V [tabulce algoritmy a klíče](#table1) výše:
  * IKE odpovídá hlavnímu režimu nebo fázi 1.
  * Protokol IPsec odpovídá rychlému režimu nebo fázi 2.
  * Skupina DH určuje skupinu Diffie-Hellmen použitou v hlavním režimu nebo fázi 1.
  * Skupina PFS zadala skupinu Diffie-Hellmen použitou v rychlém režimu nebo ve fázi 2.

* Doba životnosti SA hlavního režimu IKE je opravena na 28 800 sekund ve službě Azure VPN Gateway.

* Pokud nastavíte **UsePolicyBasedTrafficSelectors** na $true v připojení, nakonfiguruje se brána Azure VPN Gateway, aby se připojovala k místní bráně firewall sítě VPN založené na zásadách. Pokud povolíte PolicyBasedTrafficSelectors, musíte zajistit, aby vaše zařízení VPN odpovídalo selektorům přenosu, které jsou definované se všemi kombinacemi předpon vaší místní sítě (místní síťová brána), a to místo any-to-Any. Například pokud jsou předpony vaší místní sítě 10.1.0.0/16 a 10.2.0.0/16 a předpony vaší virtuální sítě jsou 192.168.0.0/16 a 172.16.0.0/16, je potřeba zadat následující selektory provozu:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   Další informace o selektorech provozu na základě zásad najdete v tématu [připojení několika místních zařízení VPN založených na zásadách](vpn-gateway-connect-multiple-policybased-rm-ps.md).

* DPD timeout – výchozí hodnota je 45 sekund u bran Azure VPN Gateway. Nastavení časového limitu na kratší období způsobí, že IKE může znovu použít klíč, což způsobí, že se připojení v některých případech odpojí. To nemusí být žádoucí, pokud vaše místní umístění docházejí z oblasti Azure, ve které se nachází brána sítě VPN, nebo když podmínka fyzického propojení může způsobit ztrátu paketů. Obecně doporučujeme nastavit časový limit mezi **30 až 45** sekundami.

### <a name="diffie-hellman-groups"></a>Skupiny Diffie-Hellman

V následující tabulce jsou uvedeny odpovídající skupiny Diffie-Hellman podporované vlastními zásadami:

| **Skupina Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Délka klíče** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768bitová skupina MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024bitová skupina MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048bitová skupina MODP  |
| 19                        | ECP256                   | ECP256       | 256bitová skupina ECP    |
| 20                        | ECP384                   | ECP384       | 384bitová skupina ECP    |
| 24                        | DHGroup24                | PFS24        | 2048bitová skupina MODP  |

Další podrobnosti najdete v článcích týkajících se [RFC3526](https://tools.ietf.org/html/rfc3526) a [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>S2S VPN se zásadami IPsec/IKE

V této části se seznámíte s postupem vytvoření připojení VPN typu Site-to-site pomocí zásad IPsec/IKE. Následující postup vytvoří připojení, jak je znázorněno na následujícím diagramu:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Zásady pro site-to-site" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Krok 1 – vytvoření virtuální sítě, brány sítě VPN a brány místní sítě

Vytvořte následující prostředky, jak je znázorněno na snímcích obrazovky níže. Postup najdete v tématu [vytvoření připojení VPN typu Site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

* **Virtuální síť:**  Virtuální sítě testvnet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="Sítě":::

* **Brána sítě VPN:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Brány":::

* **Brána místní sítě:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Web":::

* **Připojení:** VNet1 na Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Připojení":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>Krok 2 – konfigurace zásad IPsec/IKE u připojení S2S VPN

V této části nakonfigurujte zásadu IPsec/IKE s následujícími algoritmy a parametry:

* IKE: AES256, SHA384, DHGroup24, DPD časový limit 45 sekund
* IPsec: AES256, SHA256, PFS None, životnost SA 30000 sekund a 102400000KB

1. V Azure Portal přejděte na prostředek připojení a **VNet1toSite6**. Vyberte **konfigurační** stránku a vyberte **vlastní** zásady IPSec/IKE, abyste zobrazili všechny možnosti konfigurace. Níže uvedený snímek obrazovky ukazuje konfiguraci podle seznamu:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Lokalita 6":::

1. Pokud pro protokol IPsec používáte GCMAES, musíte použít stejný algoritmus GCMAES a délku klíče pro šifrování a integritu protokolu IPsec. Například následující snímek obrazovky určuje GCMAES128 pro šifrování protokolu IPsec a integritu protokolu IPsec:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="GCMAES pro protokol IPsec":::

1. Volitelně můžete vybrat **Povolit** pro možnost **použít zásady provozu na základě zásad** pro povolení služby Azure VPN Gateway k místnímu připojení k zařízením VPN založeným na zásadách, jak je popsáno výše.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="Výběr provozu na základě zásad":::

1. Po výběru všech možností vyberte **Uložit** a potvrďte změny prostředku připojení. Zásada bude vynutila přibližně minutu.

> [!IMPORTANT]
>
> * Po zadání zásad IPsec/IKE v připojení bude brána Azure VPN Gateway odesílat nebo přijímat jenom návrh IPsec/IKE se zadanými kryptografickými algoritmy a silnými klíči u tohoto konkrétního připojení. Ujistěte se, že vaše místní zařízení VPN pro připojení používá nebo přijímá přesnou kombinaci zásad, jinak tunel VPN S2S nebude vytvořen.
>
> * Pro **Selektory provozu na základě zásad** a možnosti **časového limitu DPD** se dají zadat **výchozí** zásady bez vlastní zásady IPSec/IKE, jak je znázorněno na snímku obrazovky výše.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>VNet-to-VNet se zásadami IPsec/IKE

Postup vytvoření připojení typu VNet-to-VNet se zásadami IPsec/IKE je podobný jako u připojení S2S VPN.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="Diagram zásad VNet-to-VNet" border="false":::

1. Pomocí kroků v článku [vytvoření připojení typu VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) vytvořte připojení typu VNet-to-VNet.

2. Po dokončení kroků se zobrazí dvě připojení VNet-to-VNet, jak je znázorněno na snímku obrazovky níže v prostředku VNet2GW:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="Připojení typu VNet-to-VNet":::

3. Přejděte na prostředek připojení a na portálu přejděte na stránku **Konfigurace** . Pokud chcete zobrazit možnosti vlastní zásady, vyberte v **zásadách IPSec/IKE** možnost **vlastní** . Vyberte kryptografické algoritmy s odpovídajícími délkami klíčů.

   Snímek obrazovky ukazuje různé zásady IPsec/IKE s následujícími algoritmy a parametry:
   * IKE: AES128, SHA1, DHGroup14, DPD timeout 45 sekund
   * IPsec: GCMAES128, GCMAES128, PFS14, životnost SA 14400 sekund & 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Zásady připojení":::

4. Vyberte **Uložit** a použijte tak změny zásad v prostředku připojení.

5. Použijte stejné zásady na jiný prostředek připojení VNet2toVNet1. Pokud to neuděláte, tunelové propojení VPN IPsec/IKE se nepřipojí kvůli neshodě zásad.

   > [!IMPORTANT]
   > Po zadání zásad IPsec/IKE v připojení bude brána Azure VPN Gateway odesílat nebo přijímat jenom návrh IPsec/IKE se zadanými kryptografickými algoritmy a silnými klíči u tohoto konkrétního připojení. Ujistěte se, že zásady protokolu IPsec pro obě připojení jsou stejné, jinak se připojení VNet-to-VNet nevytvoří.

6. Po dokončení těchto kroků se připojení naváže během několika minut a bude k dispozici následující topologie sítě:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagram zásad IPsec/IKE" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Postup odebrání vlastních zásad IPsec/IKE z připojení

1. Pokud chcete odebrat vlastní zásadu z připojení, přejděte k prostředku připojení a přejděte na stránku **Konfigurace** . zobrazí se aktuální zásada.

2. V možnosti **zásady IPSec/IKE** vyberte **výchozí** . Tím se odeberou všechny vlastní zásady, které jste dříve zadali v připojení, a obnovíte výchozí nastavení protokolu IPsec/IKE v tomto připojení:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="Odstranit zásadu":::

3. Výběrem možnosti **Uložit** odeberte vlastní zásadu a obnovte výchozí nastavení protokolu IPSec/IKE na připojení.

## <a name="next-steps"></a>Další kroky

Další podrobnosti týkající se selektorů provozu na základě zásad najdete v tématu [připojení několika místních zařízení VPN založených na zásadách](vpn-gateway-connect-multiple-policybased-rm-ps.md) .
