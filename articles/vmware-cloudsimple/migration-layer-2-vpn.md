---
title: Řešení Azure VMware podle CloudSimple – roztažení místní sítě vrstvy 2 do privátního cloudu
description: Popisuje, jak nastavit síť VPN vrstvy 2 mezi NSX-T v privátním cloudu CloudSimple a místním klientovi samostatného NSX Edge.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a530a6f656f37657a198af85d93d5404ac88d0e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83651022"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrace úloh pomocí roztažené sítě vrstvy 2

V této příručce se dozvíte, jak pomocí sítě VPN úrovně 2 roztáhnout síť 2 z místního prostředí do privátního cloudu CloudSimple. Toto řešení umožňuje migrovat úlohy běžící v místním prostředí VMware do privátního cloudu v Azure v rámci stejného adresního prostoru podsítě, aniž by bylo nutné znovu přinášet vaše úlohy.

Roztažení sítí vrstvy 2 na základě L2VPN může spolupracovat se sítěmi založenými na NSX nebo bez nich v místním prostředí VMware. Pokud nemáte sítě založené na NSX pro úlohy v místním prostředí, můžete použít samostatnou bránu služby NSX Edge.

> [!NOTE]
> Tato příručka se zabývá scénářem, ve kterém jsou připojení k síti VPN typu Site-to-site připojená k místním i datacentrům privátního cloudu.

## <a name="deployment-scenario"></a>Scénář nasazení

K roztažení místní sítě pomocí L2VPN musíte nakonfigurovat L2VPN Server (cílový směrovač NSX-T Tier0) a klienta L2VPN (zdrojový samostatný klient).  

V tomto scénáři nasazení je váš privátní cloud připojený k místnímu prostředí prostřednictvím tunelu VPN typu Site-to-site, který umožňuje místní správě a vMotion podsítě komunikovat s podsítěmi privátního cloudu a vMotion. Toto uspořádání je nezbytné pro vMotion pro různé platformy vCenter (xVC-vMotion). Tier0 směrovač NSX-T je nasazený jako server L2VPN v privátním cloudu.

Samostatná NSX Edge se v místním prostředí nasadí jako klient L2VPN a následně se spáruje se serverem L2VPN. Koncový bod tunelu GRE se vytvoří na každé straně a nakonfiguruje se tak, aby se do vašeho privátního cloudu natáhla místní síť vrstvy 2. Tato konfigurace je znázorněna na následujícím obrázku.

![Scénář nasazení](media/l2vpn-deployment-scenario.png)

Další informace o migraci pomocí sítě VPN L2 najdete v tématu [virtuální privátní sítě](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) v dokumentaci k VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Předpoklady pro nasazení řešení

Před nasazením a konfigurací řešení ověřte, zda je k dismístě:

* Místní verze vSphere je 6.7 U1 + nebo 6.5 P03 + +.
* Místní licence vSphere je na úrovni podniku plus (pro distribuovaný přepínač vSphere).
* Identifikujte síť 2 úlohy zatížení, která se má roztáhnout do vašeho privátního cloudu.
* Identifikujte síť ve vrstvě 2 v místním prostředí pro nasazení klientského zařízení L2VPN.
* [Privátní cloud je již vytvořen](create-private-cloud.md).
* Verze samostatného hraničního zařízení NSX-T je kompatibilní s verzí Správce NSX-t (NSX-T 2.3.0), která se používá ve vašem privátním cloudovém prostředí.
* Skupina portů se vytvořila v místním serveru vCenter s povolenými falešnými přenosy.
* Veřejná IP adresa byla rezervována pro použití pro samostatnou IP adresu odchozího připojení klienta NSX-T a 1:1 NAT pro překlad mezi těmito dvěma adresami.
* Předávání DNS se nastavuje na místních serverech DNS pro doménu az.cloudsimple.io tak, aby odkazovaly na servery DNS privátního cloudu.
* Latence RTT je menší nebo rovna 150 ms, jak je potřeba, aby vMotion fungovalo na těchto dvou lokalitách.

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

V následující tabulce jsou uvedeny podporované verze vSphere a typy síťových adaptérů.  

| verze vSphere | Zdrojový typ virtuálního přepínače | Ovladač virtuální síťové karty | Cílový typ virtuálního přepínače | Podporované? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Vše | DVS | Vše | DVS | Yes |
| vSphere 6.7 UI nebo vyšší, 6.5 P03 nebo vyšší | DVS | VMXNET3 | N-VDS | Yes |
| vSphere 6.7 UI nebo vyšší, 6.5 P03 nebo vyšší | DVS | E1000 | N-VDS | [Nepodporováno na VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7 UI nebo 6.5 P03, NSX-V nebo verze nižší než NSX-T 2.2, 6.5 P03 nebo vyšší | Vše | Vše | N-VDS | [Nepodporováno na VWware](https://kb.vmware.com/s/article/56991) |

Od verze VMware NSX-T 2,3:

* Logický přepínač na straně privátního cloudu, která je roztažená na místní přes L2VPN, se nedá směrovat současně. Roztažený logický přepínač nelze připojit k logickému směrovači.
* Sítě VPN v L2VPN a protokolu IPSEC založené na směrování se dají konfigurovat jenom pomocí volání rozhraní API.

Další informace najdete v dokumentaci k VMware v tématu [virtuální privátní sítě](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) .

### <a name="sample-l2-vpn-deployment-addressing"></a>Ukázka adresování pro nasazení sítě VPN L2

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Místní síť, ve které je nasazen samostatný ESG (L2 VPN)

| **Položka** | **Hodnota** |
|------------|-----------------|
| Název sítě | MGMT_NET_VLAN469 |
| REŽIM | 469 |
| IPv4/IPv6| 10.250.0.0/24 |
| IP adresa samostatného hraničního zařízení | 10.250.0.111 |
| IP adresa zařízení na samostatném hraničním zařízení | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Místní síť, která se má roztáhnout

| **Položka** | **Hodnota** |
|------------|-----------------|
| REŽIM | 472 |
| IPv4/IPv6| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Schéma IP adresy privátního cloudu pro směrovač NSX-T Tier0 (L2 VPN slouží)

| **Položka** | **Hodnota** |
|------------|-----------------|
| Rozhraní zpětné smyčky | 192.168.254.254/32 |
| Rozhraní tunelu | 5.5.5.1/29 |
| Logický přepínač (roztaženo) | Stretch_LS |
| Rozhraní zpětné smyčky (IP adresa NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Síť privátního cloudu, která má být namapována na roztaženou síť

| **Položka** | **Hodnota** |
|------------|-----------------|
| REŽIM | 712 |
| IPv4/IPv6| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Načtení ID logického směrovače potřebného pro L2VPN

Následující kroky ukazují, jak načíst ID logického směrovače Tier0 DR instance logického směrovače pro služby IPsec a L2VPN. ID logického směrovače je potřeba později při implementaci rozhraní L2VPN.

1. Přihlaste se ke Správci NSX-T `https://*nsx-t-manager-ip-address*` a vyberte **síťové**  >  **směrovače**  >  **poskytovatel –**  >  **Přehled**LR. V **režimu vysoké dostupnosti**vyberte **aktivní – pohotovostní**. Tato akce otevře automaticky otevírané okno, ve kterém se zobrazí hraniční virtuální počítač, na kterém je směrovač Tier0 aktuálně aktivní.

    ![Vybrat aktivní – pohotovostní](media/l2vpn-fetch01.png)

2. Vyberte **okraje Fabric**  >  **uzly**  >  **Edges**. Poznamenejte si IP adresu pro správu virtuálního počítače s aktivním okrajem (Edge VM1) identifikovanou v předchozím kroku.

    ![Poznámka k IP správě](media/l2vpn-fetch02.png)

3. Otevřete relaci SSH s IP adresou pro správu virtuálního počítače Edge. Spusťte ```get logical-router``` příkaz s uživatelským jménem **správce** a heslem **CloudSimple 123!**.

    ![získat výstup logického směrovače](media/l2vpn-fetch03.png)

4. Pokud nevidíte položku DR-Provider-LR, proveďte následující kroky.

5. Vytvoření dvou překrytých logických přepínačů. Jeden logický přepínač je roztažen na místní, kde jsou umístěny migrované úlohy. Jiný logický přepínač je fiktivní přepínač. Pokyny najdete v tématu [Vytvoření logického přepínače](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) v dokumentaci k VMware.

    ![Vytvořit logický přepínač](media/l2vpn-fetch04.png)

6. Připojte zástupný přepínač ke směrovači Tier1 s místní IP adresou propojení nebo jinou překrývající podsíť z místního počítače nebo vašeho privátního cloudu. Další informace najdete v dokumentaci k VMware v tématu [Přidání portu pro stahování na logický směrovač vrstvy 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) .

    ![Připojit fiktivní přepínač](media/l2vpn-fetch05.png)

7. Znovu spusťte `get logical-router` příkaz v relaci SSH virtuálního počítače Edge. Je zobrazen identifikátor UUID logického směrovače DR-Provider-LR. Poznamenejte si identifikátor UUID, který se vyžaduje při konfiguraci L2VPN.

    ![získat výstup logického směrovače](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Načtení IDENTIFIKÁTORu logického přepínače potřebného pro L2VPN

1. Přihlaste se k NSX Manageru ( `https://nsx-t-manager-ip-address` ).
2. Vyberte přepínač **sítě**  >  **přepínání**  >  **Switches**  >  **< přehled přepínače \> \logical**  >  **Overview**.
3. Poznamenejte si identifikátor UUID roztaženého logického přepínače, který se při konfiguraci L2VPN vyžaduje.

    ![získat výstup logického směrovače](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Otázky týkající se směrování a zabezpečení pro L2VPN

Aby bylo možné vytvořit síť VPN založenou na trasách protokolu IPsec mezi směrovačem NSX-T Tier0 a samostatným klientem NSX Edge, musí být rozhraní zpětné smyčky Tier0 směrovače NSX-T schopné komunikovat s veřejnou IP adresou NSX samostatného klienta v místním prostředí přes UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Povolení UDP 500/4500 pro protokol IPsec

1. [Vytvořte veřejnou IP adresu](public-ips.md) rozhraní zpětné smyčky NSX-T Tier0 na portálu CloudSimple.

2. [Vytvořte tabulku brány firewall](firewall.md) se stavovým pravidlem, která povolují příchozí přenosy UDP 500/4500 a připojte tabulku brány firewall k podsíti NSX-T HostTransport.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Inzerování IP adresy rozhraní zpětné smyčky do sítě Underlay

1. Vytvořte trasu s hodnotou null pro síť rozhraní zpětné smyčky. Přihlaste se ke Správci NSX-T a vyberte **sítě**  >  **Směrování**  >  **směrovače**směrování  >  **– LR**  >  **Routing**  >  **statické trasy**. Klikněte na **Přidat**. V poli **síť**zadejte IP adresu rozhraní zpětné smyčky. Pro **Další segmenty směrování**klikněte na **Přidat**, pro další segment směrování zadejte null a pro vzdálenost správce ponechte výchozí hodnotu 1.

    ![Přidat statickou trasu](media/l2vpn-routing-security01.png)

2. Vytvoří seznam předpon IP adres. Přihlaste se ke Správci NSX-T a vyberte **sítě**  >  poskytovatel směrovače**Směrování směrování**  >  **Routers**  >  **–**  >  **Routing**  >  **seznamy předpon IP adres**směrování LR. Klikněte na **Přidat**. Zadejte název pro identifikaci seznamu. V případě **předpon**klikněte dvakrát na tlačítko **Přidat** . Do prvního řádku zadejte pro **akci** **Network** a Deny hodnotu 0.0.0.0/0. Ve druhém řádku vyberte možnost **kterákoli** pro **síť** a **Povolit** **akci**.
3. Přiřaďte seznam předpon IP adres oběma sousedním uzlům protokolu BGP (mandát). Připojením seznamu předpony IP k sousednímu směrovači protokolu BGP znemožníte inzerování výchozí trasy v protokolu BGP k přepínačům MANDÁTu. Nicméně jakákoli jiná trasa, která obsahuje trasu s hodnotou null, bude inzerovat IP adresu rozhraní zpětné smyčky pro přepínače MANDÁTu.

    ![Vytvořit seznam předpon IP adres](media/l2vpn-routing-security02.png)

4. Přihlaste se ke Správci NSX-T a vyberte **síť**  >  **Směrování**  >  **směrovače**  >  **Provider-LR**  >  **Routing**  >  **protokolu BGP**LR směrování  >  **Neighbors**. Vyberte první sousední uzel. Klikněte na **Upravit**  >  **rodin adres**. V případě řady IPv4 upravte sloupec **Filtr pro výstup** a vyberte seznam předpon IP adres, který jste vytvořili. Klikněte na **Uložit**. Tento krok opakujte pro druhý sousední uzel.

    ![Připojit seznam předpon IP adres 1 ](media/l2vpn-routing-security03.png) ![ Připojit seznam předpon IP adres 2](media/l2vpn-routing-security04.png)

5. Znovu distribuujte statickou trasu null do protokolu BGP. Chcete-li inzerovat směrování rozhraní zpětné smyčky do Underlay, je nutné znovu distribuovat statickou trasu null do protokolu BGP. Přihlaste se ke Správci NSX-T a vyberte **sítě**  >  **Routing**  >  Poskytovatel**směrovače**směrování  >  **-LR**  >  **Směrování**  >  **Route Redistribution**  >  **sousedů**. Vyberte **Provider-LR-Route_Redistribution** a klikněte na **Upravit**. Zaškrtněte políčko **static** a klikněte na **Uložit**.

    ![Znovu distribuovat statickou trasu null do protokolu BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Konfigurace sítě VPN založené na směrování ve směrovači NSX-T Tier0

Pomocí následující šablony vyplňte všechny podrobnosti o konfiguraci sítě VPN založené na směrování ve směrovači NSX-T Tier0. V následných voláních POST jsou vyžadovány identifikátory UUID v každém volání POST. IP adresy pro rozhraní zpětné smyčky a tunelu pro L2VPN musí být jedinečné a nesmí se překrývat s místními nebo privátními cloudy.

IP adresy zvolené pro rozhraní zpětné smyčky a tunelu používané pro L2VPN musí být jedinečné a nesmí se překrývat s místními nebo privátními cloudy. Síť rozhraní zpětné smyčky musí být vždy/32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

U všech následujících volání rozhraní API nahraďte IP adresu svou IP adresou správce NSX-T. Všechna tato volání rozhraní API můžete spustit z klienta na straně klienta nebo pomocí `curl` příkazů.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Povolte službu VPN IPSec na logickém směrovači.

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Vytváření profilů: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Vytváření profilů: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Vytváření profilů: tunel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Vytvoření místního koncového bodu

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Vytvořit rovnocenný koncový bod

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Vytvoření relace sítě VPN založené na trasách

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Konfigurace L2VPN na směrovači NSX-T Tier0

Po každém volání POST zadejte následující informace. V následných voláních POST jsou požadována ID.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Vytvoření služby L2VPN

Výstup následujícího příkazu GET bude prázdný, protože konfigurace ještě není dokončená.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Pro následující příkaz POST je ID logického směrovače identifikátor UUID logického směrovače Tier0 DR, který jste získali dříve.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Vytvoření relace L2VPN

Pro následující příkaz POST je ID služby L2VPN ID, které jste právě získali, a ID relace VPN IPsec je ID získané v předchozí části.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Tato volání vytvoří koncový bod tunelu GRE. Chcete-li zjistit stav, spusťte následující příkaz.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Vytvořit logický port se zadaným ID tunelu

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Získání partnerského kódu pro L2VPN na straně NSX-T

Získejte partnerský kód koncového bodu NSX-T. Při konfiguraci vzdáleného koncového bodu je vyžadován partnerský kód. V předchozí části se dá získat> L2VPN <ID relace. Další informace najdete v příručce k [rozhraní API NSX-T 2,3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Nasazení samostatného klienta NSX-T (místně)

Před nasazením ověřte, že vaše místní pravidla brány firewall umožňují příchozí a odchozí přenosy UDP 500/4500 z/na veřejnou IP adresu CloudSimple, která byla vyhrazená dříve pro rozhraní zpětné smyčky směrovače NSX-T T0. 

1. [Stažení samostatného klienta NSX Edge](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF a extrahujte soubory ze stažené sady do složky.

    ![Stáhnout samostatného klienta NSX Edge](media/l2vpn-deploy-client01.png)

2. Přejít do složky se všemi extrahovaných souborů. Vyberte všechna VMDK (NSX-l2t-Client-large. MF a NSX-l2t-client-large. ovf pro velkou velikost zařízení nebo NSX-l2t-Client-XLarge. MF a NSX-l2t-client-Xlarge. ovf pro největší velikost zařízení s velkou velikostí). Klikněte na **Next** (Další).

    ![Vybrat šablonu ](media/l2vpn-deploy-client02.png) ![ Vybrat šablonu](media/l2vpn-deploy-client03.png)

3. Zadejte název samostatného klienta NSX-T a klikněte na **Další**.

    ![Zadejte název šablony.](media/l2vpn-deploy-client04.png)

4. Klikněte na **Další** , jak je potřeba, abyste dosáhli nastavení úložiště dat. Vyberte vhodné úložiště dat pro samostatného klienta NSX-T a klikněte na **Další**.

    ![Vyberte úložiště dat.](media/l2vpn-deploy-client06.png)

5. Vyberte pro samostatného klienta NSX-T správné skupiny portů pro datový kmen (šachta PG), veřejné (pro odesílání) a rozhraní HA (odchozí připojení). Klikněte na **Next** (Další).

    ![Výběr skupin portů](media/l2vpn-deploy-client07.png)

6. Na obrazovce **přizpůsobit šablonu** vyplňte následující podrobnosti a klikněte na **Další**:

    Rozbalte L2T:

    * **Adresa partnera**. Zadejte IP adresu rezervovanou na portálu Azure CloudSimple pro rozhraní NSX-T Tier0 Loopback.
    * **Partnerský kód**. Vložte partnerský kód získaný z posledního kroku nasazení L2VPN serveru.
    * **Dílčí rozhraní sítě VLAN (ID tunelu)**. Zadejte ID sítě VLAN, které chcete roztáhnout. V závorkách () zadejte ID tunelu, které jste dříve nakonfigurovali.

    Rozbalte rozhraní pro odesílání:

    * **IP adresa DNS**. Zadejte místní IP adresu DNS.
    * **Výchozí brána**.  Zadejte výchozí bránu sítě VLAN, která bude pro tohoto klienta sloužit jako výchozí brána.
    * **IP adresa**. Zadejte IP adresu pro odesílání samostatného klienta.
    * **Délka předpony**. Zadejte délku předpony pro síť VLAN nebo podsíť pro odesílání.
    * Rozhraní **CLI správce/povolení/heslo uživatele root**. Nastavte heslo pro účet správce/Enable/root.

      ![Přizpůsobení šablony ](media/l2vpn-deploy-client08.png)
       ![ přizpůsobení šablon – další](media/l2vpn-deploy-client09.png)

7. Zkontrolujte nastavení a klikněte na **Dokončit**.

    ![Dokončit konfiguraci](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Konfigurace místního portu jímky

Pokud na jednom z webů sítě VPN není nasazený NSX, můžete nakonfigurovat síť VPN L2 nasazením samostatné NSX Edge v této lokalitě. Samostatná NSX Edge se nasadí pomocí souboru OVF na hostiteli, který není spravovaný pomocí NSX. Tím se nasadí zařízení brány NSX hraniční služby, které bude fungovat jako klient sítě VPN L2.

Pokud je samostatný hraniční kmen vNIC připojený k vSphere distribuovanému přepínači, pro funkci L2 sítě VPN se vyžaduje buď smíšený režim, nebo port jímky. Použití promiskuitního režimu může způsobit duplicity příkazů a duplicitní odpovědi. Z tohoto důvodu použijte režim portů jímky v konfiguraci NSX Edge pro nastavení L2 – samostatná konfigurace. Viz část [Konfigurace portu jímky](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) v dokumentaci k VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Ověřování IPsec VPN a L2VPN

Pomocí následujících příkazů můžete ověřit relace protokolu IPsec a L2VPN ze samostatného hraničního NSX – T.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Pomocí následujících příkazů ověřte relace protokolu IPsec a L2VPN ze směrovače NSX-T Tier0.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Pomocí následujících příkazů ověřte port jímky na hostiteli ESXi, kde se virtuální počítač samostatného klienta NSX-T nachází v místním prostředí.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
