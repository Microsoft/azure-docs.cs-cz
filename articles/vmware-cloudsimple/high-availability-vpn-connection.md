---
title: Řešení Azure VMware podle cloudu– konfigurace vysoké dostupnosti z místní na bránu VPN CloudSimple
description: Popisuje, jak nakonfigurovat připojení s vysokou dostupností z místního prostředí do brány CloudSimple VPN povolené pro vysokou dostupnost.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025261"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Konfigurace připojení s vysokou dostupností z místní ho sazí do brány CLOUDSimple VPN

Správci sítě mohou nakonfigurovat připojení VPN iPsec site-to-site s vysokou dostupností z místního prostředí na bránu CloudSimple VPN.

Tato příručka představuje postup konfigurace místní brány firewall pro připojení VPN s vysokou dostupností webu k webu iPsec. Podrobné kroky jsou specifické pro typ místní brány firewall. Jako příklady tato příručka představuje kroky pro dva typy bran firewall: Cisco ASA a Palo Alto Networks.

## <a name="before-you-begin"></a>Než začnete

Před konfigurací místní brány firewall proveďte následující úkoly.

1. Ověřte, zda vaše organizace [zřídí](create-nodes.md) požadované uzly a vytvořila alespoň jeden cloudový privátní cloud.
2. [Nakonfigurujte bránu VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mezi místní sítí a privátním cloudem CloudSimple.

Viz [Přehled bran VPN](cloudsimple-vpn-gateways.md) pro podporované návrhy fáze 1 a fáze 2.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Konfigurace místní brány firewall Cisco ASA

Pokyny v této části platí pro cisco ASA verze 8.4 a novější. V příkladu konfigurace je software Cisco Adaptive Security Appliance software verze 9.10 nasazen a konfigurován v režimu IKEv1.

Aby síť SITE-to-Site VPN fungovala, musíte povolit UDP 500/4500 a ESP (IP protokol 50) z primární a sekundární veřejné IP adresy CloudSimple (ip. partnerské strany) na vnějším rozhraní místní brány Cisco ASA VPN.

### <a name="1-configure-phase-1-ikev1"></a>1. Konfigurace fáze 1 (IKEv1)

Chcete-li povolit fázi 1 (IKEv1) ve vnějším rozhraní, zadejte následující příkaz rozhraní příkazu PŘÍKAZ CLI do brány firewall Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Vytvoření zásady IKEv1

Vytvořte zásadu IKEv1, která definuje algoritmy a metody, které mají být použity pro algoritmus hash, ověřování, skupinu Diffie-Hellman, životnost a šifrování.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Vytvoření skupiny tunelů

Vytvořte skupinu tunelového propojení pod atributy IPsec. Nakonfigurujte ip adresu druhé strany a předsdílený klíč tunelového propojení, který nastavíte při [konfiguraci brány VPN typu Site-to-Site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Konfigurace fáze 2 (IPsec)

Chcete-li nakonfigurovat fázi 2 (IPsec), vytvořte seznam řízení přístupu (ACL), který definuje provoz, který má být šifrován a tunelován. V následujícím příkladu je provoz zájmu z tunelu, který pochází z místní místní podsítě (10.16.1.0/24) do vzdálené podsítě Privátního cloudu (192.168.0.0/24). Seznam ACL může obsahovat více položek, pokud mezi sítěmi existuje více podsítí.

Ve verzích Cisco ASA 8.4 a novějších lze vytvořit objekty nebo skupiny objektů, které slouží jako kontejnery pro sítě, podsítě, hostitelské IP adresy nebo více objektů. Vytvořte objekt pro místní a objekt pro vzdálené podsítě a použijte je pro kryptografické a cl a příkazy NAT.

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

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Konfigurace seznamu přístupu pro provoz, který je v zájmu

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Konfigurace transformační sady

Nakonfigurujte sadu transformace (TS), která musí zahrnovat klíčové slovo ```ikev1```. Atributy šifrování a hodnoty hash zadané v ts musí odpovídat parametrům uvedeným ve [výchozí konfiguraci pro brány CloudSimple VPN](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Konfigurace kryptomapy

Nakonfigurujte kryptomapu, která obsahuje tyto součásti:

* IP adresa partnera
* Definovaný acl, který obsahuje provoz zájmu
* Transformace sada

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Použití kryptomapy

Použijte kryptomapu na vnějším rozhraní:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Potvrdit příslušná pravidla NAT

Následuje pravidlo NAT, které se používá. Ujistěte se, že provoz VPN není předmětem žádného jiného pravidla NAT.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Ukázkový výstup sítě IPsec site-to-site VPN vytvořený výstup z cisco asa

Výstup fáze 1:

![Výstup fáze 1 pro bránu firewall Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Výstup fáze 2:

![Výstup fáze 2 pro bránu firewall Cisco ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Konfigurace brány firewall pro místní sítě Palo Alto

Pokyny v této části platí pro Palo Alto Networks verze 7.1 a novější. V tomto příkladu konfigurace je software Palo Alto Networks Řady VM verze 8.1.0 nasazen a nakonfigurován v režimu IKEv1.

Aby síť SITE-to-Site VPN fungovala, musíte povolit UDP 500/4500 a ESP (IP protokol 50) z primární a sekundární veřejné IP adresy CloudSimple (ip. partnerské strany) na vnějším rozhraní místní brány Palo Alto Networks.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Vytvořte primární a sekundární rozhraní tunelů

Přihlaste se k bráně firewall Palo Alto, vyberte**přidat tunelové propojení** > **Add** **síťových** > **rozhraní** > , nakonfigurujte následující pole a klepněte na tlačítko **OK**.

* Název rozhraní. První pole je automaticky vyplněno klíčovým slovem "tunel". Do přilehlého pole zadejte libovolné číslo od 1 do 9999. Toto rozhraní se použije jako primární rozhraní tunelového propojení pro přenos provozu site-to-site mezi místním datovým centrem a privátním cloudem.
* Komentář. Zadejte komentáře pro snadnou identifikaci účelu tunelu
* Profil Netflow. Ponechte výchozí hodnoty.
* Config. Přiřadit rozhraní: Virtuální směrovač: Vyberte **výchozí**. 
        Zóna zabezpečení: Vyberte zónu pro důvěryhodný provoz sítě LAN. V tomto příkladu je název zóny pro provoz v síti LAN "Důvěra".
* IPv4. Klikněte na **Tlačítko Přidat** a přidejte nepřekrývající se nepoužívanou adresu IP /32 ve vašem prostředí, která bude přiřazena k primárnímu rozhraní tunelového propojení a bude použita pro sledování tunelových propojení (vysvětleno později).

Vzhledem k tomu, že tato konfigurace je pro síť VPN s vysokou dostupností, jsou vyžadována dvě rozhraní tunelového propojení: jedna primární a jedna sekundární. Opakováním předchozích kroků vytvořte rozhraní sekundárního tunelu. Vyberte jiné ID tunelového propojení a jinou nepoužitou adresu /32 IP.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Nastavte statické trasy pro podsítě privátního cloudu, které mají být dosažitelné prostřednictvím sítě VPN site-to-site

Trasy jsou nezbytné pro místní podsítě k dosažení CloudSimple privátní cloudové podsítě.

Vyberte **možnost Síťové** > **virtuální směrovače** > *jako výchozí* > **Statické trasy** > **Přidat**, nakonfigurujte následující pole a klepněte na tlačítko **OK**.

* Název. Zadejte libovolný název pro snadnou identifikaci účelu trasy.
* Cíl. Určení podsítí cloudového privátního cloudu, které mají být dosažitelné přes rozhraní tunelového propojení S2S z místního prostředí
* Rozhraní. V rozevíracím souboru vyberte primární rozhraní tunelového propojení vytvořené v kroku 1 (oddíl-2). V tomto příkladu je tunnel.20.
* Další Hop. Vyberte **Žádná**.
* Vzdálenost správce. Ponechte výchozí hodnoty.
* Metrika. Zadejte libovolnou hodnotu od 1 do 65535. Klíčem je zadat nižší metriku pro trasu odpovídající primárnímu rozhraní tunelu ve srovnání s odpovídajícím rozhraním sekundárního tunelu, díky čemuž je přednostní předchozí trasa. Pokud tunnel.20 má metrickou hodnotu 20 na rozdíl od metrické hodnoty 30 pro tunnel.30, tunnel.20 je přednostní.
* Tabulka trasy. Ponechte výchozí hodnoty.
* PROFIL BFD. Ponechte výchozí hodnoty.
* Sledování cesty. Tuto možnost nechte nezaškrtnutou.

Opakováním předchozích kroků vytvořte další trasu pro podsítě privátního cloudu, která se použije jako sekundární nebo záložní trasa prostřednictvím rozhraní sekundárního tunelu. Tentokrát vyberte jiné ID tunelového propojení a vyšší metriku než pro primární trasu.

### <a name="3-define-the-cryptographic-profile"></a>3. Definujte kryptografický profil

Definujte kryptografický profil, který určuje protokoly a algoritmy pro identifikaci, ověřování a šifrování, které mají být použity pro nastavení tunelových propojení VPN v ikEv1 fáze 1.

Vyberte **možnost Rozšířit** > **síť Síťové profily** > **IKE Crypto** > **Přidat**, nakonfigurovat následující pole a klepnout na tlačítko **OK**.

* Název. Zadejte libovolný název kryptografického profilu IKE.
* skupina DH. Klikněte na **Přidat** a vyberte příslušnou skupinu DH.
* Šifrování. Klikněte na **Přidat** a vyberte příslušnou metodu šifrování.
* ověřování Klikněte na **Přidat** a vyberte příslušnou metodu ověřování.
* Životnost klíče. Ponechte výchozí hodnoty.
* IKEv2 Ověřování více. Ponechte výchozí hodnoty.

### <a name="4-define-ike-gateways"></a>4. Definování bran IKE

Definujte brány Protokolu IKE pro navázání komunikace mezi partnery na každém konci tunelu VPN.

Vyberte **možnost Rozšířit** > síť Přidá**v bráně IKE síťové** > **profily** > **,** nakonfigurujte následující pole a klepněte na tlačítko **OK**.

Karta Obecné:

* Název. Zadejte název brány IKE, která má být peered s primární cloudsimple vpn peer.
* Verze. Vyberte **pouze režim IKEv1**.
* Typ adresy. Vyberte **IPv4**.
* Rozhraní. Vyberte veřejné nebo vnější rozhraní.
* Místní IP adresa. Ponechte výchozí hodnoty.
* Typ ip adresy partnera. Vyberte **ip**.
* Adresa partnera. Zadejte primární IP adresu partnera CloudSimple VPN.
* ověřování Vyberte **předsdílený klíč**.
* Předsdílený klíč / Potvrdit předsdílený klíč. Zadejte předsdílený klíč tak, aby odpovídal klíči brány CloudSimple VPN.
* Místní identifikace. Zadejte veřejnou IP adresu místní brány firewall Palo Alto.
* Identifikace druhé strany. Zadejte primární IP adresu partnera CloudSimple VPN.

Karta Upřesnit možnosti:

* Povolte pasivní režim. Tuto možnost nechte nezaškrtnutou.
* Povolte traversal NAT. Pokud místní brána firewall Palo Alto není za žádným zařízením NAT, ponechte ji nezaškrtnutou. V opačném případě zaškrtněte políčko.

IKEv1:

* režimu výměny. Vyberte **hlavní**.
* Kryptografický profil IKE. Vyberte profil IKE Crypto, který jste vytvořili dříve. Ponechte políčko Povolit fragmentaci nezaškrtnuté.
* Detekce mrtvého partnera. Ponechte políčko nezaškrtnuté.

Opakováním předchozích kroků vytvořte sekundární bránu IKE.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definování kryptografických profilů IPSEC

Vyberte **možnost Rozbalení** > **síťových profilů** > **Přidejte ipsec crypto** > **,** nakonfigurujte následující pole a klepněte na tlačítko **OK**.

* Název. Zadejte název kryptografického profilu IPsec.
* Protokol IPsec. Vyberte **esp**.
* Šifrování. Klikněte na **Přidat** a vyberte příslušnou metodu šifrování.
* ověřování Klikněte na **Přidat** a vyberte příslušnou metodu ověřování.
* skupina DH. Vyberte **no-pfs**.
* Životnost: Nastaveno na 30 minut.
* Povolit. Ponechte políčko nezaškrtnuté.

Opakujte předchozí kroky k vytvoření jiného kryptografického profilu IPsec, který bude použit jako sekundární partner cloudové sítě VPN. Stejný profil IPSEC Crypto lze také použít pro primární i sekundární tunelové propojení IPsec (viz následující postup).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Definujte profily monitorů pro monitorování tunelů

Vyberte **možnost Přidat** > **položku Sledování** > **profilů** > sítě **,** nakonfigurovat následující pole a klepnout na tlačítko **OK**.

* Název. Zadejte libovolný název profilu monitoru, který se má použít pro monitorování tunelového propojení pro aktivní reakci na poruchu.
* Akce. Vyberte **možnost Převzetí služeb při selhání**.
* Interval. Zadejte hodnotu **3**.
* Práh. Zadejte hodnotu **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Nastavte primární a sekundární tunely IPsec.

Vyberte **možnost Přidat** > **tunelové** > propojení protokolu IPsec**sítě**, nakonfigurujte následující pole a klepněte na tlačítko **OK**.

Karta Obecné:

* Název. Zadejte libovolný název primárního tunelového propojení IPSEC, který má být partnerem s primárním partnerem CloudSimple VPN.
* Rozhraní tunelového propojení. Vyberte primární rozhraní tunelového propojení.
* Typ. Ponechte výchozí hodnoty.
* Typ adresy. Vyberte **IPv4**.
* Brána IKE. Vyberte primární bránu IKE.
* Krypto profil IPsec. Vyberte primární profil IPsec. Vyberte **Zobrazit rozšířené možnosti**.
* Povolte ochranu proti přehrání. Ponechte výchozí hodnoty.
* Zkopírujte hlavičku TOS. Ponechte políčko nezaškrtnuté.
* Monitor tunelu. Zaškrtněte políčko.
* Cílová IP adresa. Zadejte libovolnou IP adresu patřící podsíti CloudSimple Private Cloud, která je povolena přes připojení Site-to-Site. Ujistěte se, že rozhraní tunelu (například tunnel.20 - 10.64.5.2/32 a tunnel.30 - 10.64.6.2/32) na Palo Alto mohou dosáhnout IP adresy CloudSimple Private Cloud přes VPN site-to-site. V následující konfiguraci naleznete ID proxy serveru.
* Profil. Vyberte profil monitoru.

Karta Proxy ID: Klikněte na **IPv4** > **Přidat** a nakonfigurovat následující:

* ID proxy serveru. Zadejte libovolný název zajímavého provozu. V jednom tunelu IPsec může být více proxy ID.
* Místní. Zadejte místní místní podsítě, které mohou komunikovat s podsítěmi Privátního cloudu prostřednictvím sítě VPN site-to-site.
* Vzdálené. Zadejte vzdálené podsítě Privátního cloudu, které mohou komunikovat s místními podsítěmi.
* Protokol. Vyberte **libovolnou**možnost .

Opakujte předchozí kroky k vytvoření jiného tunelu IPsec, který se použije pro sekundární partner skály CloudSimple VPN.

## <a name="references"></a>Odkazy

Konfigurace NAT na Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Průvodce konfigurací řady Cisco ASA 5500</a>

Podporované atributy IKEv1 a IKEv2 ve společnosti Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Průvodce konfigurací CLI řady Cisco ASA</a>

Konfigurace sítě VPN mezi lokalitami protokolu IPsec ve verzi CISCO ASA ve verzi 8.4 a novějších:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Konfigurace tunelů IKEv1 IPsec Site-to-Site pomocí asdm nebo cli na ASA</a>

Konfigurace virtuálního zařízení Cisco Adaptive Security Appliance (ASAv) v Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Cisco Adaptive Security Virtual Appliance (ASAv) průvodce rychlým startem</a>

Konfigurace sítě VPN site-to-site s Proxy ID na Palo Alto:

[Nastavit síť VPN podle webu](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Nastavení monitorování tunelového propojení:

[Nastavit monitorování tunelového propojení](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Operace tunelového propojení brány IKE nebo IPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Povolit nebo zakázat, aktualizovat nebo restartovat bránu IKE nebo tunelové propojení IPsec</a>
