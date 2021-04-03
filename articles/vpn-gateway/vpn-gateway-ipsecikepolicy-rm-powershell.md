---
title: Zásady IPsec/IKE pro S2S VPN & připojení VNet-to-VNet
titleSuffix: Azure VPN Gateway
description: Nakonfigurujte zásady IPsec/IKE pro připojení S2S nebo VNet-to-VNet se službami Azure VPN Gateway pomocí Azure Resource Manager a PowerShellu.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 96931d2dd94a8a31021ebe62caaefc54f643b007
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94649258"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurace zásad IPsec/IKE pro připojení S2S VPN nebo připojení typu VNet-to-VNet

Tento článek vás provede jednotlivými kroky konfigurace zásad IPsec/IKE pro připojení VPN typu Site-to-site nebo VNet-to-VNet pomocí modelu nasazení Správce prostředků a PowerShellu.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Parametry zásad IPsec a IKE pro Azure VPN Gateway
Protokol IPsec a IKE standard podporuje široké spektrum kryptografických algoritmů v různých kombinacích. Podívejte se na [informace o kryptografických požadavcích a branách Azure VPN Gateway](vpn-gateway-about-compliance-crypto.md) , abyste viděli, jak to může přispět k tomu, že připojení mezi různými místy a VNET-to-VNet splňuje požadavky na dodržování předpisů a zabezpečení.

Tento článek poskytuje pokyny k vytvoření a konfiguraci zásad IPsec/IKE a použití pro nové nebo existující připojení:

* [Část 1 – pracovní postup vytvoření a nastavení zásad IPsec/IKE](#workflow)
* [Část 2 – podporované kryptografické algoritmy a síly klíčů](#params)
* [Část 3 – vytvoření nového připojení S2S VPN pomocí zásad IPsec/IKE](#crossprem)
* [Část 4 – Vytvoření nového připojení typu VNet-to-VNet pomocí zásad IPsec/IKE](#vnet2vnet)
* [Část 5 – Správa (vytváření, přidávání, odebírání) zásad IPsec/IKE pro připojení](#managepolicy)

> [!IMPORTANT]
> 1. Zásady IPsec/IKE fungují jenom na následujících SKU brány:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (směrování založené na trasách)
>    * ***Standard** _ a _ *_HighPerformance_** (směrování založené na trasách)
> 2. Pro jedno připojení můžete zadat pouze ***jednu*** kombinaci zásad.
> 3. Je nutné zadat všechny algoritmy a parametry pro protokol IKE (hlavní režim) i pro protokol IPsec (rychlý režim). Zadání částečných zásad není povoleno.
> 4. Pokud chcete zajistit, aby se zásady na místních zařízeních VPN podporovaly, kontaktujte specifikace dodavatele zařízení VPN. Připojení S2S nebo VNet-to-VNet nelze nastavit, pokud jsou zásady nekompatibilní.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Část 1 – pracovní postup vytvoření a nastavení zásad IPsec/IKE
Tato část popisuje pracovní postup vytvoření a aktualizace zásad IPsec/IKE na připojení S2S VPN nebo VNet-to-VNet:
1. Vytvoření virtuální sítě a brány VPN
2. Vytvořte bránu místní sítě pro připojení mezi různými místy nebo jinou virtuální síť a bránu pro připojení typu VNet-to-VNet.
3. Vytvoření zásady IPsec/IKE s vybranými algoritmy a parametry
4. Vytvoření připojení (IPsec nebo VNet2VNet) pomocí zásad IPsec/IKE
5. Přidat nebo aktualizovat nebo odebrat zásady IPsec/IKE pro existující připojení

Pokyny v tomto článku vám pomůžou nastavit a nakonfigurovat zásady IPsec/IKE, jak je znázorněno v diagramu:

![IPSec – IKE – zásada](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Část 2 – podporované kryptografické algoritmy & síly klíčů

Následující tabulka uvádí podporované kryptografické algoritmy a síly klíče, které můžou zákazníci konfigurovat:

| **IPsec/IKEv2**  | **Možnosti**    |
| ---  | --- 
| Šifrování protokolem IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integrita protokolu IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Skupina DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Šifrování protokolem IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Žádné    |
| Integrita protokolu IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Skupina PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Žádná 
| Doba života přidružení zabezpečení v rychlém režimu   | (**Volitelné**: použijí se výchozí hodnoty, pokud není zadaný)<br>Sekundy (integer; **min. 300** / výchozí hodnota 27 000 sekund)<br>Kilobajty (integer; **min. 1024** / výchozí hodnota 102 400 000 kilobajtů)   |
| Selektor provozu | UsePolicyBasedTrafficSelectors * * ($True/$False; **Volitelné**, výchozí $false, pokud není zadané)    |
|  |  |

> [!IMPORTANT]
> 1. **Konfigurace vašeho místního zařízení VPN musí odpovídat zásadám brány Azure VPN Gateway nebo musí obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec/IKE Azure:**
>    * Šifrovací algoritmus IKE (hlavní režim/fáze 1)
>    * Algoritmus integrity IKE (hlavní režim/fáze 1)
>    * Skupina DH (hlavní režim/fáze 1)
>    * Šifrovací algoritmus IPsec (rychlý režim/fáze 2)
>    * Algoritmus integrity protokolu IPsec (rychlý režim/fáze 2)
>    * Skupina PFS (rychlý režim/fáze 2)
>    * Výběr provozu (Pokud se používá UsePolicyBasedTrafficSelectors)
>    * Doby životnosti přidružení zabezpečení jsou pouze místní specifikace, nemusí se shodovat.
>
> 2. **Pokud se pro šifrovací algoritmus IPsec používá GCMAES, musíte vybrat stejný algoritmus GCMAES a délku klíče pro integritu protokolu IPsec. například použití GCMAES128 pro obojí**
> 3. V tabulce výše:
>    * IKEv2 odpovídá hlavnímu režimu nebo fázi 1.
>    * Protokol IPsec odpovídá rychlému režimu nebo fázi 2.
>    * Skupina DH určuje Diffie-Hellmen skupinu použitou v hlavním režimu nebo ve fázi 1.
>    * Skupina PFS zadala skupinu Diffie-Hellmen použitou v rychlém režimu nebo ve fázi 2.
> 4. V branách Azure VPN Gateway je doba života přidružení zabezpečení protokolu IKEv2 v hlavním režimu pevně nastavena na 28 800 sekund.
> 5. Nastavení "UsePolicyBasedTrafficSelectors" na $True v připojení nakonfiguruje bránu Azure VPN Gateway, aby se připojovala k místní bráně firewall sítě VPN na základě zásad. Pokud povolíte PolicyBasedTrafficSelectors, musíte zajistit, aby vaše zařízení VPN odpovídalo selektorům přenosu, které jsou definované se všemi kombinacemi předpon vaší místní sítě (místní síťová brána), a to místo any-to-Any. Například pokud jsou předpony vaší místní sítě 10.1.0.0/16 a 10.2.0.0/16 a předpony vaší virtuální sítě jsou 192.168.0.0/16 a 172.16.0.0/16, je potřeba zadat následující selektory provozu:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Další informace o selektorech provozu na základě zásad najdete v tématu [připojení několika místních zařízení VPN založených na zásadách](vpn-gateway-connect-multiple-policybased-rm-ps.md).

Následující tabulka obsahuje seznam odpovídajících skupin Diffie-Hellman podporovaných vlastními zásadami:

| **Skupina Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Délka klíče** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768bitová skupina MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024bitová skupina MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048bitová skupina MODP  |
| 19                        | ECP256                   | ECP256       | 256bitová skupina ECP    |
| 20                        | ECP384                   | ECP384       | 384bitová skupina ECP    |
| 24                        | DHGroup24                | PFS24        | 2048bitová skupina MODP  |

Další podrobnosti najdete v článcích týkajících se [RFC3526](https://tools.ietf.org/html/rfc3526) a [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Část 3 – vytvoření nového připojení S2S VPN pomocí zásad IPsec/IKE

V této části se seznámíte s postupem vytvoření připojení S2S VPN se zásadami IPsec/IKE. V následujících krocích se vytvoří připojení, jak je znázorněno v diagramu:

![S2S – zásady](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Podrobnější pokyny k vytvoření připojení S2S VPN najdete v tématu [vytvoření připojení S2S VPN](vpn-gateway-create-site-to-site-rm-powershell.md) .

### <a name="before-you-begin"></a><a name="before"></a>Než začnete

* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nainstalujte rutiny Azure Resource Manager PowerShellu. Další informace o instalaci rutin PowerShellu najdete v tématu [přehled Azure PowerShell](/powershell/azure/) .

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Krok 1 – vytvoření virtuální sítě, brány sítě VPN a brány místní sítě

#### <a name="1-declare-your-variables"></a>1. deklarace proměnných

Pro toto cvičení začneme deklarací proměnných. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojte se k předplatnému a vytvořte novou skupinu prostředků.

Ujistěte se, že jste přešli do režimu prostředí PowerShell, aby bylo možné používat rutiny Resource Manageru. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../azure-resource-manager/management/manage-resources-powershell.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Vytvořte virtuální síť, bránu VPN a bránu místní sítě.

Následující ukázka vytvoří virtuální síť, virtuální sítě testvnet1 se třemi podsítěmi a bránu VPN. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

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

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Krok 2 – Vytvoření připojení S2S VPN pomocí zásad IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. vytvoření zásady IPsec/IKE

Následující vzorový skript vytvoří zásadu IPsec/IKE s následujícími algoritmy a parametry:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, životnost SA 14400 sekund & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Pokud pro protokol IPsec používáte GCMAES, musíte použít stejný algoritmus GCMAES a délku klíče pro šifrování a integritu protokolu IPsec. Ve výše uvedeném příkladu budou odpovídající parametry "-IpsecEncryption GCMAES256-IpsecIntegrity GCMAES256" při použití GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. vytvoření připojení S2S VPN pomocí zásad IPsec/IKE

Vytvořte připojení S2S VPN a použijte zásadu IPsec/IKE, kterou jste vytvořili dříve.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Volitelně můžete přidat "-UsePolicyBasedTrafficSelectors $True" do rutiny Create Connection, aby se mohla brána Azure VPN Gateway připojit k místním zařízením VPN založeným na zásadách, jak je popsáno výše.

> [!IMPORTANT]
> Po zadání zásad IPsec/IKE v připojení bude brána Azure VPN Gateway odesílat nebo přijímat jenom návrh IPsec/IKE se zadanými kryptografickými algoritmy a silnými klíči u tohoto konkrétního připojení. Ujistěte se, že vaše místní zařízení VPN pro připojení používá nebo přijímá přesnou kombinaci zásad, jinak tunel VPN S2S nebude vytvořen.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Část 4 – Vytvoření nového připojení typu VNet-to-VNet pomocí zásad IPsec/IKE

Postup vytvoření připojení typu VNet-to-VNet se zásadami IPsec/IKE je podobný jako u připojení S2S VPN. V následujících ukázkových skriptech se vytvoří připojení, jak je znázorněno v diagramu:

![V2V – zásada](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Podrobnější kroky pro vytvoření připojení typu VNet-to-VNet najdete v tématu [vytvoření připojení typu VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) . Aby bylo možné vytvořit a nakonfigurovat virtuální sítě testvnet1 a VPN Gateway, je nutné dokončit [část 3](#crossprem) .

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Krok 1 – vytvoření druhé virtuální sítě a brány VPN

#### <a name="1-declare-your-variables"></a>1. deklarace proměnných

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

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. vytvořte druhou virtuální síť a bránu VPN v nové skupině prostředků.

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

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Krok 2 – Vytvoření připojení VNet-toVNet pomocí zásad IPsec/IKE

Podobně jako u připojení VPN S2S Vytvořte zásady IPsec/IKE a pak použijte zásady na nové připojení.

#### <a name="1-create-an-ipsecike-policy"></a>1. vytvoření zásady IPsec/IKE

Následující ukázkový skript vytvoří jinou zásadu IPsec/IKE s následujícími algoritmy a parametry:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, životnost SA 14400 sekund & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. vytvoření připojení VNet-to-VNet pomocí zásad IPsec/IKE

Vytvořte připojení typu VNet-to-VNet a použijte zásadu IPsec/IKE, kterou jste vytvořili. V tomto příkladu jsou obě brány ve stejném předplatném. Proto je možné vytvořit a nakonfigurovat obě připojení se stejnou zásadou IPsec/IKE ve stejné relaci PowerShellu.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Po zadání zásad IPsec/IKE v připojení bude brána Azure VPN Gateway odesílat nebo přijímat jenom návrh IPsec/IKE se zadanými kryptografickými algoritmy a silnými klíči u tohoto konkrétního připojení. Ujistěte se, že zásady protokolu IPsec pro obě připojení jsou stejné, jinak se připojení VNet-to-VNet nevytvoří.

Po dokončení těchto kroků se připojení naváže během několika minut a bude mít následující topologii sítě, jak je znázorněno na začátku:

![IPSec – IKE – zásada](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Část 5 – aktualizace zásad IPsec/IKE pro připojení

V poslední části se dozvíte, jak spravovat zásady IPsec/IKE pro existující připojení S2S nebo VNet-to-VNet. Níže uvedené cvičení vás provede následujícími operacemi s připojením:

1. Zobrazit zásady IPsec/IKE připojení
2. Přidání nebo aktualizace zásad IPsec/IKE pro připojení
3. Odebrání zásad IPsec/IKE z připojení

Stejný postup platí i pro připojení S2S i VNet-to-VNet.

> [!IMPORTANT]
> Zásady IPsec/IKE se podporují jenom pro brány VPN založené na *standardech* a *HighPerformance* trasách. Nefunguje na základní skladové jednotce brány ani v bráně VPN založené na zásadách.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. zobrazení zásad IPsec/IKE připojení

Následující příklad ukazuje, jak získat zásadu IPsec/IKE nakonfigurovanou pro připojení. Skripty také pokračují ve výše uvedených cvičeních.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Poslední příkaz vypíše aktuální zásadu IPsec/IKE nakonfigurovanou v připojení, pokud existuje. Následuje ukázkový výstup pro připojení:

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

Pokud nejsou nakonfigurované žádné zásady IPsec/IKE, příkaz (PS> $connection 6. IpsecPolicies) vrátí prázdný návrat. Neznamená to, že protokol IPsec/IKE není pro připojení nakonfigurovaný, ale neexistují žádné vlastní zásady IPsec/IKE. Skutečné připojení používá výchozí zásady sjednané mezi místním zařízením VPN a službou Azure VPN Gateway.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Přidání nebo aktualizace zásad IPsec/IKE pro připojení

Postup přidání nové zásady nebo aktualizace existující zásady u připojení je stejný: Vytvořte novou zásadu a pak použijte novou zásadu pro připojení.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Pokud chcete při připojování k místnímu zařízení VPN založeného na zásadách povolit "UsePolicyBasedTrafficSelectors", přidejte do rutiny parametr-UsePolicyBaseTrafficSelectors nebo ho nastavte na $False, aby se zakázala možnost:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Připojení můžete znovu získat a ověřit, jestli se zásada aktualizovala.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Měl by se zobrazit výstup z posledního řádku, jak je znázorněno v následujícím příkladu:

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

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. odebrání zásad IPsec/IKE z připojení

Když z připojení odeberete vlastní zásady, brána Azure VPN Gateway se vrátí k [výchozímu seznamu návrhů IPSec/IKE](vpn-gateway-about-vpn-devices.md) a znovu se dokončí s vaším místním zařízením VPN.

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

Další podrobnosti týkající se selektorů provozu na základě zásad najdete v tématu [připojení několika místních zařízení VPN založených na zásadách](vpn-gateway-connect-multiple-policybased-rm-ps.md) .

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md).