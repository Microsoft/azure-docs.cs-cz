---
title: Azure VPN Gateway v Azure Austrálie
description: Implementace VPN Gateway v Austrálii v Azure, která bude vyhovovat předpisům ISM a efektivně chránit australské státní úřady
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571845"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure VPN Gateway v Azure Austrálie

Kritická služba s jakýmkoli veřejným cloudem představuje zabezpečené připojení cloudových prostředků a služeb ke stávajícím místním systémům.  Služba, která poskytuje tuto možnost v Azure, je Azure VPN Gateway (VPN Gateway). Tento článek popisuje klíčové důležité informace týkající se konfigurace VPN Gateway, aby odpovídaly ručním kontrolním mechanismům (ISM) v programu [Information](https://acsc.gov.au/infosec/ism/) Signal ředitelství pro australské signály.

K posílání šifrovaného provozu mezi virtuální sítí v Azure a jinou sítí se používá VPN Gateway.  Brány VPN používají tři scénáře:

- **Site-to-site** S2S
- **Point-to-site** P2S
- **VNet-to-VNet**

Tento článek se soustředí na brány VPN S2S. Diagram 1 ukazuje příklad konfigurace brány VPN typu Site-to-site.

![VPN Gateway s připojeními s více lokalitami](media/vpngateway-multisite-connection-diagram.png)

*Diagram 1 – Azure Site-to-Site VPN Gateway*

## <a name="key-design-considerations"></a>Klíčové faktory návrhu

Existují tři možnosti sítě, jak propojit Azure s australskými zákazníky z oblasti státní správy:

- **IKONA**
- **ExpressRoute**
- **Veřejný Internet**

[Zákaznická příručka pro uživatele](https://servicetrust.microsoft.com/viewpage/Australia) australského centra zabezpečení pro Azure doporučuje, aby se v kombinaci se třemi možnostmi sítě používala služba VPN Gateway (nebo ekvivalentní chráněná certifikovaná služba třetí strany), která zajistí, aby připojení splňovala požadavky na Ovládací prvky ISM pro šifrování a integritu.

### <a name="encryption-and-integrity"></a>Šifrování a integrita

Ve výchozím nastavení vyjednává síť VPN algoritmy šifrování a integrity a parametry během navazování připojení jako součást ověřování IKE.  V průběhu ověřování IKE metodou handshake bude konfigurace a pořadí předvolby záviset na tom, jestli je VPN Gateway iniciátorem nebo respondérem (NB: Tato akce je řízena přes zařízení VPN).  Konečná konfigurace připojení je řízena konfigurací zařízení VPN.  Podrobnosti o ověřených zařízeních VPN a jejich konfiguraci najdete tady: [Informace o zařízeních VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

Brány VPN můžou řídit šifrování a integritu konfigurací vlastních zásad IPsec/IKE na připojení.

### <a name="resource-operations"></a>Operace prostředků

Brány VPN vytvářejí propojení mezi prostředími Azure a mimo Azure přes veřejný Internet.  ISM má ovládací prvky, které se vztahují k explicitnímu autorizaci připojení.  Ve výchozím nastavení je možné používat brány VPN k vytváření neautorizovaných tunelů v zabezpečených prostředích.  Proto je důležité, aby organizace používaly Azure role založené Access Control (RBAC) k řízení, kdo může vytvářet a upravovat brány sítě VPN a jejich připojení.  Azure nemá žádnou integrovanou roli pro správu bran VPN, proto tato akce vyžaduje vlastní roli.

Přístup k rolím vlastník, přispěvatel a přispěvatel sítě je pevně kontrolovaný.  Pro přesnější řízení přístupu se taky doporučuje použít Azure AD Privileged Identity Management.

### <a name="high-availability"></a>Vysoká dostupnost

Brány VPN Azure můžou mít více připojení (viz diagram 1) a podporují několik místních zařízení VPN ke stejnému místnímu prostředí.  

Virtuální sítě v Azure můžou mít několik bran VPN Gateway, které se dají nasadit v nezávislých, aktivních, pasivních a aktivních konfiguracích.

Doporučuje se, aby všechny brány VPN byly nasazené v [konfiguraci s vysokou dostupností](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable): například dvě místní zařízení VPN připojená ke dvěma branám VPN v režimu aktivní – pasivní nebo aktivní – aktivní (viz diagram 2).

![VPN Gateway redundantní připojení](media/dual-redundancy.png)

*Diagram 2 – aktivní-aktivní brány VPN Gateway a dvě zařízení VPN*

### <a name="forced-tunneling"></a>Vynucené tunelování

Vynucené tunelování přesměrovává nebo vynutí všechny přenosy vázané na Internet zpátky do místního prostředí prostřednictvím VPN Gateway pro kontrolu a auditování. Bez vynuceného tunelování se internetový provoz z virtuálních počítačů v Azure přesměruje k síťové infrastruktuře Azure přímo na veřejný Internet bez možnosti kontroly a auditu provozu.  To je důležité v případě, že organizace vyžaduje, aby pro prostředí používala zabezpečenou internetovou bránu (SIG).

## <a name="detailed-configuration"></a>Podrobná konfigurace

### <a name="service-attributes"></a>Atributy služby

Brány VPN pro připojení S2S konfigurovaná pro australské vlády musí mít následující atributy:

|Atribut | VINEN|
|--- | --- |
|gatewayType | S2S|
|

Nastavení atributů vyžadované pro zajištění shody s ovládacími prvky ISM pro ochranu jsou:

|Atribut | VINEN|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Brány VPN Azure podporují rozsah kryptografických algoritmů z standardů protokolu IPsec a IKE.  Výchozí zásady maximalizují interoperabilitu se široké škálou zařízení VPN třetích stran.  V důsledku toho je možné vyjednat nekompatibilní konfigurace metodou handshake protokolu IKE.  Proto se důrazně doporučuje, aby se pro vpnClientConfiguration v bránách VPN používaly [vlastní parametry zásad IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) , aby se zajistilo, že připojení vyhovují ovládacím prvkům ISM pro místní připojení prostředí k Azure.  Klíčové atributy jsou:

|Atribut|NEMĚL|VINEN|
|---|---|---|
|saLifeTimeSeconds|< 14 400 sekund|> 300 sekund|
|saDataSizeKilobytes| |> 1024 KB|
|ipsecEncryption| |AES256 – GCMAES256|
|ipsecIntegrity| |SHA256 – GCMAES256|
|ikeEncryption| |AES256 – GCMAES256|
|ikeIntegrity| |SHA256 – GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

*Pro dhGroup a pfsGroup ve výše uvedené tabulce jsou upřednostňovány ECP256 a ECP384, i když je možné použít další nastavení.*

### <a name="related-services"></a>Související služby

Když navrhujete a konfigurujete VPN Gateway Azure, existuje několik souvisejících služeb, které musí také existovat a být nakonfigurované:

|Služba | Vyžaduje se akce|
|--- | ---|
|Virtuální sítě | Brány VPN jsou připojené k virtuální síti.  Před vytvořením nové VPN Gateway je třeba vytvořit virtuální síť.|
|Veřejná IP adresa | Brány VPN S2S potřebují veřejnou IP adresu pro navázání připojení mezi místním zařízením VPN a VPN Gateway.  Před vytvořením VPN Gateway S2S se musí vytvořit veřejná IP adresa.|
|Subnet | Pro VPN Gateway je třeba vytvořit podsíť virtuální sítě.|
|

## <a name="implementation-steps-using-powershell"></a>Postup implementace pomocí prostředí PowerShell

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)

1. Vytvořte vlastní roli (například Přispěvatel virtualNetworkGateway).  Vytvořte roli, která se přiřadí uživatelům, kteří budou moct vytvářet a upravovat brány VPN Gateway. Vlastní role by měla umožňovat tyto operace:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/připojení/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft. Network/routeTables/*  

2. Přidejte vlastní roli uživatelům, kteří můžou vytvářet a spravovat brány sítě VPN a připojení k místním prostředím.

### <a name="create-vpn-gateway"></a>Vytvořit VPN Gateway

*Tyto kroky předpokládají, že virtuální síť už je vytvořená.*

1. Vytvořit novou veřejnou IP adresu
2. Vytvoření podsítě VPN Gateway
3. Vytvořit konfiguraci VPN Gateway IP
4. Vytvořte si bránu VPN Gateway
5. Vytvoření brány místní sítě pro místní zařízení VPN
6. Vytvoření zásady protokolu IPsec (za předpokladu použití vlastních zásad IPsec/IKE)
7. Vytvoření připojení mezi VPN Gateway a bránou místní sítě pomocí zásad protokolu IPsec

### <a name="enforce-tunneling"></a>Vynucení tunelování

Pokud je vyžadováno vynucené tunelování, před vytvořením VPN Gateway:

1. Vytvořit směrovací tabulku a pravidla směrování
2. Přidružit směrovací tabulku k příslušným podsítím

Po vytvoření VPN Gateway:

1. Nastavte GatewayDefaultSite na místní prostředí na VPN Gateway

### <a name="example-powershell-script"></a>Ukázkový skript prostředí PowerShell

Ukázkový skript PowerShellu pro vytvoření vlastní zásady IPSEC/IKE, která je v souladu s ovládacími prvky ISM pro klasifikaci CHRÁNĚNÉho zabezpečení pro australské služby.

Předpokládá, že existuje virtuální síť, VPN Gateway a místní brány.

#### <a name="create-an-ipsecike-policy"></a>Vytvoření zásady IPsec/IKE

Následující vzorový skript vytvoří zásadu IPsec/IKE s následujícími algoritmy a parametry:

- IKEv2: AES256, SHA256, DHGroup ECP256
- Podporují AES256, SHA256, PFS ECP256, životnost SA 14 400 sekund & 102400000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>Vytvoření připojení S2S VPN pomocí vlastní zásady IPsec/IKE

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>Další postup

Tento článek pojednává o konkrétní konfiguraci VPN Gateway pro splnění požadavků uvedených v příručce Information Security Manual (ISM) pro zabezpečení australského data CHRÁNĚNých státní správou během přenosu. Podrobný postup konfigurace VPN Gateway:

- [Přehled služby Azure Virtual Network Gateway](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Co je VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Vytvoření virtuální sítě s připojením VPN typu Site-to-site pomocí PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Vytvoření a správa služby VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)