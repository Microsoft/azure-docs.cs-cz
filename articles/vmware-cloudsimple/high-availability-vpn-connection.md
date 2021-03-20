---
title: Řešení Azure VMware podle CloudSimple – konfigurace vysoké dostupnosti z místního úložiště na CloudSimple VPN Gateway
description: Popisuje, jak nakonfigurovat připojení s vysokou dostupností z místního prostředí na CloudSimple bránu VPN, která je povolená pro vysokou dostupnost.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80805aaa172518c40c7ad123ca24361ee0f15e69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895695"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Konfigurace připojení s vysokou dostupností z místního prostředí do CloudSimple VPN Gateway

Správci sítě můžou nakonfigurovat připojení VPN typu Site-to-site s vysokou dostupností z místního prostředí na CloudSimple bránu VPN.

V této příručce najdete postup konfigurace místní brány firewall pro připojení VPN typu Site-to-Site VPN s vysokou dostupností. Podrobné pokyny jsou specifické pro typ místní brány firewall. V příkladech se v tomto průvodci zobrazují kroky pro dva typy bran firewall: Cisco ASA a Palo Alto Networks.

## <a name="before-you-begin"></a>Než začnete

Před konfigurací místní brány firewall proveďte následující úlohy.

1. Ověřte, že vaše organizace [zřídila](create-nodes.md) požadované uzly a vytvořila aspoň jeden privátní cloud CloudSimple.
2. [Nakonfigurujte bránu VPN typu Site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mezi vaší místní sítí a privátním cloudem CloudSimple.

V tématu [Přehled bran VPN](cloudsimple-vpn-gateways.md) najdete podporované návrhy fáze 1 a fáze 2.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Konfigurace místní brány firewall pro Cisco ASA

Pokyny v této části se vztahují na Cisco ASA verze 8,4 a novější. V příkladu konfigurace je software Cisco Adaptive Security pro zařízení verze 9,10 nasazený a nakonfigurovaný v IKEv1 režimu.

Aby síť VPN typu Site-to-site fungovala, je nutné na vnějším rozhraní pro místní bránu Cisco ASA VPN zakázat protokol UDP 500/4500 a ESP (IP Protocol 50) z primární a sekundární veřejné IP adresy CloudSimple (peer IP adresa).

### <a name="1-configure-phase-1-ikev1"></a>1. konfigurace fáze 1 (IKEv1)

Pokud chcete na vnějším rozhraní povolit fázi 1 (IKEv1), zadejte následující příkaz CLI v bráně firewall Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. vytvoření zásady IKEv1

Vytvořte zásady IKEv1, které definují algoritmy a metody, které se mají použít k výpočtu hodnoty hash, ověřování, Diffie-Hellman skupiny, životnosti a šifrování.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Vytvoření skupiny tunelových propojení

V části atributy IPsec vytvořte tunelovou skupinu. Nakonfigurujte IP adresu partnerského vztahu a předsdílený klíč tunelového propojení, který jste nastavili při [konfiguraci brány VPN typu Site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. konfigurace fáze 2 (IPsec)

Pokud chcete konfigurovat fázi 2 (IPsec), vytvořte seznam řízení přístupu (ACL), který definuje šifrovaný a tunelový přenos. V následujícím příkladu je přenos dat z tunelu z tunelu, který je vytvořen z místní podsítě (10.16.1.0/24) do vzdálené podsítě privátního cloudu (192.168.0.0/24). Seznam ACL může obsahovat více položek v případě, že mezi lokalitami existuje více podsítí.

Ve verzích Cisco ASA 8,4 a novějších lze vytvořit objekty nebo skupiny objektů, které slouží jako kontejnery pro sítě, podsítě, IP adresy hostitelů nebo více objektů. Vytvořte objekt pro místní a objekt pro vzdálené podsítě a použijte je pro seznam ACL šifrování a příkazy NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Definování místní podsítě jako objektu

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Definování vzdálené podsítě CloudSimple jako objektu

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Konfigurace seznamu přístupu pro přenos zájmu

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. konfigurace sady transformací

Nakonfigurujte sadu transformací (TS), která musí zahrnovat klíčové slovo ```ikev1``` . Atributy šifrování a hash zadané v TS se musí shodovat s parametry uvedenými ve [výchozí konfiguraci pro brány VPN CloudSimple](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. konfigurace kryptografické mapy

Nakonfigurujte kryptografickou mapu, která obsahuje tyto součásti:

* IP adresa partnerského uzlu
* Definovaný seznam ACL, který obsahuje provoz, který vás zajímá
* Sada transformací

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. použijte kryptografickou mapu.

Použít kryptografickou mapu na vnějším rozhraní:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Potvrďte příslušná pravidla překladu adres (NAT)

Následuje pravidlo překladu adres (NAT), které se používá. Zajistěte, aby se přenosy VPN nevztahovaly na žádné jiné pravidlo překladu adres (NAT).

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Ukázkový výstup z webu Cisco ASA vytvořeného z typu Site-to-Site VPN s protokolem IPsec

Výstup fáze 1:

![Výstup fáze 1 pro firewall Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Výstup fáze 2:

![Výstup fáze 2 pro bránu Cisco ASA firewall](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Konfigurace místních Palo Alto Networks firewall

Pokyny v této části se vztahují na Palo Alto Networks verze 7,1 a novější. V tomto příkladu konfigurace je Palo Alto Networks VM-Series software verze 8.1.0 nasazená a nakonfigurovaná v režimu IKEv1.

Aby síť VPN typu Site-to-site fungovala, je nutné na vnějším rozhraní místní brány Palo Alto Networks zakázat protokol UDP 500/4500 a ESP (IP Protocol 50) z primární a sekundární veřejné IP adresy CloudSimple (peer IP adresa).

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Vytvoření primárních a sekundárních tunelových rozhraní

Přihlaste se k bráně firewall Palo Alto, vyberte **Síťová**  >  **rozhraní**  >  **tunel**  >  **Přidat**, nakonfigurujte následující pole a klikněte na **OK**.

* Název rozhraní. První pole je automaticky vyplněné pomocí klíčového slova Tunnel. Do sousedního pole zadejte číslo od 1 do 9999. Toto rozhraní se použije jako primární tunelové rozhraní k přenosu dat mezi místním datacentrem a privátním cloudem.
* Vytvořena. Zadejte komentáře pro snadnější identifikaci účelu tunelu
* Profil NetFlow Ponechte výchozí hodnoty.
* Konfigurace. Přiřadit rozhraní k: virtuální směrovač: vyberte **výchozí**. 
        Zóna zabezpečení: Vyberte zónu pro důvěryhodný síťový provoz v síti LAN. V tomto příkladu je název zóny pro provoz v síti LAN "Trust".
* IPv4. Klikněte na **Přidat** a do svého prostředí přidejte všechny překrývající se nevyužité/32 IP adresy, které se přiřadí k primárnímu rozhraní tunelového propojení a budou se používat pro monitorování tunelů (vysvětlení později).

Vzhledem k tomu, že tato konfigurace je pro síť VPN s vysokou dostupností, vyžadují se dvě rozhraní tunelu: jedna primární a jedna sekundární. Opakujte předchozí postup pro vytvoření sekundárního tunelového připojení. Vyberte jiné ID tunelu a jinou nevyužitou IP adresu/32.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Nastavení statických tras pro podsítě privátního cloudu, které se mají dosáhnout přes síť VPN typu Site-to-site

Trasy jsou nezbytné pro místní podsítě, aby se dostaly k podsítím privátního cloudu CloudSimple.

Vyberte   >  **virtuální síťové směrovače**  >  *výchozí*  >  **statické trasy**  >  **Přidat**, nakonfigurujte následující pole a klikněte na **OK**.

* Název. Zadejte libovolný název, který umožňuje snadnou identifikaci účelu trasy.
* Tabulka. Zadejte CloudSimple podsítě privátního cloudu, které se mají kontaktovat přes tunelová rozhraní S2S z místního prostředí.
* Prostředí. Vyberte primární tunelové rozhraní vytvořené v kroku 1 (oddíl-2) z rozevíracího seznamu. V tomto příkladu je to Tunnel. 20.
* Další segment směrování. Vyberte **Žádná**.
* Vzdálenost správce. Ponechte výchozí hodnoty.
* Metriky. Zadejte libovolnou hodnotu od 1 do 65535. Klíčem je zadání nižší metriky pro trasu odpovídající primárnímu tunelovým rozhraním v porovnání s odpovídajícím sekundárním tunelovým rozhraním, které vede k upřednostnění bývalého postupu. Pokud Tunnel. 20 má hodnotu metriky 20 na rozdíl od hodnoty metriky 30 pro Tunnel. 30, Tunnel. 20 se upřednostňuje.
* Směrovací tabulka Ponechte výchozí hodnoty.
* Profil BFD Ponechte výchozí hodnoty.
* Monitorování cesty. Tuto možnost nechte nezaškrtnutou.

Zopakováním předchozích kroků vytvořte další trasu pro podsítě privátního cloudu, která se použije jako sekundární a záložní postup přes sekundární tunelové rozhraní. Tentokrát vyberte jiné ID tunelu a vyšší metriku než u primární trasy.

### <a name="3-define-the-cryptographic-profile"></a>3. definování kryptografického profilu

Definujte kryptografický profil, který určuje protokoly a algoritmy pro identifikaci, ověřování a šifrování, které se mají použít k nastavení tunelů VPN v IKEv1 fázi 1.

Vyberte **síť**  >  **rozbalte položku profily sítě**  >  **IKE šifrování**  >  **Přidat**, nakonfigurujte následující pole a klikněte na **OK**.

* Název. Zadejte libovolný název kryptografického profilu protokolu IKE.
* Skupina DH. Klikněte na **Přidat** a vyberte příslušnou skupinu DH.
* Šifrování Klikněte na tlačítko **Přidat** a vyberte příslušnou metodu šifrování.
* ověřování Klikněte na tlačítko **Přidat** a vyberte příslušnou metodu ověřování.
* Životnost klíče. Ponechte výchozí hodnoty.
* Ověřování IKEv2 – vícenásobné Ponechte výchozí hodnoty.

### <a name="4-define-ike-gateways"></a>4. definování bran IKE

Definujte brány IKE k navázání komunikace mezi partnerskými uzly na každém konci tunelového propojení sítě VPN.

Vyberte **síť**  >  **rozbalte položku profily sítě**  >  **IKE brány**–  >  **Přidat**, nakonfigurujte následující pole a klikněte na **OK**.

Karta Obecné:

* Název. Zadejte název brány IKE, ke které se má navázat partnerský vztah primárního CloudSimple VPN.
* Verze. Vyberte **režim pouze IKEv1**.
* Typ adresy Vyberte **IPv4**.
* Prostředí. Vyberte veřejné nebo vnější rozhraní.
* Místní IP adresa. Ponechte výchozí hodnoty.
* Typ IP adresy partnerského vztahu Vyberte **IP adresa**.
* Adresa partnera. Zadejte IP adresu primárního partnera VPN CloudSimple.
* ověřování Vyberte **předsdílený klíč**.
* Předsdílený klíč/potvrzení předsdíleného klíče. Zadejte předsdílený klíč, který bude odpovídat klíči brány VPN CloudSimple.
* Místní identifikace. Zadejte veřejnou IP adresu místní brány firewall Palo Alto.
* Identifikace partnerského vztahu. Zadejte IP adresu primárního partnera VPN CloudSimple.

Karta Upřesnit možnosti:

* Povolí pasivní režim. Tuto možnost nechte nezaškrtnutou.
* Povolte procházení NAT. Pokud místní brána firewall Palo Alto není za žádným zařízením NAT, nechte nezaškrtnutou. V opačném případě zaškrtněte políčko.

IKEv1

* Režim výměny. Vyberte **Main (Hlavní**).
* Kryptografický profil protokolu IKE. Vyberte kryptografický profil protokolu IKE, který jste vytvořili dříve. Nechejte políčko Povolit fragmentaci nezaškrtnuté.
* Detekce mrtvého partnera. Nechejte políčko nezaškrtnuté.

Zopakováním předchozích kroků vytvořte sekundární bránu IKE.

### <a name="5-define-ipsec-crypto-profiles"></a>5. definování šifrovacích profilů IPSEC

Vyberte **síť**  >  **rozbalte položku profily sítě**  >  **IPSec šifrování**  >  **Přidat**, nakonfigurujte následující pole a klikněte na **OK**.

* Název. Zadejte název kryptografického profilu IPsec.
* Protokol IPsec. Vyberte možnost **ESP**.
* Šifrování Klikněte na tlačítko **Přidat** a vyberte příslušnou metodu šifrování.
* ověřování Klikněte na tlačítko **Přidat** a vyberte příslušnou metodu ověřování.
* Skupina DH. Vyberte možnost **ne-PFS**.
* Platné. Nastaví se na 30 minut.
* Aby. Nechejte políčko nezaškrtnuté.

Zopakováním předchozích kroků vytvořte další kryptografický profil IPsec, který bude použit jako sekundární partnerský uzel CloudSimple VPN. Stejný kryptografický profil IPSEC se dá použít i pro primární i sekundární tunelové propojení IPsec (viz následující postup).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. definování profilů monitorování pro monitorování tunelu

Vyberte **síť**  >  **rozbalte položku Monitorování profilů sítě**  >    >  **Přidat**, nakonfigurujte následující pole a klikněte na tlačítko **OK**.

* Název. Zadejte libovolný název profilu monitorování, který se má použít pro monitorování tunelu pro proaktivní reakci na selhání.
* Kroky. Vyberte **převzetí služeb při selhání**.
* Doba. Zadejte hodnotu **3**.
* Mezí. Zadejte hodnotu **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Nastavte primární a sekundární tunelové propojení IPsec.

Vyberte **síťové**  >  **tunely IPSec**  >  **Přidat**, nakonfigurujte následující pole a klikněte na **OK**.

Karta Obecné:

* Název. Zadejte libovolný název primárního tunelového propojení IPSEC, kterému se má navázat partnerský vztah primárního CloudSimple VPN.
* Rozhraní tunelu. Vyberte primární tunelové rozhraní.
* Textový. Ponechte výchozí hodnoty.
* Typ adresy Vyberte **IPv4**.
* Brána IKE. Vyberte primární bránu IKE.
* Kryptografický profil protokolu IPsec. Vyberte primární profil protokolu IPsec. Vyberte **Zobrazit upřesňující možnosti**.
* Povolte ochranu před přehráním. Ponechte výchozí hodnoty.
* Zkopírujte hlavičku TOS. Nechejte políčko nezaškrtnuté.
* Monitorování tunelu. Zaškrtněte políčko.
* Cílová IP adresa. Zadejte libovolnou IP adresu patřící do podsítě privátního cloudu CloudSimple, která je povolená přes připojení Site-to-site. Ujistěte se, že rozhraní tunelu (například Tunnel. 20-10.64.5.2/32 a Tunnel. 30-10.64.6.2/32) v Palo Alto mají povolený přístup k IP adrese privátního cloudu CloudSimple přes síť Site-to-Site VPN. Podívejte se na následující konfiguraci ID proxy serveru.
* Profilu. Vyberte profil monitorování.

Karta ID proxy serveru: klikněte na  >  **Přidat** IPv4 a nakonfigurujte následující:

* ID proxy serveru. Zadejte libovolný název zajímavého provozu. V rámci jednoho tunelu IPsec mohlo být provedeno více ID proxy serveru.
* Místní. Zadejte místní místní podsítě, které mohou komunikovat s podsítěmi privátního cloudu prostřednictvím sítě VPN typu Site-to-site.
* Vzdálené. Zadejte vzdálené podsítě privátního cloudu, které mají povoleno komunikovat s místními podsítěmi.
* Protokol. Vyberte **libovolný**.

Zopakováním předchozích kroků vytvořte další tunelové propojení IPsec, které chcete použít pro sekundárního partnera VPN CloudSimple.

## <a name="references"></a>Reference

Konfigurace překladu adres (NAT) na počítači Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Průvodce konfigurací řady Cisco ASA 5500</a>

Podporované atributy IKEv1 a IKEv2 na Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Průvodce konfigurací rozhraní příkazového řádku Cisco ASA Series</a>

Konfigurace sítě IPsec Site-to-Site VPN v Cisco ASA s verzí 8,4 a novější:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Konfigurace tunelových propojení typu Site-to-site protokolu IPsec v IKEv1 pomocí ASDM nebo CLI v ASA</a>

Konfigurace virtuálního zařízení ASAv (Cisco Adaptive Security) na platformě Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Příručka rychlý Start pro virtuální zařízení Cisco Adaptive Security (ASAv)</a>

Konfigurace sítě Site-to-Site VPN s ID proxy serveru v Palo Alto:

[Nastavení sítě VPN typu Site-to-site](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Nastavení sledování tunelu:

[Nastavení monitorování tunelu](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Operace brány IKE nebo tunelové propojení IPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Umožňuje povolit nebo zakázat, aktualizovat nebo restartovat bránu IKE nebo tunelové propojení IPsec.</a>
