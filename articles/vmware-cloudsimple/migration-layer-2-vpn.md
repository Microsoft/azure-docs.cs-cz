---
title: Řešení Azure VMware od CloudSimple – místní roztažení sítě vrstvy 2 na privátní cloud
description: Popisuje, jak nastavit síť VPN vrstvy 2 mezi sítí NSX-T v privátním cloudu CloudSimple a místním samostatným klientem NSX Edge.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2ddfa9611143d5c3f823539e018c8afc885c6a46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083225"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrace úloh pomocí roztažené sítě vrstvy 2

V této příručce se dozvíte, jak pomocí vrstvy 2 VPN (L2VPN) roztáhnout síť vrstvy 2 z místního prostředí do cloudového cloudu CloudSimple. Toto řešení umožňuje migraci úloh spuštěných v místním prostředí VMware do privátního cloudu v Azure ve stejném adresním prostoru podsítě, aniž byste museli znovu ip úlohy.

L2VPN na základě roztahování sítí vrstvy 2 může pracovat s nebo bez sítí založených na NSX ve vašem místním prostředí VMware. Pokud nemáte sítě založené na NSX pro úlohy v místním prostředí, můžete použít samostatnou bránu NSX Edge Services Gateway.

> [!NOTE]
> Tato příručka popisuje scénář, kde jsou místní a datová centra privátního cloudu propojeny přes síť VPN site-to-site.

## <a name="deployment-scenario"></a>Scénář nasazení

Chcete-li roztáhnout místní síť pomocí L2VPN, musíte nakonfigurovat server L2VPN (cílový směrovač NSX-T Tier0) a klienta L2VPN (zdrojový samostatný klient).  

V tomto scénáři nasazení je váš privátní cloud připojený k místnímu prostředí prostřednictvím tunelového propojení VPN mezi lokalitami, který umožňuje místní správu a podsítě vMotion komunikovat se správou privátního cloudu a podsítěmi vMotion. Toto uspořádání je nezbytné pro Cross vCenter vMotion (xVC-vMotion). Směrovač NSX-T Tier0 je nasazen jako server L2VPN v privátním cloudu.

Samostatný NSX Edge se nasazuje ve vašem místním prostředí jako klient L2VPN a následně je spárován se serverem L2VPN. Koncový bod tunelového propojení GRE je vytvořen na každé straně a nakonfigurován tak, aby "roztáhl" místní síť vrstvy 2 do vašeho privátního cloudu. Tato konfigurace je znázorněna na následujícím obrázku.

![Scénář nasazení](media/l2vpn-deployment-scenario.png)

Další informace o migraci pomocí sítě L2 VPN najdete v tématu [Virtuální privátní sítě](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) v dokumentaci k vmware.

## <a name="prerequisites-for-deploying-the-solution"></a>Předpoklady pro nasazení řešení

Před nasazením a konfigurací řešení ověřte, zda jsou k onomu následující:

* Místní verze vSphere je 6.7U1+ nebo 6.5P03+.
* Místní licence vSphere je na úrovni Enterprise Plus (pro distribuovaný přepínač vSphere).
* Identifikujte síť vrstvy 2, která má být roztažena do privátního cloudu.
* Identifikujte síť vrstvy 2 v místním prostředí pro nasazení klientského zařízení L2VPN.
* [Privátní cloud je již vytvořen](create-private-cloud.md).
* Verze samostatného zařízení NSX-T Edge je kompatibilní s verzí NSX-T Manager (NSX-T 2.3.0) používanou ve vašem prostředí Privátního cloudu.
* Skupina portů kmene byla vytvořena v místním virtuálním počítači s povoleným kovaným přenosům.
* Veřejná IP adresa byla vyhrazena pro použití pro samostatnou ip adresu nsx-t klienta a 1:1 NAT je na místě pro překlad mezi těmito dvěma adresami.
* Předávání DNS je nastaveno na místních serverech DNS pro az.cloudsimple.io doménu tak, aby přecházení na servery DNS privátního cloudu.
* Latence RTT je menší než nebo rovna 150 ms, jak je požadováno pro vMotion pracovat napříč dvěma weby.

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

V následující tabulce jsou uvedeny podporované verze vSphere a typy síťových adaptérů.  

| verze vSphere | Zdroj vTypPřepínač | Ovladač virtuální karty NIC | Typ vPřepínač cíle | Podporované? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Všechny | Dvs | Všechny | Dvs | Ano |
| vSphere 6.7UI nebo vyšší, 6,5 P03 nebo vyšší | Dvs | VMXNET3 | N-VDS | Ano |
| vSphere 6.7UI nebo vyšší, 6,5 P03 nebo vyšší | Dvs | E1000 | N-VDS | [Není podporováno podle VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI nebo 6.5P03, NSX-V nebo verze pod NSX-T2.2, 6.5P03 nebo vyšší | Všechny | Všechny | N-VDS | [Není podporováno podle VWware](https://kb.vmware.com/s/article/56991) |

Od vydání VMware NSX-T 2.3:

* Logický přepínač na straně Privátního cloudu, který je napnutý na místní prostor přes L2VPN, nelze směrovat současně. Roztažený logický přepínač nelze připojit k logickému směrovači.
* L2VPN a sítě IPSEC založené na trasách lze konfigurovat pouze pomocí volání rozhraní API.

Další informace naleznete [v tématu Virtuální privátní sítě](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) v dokumentaci společnosti VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Ukázkové adresování nasazení L2 VPN

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Místní síť, kde je nasazen samostatný klient ESG (L2 VPN)

| **Položka** | **Hodnotu** |
|------------|-----------------|
| Název sítě | MGMT_NET_VLAN469 |
| Vlan | 469 |
| CIDR| 10.250.0.0/24 |
| Ip adresa samostatného zařízení Edge | 10.250.0.111 |
| Ip adresa NAT samostatného zařízení Edge | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Místní síť, která má být roztažena

| **Položka** | **Hodnotu** |
|------------|-----------------|
| Vlan | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Schéma IP privátního cloudu pro směrovač NSX-T Tier0 (l2 VPN serve)

| **Položka** | **Hodnotu** |
|------------|-----------------|
| Rozhraní zpětné smyčky | 192.168.254.254/32 |
| Rozhraní tunelového propojení | 5.5.5.1/29 |
| Logický přepínač (roztažený) | Stretch_LS |
| Rozhraní zpětné smyčky (IP adresa NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Privátní cloudová síť, která má být mapována na roztaženou síť

| **Položka** | **Hodnotu** |
|------------|-----------------|
| Vlan | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Načtení ID logického směrovače potřebného pro L2VPN

Následující kroky ukazují, jak načíst ID logického směrovače instance logického směrovače Tier0 DR pro služby IPsec a L2VPN. ID logického směrovače je potřeba později při implementaci L2VPN.

1. Přihlaste se k NSX-T Manager https://*nsx-t-manager-ip-adresa* a vyberte **síťové** > **směrovače** > **Provider-LR** > **Přehled**. V **režimu vysoké dostupnosti**vyberte **možnost Aktivní pohotovostní režim**. Tato akce otevře rozbalovací okno, které zobrazuje virtuální počítač Edge, na kterém je aktuálně aktivní směrovač Tier0.

    ![Výběr aktivního pohotovostního režimu](media/l2vpn-fetch01.png)

2. Vyberte možnost Okraje**uzlů prostředků** >  **infrastruktury** > .**Edges** Poznamenejte si adresu IP správy aktivního virtuálního virtuálního počítačů Edge (Edge VM1) identifikovaného v předchozím kroku.

    ![IP pro správu poznámek](media/l2vpn-fetch02.png)

3. Otevřete relaci SSH na adresu IP správy virtuálního virtuálního počítačů Edge. Spusťte ```get logical-router``` příkaz s uživatelským **jménem admin** a heslo **CloudSimple 123!**.

    ![získat výstup logického směrovače](media/l2vpn-fetch03.png)

4. Pokud položku DR-Provider-LR nevidíte, proveďte následující kroky.

5. Vytvořte dva logické přepínače zálohované překrytím. Jeden logický přepínač je roztažen na místní, kde se nacházejí přenesené úlohy. Dalším logickým přepínačem je fiktivní spínač. Pokyny naleznete v [tématu Vytvoření logického přepínače](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) v dokumentaci společnosti VMware.

    ![Vytvořit logický přepínač](media/l2vpn-fetch04.png)

6. Připojte fiktivní přepínač k routeru Tier1 pomocí místní IP adresy propojení nebo jakékoli nepřekrývající se podsítě z místního nebo privátního cloudu. Viz [Přidání portu Downlink na logický směrovač úrovně 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) v dokumentaci společnosti VMware.

    ![Připojit fiktivní spínač](media/l2vpn-fetch05.png)

7. Spusťte `get logical-router` příkaz znovu v relaci SSH virtuálního počítači Edge. Zobrazí se UUID logického směrovače "DR-Provider-LR". Poznamenejte si UUID, které je vyžadováno při konfiguraci L2VPN.

    ![získat výstup logického směrovače](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Načtení ID logického přepínače potřebného pro L2VPN

1. Přihlaste se do [Správce NSX-T](https://nsx-t-manager-ip-address).
2. Vyberte**přepínače**  >  **přepínání** >  **v síti** > **<\Přehled logických\>přepínačů****Overview** > .
3. Poznamenejte si UUID logického přepínače stretch, který je vyžadován při konfiguraci L2VPN.

    ![získat výstup logického směrovače](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Aspekty směrování a zabezpečení l2VPN

Chcete-li vytvořit síť VPN založenou na směrování IPsec mezi směrovačem NSX-T Tier0 a samostatným klientem NSX Edge, musí být rozhraní zpětné smyčky směrovače NSX-T Tier0 schopno komunikovat s veřejnou IP adresou samostatného klienta NSX v místním prostředí přes UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Povolit UDP 500/4500 pro IPsec

1. [Vytvořte veřejnou IP adresu](public-ips.md) pro rozhraní zpětné smyčky NSX-T Tier0 na portálu CloudSimple.

2. [Vytvořte tabulku brány firewall](firewall.md) se stavovými pravidly, která umožňují příchozí přenosy UDP 500/ 4500 a připojte tabulku brány firewall k podsíti NSX-T HostTransport.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Inzerujte IP adresu rozhraní zpětné smyčky do sítě podložení

1. Vytvořte nulovou trasu pro síť rozhraní zpětné smyčky. Přihlaste se ke Správci NSX-T a vyberte**Routers** > možnost Zprostředkovatel**směrování** > **sítí-LR** >  **Networking** > **Směrovací** > **statické trasy**. Klikněte na **Přidat**. V **části Síť**zadejte ip adresu rozhraní zpětné smyčky. V **poli Další směrování**klepněte na tlačítko **Přidat**, zadejte hodnotu Null pro další směrování a ponechte výchozí hodnotu 1 pro vzdálenost správce.

    ![Přidat statickou trasu](media/l2vpn-routing-security01.png)

2. Vytvořte seznam předponek IP. Přihlaste se ke Správci NSX-T a vyberte **možnost Zprostředkovatel síťových** > **směrovačů-LR** > **Provider-LR** > **Routing** > **Seznamy** > **předponek PROTOKOLU IP**. Klikněte na **Přidat**. Zadejte název pro identifikaci seznamu. U **předpon**klepněte dvakrát na **Přidat.** V prvním řádku zadejte '0.0.0.0/0' pro **síť** a 'Odepřít' pro **akci**. Na druhém řádku vyberte **any** for **Network** a **Permit** for **Action**.
3. Připojte seznam předpony IP k oběma sousedům Protokolu BGP (TOR). Připojení seznamu předpony IP k sousednímu zařízení Protokolu BGP zabrání tomu, aby byla výchozí trasa inzerována v protokolu BGP přepínačům TOR. Však všechny ostatní trasy, která zahrnuje nulovou trasu bude inzerovat ip adresu rozhraní zpětné smyčky přepínače.

    ![Vytvořit seznam předpon IP](media/l2vpn-routing-security02.png)

4. Přihlaste se ke Správci NSX-T a vyberte **síťové** > **směrovače** > **směrování** > **Provider-LR** > **Routing** > **BGP** > **Neighbors**. Vyberte prvního souseda. Klepněte na tlačítko **Upravit** > **rodiny adres**. Pro rodinu IPv4 upravte sloupec **Out Filter** a vyberte seznam předponek IP, který jste vytvořili. Klikněte na **Uložit**. Opakujte tento krok pro druhého souseda.

    ![Připojit seznam předpon IP 1](media/l2vpn-routing-security03.png) ![Připojit seznam předponek IP 2](media/l2vpn-routing-security04.png)

5. Znovu distribuovat nulovou statickou trasu do protokolu BGP. Chcete-li inzerovat trasu rozhraní zpětné smyčky do podložení, je nutné znovu distribuovat nulovou statickou trasu do protokolu BGP. Přihlaste se ke Správci NSX-T a vyberte **síťové** > **směrovače** > **směrování** > **Provider-LR** > **Směrování** > **Směrování Přesměrování redistribuce** > **sousedí .** Vyberte **Zprostředkovatel-LR-Route_Redistribution** a klepněte na **tlačítko Upravit**. Zaškrtněte políčko **Statický** a klepněte na **tlačítko Uložit**.

    ![Redistribuovat nulovou statickou trasu do protokolu BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Konfigurace sítě VPN založené na trasách na směrovači NSX-T Tier0

Pomocí následující šablony vyplňte všechny podrobnosti pro konfiguraci sítě VPN založené na trase na směrovači NSX-T Tier0. UUID v každém volání POST jsou vyžadovány v následných voláních POST. IP adresy pro rozhraní zpětné smyčky a tunelového propojení pro L2VPN musí být jedinečné a nesmí se překrývat s místními nebo privátními cloudovými sítěmi.

IP adresy zvolené pro rozhraní zpětné smyčky a tunelového propojení používané pro L2VPN musí být jedinečné a nesmí se překrývat s místními nebo privátními cloudovými sítěmi. Síť rozhraní zpětné smyčky musí být vždy /32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
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

U všech následujících volání rozhraní API nahraďte IP adresu IP adresou NSX-T Manager. Všechna tato volání rozhraní API můžete spustit z `curl` klienta POSTMAN nebo pomocí příkazů.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Povolení služby IPSec VPN na logickém směrovači

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

### <a name="create-profiles-ike"></a>Vytvořit profily: IKE

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

### <a name="create-profiles-dpd"></a>Vytvořit profily: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Vytvořit profily: Tunelové propojení

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

### <a name="create-a-peer-endpoint"></a>Vytvoření koncového bodu partnera

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

### <a name="create-a-route-based-vpn-session"></a>Vytvoření relace VPN založené na směrování

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

Po každém volání POST vyplňte následující informace. ID jsou vyžadovány v následných voláních POST.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Vytvoření služby L2VPN

Výstup následujícího příkazu GET bude prázdný, protože konfigurace ještě není dokončena.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Pro následující příkaz POST je ID logického směrovače UUID logického směrovače Tier0 DR získaného dříve.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Vytvoření relace L2VPN

Pro následující příkaz POST je ID služby L2VPN ID ID, které jste právě získali, a ID relace IPsec VPN je ID získané v předchozí části.

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

Tato volání vytvořit koncový bod tunelového propojení GRE. Chcete-li zkontrolovat stav, spusťte následující příkaz.

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

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Vytvoření logického portu se zadaným ID tunelového propojení

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

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Získat kód partnera pro L2VPN na straně NSX-T

Získejte kód partnera koncového bodu NSX-T. Při konfiguraci vzdáleného koncového bodu je vyžadován kód partnerské strany. L2VPN <session-id> lze získat z předchozí části. Další informace naleznete v [průvodci rozhraním API NSX-T 2.3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Nasazení samostatného klienta NSX-T (místního)

Před nasazením ověřte, zda místní pravidla brány firewall povolují příchozí a odchozí přenosy UDP 500/4500 z/na veřejnou IP adresu CloudSimple, která byla dříve vyhrazena pro rozhraní zpětné smyčky směrovače NSX-T T0. 

1. [Stáhněte si samostatného klienta NSX Edge](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF a Extrahovat soubory ze staženého balíčku do složky.

    ![Stažení samostatného klienta NSX Edge](media/l2vpn-deploy-client01.png)

2. Přejděte do složky se všemi extrahovanými soubory. Vyberte všechny vmdks (NSX-l2t-client-large.mf a NSX-l2t-client-large.ovf pro velké velikosti zařízení nebo NSX-l2t-client-Xlarge.mf a NSX-l2t-client-Xlarge.ovf pro extra velké velikosti zařízení). Klikněte na **Další**.

    ![Vybrat](media/l2vpn-deploy-client02.png) ![šablonu Vybrat šablonu](media/l2vpn-deploy-client03.png)

3. Zadejte název samostatného klienta NSX-T a klepněte na tlačítko **Další**.

    ![Zadat název šablony](media/l2vpn-deploy-client04.png)

4. Podle potřeby klikněte na **Další,** abyste dosáhli nastavení úložiště dat. Vyberte příslušné úložiště dat pro samostatného klienta NSX-T a klepněte na tlačítko **Další**.

    ![Vybrat úložiště dat](media/l2vpn-deploy-client06.png)

5. Vyberte správné skupiny portů pro rozhraní Trunk (Trunk PG), Public (Uplink PG) a HA (Uplink PG) pro samostatného klienta NSX-T. Klikněte na **Další**.

    ![Výběr skupin portů](media/l2vpn-deploy-client07.png)

6. Na obrazovce Přizpůsobit **šablonu** vyplňte následující podrobnosti a klepněte na **tlačítko Další**:

    Rozbalit L2T:

    * **Adresa partnera**. Zadejte IP adresu vyhrazenou na portálu Azure CloudSimple pro rozhraní Zpětné smyčky NSX-T Tier0.
    * **Kód partnera**. Vložte kód partnera získaný z posledního kroku nasazení serveru L2VPN.
    * **Dílčí rozhraní VLAN (ID tunelového propojení).** Zadejte ID sítě VLAN, které má být roztaženo. V závorce () zadejte ID tunelového propojení, které bylo dříve nakonfigurováno.

    Rozbalte rozhraní pro uplink:

    * **IP adresa DNS**. Zadejte místní IP adresu DNS.
    * **Výchozí brána**.  Zadejte výchozí bránu sítě VLAN, která bude pro tohoto klienta sloužit jako výchozí brána.
    * **IP adresa**. Zadejte adresu IP služby pro připojení k připojení samostatného klienta.
    * **Délka předpony**. Zadejte délku předpony sítě VLAN/podsítě.
    * **Správce/povolit/root uživatelské heslo**rozhraní CLI . Nastavte heslo pro účet admin /enable /root.

      ![Přizpůsobit](media/l2vpn-deploy-client08.png)
      ![šablonu Přizpůsobit šablonu - více](media/l2vpn-deploy-client09.png)

7. Zkontrolujte nastavení a klepněte na tlačítko **Dokončit**.

    ![Kompletní konfigurace](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Konfigurace místního portu jímky

Pokud jeden z lokalit VPN nemá nasazený NSX, můžete nakonfigurovat L2 VPN nasazením samostatného NSX Edge v této lokalitě. Samostatný NSX Edge je nasazen pomocí souboru OVF na hostiteli, který není spravován NSX. Tím se nasadí zařízení NSX Edge Services Gateway, které bude fungovat jako klient sítě VPN L2.

Pokud je k vsítilizovanému přepínači vSphere připojen samostatný hraniční trunk vNIC, je pro funkci L2 VPN vyžadován buď promiskuitní režim, nebo port dřezu. Použití promiskuitního režimu může způsobit duplicitní příkazy ping a duplicitní odpovědi. Z tohoto důvodu použijte režim portu jímky v samostatné konfiguraci NSX Edge L2 VPN. Viz [Konfigurace portu jímky](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) v dokumentaci společnosti VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Ověření IPsec VPN a L2VPN

Pomocí následujících příkazů ověřte relace IPsec a L2VPN ze samostatného nsx-t edge.

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

Pomocí následujících příkazů ověřte relace IPsec a L2VPN ze směrovače NSX-T Tier0.

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

Pomocí následujících příkazů ověřte port jímky na hostiteli ESXi, kde se nachází samostatný virtuální počítač klienta NSX-T v místním prostředí.

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
