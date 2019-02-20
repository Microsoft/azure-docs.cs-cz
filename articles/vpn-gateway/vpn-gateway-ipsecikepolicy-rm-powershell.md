---
title: 'Konfigurace zásad IPsec/IKE pro připojení S2S VPN nebo VNet-to-VNet: Azure Resource Manageru: Prostředí PowerShell | Dokumentace Microsoftu'
description: Konfigurace zásad IPsec/IKE pro připojení typu S2S nebo VNet-to-VNet pomocí Azure VPN Gateway pomocí Azure Resource Manageru a Powershellu.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 72c597a6258fbe43e718714ab346d3e10cb97463
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417257"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurace zásad IPsec/IKE pro připojení S2S VPN nebo VNet-to-VNet

Tento článek vás provede kroky konfigurace zásad IPsec/IKE pro připojení Site-to-Site VPN nebo VNet-to-VNet pomocí modelu nasazení Resource Manageru a Powershellu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>O parametrech zásad protokolu IPsec a IKE pro brány Azure VPN Gateway
Protokol IPsec a IKE standard podporuje širokou škálu kryptografických algoritmů v různých kombinacích. Odkazovat na [o kryptografických požadavcích a branách Azure VPN Gateway](vpn-gateway-about-compliance-crypto.md) zobrazíte, jak to může pomoct zajistit mezi různými místy a VNet-to-VNet připojení splňují vaše požadavky na dodržování předpisů a zabezpečení.

Tento článek obsahuje pokyny k vytvoření a konfigurace zásad IPsec/IKE a použít pro nové nebo existující připojení:

* [Část 1 – pracovní postup pro vytvoření a nastavení zásad IPsec/IKE](#workflow)
* [Část 2 – podporované kryptografické algoritmy a síly klíče](#params)
* [Část 3 – vytvoření nové připojení S2S VPN s zásady IPsec/IKE](#crossprem)
* [Část 4 – vytvoření nového připojení VNet-to-VNet pomocí zásady IPsec/IKE](#vnet2vnet)
* [5. díl – Správa (vytvoření, přidat nebo odebrat) zásady IPsec/IKE pro připojení](#managepolicy)

> [!IMPORTANT]
> 1. Všimněte si, že zásady IPsec/IKE funguje pouze na následující SKU brány:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (route-based)
>    * ***Standardní*** a ***HighPerformance*** (trasové)
> 2. Pro jedno připojení můžete zadat pouze ***jednu*** kombinaci zásad.
> 3. Musíte zadat všechny algoritmy a parametry protokolu IKE (hlavní režim) a IPsec (rychlý režim). Zadání částečných zásad není povoleno.
> 4. Poraďte se s vaší sítě VPN zařízení dodavatele specifikace zajistit, že zásady platí pro vaše místní zařízení VPN. S2S nebo VNet-to-VNet připojení nejde vytvořit, pokud zásady nejsou kompatibilní.

## <a name ="workflow"></a>Část 1 – pracovní postup pro vytvoření a nastavení zásad IPsec/IKE
Tato část popisuje postup vytvoření a aktualizace zásad IPsec/IKE na připojení S2S VPN nebo VNet-to-VNet:
1. Vytvoření virtuální sítě a brány VPN
2. Vytvoření brány místní sítě pro různé místní připojení nebo jinou virtuální sítí a bránu pro připojení VNet-to-VNet
3. Vytvoření zásady IPsec/IKE s vybranou algoritmů a parametrů
4. Vytvořte připojení (IPsec nebo VNet2VNet) zásady IPsec/IKE
5. Přidání/aktualizaci/odebrání zásad protokolu IPsec/IKE pro připojení k existující

Pokyny v tomto článku umožňuje nastavení a konfigurace zásad IPsec/IKE, jak je znázorněno na obrázku:

![zásady protokolu ike IPSec](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Část 2 – podporované kryptografické algoritmy a síly klíče

Následující tabulka uvádí podporované kryptografické algoritmy a síly klíče, konfigurovat zákazníky:

| **IPsec/IKEv2**  | **Možnosti**    |
| ---  | --- 
| Šifrování protokolem IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integrita protokolu IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Skupina DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Šifrování protokolem IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Žádné    |
| Integrita protokolu IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Skupina PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Žádná 
| Doba života přidružení zabezpečení v rychlém režimu   | (**Volitelné**: výchozí hodnoty jsou použity, pokud není zadána)<br>Sekundy (integer; **min. 300** / výchozí hodnota 27 000 sekund)<br>Kilobajty (integer; **min. 1024** / výchozí hodnota 102 400 000 kilobajtů)   |
| Selektor provozu | UsePolicyBasedTrafficSelectors ** ($True nebo $False; **Volitelné**, výchozí $False, pokud není zadána)    |
|  |  |

> [!IMPORTANT]
> 1. **Konfigurace místní zařízení VPN musí odpovídat nebo obsahovat následující algoritmy a parametry, které zadáte v zásadách Azure IPsec/IKE:**
>    * Algoritmus šifrování protokolem IKE (hlavní režim / fáze 1)
>    * Algoritmus integrity protokolu IKE (hlavní režim / fáze 1)
>    * Skupina Diffie-Hellman (hlavní režim / fáze 1)
>    * Algoritmus šifrování protokolem IPsec (rychlý režim / fáze 2)
>    * Algoritmus integrity protokolu IPsec (rychlý režim / fáze 2)
>    * Skupina PFS (rychlý režim / fáze 2)
>    * Selektor provozu (Pokud se používá UsePolicyBasedTrafficSelectors)
>    * Doby životnosti přidružení zabezpečení jsou pouze místní specifikace, nemusí se shodovat.
>
> 2. **Pokud GCMAES se používá také u algoritmus šifrování protokolem IPsec, musíte vybrat stejný algoritmus GCMAES a délku klíče pro Integrity protokolu IPsec; například pro obě pomocí GCMAES128**
> 3. V předchozí tabulce:
>    * Odpovídá IKEv2 v hlavním režimu nebo fáze 1
>    * Protokol IPsec odpovídá rychlého režimu nebo fázi 2.
>    * Skupina Diffie-Hellman Určuje skupiny Diffie-Hellmen v hlavním režimu nebo fáze 1
>    * Skupina PFS zadaná skupina Diffie-Hellmen použitá v rychlém režimu nebo fázi 2.
> 4. V branách Azure VPN Gateway je doba života přidružení zabezpečení protokolu IKEv2 v hlavním režimu pevně nastavena na 28 800 sekund.
> 5. Nastavení "UsePolicyBasedTrafficSelectors" na $True připojení nakonfiguruje bránu Azure VPN pro připojení k založené na zásadách VPN brány firewall v místním prostředí. Pokud povolíte PolicyBasedTrafficSelectors, je potřeba zajistit, že vaše zařízení VPN mělo definované odpovídající selektory provozu všechny kombinace místní sítě (brány místní sítě) a předpon virtuální sítě Azure předpony, místo any-to-any. Například pokud jsou předpony vaší místní sítě 10.1.0.0/16 a 10.2.0.0/16 a předpony vaší virtuální sítě jsou 192.168.0.0/16 a 172.16.0.0/16, je potřeba zadat následující selektory provozu:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Další informace týkající se selektory provozu na základě zásad najdete v tématu [připojení několika místních na základě zásad zařízení VPN](vpn-gateway-connect-multiple-policybased-rm-ps.md).

V následující tabulce jsou uvedeny odpovídající skupiny Diffie-Hellman nepodporuje vlastní zásady:

| **Skupina Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Délka klíče** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768bitová skupina MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024bitová skupina MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048bitová skupina MODP  |
| 19                        | ECP256                   | ECP256       | 256bitová skupina ECP    |
| 20                        | ECP384                   | ECP284       | 384bitová skupina ECP    |
| 24                        | DHGroup24                | PFS24        | 2048bitová skupina MODP  |

Další podrobnosti najdete v článcích týkajících se [RFC3526](https://tools.ietf.org/html/rfc3526) a [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name ="crossprem"></a>Část 3 – vytvoření nové připojení S2S VPN s zásady IPsec/IKE

Tato část vás provede kroky k vytvoření připojení S2S VPN pomocí zásad IPsec/IKE. Následujícím postupem se vytvoří připojení, jak je znázorněno na obrázku:

![s2s-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Zobrazit [vytvořit připojení S2S VPN](vpn-gateway-create-site-to-site-rm-powershell.md) podrobnější podrobné pokyny pro vytvoření připojení S2S VPN.

### <a name="before"></a>Než začnete

* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nainstalujte nejnovější verzi rutin Powershellu pro Azure Resource Manager. Zobrazit [Přehled prostředí Azure PowerShell](/powershell/azure/overview) pro další informace o instalaci rutin prostředí PowerShell.

### <a name="createvnet1"></a>Krok 1 – vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě

#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných

Pro toto cvičení začneme zahájíme deklarací proměnných. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojení k vašemu předplatnému a vytvořte novou skupinu prostředků

Ujistěte se, že jste přešli do režimu prostředí PowerShell, aby bylo možné používat rutiny Resource Manageru. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../powershell-azure-resource-manager.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě

Následující příklad vytvoří virtuální síť, se třemi podsítěmi virtuální sítě TestVNet1 a bránu VPN. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>Krok 2: vytvoření připojení S2S VPN pomocí zásad IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Vytvoření zásady IPsec/IKE

Následující ukázkový skript vytvoří zásady IPsec/IKE s následující algoritmy a parametry:

* IKEv2: AES256, SHA384, DHGroup24
* Protokol IPsec: AES256, SHA256, PFS None, SA Lifetime 14400 seconds & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Pokud používáte GCMAES pro protokol IPsec, musíte použít stejný algoritmus GCMAES a délku klíče pro šifrování protokolem IPsec i integritu. Například výše, bude se odpovídající parametry "-IpsecEncryption GCMAES256 - IpsecIntegrity GCMAES256" při použití GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Vytvoření připojení S2S VPN pomocí zásady IPsec/IKE

Vytvoření připojení S2S VPN a použití zásady IPsec/IKE vytvořili dříve.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Volitelně můžete přidat "-UsePolicyBasedTrafficSelectors $True" do rutiny vytvořit připojení k povolení brány Azure VPN pro připojení k zařízení VPN na základě zásad v místním prostředí, jak je popsáno výše.

> [!IMPORTANT]
> Jakmile zásady IPsec/IKE je zadáno na připojení, brány Azure VPN pouze odeslat nebo přijmout návrh protokolu IPsec/IKE s zadané kryptografické algoritmy a silami klíče v tomto konkrétním připojení. Ujistěte se, že vaše místní zařízení VPN pro připojení používá nebo přijímá kombinaci přesné zásady, jinak nebude vytvořit tunel S2S VPN.


## <a name ="vnet2vnet"></a>Část 4 – vytvoření nového připojení VNet-to-VNet pomocí zásady IPsec/IKE

Kroky k vytvoření připojení typu VNet-to-VNet pomocí zásad IPsec/IKE jsou podobná připojení S2S VPN. Následující ukázkové skripty vytvořte připojení, jak je znázorněno na obrázku:

![v2v-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Zobrazit [vytvoření připojení typu VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) podrobný postup vytvoření připojení typu VNet-to-VNet. Je třeba provést [část 3](#crossprem) vytvoření a konfigurace virtuální sítě TestVNet1 a bránu VPN.

### <a name="createvnet2"></a>Krok 1: vytvoření druhé virtuální sítě a brány VPN

#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných

Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Vytvoření druhé virtuální sítě a brány VPN v nové skupině prostředků

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Krok 2: vytvoření připojení typu VNet-toVNet s zásady IPsec/IKE

Připojení S2S VPN, podobně jako vytvoření zásady IPsec/IKE, pak platí zásady, abyste mohli nové připojení.

#### <a name="1-create-an-ipsecike-policy"></a>1. Vytvoření zásady IPsec/IKE

Následující ukázkový skript vytvoří jinou zásadu IPsec/IKE s následujícími algoritmy a parametry:
* IKEv2: AES128, SHA1, DHGroup14
* Protokol IPsec: GCMAES128, GCMAES128, PFS14, SA Lifetime 14400 seconds & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Vytvoření připojení VNet-to-VNet pomocí zásady IPsec/IKE

Vytvoření připojení typu VNet-to-VNet a použití zásady IPsec/IKE, kterou jste vytvořili. V tomto příkladu jsou obě brány ve stejném předplatném. Proto je možné vytvořit a nakonfigurovat obě připojení se stejnými zásadami protokolu IPsec/IKE ve stejné relaci prostředí PowerShell.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Jakmile zásady IPsec/IKE je zadáno na připojení, brány Azure VPN pouze odeslat nebo přijmout návrh protokolu IPsec/IKE s zadané kryptografické algoritmy a silami klíče v tomto konkrétním připojení. Zajistěte, aby že zásady protokolu IPsec pro obě připojení jsou stejné, jinak připojení VNet-to-VNet nevytvoří.

Po dokončení těchto kroků, připojení se naváže za pár minut a bude mít následující topologie sítě, jak je znázorněno na začátku:

![zásady protokolu ike IPSec](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>5. díl – zásady aktualizací IPsec/IKE pro připojení

Poslední části se dozvíte, jak spravovat zásady IPsec/IKE pro existující připojení typu S2S nebo VNet-to-VNet. Cvičení níže vás provede následující operace v připojení:

1. Zobrazit zásady IPsec/IKE připojení
2. Přidáte nebo aktualizujete zásady IPsec/IKE pro připojení
3. Odebrání zásad IPsec/IKE připojení

Stejný postup platí pro připojení typu S2S a připojení typu VNet-to-VNet.

> [!IMPORTANT]
> Zásady IPsec/IKE je podporována v *standardní* a *HighPerformance* trasovými bránami VPN jenom. Nefunguje v základní SKU brány nebo brány sítě VPN založené na zásadách.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Zobrazit zásady IPsec/IKE připojení

Následující příklad ukazuje, jak získat zásady IPsec/IKE na připojení nakonfigurované. Skripty také pokračovat z výše uvedeného cvičení.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Poslední příkaz vypíše aktuální zásady IPsec/IKE nakonfigurované na připojení, pokud existuje. Tady je ukázkový výstup pro připojení:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Pokud neexistuje žádné zásady protokolu IPsec/IKE nakonfigurovaná, příkazu (PS > $connection6.policy) získá návratový prázdná. Neznamená to však není nakonfigurováno protokolu IPsec/IKE pro připojení, ale, že neexistuje žádná vlastní zásady IPsec/IKE. Aktuální připojení využívá výchozí zásady mezi vaše místní zařízení VPN a bránu Azure VPN.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Přidat nebo aktualizovat zásady IPsec/IKE pro připojení

Postup přidání nové zásady nebo aktualizovat existující zásady na připojení je stejný: Vytvořte novou zásadu a použít nové zásady připojení.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Pokud chcete povolit "UsePolicyBasedTrafficSelectors" při připojování zařízení sítě VPN založené na zásadách místní, přidejte "-UsePolicyBaseTrafficSelectors" do rutiny, parametr nebo ji nastavte na $False zakázat možnost:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Můžete získat připojení znovu ke kontrole, pokud se zásada se aktualizuje.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Jak je znázorněno v následujícím příkladu by měl zobrazit výstup z poslední řádek:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Odebrání zásad protokolu IPsec/IKE připojení

Jakmile z připojení odeberete vlastní zásady, brána Azure VPN se vrátí zpátky na [výchozímu seznamu návrhů IPsec/IKE](vpn-gateway-about-vpn-devices.md) a vyjednávání znovu s vaším místním zařízením VPN.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Stejný skript můžete použít ke kontrole, pokud zásady byl odebrán z připojení.

## <a name="next-steps"></a>Další postup

Zobrazit [připojení několika místních na základě zásad zařízení VPN](vpn-gateway-connect-multiple-policybased-rm-ps.md) pro další podrobnosti týkající se selektory provozu na základě zásad.

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
