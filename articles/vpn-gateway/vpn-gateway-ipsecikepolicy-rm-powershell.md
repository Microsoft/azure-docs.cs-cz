---
title: Zásady Protokolu IPsec/IKE pro & připojení virtuální sítě K virtuální síti S2S
titleSuffix: Azure VPN Gateway
description: Konfigurace zásad IPsec/IKE pro připojení S2S nebo Virtuální sítě k virtuální síti pomocí bran Azure VPN pomocí Azure Resource Manager a PowerShellu.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: eaca48fc354f1cf37635e9729b04eaaaa882ba1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161898"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurace zásad IPsec/IKE pro připojení S2S VPN nebo připojení typu VNet-to-VNet

Tento článek vás provede kroky konfigurace zásad IPsec/IKE pro připojení VPN site-to-site nebo připojení virtuální sítě k virtuální síti pomocí modelu nasazení Resource Manager a prostředí PowerShell.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Parametry zásad Protokolu IPsec a IKE pro brány Azure VPN
Standard protokolu IPsec a IKE podporuje širokou škálu kryptografických algoritmů v různých kombinacích. Informace [o kryptografických požadavcích a branách Azure VPN](vpn-gateway-about-compliance-crypto.md) najdete v tématu, jak to může pomoci zajistit, aby připojení mezi virtuálními sítěmi a virtuální sítí splňovalo vaše požadavky na dodržování předpisů nebo zabezpečení.

Tento článek obsahuje pokyny k vytvoření a konfiguraci zásad protokolu IPsec/IKE a použití nového nebo existujícího připojení:

* [Část 1 – Pracovní postup pro vytvoření a nastavení zásad IPsec/IKE](#workflow)
* [Část 2 - Podporované kryptografické algoritmy a silné stránky klíčů](#params)
* [Část 3 - Vytvoření nového připojení S2S VPN se zásadami IPsec/IKE](#crossprem)
* [Část 4 – Vytvoření nového připojení virtuální sítě k virtuální síti se zásadami IPsec/IKE](#vnet2vnet)
* [Část 5 - Správa (vytvoření, přidání, odebrání) zásady IPsec/IKE pro připojení](#managepolicy)

> [!IMPORTANT]
> 1. Všimněte si, že zásady IPsec/IKE fungují pouze na následujících virtuálních počítačích brány:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (na trase)
>    * ***Standardní*** a ***vysoce výkonná*** (založená na trase)
> 2. Pro jedno připojení můžete zadat pouze ***jednu*** kombinaci zásad.
> 3. Je nutné zadat všechny algoritmy a parametry pro ike (hlavní režim) a IPsec (rychlý režim). Zadání částečných zásad není povoleno.
> 4. Poraďte se se specifikacemi dodavatele zařízení VPN, abyste zajistili, že zásady jsou podporovány na místních zařízeních VPN. Připojení S2S nebo Virtuální síť k virtuální síti nelze zjistit, pokud jsou zásady nekompatibilní.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Část 1 – Pracovní postup pro vytvoření a nastavení zásad IPsec/IKE
Tato část popisuje pracovní postup pro vytvoření a aktualizaci zásad IPsec/IKE v připojení S2S VPN nebo virtuální sítě k virtuální síti:
1. Vytvoření virtuální sítě a brány VPN
2. Vytvoření brány místní sítě pro připojení mezi místními prostory nebo jiné virtuální sítě a brány pro připojení k virtuální síti
3. Vytvoření zásady IPsec/IKE s vybranými algoritmy a parametry
4. Vytvoření připojení (IPsec nebo VNet2VNet) se zásadami IPsec/IKE
5. Přidání nebo aktualizace/odebrání zásad y IPsec/IKE pro existující připojení

Pokyny v tomto článku vám pomohou nastavit a nakonfigurovat zásady protokolu IPsec/IKE, jak je znázorněno na obrázku:

![ipsec-ike-politika](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Část 2 - Podporované kryptografické algoritmy & silné stránky klíče

V následující tabulce jsou uvedeny podporované kryptografické algoritmy a silné stránky klíčů konfigurovatelné zákazníky:

| **IPsec/IKEv2**  | **Možnosti**    |
| ---  | --- 
| Šifrování protokolem IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integrita protokolu IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Skupina DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Žádný |
| Šifrování protokolem IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Žádné    |
| Integrita protokolu IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Skupina PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Žádná 
| Doba života přidružení zabezpečení v rychlém režimu   | **(Volitelné:** výchozí hodnoty se používají, pokud nejsou zadány)<br>Sekundy (integer; **min. 300** / výchozí hodnota 27 000 sekund)<br>Kilobajty (integer; **min. 1024** / výchozí hodnota 102 400 000 kilobajtů)   |
| Selektor provozu | UsePolicyBasedTrafficSelectors** ($True/$False; **Volitelné**, výchozí $False, pokud není zadáno)    |
|  |  |

> [!IMPORTANT]
> 1. **Konfigurace vašeho místního zařízení VPN musí odpovídat zásadám brány Azure VPN Gateway nebo musí obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec/IKE Azure:**
>    * Šifrovací algoritmus IKE (hlavní režim / fáze 1)
>    * Algoritmus integrity IKE (hlavní režim / fáze 1)
>    * Skupina DH (hlavní režim / fáze 1)
>    * Šifrovací algoritmus IPsec (rychlý režim / fáze 2)
>    * Algoritmus integrity IPsec (rychlý režim / fáze 2)
>    * Skupina PFS (rychlý režim / fáze 2)
>    * Volič provozu (pokud se používá useUsePolicyBasedTrafficSelectors)
>    * Doby životnosti přidružení zabezpečení jsou pouze místní specifikace, nemusí se shodovat.
>
> 2. **Pokud se používá gcmaes jako pro algoritmus šifrování IPsec, musíte vybrat stejný algoritmus GCMAES a délku klíče pro integritu IPsec; například použití GCMAES128 pro**
> 3. V tabulce výše:
>    * IKEv2 odpovídá hlavnímu režimu nebo fázi 1
>    * IPsec odpovídá rychlému režimu nebo fázi 2
>    * Skupina DH specifikuje skupinu Diffie-Hellmen používanou v hlavním režimu nebo ve fázi 1
>    * Skupina PFS specifikovala skupinu Diffie-Hellmen používanou v rychlém režimu nebo ve fázi 2
> 4. V branách Azure VPN Gateway je doba života přidružení zabezpečení protokolu IKEv2 v hlavním režimu pevně nastavena na 28 800 sekund.
> 5. Nastavení "UsePolicyBasedTrafficSelectors" pro $True připojení nakonfiguruje bránu Azure VPN pro připojení k místní bráně brány VPN založené na zásadách. Pokud povolíte PolicyBasedTrafficSelectors, musíte zajistit, že vaše zařízení VPN má odpovídající voliči provozu definované se všemi kombinacemi předpon místní sítě (místní síťová brána) do nebo z předpon virtuální sítě Azure, namísto libovolného. Například pokud jsou předpony vaší místní sítě 10.1.0.0/16 a 10.2.0.0/16 a předpony vaší virtuální sítě jsou 192.168.0.0/16 a 172.16.0.0/16, je potřeba zadat následující selektory provozu:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Další informace týkající se selektorů provozu na základě zásad naleznete v [tématu Připojení více místních zařízení VPN založených na zásadách](vpn-gateway-connect-multiple-policybased-rm-ps.md).

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

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Část 3 - Vytvoření nového připojení S2S VPN se zásadami IPsec/IKE

Tato část vás provede kroky vytvoření připojení S2S VPN se zásadami IPsec/IKE. Připojení, jak je znázorněno na obrázku, vytvoří následující kroky:

![s2s-politika](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Viz [Vytvoření připojení VPN S2S,](vpn-gateway-create-site-to-site-rm-powershell.md) kde najdete podrobnější podrobné pokyny pro vytvoření připojení VPN S2S.

### <a name="before-you-begin"></a><a name="before"></a>Než začnete

* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nainstalujte rutiny prostředí PowerShell Azure Resource Manager. Další informace o instalaci rutin PowerShellu najdete v [tématu Přehled Azure PowerShellu.](/powershell/azure/overview)

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Krok 1 – Vytvoření virtuální sítě, brány VPN a brány místní sítě

#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné

Pro toto cvičení začneme deklarováním našich proměnných. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojení k předplatnému a vytvoření nové skupiny prostředků

Ujistěte se, že jste přešli do režimu prostředí PowerShell, aby bylo možné používat rutiny Resource Manageru. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../powershell-azure-resource-manager.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Vytvoření virtuální sítě, brány VPN a brány místní sítě

Následující ukázka vytvoří virtuální síť TestVNet1 se třemi podsítěmi a bránu VPN. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

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

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Krok 2 – Vytvoření připojení VPN S2S pomocí zásad protokolu IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Vytvoření zásad y IPsec/IKE

Následující ukázkový skript vytvoří zásadu Protokolu IPsec/IKE s následujícími algoritmy a parametry:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS Žádný, SA Životnost 14400 sekund & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Pokud používáte GCMAES pro IPsec, musíte použít stejný algoritmus GCMAES a délku klíče pro šifrování i integritu IPsec. Například výše, odpovídající parametry budou "-IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256" při použití GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Vytvoření připojení S2S VPN se zásadami IPsec/IKE

Vytvořte připojení VPN S2S a použijte zásadu IPsec/IKE vytvořenou dříve.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Volitelně můžete přidat "-UsePolicyBasedTrafficSelectors $True" do rutiny vytvoření připojení, aby se brána Azure VPN mohla připojit k místním zařízením VPN založeným na zásadách, jak je popsáno výše.

> [!IMPORTANT]
> Jakmile je pro připojení zadána zásada IPsec/IKE, brána Azure VPN odešle nebo přijme pouze návrh IPsec/IKE se zadanými kryptografickými algoritmy a silnými stránkami klíčů v tomto konkrétním připojení. Ujistěte se, že vaše místní zařízení VPN pro připojení používá nebo přijímá přesnou kombinaci zásad, jinak tunelový propojení S2S VPN nevytvoří.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Část 4 – Vytvoření nového připojení virtuální sítě k virtuální síti se zásadami IPsec/IKE

Kroky vytvoření připojení virtuální sítě k virtuální síti se zásadami IPsec/IKE jsou podobné jako u připojení VPN S2S. Následující ukázkové skripty vytvoří připojení, jak je znázorněno v diagramu:

![v2v-politika](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Viz [Vytvoření připojení virtuální sítě k virtuální síti](vpn-gateway-vnet-vnet-rm-ps.md) pro podrobnější kroky pro vytvoření připojení virtuální sítě k virtuální síti. Chcete-li vytvořit a nakonfigurovat testVNet1 a bránu VPN, musíte dokončit [část 3.](#crossprem)

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Krok 1 – Vytvoření druhé virtuální sítě a brány VPN

#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné

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

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Krok 2 – vytvoření připojení virtuální sítě k vsíti s zásadou IPsec/IKE

Podobně jako připojení S2S VPN vytvořte zásadu Protokolu IPsec/IKE a pak se u nového připojení aplikujte na zásady.

#### <a name="1-create-an-ipsecike-policy"></a>1. Vytvoření zásad y IPsec/IKE

Následující ukázkový skript vytvoří jinou zásadu IPsec/IKE s následujícími algoritmy a parametry:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA Životnost 14400 sekund & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Vytvoření připojení virtuální sítě k virtuální síti pomocí zásad IPsec/IKE

Vytvořte připojení virtuální sítě k virtuální síti a použijte zásadu IPsec/IKE, kterou jste vytvořili. V tomto příkladu jsou obě brány ve stejném předplatném. Takže je možné vytvořit a nakonfigurovat obě připojení se stejnými zásadami IPsec/IKE ve stejné relaci prostředí PowerShell.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Jakmile je pro připojení zadána zásada IPsec/IKE, brána Azure VPN odešle nebo přijme pouze návrh IPsec/IKE se zadanými kryptografickými algoritmy a silnými stránkami klíčů v tomto konkrétním připojení. Ujistěte se, že zásady IPsec pro obě připojení jsou stejné, jinak připojení virtuální sítě k virtuální síti nenaváže.

Po dokončení těchto kroků je připojení navázáno během několika minut a budete mít následující topologii sítě, jak je znázorněno na začátku:

![ipsec-ike-politika](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Část 5 – Aktualizace zásad IPsec/IKE pro připojení

Poslední část ukazuje, jak spravovat zásady IPsec/IKE pro existující připojení S2S nebo Virtuální sítě k virtuální síti. Níže uvedené cvičení vás provede následujícími operacemi na připojení:

1. Zobrazit zásady připojení IPsec/IKE
2. Přidání nebo aktualizace zásad IPsec/IKE k připojení
3. Odebrání zásady IPsec/IKE z připojení

Stejné kroky platí pro připojení S2S i Virtuální síť k virtuální síti.

> [!IMPORTANT]
> Zásady Protokolu IPsec/IKE jsou podporovány pouze u bran VPN založených na *standardních* a *vysoce výkonných* trasách. Nefunguje na základní brány Skladové položky nebo brány VPN založené na zásadách.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Zobrazit zásady IPsec/IKE připojení

Následující příklad ukazuje, jak získat zásady IPsec/IKE nakonfigurované pro připojení. Skripty také pokračovat z výše uvedených cvičení.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Poslední příkaz obsahuje seznam aktuálních zásad IPsec/IKE nakonfigurovaných v připojení, pokud existuje. Následuje ukázkový výstup pro připojení:

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

Pokud není nakonfigurována žádná zásada IPsec/IKE, příkaz (PS> $connection6. IpsecPolicies) získá prázdný návrat. Neznamená to, že iPsec/IKE není nakonfigurován na připojení, ale že neexistuje žádná vlastní zásada IPsec/IKE. Skutečné připojení používá výchozí zásady vyjednané mezi místním zařízením VPN a bránou Azure VPN.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Přidání nebo aktualizace zásad IPsec/IKE pro připojení

Kroky pro přidání nové zásady nebo aktualizaci existující zásady na připojení jsou stejné: vytvořit novou zásadu a potom použít novou zásadu připojení.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Chcete-li povolit "UsePolicyBasedTrafficSelectors" při připojování k místnímu zařízení VPN založenému na zásadách, přidejte parametr "-UsePolicyBaseTrafficSelectors" do rutiny nebo nastavte tak, aby $False zakázat možnost:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Můžete získat připojení znovu zkontrolovat, zda je aktualizován zásady.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Měli byste vidět výstup z posledního řádku, jak je znázorněno v následujícím příkladu:

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

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Odebrání zásad y IPsec/IKE z připojení

Jakmile odeberete vlastní zásady z připojení, brána Azure VPN se vrátí zpět do [výchozího seznamu návrhů IPsec/IKE](vpn-gateway-about-vpn-devices.md) a znovu vyjedná v místním zařízení VPN.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Stejný skript můžete použít ke kontrole, zda byla zásada odebrána z připojení.

## <a name="next-steps"></a>Další kroky

Další informace o voličích provozu založených na zásadách najdete v článku [Připojení více místních zařízení VPN založených na zásadách.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
