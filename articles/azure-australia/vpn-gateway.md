---
title: Azure VPN Gateway v Azure Austrálie
description: Implementace VPN Gateway v Austrálii v Azure, která bude vyhovovat předpisům ISM a efektivně chránit australské státní úřady
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575433"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure VPN Gateway v Azure Austrálie

Kritická služba s jakýmkoli veřejným cloudem představuje zabezpečené připojení cloudových prostředků a služeb ke stávajícím místním systémům. Služba, která poskytuje tuto možnost v Azure, je VPN Gateway Azure. Tento článek popisuje klíčové body, které je potřeba vzít v úvahu při konfiguraci brány VPN tak, aby odpovídaly [ovládacím prvkům ISM (Information Security Manual](https://acsc.gov.au/infosec/ism/)) pro informační instituce (ISM).

Brána sítě VPN slouží k posílání šifrovaného provozu mezi virtuální sítí v Azure a další sítí. Brány VPN řeší tři scénáře:

- Site-to-Site (S2S)
- Point-to-Site (P2S)
- Síť k síti

Tento článek se zaměřuje na brány VPN S2S. Diagram 1 ukazuje příklad konfigurace S2S VPN Gateway.

![Brána VPN s připojeními s více lokalitami](media/vpngateway-multisite-connection-diagram.png)

*Diagram 1 – Azure S2S VPN Gateway*

## <a name="key-design-considerations"></a>Klíčové faktory návrhu

Existují tři možnosti sítě, jak propojit Azure s australskými zákazníky z oblasti státní správy:

- IKONA
- Azure ExpressRoute
- Veřejný Internet

[Zákaznická příručka k](https://servicetrust.microsoft.com/viewpage/Australia) centru zabezpečení centra zabezpečení pro Azure doporučuje, aby se v kombinaci se třemi možnostmi sítě používala VPN Gateway (nebo ekvivalentní chráněná certifikovaná služba třetí strany). Toto doporučení je potřeba zajistit, aby připojení splňovala řízení ISM pro šifrování a integritu.

### <a name="encryption-and-integrity"></a>Šifrování a integrita

Ve výchozím nastavení vyjednává síť VPN algoritmy šifrování a integrity a parametry během navazování připojení jako součást ověřování IKE. Během metody handshake protokolu IKE závisí konfigurace a pořadí priorit na tom, jestli je Brána VPN iniciátorem nebo respondérem. Toto označení se řídí přes zařízení VPN. Konečná konfigurace připojení je řízena konfigurací zařízení VPN. Další informace o ověřených zařízeních VPN a jejich konfiguraci najdete v tématu [informace o službách VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

Brány VPN můžou řídit šifrování a integritu konfigurací vlastních zásad IPsec/IKE na připojení.

### <a name="resource-operations"></a>Operace prostředků

Brány VPN vytvářejí propojení mezi prostředími Azure a mimo Azure přes veřejný Internet. ISM má ovládací prvky, které se vztahují k explicitnímu autorizaci připojení. Ve výchozím nastavení je možné používat brány VPN k vytváření neautorizovaných tunelů v zabezpečených prostředích. Je důležité, aby organizace používaly řízení přístupu na základě role (RBAC) v Azure k řízení, kdo může vytvářet a upravovat brány sítě VPN a jejich připojení. Azure nemá žádnou předdefinovanou roli pro správu bran VPN, takže se vyžaduje vlastní role.

Přístup k rolím vlastník, přispěvatel a přispěvatel sítě je pevně kontrolovaný. Doporučujeme vám také použít Azure Active Directory Privileged Identity Management pro přesnější řízení přístupu.

### <a name="high-availability"></a>Vysoká dostupnost

Brány VPN Azure můžou mít několik připojení a podporují několik místních zařízení VPN ke stejnému místnímu prostředí. Viz obrázek 1.

Virtuální sítě v Azure můžou mít několik bran VPN Gateway, které se dají nasadit v nezávislých, aktivních, pasivních a aktivních konfiguracích.

Doporučujeme, abyste nasadili všechny brány VPN v [konfiguraci s vysokou dostupností](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable). Příkladem jsou dvě místní zařízení VPN připojená ke dvěma branám VPN v režimu aktivní – pasivní nebo aktivní – aktivní. Viz diagram 2.

![Redundantní připojení brány VPN Gateway](media/dual-redundancy.png)

*Diagram 2 – aktivní-aktivní brány VPN Gateway a dvě zařízení VPN*

### <a name="forced-tunneling"></a>Vynucené tunelování

Vynucené tunelování přesměrovává neboli vynutí všechny přenosy vázané na Internet zpátky do místního prostředí prostřednictvím brány VPN pro kontrolu a auditování. Bez vynuceného tunelování se internetový provoz z virtuálních počítačů v Azure přesměruje k síťové infrastruktuře Azure přímo na veřejný Internet bez možnosti kontroly a auditu provozu. Vynucené tunelování je důležité v případě, že organizace vyžaduje, aby pro prostředí používala zabezpečenou internetovou bránu (SIG).

## <a name="detailed-configuration"></a>Podrobná konfigurace

### <a name="service-attributes"></a>Atributy služby

Brány VPN pro připojení S2S konfigurovaná pro australský státní správu musí mít následující atributy:

|Atribut | Vinen|
|--- | --- |
|gatewayType | S2S|
|

Nastavení atributů vyžadované pro zajištění shody s ovládacími prvky ISM pro ochranu jsou:

|Atribut | Vinen|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Brány VPN Azure podporují rozsah kryptografických algoritmů z standardů protokolu IPsec a IKE. Výchozí zásady nastaví maximální interoperabilitu se široké škálou zařízení VPN jiných výrobců. V důsledku toho je možné, že během konfigurace protokolu IKE handshake nedodržuje předpisy bude možné vyjednat. Důrazně doporučujeme, abyste v vpnClientConfiguration brány VPN použili [vlastní parametry zásad IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) , abyste zajistili, že připojení budou splňovat ovládací prvky ISM pro připojení k místnímu prostředí do Azure. Klíčové atributy jsou uvedeny v následující tabulce.

|Atribut|Neměl|Vinen|
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

Pro dhGroup a pfsGroup v předchozí tabulce jsou upřednostňovány ECP256 a ECP384, i když je možné použít jiná nastavení.

### <a name="related-services"></a>Související služby

Když navrhujete a konfigurujete bránu Azure VPN Gateway, musí existovat i řada souvisejících služeb, které by měly být nakonfigurované.

|Služba | Je potřeba akce|
|--- | ---|
|Virtuální síť | Brány VPN jsou připojené k virtuální síti. Vytvořte virtuální síť předtím, než vytvoříte novou bránu VPN.|
|Veřejná IP adresa | Brány VPN S2S potřebují k navázání připojení mezi místním zařízením VPN a bránou VPN veřejnou IP adresu. Před vytvořením brány VPN S2S Vytvořte veřejnou IP adresu.|
|Subnet | Vytvořte podsíť virtuální sítě pro bránu VPN.|
|

## <a name="implementation-steps-using-powershell"></a>Postup implementace pomocí prostředí PowerShell

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

1. Vytvořte vlastní roli. Příkladem je virtualNetworkGateway Přispěvatel. Vytvořte roli, která se přiřadí uživatelům, kteří budou moct vytvářet a upravovat brány VPN Gateway. Vlastní role by měla umožňovat tyto operace:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/připojení/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft. Network/routeTables/*  

2. Přidejte vlastní roli uživatelům, kteří můžou vytvářet a spravovat brány sítě VPN a připojení k místním prostředím.

### <a name="create-a-vpn-gateway"></a>Vytvoření brány VPN

Tento postup předpokládá, že jste již vytvořili virtuální síť.

1. Vytvořte novou veřejnou IP adresu.
2. Vytvořte podsíť brány VPN.
3. Vytvořte soubor konfigurace IP adresy brány VPN.
4. Vytvořte bránu VPN.
5. Vytvořte bránu místní sítě pro místní zařízení VPN.
6. Vytvořte zásadu IPsec. V tomto kroku se předpokládá, že používáte vlastní zásady IPsec/IKE.
7. Vytvořte připojení mezi bránou VPN a bránou místní sítě pomocí zásad protokolu IPsec.

### <a name="enforce-tunneling"></a>Vynucení tunelování

Pokud se vyžaduje vynucené tunelování, než vytvoříte bránu VPN:

1. Vytvořte směrovací tabulku a pravidla směrování.
2. Přidružte směrovací tabulku s příslušnými podsítěmi.

Po vytvoření brány sítě VPN:

- Nastavte GatewayDefaultSite na místní prostředí v bráně VPN.

### <a name="example-powershell-script"></a>Ukázkový skript prostředí PowerShell

Příklad skriptu prostředí PowerShell, který se používá k vytvoření vlastní zásady IPsec/IKE, splňuje ovládací prvky ISM pro australský stupeň zabezpečení.

Předpokládá, že virtuální síť, brána sítě VPN a místní brány existují.

#### <a name="create-an-ipsecike-policy"></a>Vytvoření zásady IPsec/IKE

Následující vzorový skript vytvoří zásadu IPsec/IKE s následujícími algoritmy a parametry:

- IKEv2: AES256, SHA256, DHGroup ECP256
- Podporují AES256, SHA256, PFS ECP256, životnost SA 14 400 sekund a 102 400 000 KB

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

## <a name="next-steps"></a>Další kroky

Tento článek pojednává o konkrétní konfiguraci VPN Gateway pro splnění požadavků uvedených v příručce Security Security Manual pro zabezpečení Australskéch dat CHRÁNĚNých státní správou během přenosu. Postup konfigurace brány VPN najdete v těchto tématech:

- [Přehled brány virtuální sítě Azure](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Co je VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Vytvoření virtuální sítě s připojením VPN typu Site-to-site pomocí PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Vytvoření a správa služby VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)