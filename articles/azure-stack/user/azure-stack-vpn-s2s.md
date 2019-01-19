---
title: Konfigurace připojení VPN typu site-to-site Azure Stack | Dokumentace Microsoftu
description: Další informace o zásady IPsec/IKE pro připojení site-to-site VPN nebo VNet-to-VNet ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.openlocfilehash: cfd46f8178f36213ecc16db0e092e81ac2d0eff1
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54414773"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-or-vnet-to-vnet-connections"></a>Konfigurace zásad IPsec/IKE pro připojení site-to-site VPN nebo VNet-to-VNet

Tento článek vás provede kroky ke konfiguraci zásad IPsec/IKE pro site-to-site (S2S) VPN připojení ve službě Azure Stack.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Parametry zásad protokolu IPsec a IKE pro brány VPN Gateway

Standardní protokol IPsec a IKE podporuje širokou škálu kryptografických algoritmů v různých kombinacích. Jaké parametry jsou podporovány ve službě Azure Stack najdete v tématu [parametry protokolu IPsec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), které vám můžou pomoct splnit vaše požadavky na dodržování předpisů a zabezpečení.

Tento článek obsahuje pokyny o tom, jak vytvořit a nakonfigurovat zásady IPsec/IKE a použít pro nové nebo existující připojení.

## <a name="considerations"></a>Požadavky

Při použití těchto zásad, mějte na paměti následující důležité skutečnosti:

- Zásady IPsec/IKE funguje pouze na *standardní* a *HighPerformance* (trasové) SKU brány.

- Můžete zadat jenom **jeden** kombinaci zásad pro dané připojení.

- Musíte zadat všechny algoritmy a parametry protokolu IKE (hlavní režim) a IPsec (rychlý režim). Zadání částečných zásad není povoleno.

- Poraďte se s vaší sítě VPN zařízení dodavatele specifikace zajistit, že zásady platí pro vaše místní zařízení VPN. Nelze navázat připojení Site-to-site, pokud jsou nekompatibilní zásady.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Část 1 – pracovní postup pro vytvoření a nastavení zásad IPsec/IKE

Tato část popisuje pracovní postup k vytvoření a aktualizace zásad IPsec/IKE na připojení VPN typu site-to-site vyžaduje:

1. Vytvoření virtuální sítě a brány VPN.

2. Vytvořte bránu místní sítě pro připojení mezi různými místy.

3. Vytvoření zásady IPsec/IKE s vybranou algoritmů a parametrů.

4. Vytvořte připojení IPSec zásady IPsec/IKE.

5. Přidání/aktualizaci/odebrání zásad protokolu IPsec/IKE pro připojení k existující.

Podle pokynů v tomto článku nápovědy nastavení a konfigurace zásad IPsec/IKE, jak je znázorněno na následujícím obrázku:

![Nastavení a konfigurace zásad IPsec/IKE](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Část 2 – podporované kryptografické algoritmy a síly klíče

Následující tabulka uvádí podporované kryptografické algoritmy a síly klíče konfigurovat zákazníci služby Azure Stack:

| IPsec/IKEv2                                          | Možnosti                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Šifrování protokolem IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Integrita protokolu IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Skupina DH                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None         |
| Šifrování protokolem IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Žádné |
| Integrita protokolu IPsec                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| Skupina PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Žádná                         |
| Doba života přidružení zabezpečení v rychlém režimu                                       | (Volitelné: výchozí hodnoty jsou použity, pokud není zadána)<br />                         Sekundy (integer; min. 300/výchozí hodnota 27 000 sekund)<br />                         Kilobajty (integer; min. 1024/výchozí 102400000 kB) |                                                                          |
| Selektor provozu                                     | Selektory provozu na základě zásad nejsou podporované ve službě Azure Stack.         |

- Konfigurace vašeho místního zařízení VPN musí odpovídat zásadám brány Azure VPN Gateway nebo musí obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec/IKE Azure:

  - Algoritmus šifrování protokolem IKE (hlavní režim / fáze 1)
  - Algoritmus integrity protokolu IKE (hlavní režim / fáze 1)
  - Skupina Diffie-Hellman (hlavní režim / fáze 1)
  - Algoritmus šifrování protokolem IPsec (rychlý režim / fáze 2)
  - Algoritmus integrity protokolu IPsec (rychlý režim / fáze 2)
  - Skupina PFS (rychlý režim / fáze 2)
  - Doby životnosti přidružení zabezpečení jsou pouze místní specifikace, nemusí se shodovat.

- Pokud GCMAES se používá také u algoritmus šifrování protokolem IPsec, musíte vybrat stejný algoritmus GCMAES a délku klíče pro integrity protokolu IPsec; například použití GCMAES128 pro obojí.

- V předchozí tabulce:

  - Odpovídá IKEv2 v hlavním režimu nebo fáze 1
  - Protokol IPsec odpovídá rychlého režimu nebo fázi 2.
  - Skupina Diffie-Hellman Určuje skupiny Diffie-Hellmen v hlavním režimu nebo fáze 1
  - Skupina PFS zadaná skupina Diffie-Hellmen použitá v rychlém režimu nebo fázi 2.

- Doba života přidružení zabezpečení hlavního režimu IKEv2 je pevně nastavena na 28 800 sekund u bran Azure Stack VPN.

V následující tabulce jsou uvedeny odpovídající skupiny Diffie-Hellman nepodporuje vlastní zásady:

| Skupina Diffie-Hellman | DHGroup   | PFSGroup      | Délka klíče    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768bitová skupina MODP  |
| 2                    | DHGroup2  | PFS2          | 1024bitová skupina MODP |
| 14                   | DHGroup14 |               |               |
| DHGroup2048          | PFS2048   | 2048bitová skupina MODP |               |
| 19                   | ECP256    | ECP256        | 256bitová skupina ECP   |
| 20                   | ECP384    | ECP284        | 384bitová skupina ECP   |

Další informace najdete v tématu [RFC3526](https://tools.ietf.org/html/rfc3526) a [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Část 3 – vytvoření nové připojení VPN typu site-to-site pomocí zásady IPsec/IKE

Tato část vás provede kroky k vytvoření připojení site-to-site VPN pomocí zásad IPsec/IKE. Následujícím postupem se vytvoří připojení, jak je znázorněno na následujícím obrázku:

![lokality do lokality zásad](media/azure-stack-vpn-s2s/site-to-site.png)

Podrobnější podrobné pokyny pro vytvoření připojení VPN typu site-to-site najdete v článku [vytvořit připojení VPN typu site-to-site](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že splňujete následující požadavky:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat váš [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), nebo se zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

- Rutiny Powershellu pro Azure Resource Manager. Zobrazit [instalace Powershellu pro Azure Stack](../azure-stack-powershell-install.md) pro další informace o instalaci rutin prostředí PowerShell.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 – vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě

#### <a name="1-declare-variables"></a>1. Deklarujte proměnné

Pro toto cvičení začněte deklarováním následující proměnné. Ujistěte se, že zástupné názvy nahraďte vlastními hodnotami při konfiguraci pro produkční prostředí:

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojení k vašemu předplatnému a vytvořte novou skupinu prostředků

Ujistěte se, že jste přešli do režimu prostředí PowerShell, aby bylo možné používat rutiny Resource Manageru. Další informace najdete v tématu [připojit ke službě Azure Stack pomocí prostředí PowerShell jako uživatel](azure-stack-powershell-configure-user.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě

Následující příklad vytvoří virtuální síť, **virtuální sítě TestVNet1**, s třemi podsítě a bránu VPN. Při nahrazování hodnot je důležité vždy pojmenovat podsítě brány konkrétně **GatewaySubnet**. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Krok 2: vytvoření připojení site-to-site VPN pomocí zásad IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Vytvoření zásady IPsec/IKE

Tento ukázkový skript vytvoří zásady IPsec/IKE s následující algoritmy a parametry:

- IKEv2: AES128, SHA1, DHGroup14
- Protokol IPsec: AES256, SHA256, none, sekund 14400 doba života přidružení zabezpečení a 102 400 000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Pokud používáte GCMAES pro protokol IPsec, musíte použít stejný algoritmus GCMAES a délku klíče pro šifrování protokolem IPsec i integritu.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Vytvořit připojení VPN typu site-to-site pomocí zásad IPsec/IKE

Vytvoření připojení VPN typu site-to-site a použití zásady IPsec/IKE, kterou jste vytvořili dříve.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Jakmile zásady IPsec/IKE je zadáno na připojení, brány Azure VPN pouze odeslat nebo přijmout návrh protokolu IPsec/IKE s zadané kryptografické algoritmy a silami klíče v tomto konkrétním připojení. Ujistěte se, že vaše místní zařízení VPN pro připojení používá nebo přijímá přesné zásad kombinace, jinak nebude vytvořen tunel VPN typu site-to-site.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Část 4 – zásady aktualizací IPsec/IKE pro připojení

V předchozí části jsme si ukázali, jak spravovat zásady IPsec/IKE pro připojení site-to-site k existující. Následující části vás provede následující operace v připojení:

1. Zobrazit zásady IPsec/IKE připojení
2. Přidáte nebo aktualizujete zásady IPsec/IKE pro připojení
3. Odebrání zásad IPsec/IKE připojení

> [!NOTE]
> Zásady IPsec/IKE je podporována v *standardní* a *HighPerformance* trasovými bránami VPN jenom. Nelze použít u *základní* skladové položky brány.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Zobrazit zásady IPsec/IKE připojení

Následující příklad ukazuje, jak získat zásady IPsec/IKE na připojení nakonfigurované. Skripty také pokračovat v předchozím cvičení:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Poslední příkaz vypíše aktuální zásady IPsec/IKE nakonfigurovaná na připojení, pokud existuje. V následujícím příkladu je ukázkový výstup pro připojení:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Pokud neexistuje žádné zásady protokolu IPsec/IKE nakonfigurovaná, příkaz `$connection6.policy` získá návratový prázdná. Neznamená to, že není nakonfigurované protokolu IPsec/IKE na připojení. znamená to, že není žádná vlastní zásady IPsec/IKE. Aktuální připojení využívá výchozí zásady mezi vaše místní zařízení VPN a bránu Azure VPN.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Přidat nebo aktualizovat zásady IPsec/IKE pro připojení

Postup přidání nové zásady nebo aktualizovat existující zásady na připojení je stejný: Vytvořte novou zásadu a pak použijí nové zásady připojení.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Můžete získat připojení znovu ke kontrole, pokud se zásada se aktualizuje:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Jak je znázorněno v následujícím příkladu by měl zobrazit výstup z poslední řádek:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Odebrání zásad protokolu IPsec/IKE připojení

Jakmile z připojení odeberete vlastní zásady, brána Azure VPN se vrátí do [výchozí protokolu IPsec/IKE návrh](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)a vyjednávání s vaším místním zařízením VPN.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Stejný skript můžete použít ke kontrole, pokud zásady byl odebrán z připojení.

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení služby VPN gateway pro Azure Stack](azure-stack-vpn-gateway-settings.md)