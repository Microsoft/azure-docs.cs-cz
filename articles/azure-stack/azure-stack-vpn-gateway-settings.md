---
title: Nastavení brány sítě VPN pro Azure zásobníku | Microsoft Docs
description: Další informace o nastavení brány sítě VPN, které používáte pro Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2018
ms.author: brenduns
ms.openlocfilehash: d23f5b91e08c169975ac5d0bb8d9f048828c2910
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Konfigurace nastavení brány sítě VPN pro Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Brána sítě VPN je typ brány virtuální sítě, který odesílá šifrovaný provoz mezi virtuální sítí v zásobníku Azure a bránu vzdálené sítě VPN. Brána vzdáleného přístupu sítě VPN může být v Azure, zařízení ve vašem datovém centru nebo zařízení v jiné lokalitě.  Pokud je síťové připojení mezi dva koncové body, lze navázat zabezpečené připojení Site-to-Site (S2S) VPN mezi dvěma sítěmi.

Připojení k bráně VPN závisí na konfiguraci více prostředků, z nichž každý obsahuje konfigurovat nastavení. Části v tomto článku popisují prostředky a nastavení, které se týkají brány VPN pro virtuální síti vytvořené v modelu nasazení Resource Manager. Můžete najít popisy a diagramy topologie pro každé připojení řešení v [o brány sítě VPN pro Azure zásobníku](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Nastavení brány sítě VPN

### <a name="gateway-types"></a>Typy bran
Každý virtuální síť Azure zásobníku podporuje bránu jedné virtuální sítě, který musí být typu **Vpn**.  Tato podpora se liší od Azure, což podporuje další typy.  

Při vytváření brány virtuální sítě, musí se ujistěte, že je typ brány odpovídá vaší konfiguraci. Brána sítě VPN vyžaduje `-GatewayType Vpn`.

Příklad:
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Skladové jednotky (SKU) brány
Při vytváření brány virtuální sítě musíte určit SKU brány, které chcete použít. Vyberte SKU, které splňují vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA.

Zásobník Azure nabízí následující SKU brány sítě VPN:

|   | Propustnost brány sítě VPN |Brána sítě VPN maximální počet tunelových propojení IPsec |
|-------|-------|-------|
|**Základní SKU**  | 100 Mb/s  | 10    |
|**Standardní SKU**           | 100 Mb/s  | 10    |
|**SKU pro vysoký výkon** | 200 Mb/s    | 5 |

### <a name="resizing-gateway-skus"></a>Změna velikosti SKU brány
Azure zásobník nepodporuje velikosti SKU mezi podporované starší verze SKU.

Podobně zásobník Azure nepodporuje změny velikosti z podporovaných starší verze SKU (Basic, Standard a HighPerformance), na novější SKU nepodporuje v Azure (VpnGw1, VpnGw2 a VpnGw3).

### <a name="configure-the-gateway-sku"></a>Konfigurace skladová položka brány
#### <a name="azure-stack-portal"></a>Portál Azure zásobníku
Pokud používáte portál Azure zásobníku vytvořit bránu virtuální sítě Resource Manager, můžete vybrat SKU brány pomocí rozevíracího seznamu. Možnosti, které se zobrazí s odpovídají typ brány a typ sítě VPN, který jste vybrali.

#### <a name="powershell"></a>PowerShell
Následující příklad PowerShell určuje - GatewaySku jako VpnGw1.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>Typy připojení
V modelu nasazení Resource Manager Každá konfigurace vyžaduje typ připojení brány konkrétní virtuální sítě. Dostupné hodnoty prostředí PowerShell pro správce prostředků pro - ConnectionType jsou:

- Protokol IPsec

V následujícím příkladu prostředí PowerShell se vytvoří připojení S2S, která vyžaduje typ připojení IPsec.  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>Typy sítě VPN
Když vytvoříte bránu virtuální sítě pro konfiguraci brány VPN, musíte zadat typ sítě VPN. Typ sítě VPN, který zvolíte, závisí na topologii připojení, který chcete vytvořit.  Typ sítě VPN můžete také závisí na hardwaru, který používáte. Konfigurace S2S vyžadují zařízení VPN. Některá zařízení VPN podporují pouze určitý typ sítě VPN.

> [!IMPORTANT]  
> V tomto okamžiku zásobník Azure podporuje pouze typ sítě VPN na základě trasy. Pokud vaše zařízení podporuje pouze sítě VPN na základě zásad, nejsou podporovány připojení na těchto zařízeních z Azure zásobníku.  Kromě toho Azure zásobník nepodporuje použití zásad na základě provoz selektory pro brány na základě trasy v tuto chvíli jako vlastní konfigurace zásad protokolu IPSec/IKE nejsou ještě podporováno.

- **PolicyBased**: *(podporuje se v Azure, ale ne podle Azure zásobníku)* sítě VPN založené na zásadách šifrují pakety a směrují je do tunelových propojení IPsec na základě zásad IPsec nakonfigurovaných pomocí kombinace předpon adres mezi místní sítí a Azure VNet zásobníku. Zásada (nebo selektor provozu) se většinou určuje v konfiguraci zařízení VPN jako přístupový seznam.

- **RouteBased**: sítě VPN RouteBased používají IP předávání nebo směrovací tabulce ke směrování paketů do svých příslušných rozhraní tunelových propojení "trasy". Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásada (nebo selektor provozu) pro sítě VPN RouteBased jsou nakonfigurované jako any-to-any (nebo zástupné znaky) pomocí výchozí a nelze je změnit. Hodnota pro typ sítě VPN RouteBased je RouteBased.

Následující příklad PowerShell určuje parametr - VpnType jako RouteBased. Při vytváření brány se musíte ujistit, že parametr -VpnType odpovídá vaší konfiguraci.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>Požadavky na bránu
Následující tabulka uvádí požadavky na brány sítě VPN.

| |Brána sítě VPN PolicyBased Basic | Brána sítě VPN RouteBased Basic | Brána sítě VPN RouteBased Standard | RouteBased vysoce výkonná brána sítě VPN|
|--|--|--|--|--|
| **Připojení Site-to-Site (S2S připojení)** | Nepodporuje se | Konfigurace sítě VPN RouteBased | Konfigurace sítě VPN RouteBased | Konfigurace sítě VPN RouteBased |
| **Metoda ověřování**  | Nepodporuje se | Předsdílený klíč pro připojení S2S  | Předsdílený klíč pro připojení S2S  | Předsdílený klíč pro připojení S2S  |   
| **Maximální počet připojení S2S**  | Nepodporuje se | 10 | 10| 5|
|**Podpora aktivního směrování (BGP)** | Nepodporuje se | Nepodporuje se | Podporováno | Podporováno |

### <a name="gateway-subnet"></a>Podsíť brány
Před vytvořením brány VPN, musíte vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které používají bránu virtuální sítě virtuálních počítačů a služeb. Když vytvoříte bránu virtuální sítě, virtuální počítače brány jsou nasazené na podsíť brány a nakonfigurovaný pomocí požadovaného nastavení brány sítě VPN. Nenasazujte jakékoli jiné (například dalších virtuálních počítačů) k podsíti brány. Podsíť brány musí mít název "GatewaySubnet" správně fungovat. Pojmenování podsíť brány "GatewaySubnet" umožňuje zásobník Azure k identifikaci podsítě pro nasazení brány virtuální sítě virtuálních počítačů a služeb.

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. IP adresy v podsíti brány mají při přidělování brány virtuální počítače a služby brány. Některé konfigurace vyžadují víc IP adres než jiné. Podívejte se na pokyny pro konfiguraci, kterou chcete vytvořit a ověřit, jestli podsíť brány, kterou chcete vytvořit splňuje tyto požadavky. Kromě toho můžete ujistěte, že podsítě brány obsahuje dost IP adres, aby dokázala pojmout možné budoucí další konfigurace. Můžete si sice vytvořit podsíť brány jako malé/29, doporučujeme vytvořit podsíť brány/28 nebo větší (/ 28, / 27, /26 atd.). Tímto způsobem, pokud přidáte funkci v budoucnu, není nutné přerušit bránu, pak odstraňte a vytvořte znovu podsíť brány, aby bylo možné víc IP adres.

Následující příklad správce prostředků PowerShell ukazuje podsíť brány s názvem GatewaySubnet. Uvidíte, že zápis CIDR Určuje velikost/27, která umožňuje dost IP adres pro většinu konfigurace, které aktuálně neexistuje.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Pokud byste k této podsíti přidružili skupinu zabezpečení sítě, brána sítě VPN by mohla přestat fungovat podle očekávání. Další informace o skupinách zabezpečení sítě najdete v tématu [co je skupina zabezpečení sítě?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Brány místní sítě
Při vytváření konfiguraci brány VPN v Azure, bránu místní sítě často představuje vaše místní umístění. V zásobníku Azure reprezentuje vzdálené zařízení VPN, která se nachází mimo Azure zásobníku.  To může být zařízení VPN ve vašem datovém centru, vzdáleného datového centra nebo brána sítě VPN v Azure.

Pojmenujte bránu místní sítě, veřejnou IP adresu zařízení VPN a zadáte předpony adres, které jsou na místní umístění. Azure zjistí předpony cílových adres pro síťový provoz, zajímají konfiguraci, která jste zadali pro bránu místní sítě a směruje pakety odpovídajícím způsobem.

Následující příklad PowerShell vytvoří novou bránu místní sítě:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
V některých případech budete muset změnit nastavení brány místní sítě. Například když přidáváte nebo odebíráte rozsah adres nebo pokud se IP adresa zařízení VPN změní. V tématu [upravit nastavení brány místní sítě pomocí prostředí PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parametry protokolu IPsec/IKE
Při nastavování připojení k síti VPN v zásobníku Azure, musíte nakonfigurovat na obou koncích připojení.  Při konfiguraci připojení VPN mezi Azure zásobníku a hardwarové zařízení, jako je přepínač nebo směrovač, který funguje jako brána sítě VPN, že zařízení může můžete požádat o další nastavení.

Na rozdíl od Azure, která podporuje více nabízí jako iniciátor i respondér, zásobník Azure podporuje jenom jednu nabídku.

###  <a name="ike-phase-1-main-mode-parameters"></a>Parametry protokolu IKE fáze 1 (hlavní režim)
| Vlastnost              | Hodnota|
|-|-|
| Verze IKE           | IKEv2 |
|Skupina Diffie-Hellman   | Skupina 2 (1 024 bitů) |
| Metoda ověřování | Předsdílený klíč |
|Algoritmy šifrování a hash | AES256, SHA256 |
|Životnost SA (čas)     | 28 800 sekund|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametry protokolu IKE fáze 2 (rychlý režim)
| Vlastnost| Hodnota|
|-|-|
|Verze IKE |IKEv2 |
|Šifrování a použití algoritmu hash algoritmy (šifrování)     | GCMAES256|
|Šifrování a použití algoritmu hash algoritmy (ověřování) | GCMAES256|
|Životnost SA (čas)  | 27 000 sekund |
|Životnost SA (bajty) | 819,200       |
|Metoda Perfect Forward Secrecy (PFS) |PFS2048 |
|Detekce mrtvých partnerských zařízení | Podporováno|  
