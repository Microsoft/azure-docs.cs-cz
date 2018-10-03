---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 87d5389abd72875c62ea3484fc3f9ca43c339447
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240012"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>Jsou vlastní zásady IPsec/IKE podporovány ve všech skladových jednotkách (SKU) služby Azure VPN Gateway?
Vlastní zásady IPsec/IKE jsou podporovány v branách Azure VPN Gateway úrovně **VpnGw1, VpnGw2, VpnGw3, Standard** a **HighPerformance**. Skladová položka **Basic** **není** podporována.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Kolik zásad můžu zadat pro jedno připojení?
Pro jedno připojení můžete zadat pouze ***jednu*** kombinaci zásad.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Můžu pro připojení zadat částečné zásady? (například pouze algoritmy IKE, ale ne IPsec)
Ne, musíte zadat všechny algoritmy a parametry pro protokol IKE (hlavní režim) i protokol IPsec (rychlý režim). Zadání částečných zásad není povoleno.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Jaké algoritmy a síly klíče jsou podporované ve vlastních zásadách?
Následující tabulka uvádí podporované kryptografické algoritmy a síly klíče, které můžou zákazníci konfigurovat. Pro každé pole musíte vybrat jednu možnost.

| **IPsec/IKEv2**  | **Možnosti**                                                                   |
| ---              | ---                                                                           |
| Šifrování protokolem IKEv2 | AES256, AES192, AES128, DES3, DES                                             |
| Integrita protokolu IKEv2  | SHA384, SHA256, SHA1, MD5                                                     |
| Skupina DH         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, Žádná |
| Šifrování protokolem IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Žádné      |
| Integrita protokolu IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| Skupina PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Žádná                              |
| Doba života přidružení zabezpečení v rychlém režimu   | Sekundy (integer; **min. 300** / výchozí hodnota 27 000 sekund)<br>Kilobajty (integer; **min. 1024** / výchozí hodnota 102 400 000 kilobajtů)           |
| Selektor provozu | UsePolicyBasedTrafficSelectors ($True nebo $False; výchozí hodnota je $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 a PFS2048 jsou stejná skupina Diffie-Hellman **14** v PFS protokolů IKE a IPsec. Kompletní mapování najdete v části [Skupiny Diffie-Hellman](#DH).
> 2. V případě algoritmů GCMAES musíte zadat stejný algoritmus GCMAES a délku klíče pro šifrování protokolem IPsec i integritu dat.
> 3. V branách Azure VPN Gateway je doba života přidružení zabezpečení protokolu IKEv2 v hlavním režimu pevně nastavena na 28 800 sekund.
> 4. Doby života přidružení zabezpečení v rychlém režimu jsou volitelné parametry. Pokud nebyla zadána žádná doba života, použijí se výchozí hodnoty 27 000 sekund (7,5 hodiny) a 102 400 000 kilobajtů (102 GB).
> 5. UsePolicyBasedTrafficSelector je parametr možnosti v připojení. Podívejte se na další položku nejčastějších dotazů týkající se možnosti UsePolicyBasedTrafficSelectors.

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Je nutné, aby zásady brány Azure VPN Gateway přesně odpovídaly konfiguraci místního zařízení VPN?
Konfigurace vašeho místního zařízení VPN musí odpovídat zásadám brány Azure VPN Gateway nebo musí obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec/IKE Azure:

* Algoritmus šifrování protokolem IKE
* Algoritmus integrity protokolu IKE
* Skupina DH
* Algoritmus šifrování protokolem IPsec
* Algoritmus integrity protokolu IPsec
* Skupina PFS
* Selektor provozu (*)

Doby životnosti přidružení zabezpečení jsou pouze místní specifikace, nemusí se shodovat.

Pokud povolíte možnost **UsePolicyBasedTrafficSelectors**, je potřeba zajistit, aby vaše zařízení VPN mělo definované odpovídající selektory provozu pro všechny kombinace předpon místní sítě (brány místní sítě) a předpon virtuální sítě Azure (oběma směry), namísto použití konfigurace typu any-to-any. Například pokud jsou předpony vaší místní sítě 10.1.0.0/16 a 10.2.0.0/16 a předpony vaší virtuální sítě jsou 192.168.0.0/16 a 172.16.0.0/16, je potřeba zadat následující selektory provozu:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Další informace najdete v článku [Připojení několika místních zařízení VPN založených na zásadách](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name ="DH"></a>Které skupiny Diffie-Hellman jsou podporovány?
Následující tabulka uvádí podporované skupiny Diffie-Hellman pro protokoly IKE (DHGroup) a IPsec (PFSGroup):

| **Skupina Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Délka klíče** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768bitová skupina MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024bitová skupina MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048bitová skupina MODP  |
| 19                        | ECP256                   | ECP256       | 256bitová skupina ECP    |
| 20                        | ECP384                   | ECP384       | 384bitová skupina ECP    |
| 24                        | DHGroup24                | PFS24        | 2048bitová skupina MODP  |
|                           |                          |              |                |

Další informace najdete na stránkách [RFC3526](https://tools.ietf.org/html/rfc3526) a [RFC5114](https://tools.ietf.org/html/rfc5114).

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>Nahrazují vlastní zásady výchozí sady zásad IPsec/IKE pro brány Azure VPN Gateway?
Ano, jakmile jsou pro připojení zadány vlastní zásady, brána Azure VPN Gateway bude používat pouze zásady pro připojení, a to jako iniciátor IKE i jako respondér IKE.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Pokud odeberu vlastní zásady IPsec/IKE, stane se připojení nechráněným?
Ne, připojení bude i nadále chráněno protokolem IPsec/IKE. Jakmile z připojení odeberete vlastní zásady, brána Azure VPN Gateway se vrátí k [výchozímu seznamu návrhů IPsec/IKE](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) a znovu spustí metodu handshake protokolu IKE s vaším místním zařízením VPN.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Přerušilo by přidání nebo aktualizace zásad IPsec/IKE připojení VPN?
Ano, mohlo by dojít ke krátkému přerušení (několik sekund), protože brána Azure VPN Gateway přeruší stávající připojení a znovu spustí metodu handshake protokolu IKE pro opětovné vytvoření tunelu IPsec s využitím nových kryptografických algoritmů a parametrů. Pokud chcete přerušení minimalizovat, ujistěte se, že vaše místní zařízení VPN je také nakonfigurováno s odpovídajícími algoritmy a silami klíče.

### <a name="can-i-use-different-policies-on-different-connections"></a>Můžou se pro různá připojení použít různé zásady?
Ano. Vlastní zásady se aplikují na jednotlivá připojení. Pro různá připojení můžete vytvořit a použít různé zásady IPsec/IKE. Můžete také použít vlastní zásady pro podmnožinu připojení. Zbývající připojení budou používat výchozí sady zásad IPsec/IKE Azure.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Dají se vlastní zásady použít také pro připojení typu VNet-to-VNet?
Ano, vlastní zásady můžete použít pro připojení IPsec mezi různými místy i pro připojení typu VNet-to-VNet.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>Je nutné zadat stejné zásady pro oba prostředky připojení typu VNet-to-VNet?
Ano. Tunel typu VNet-to-VNet se skládá ze dvou prostředků připojení v Azure (jeden pro každý směr). Zajistěte, aby oba prostředky připojení měly stejné zásady, jinak se připojení typu VNet-to-VNet nevytvoří.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>Fungují zásady IPsec/IKE na připojení ExpressRoute?
Ne. Zásady IPsec/IKE fungují pouze na připojeních VPN typu Site-to-Site a VNet-to-VNet prostřednictvím bran Azure VPN Gateway.
