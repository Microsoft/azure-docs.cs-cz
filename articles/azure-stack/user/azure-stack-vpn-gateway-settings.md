---
title: Nastavení služby VPN gateway pro Azure Stack | Dokumentace Microsoftu
description: Přečtěte si o nastaveních pro brány VPN Gateway, které používáte pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: e7a22e6699a69bf89a9c3828433affa0a212116f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240773"
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Konfigurace nastavení služby VPN gateway pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Brána VPN je typem brány virtuální sítě, která odesílá šifrovaný síťový provoz mezi vaší virtuální sítí ve službě Azure Stack a vzdálenou bránu VPN. Vzdálené brány VPN může být v Azure, zařízení ve vašem datovém centru nebo zařízení v jiné lokalitě. Pokud je síťové připojení mezi dva koncové body, můžete navázat zabezpečené připojení VPN typu Site-to-Site (S2S) mezi těmito dvěma sítěmi.

Připojení brány VPN se spoléhá na konfiguraci více zdrojů, z nichž každý obsahuje konfigurovatelné nastavení. Tento článek popisuje prostředky a nastavení, které se týkají brány sítě VPN pro virtuální síť, kterou vytvoříte v modelu nasazení Resource Manager. Můžete najít popisy a diagramy topologie pro každé připojení řešení [informace o VPN Gateway pro Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Nastavení služby VPN gateway

### <a name="gateway-types"></a>Typy bran

Každá virtuální síť Azure Stack podporuje brány jedné virtuální sítě, který musí být typu **Vpn**.  Tato podpora se liší od Azure, což podporuje další typy.  

Při vytváření brány virtuální sítě, musí se ujistěte, že je typ brány odpovídá vaší konfiguraci. Vyžaduje bránu sítě VPN `-GatewayType Vpn`příznak; například:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Skladové jednotky (SKU) brány

Při vytváření brány virtuální sítě musíte určit SKU brány, kterou chcete použít. Vyberte SKU, které splňují vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA.

Azure Stack nabízí VPN gateway skladové položky uvedené v následující tabulce.

|   | Propustnost brány sítě VPN |Tunelových propojení IPsec maximální brány sítě VPN |
|-------|-------|-------|
|**Základní SKU**  | 100 Mb/s  | 20    |
|**Standardní SKU**           | 100 Mb/s  | 20    |
|**SKU pro vysoký výkon** | 200 Mb/s    | 10    |

### <a name="resizing-gateway-skus"></a>Změna velikosti SKU brány

Azure Stack nepodporuje změny velikosti mezi podporované starší skladové položky SKU.

Podobně Azure Stack nepodporuje změnu velikosti z podporovaných starší verze SKU (Basic, Standard a HighPerformance) na novější SKU nepodporuje v Azure (VpnGw1, VpnGw2 a VpnGw3.)

### <a name="configure-the-gateway-sku"></a>Konfigurace skladové položky brány

#### <a name="azure-stack-portal"></a>Portál Azure Stack

Pokud použijete k vytvoření brány virtuální sítě Resource Manageru na portálu Azure Stack, můžete pomocí rozevíracího seznamu vyberte SKU brány. Možnosti odpovídají typ brány a typ sítě VPN, kterou jste vybrali.

#### <a name="powershell"></a>PowerShell

Následující příklad Powershellu Určuje, **- GatewaySku** jako `VpnGw1`:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Typy připojení

V modelu nasazení Resource Manager Každá konfigurace vyžaduje typ připojení brány konkrétní virtuální sítě. Hodnoty dostupné prostředí PowerShell Resource Manageru pro **- ConnectionType** jsou:

* Protokol IPsec

   V následujícím příkladu Powershellu se vytvoří připojení S2S, který vyžaduje typ připojení IPsec:

   ```PowerShell
   New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

### <a name="vpn-types"></a>Typy sítě VPN

Při vytváření brány virtuální sítě pro konfiguraci brány VPN, musíte zadat typ sítě VPN. Typ sítě VPN, kterou zvolíte, závisí na topologie připojení, který chcete vytvořit. Typ sítě VPN může také záviset na hardwaru, který používáte. Konfigurace S2S vyžadují zařízení VPN. Některá zařízení VPN podporují pouze určitého typu sítě VPN.

> [!IMPORTANT]  
> Azure Stack v současné době podporuje pouze typ sítě VPN na základě trasy. Pokud vaše zařízení podporuje pouze sítě VPN na základě zásad, nejsou podporována připojení na těchto zařízeních ze služby Azure Stack.  
>
> Kromě toho Azure Stack nepodporuje použití selektory provozu na základě zásad pro brány podle postupu v tuto chvíli, protože vlastní konfigurace zásad IPSec/IKE nejsou podporovány.

* **PolicyBased**: Sítě VPN založené na zásadách šifrují pakety a směrují je do tunelových propojení IPsec na základě zásad IPsec nakonfigurovaných pomocí kombinace předpon adres mezi vaší místní sítí a virtuální sítě Azure Stack. Zásady nebo selektor provozu, je obvykle přístupový seznam v konfiguraci zařízení VPN.

  >[!NOTE]
  >**PolicyBased** se podporuje v Azure, ale ne ve službě Azure Stack.

* **RouteBased**: Připojení VPN typu RouteBased pomocí tras, které jsou nakonfigurované v předávání IP nebo směrovací tabulce ke směrování paketů do svých příslušných rozhraní tunelových propojení. Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásady nebo selektor provozu pro **RouteBased** sítí VPN jsou nakonfigurované jako any-to-any (nebo použít zástupné znaky.) Ve výchozím nastavení nedá se změnit. Hodnota **RouteBased** typ sítě VPN je **RouteBased**.

Následující příklad Powershellu Určuje, **- VpnType** jako **RouteBased**. Při vytváření brány je nutné, který **- VpnType** je správný pro vaši konfiguraci.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Požadavky na bránu

V následující tabulce jsou uvedeny požadavky pro brány VPN Gateway.

| |Brány sítě VPN PolicyBased Basic | Brána VPN typu RouteBased Basic | RouteBased Standard VPN Gateway | RouteBased vysoce výkonná brána sítě VPN|
|--|--|--|--|--|
| **Připojení Site-to-Site (S2S připojení)** | Nepodporuje se | Konfigurace VPN typu RouteBased | Konfigurace VPN typu RouteBased | Konfigurace VPN typu RouteBased |
| **Metoda ověřování**  | Nepodporuje se | Předsdílený klíč pro připojení S2S  | Předsdílený klíč pro připojení S2S  | Předsdílený klíč pro připojení S2S  |   
| **Maximální počet připojení S2S**  | Nepodporuje se | 20 | 20| 10|
|**Podpora aktivního směrování (BGP)** | Nepodporuje se | Nepodporuje se | Podporováno | Podporováno |

### <a name="gateway-subnet"></a>Podsíť brány

Než vytvoříte bránu VPN, musíte vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které používají bránu virtuální sítě virtuálních počítačů a služeb. Při vytváření brány virtuální sítě, virtuální počítače brány se nasazují do podsítě brány a nakonfigurovanou povinné nastavení služby VPN gateway. Nenasazujte cokoli, (například další virtuální počítače) do podsítě brány.

>[!IMPORTANT]
>Pro správné fungování podsítě brány je nutné, aby měla název **GatewaySubnet**. Azure Stack používá tento název k identifikaci podsíť, kterou chcete nasadit virtuální počítače brány virtuální sítě a služby.

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. Virtuální počítače brány a služby brány se přidělují IP adresy v podsíti brány. Některé konfigurace vyžadují víc IP adres než jiné. Podívejte se na pokyny pro konfiguraci, kterou chcete vytvořit a ověřit, že podsíť brány, kterou chcete vytvořit tyto požadavky splňuje.

Navíc je dobré mít že podsíť brány obsahuje dostatek IP adres pro zpracování dalších budoucích konfiguracích. I když můžete vytvořit podsíť brány malá jako minimální velikostí/29, doporučujeme že vytvořit podsíť brány o velikosti/28 nebo větší (/ 28, / 27, / 26 atd.) Tímto způsobem, pokud v budoucnu přidat funkce nemusíte dovolí bránu, pak odstraňte a znovu vytvořte podsíť brány umožňující další IP adresy.

Následující příklad Powershellu pro Resource Manager ukazuje podsíť brány s názvem **GatewaySubnet**. Uvidíte, že zápis CIDR Určuje velikost/27, která zajistíte dostatek IP adres u většiny konfigurací, které momentálně existují.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Přidružení skupiny zabezpečení sítě s touto podsítí může způsobit, že brána sítě VPN přestane fungovat podle očekávání. Další informace o skupinách zabezpečení sítě najdete v tématu [co je skupina zabezpečení sítě?](../../virtual-network/virtual-networks-nsg.md).

### <a name="local-network-gateways"></a>Brány místní sítě

Při vytváření konfiguraci brány VPN v Azure, místní síťové brány často představuje místní umístění. Ve službě Azure Stack představuje všechny vzdálené zařízení VPN, který je umístěný mimo Azure Stack. To může být zařízení VPN ve vašem datovém centru (i vzdálené datacentrum) nebo VPN Gateway v Azure.

Pojmenujte bránu místní sítě, veřejnou IP adresu zařízení VPN a zadáte předpony adres, které jsou na místní umístění. Azure zjistí pro síťový provoz předpony cílových adres consults konfiguraci, kterou jste zadali pro bránu místní sítě a směruje pakety odpovídajícím způsobem.

Následující příklad Powershellu vytvoří novou bránu místní sítě:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Někdy je potřeba upravit nastavení místní síťové brány; například když přidáváte nebo odebíráte rozsah adres, nebo pokud IP adresa zařízení VPN bude měnit. Zobrazit [úprava nastavení místní síťové brány pomocí Powershellu](../../vpn-gateway/vpn-gateway-modify-local-network-gateway.md).

## <a name="ipsecike-parameters"></a>Parametry protokolu IPsec/IKE

Při nastavování připojení VPN ve službě Azure Stack, musíte nakonfigurovat na obou koncích připojení. Při konfiguraci připojení VPN mezi Azure Stack a hardwarové zařízení, jako je přepínač nebo směrovač, který funguje jako brána VPN, zařízení může výzvu k zadání dalších nastavení.

Na rozdíl od Azure, která podporuje několik nabídek jako iniciátor i respondér, Azure Stack podporuje jenom jednu nabídku.

### <a name="ike-phase-1-main-mode-parameters"></a>Parametry protokolu IKE fáze 1 (hlavní režim)

| Vlastnost              | Value|
|-|-|
| Verze IKE           | IKEv2 |
|Skupina Diffie-Hellman   | Skupina 2 (1 024 bitů) |
| Metoda ověřování | Předsdílený klíč |
|Algoritmy šifrování a hash | AES256, SHA256 |
|Životnost SA (čas)     | 28 800 sekund|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametry protokolu IKE fáze 2 (rychlý režim)

| Vlastnost| Value|
|-|-|
|Verze IKE |IKEv2 |
|Šifrování a hash algoritmy (šifrování)     | GCMAES256|
|Šifrování a hash algoritmy (ověřování) | GCMAES256|
|Životnost SA (čas)  | 27 000 sekund  |
|Životnost SA (kB) | 33,553,408     |
|Metoda Perfect Forward Secrecy (PFS) |Žádný<sup>viz poznámka 1</sup> |
|Detekce mrtvých partnerských zařízení | Podporováno|  

* *Poznámka 1:*  Azure Stack starší než verze 1807 používá hodnotu PFS2048 pro ideální Forward Secrecy (PFS).

## <a name="next-steps"></a>Další postup

* [Připojení pomocí ExpressRoute](../azure-stack-connect-expressroute.md)
