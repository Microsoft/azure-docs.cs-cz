---
title: "Připojit zásobník Azure do Azure pomocí služby ExpressRoute"
description: "Postup připojení virtuální sítě v Azure zásobníku virtuálních sítí v Azure pomocí ExpressRoute."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 544fc1bcc9212fd38938d58447f5050df2a08796
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Připojit zásobník Azure do Azure pomocí služby ExpressRoute

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Existují dvě podporované metody pro připojení virtuálních sítí v Azure zásobníku virtuálních sítí v Azure:
   * **Site-to-Site**

     Připojení k síti VPN prostřednictvím protokolu IPsec (IKE v1 a IKE v2). Tento typ připojení vyžaduje zařízení VPN nebo službu RRAS. Další informace najdete v tématu [připojit zásobník Azure do Azure pomocí sítě VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     Přímé připojení k Azure z vašeho nasazení Azure zásobníku. ExpressRoute je **není** připojení VPN prostřednictvím veřejného Internetu. Další informace o Azure ExpressRoute najdete v tématu [přehled ExpressRoute](../expressroute/expressroute-introduction.md).

Tento článek ukazuje příklad pomocí ExpressRoute připojit zásobník Azure do Azure.
## <a name="requirements"></a>Požadavky
Tady jsou konkrétní požadavky na připojení zásobník Azure a Azure pomocí ExpressRoute:
* Předplatné Azure k vytvoření okruhu ExpressRoute a virtuálních sítí v Azure.
* A zřídit okruh ExpressRoute prostřednictvím [poskytovatele připojení](../expressroute/expressroute-locations.md).
* Směrovač, který je připojen k jeho WAN porty okruh ExpressRoute.
* Na straně LAN směrovače se propojí víceklientské bráně Azure zásobníku.
* Směrovači musí podporovat připojení Site-to-Site VPN mezi jeho rozhraní LAN a Azure zásobníku víceklientské brány.
* Pokud ve vašem nasazení zásobník Azure přidáte více než jednoho klienta, musí být možné vytvořit více VRFs (virtuální směrování a předávání) směrovači.

Následující diagram znázorňuje koncepční sítě zobrazení po dokončení konfigurace:

![Koncepční diagram](media/azure-stack-connect-expressroute/Conceptual.png)

**Diagram 1**

Následující diagram architektury ukazuje, jak více klientů připojení z infrastruktury zásobník Azure prostřednictvím ExpressRoute směrovače k Azure v Microsoft edge:

![Diagram architektury](media/azure-stack-connect-expressroute/Architecture.png)

**Diagram 2**

Z příkladu v tomto článku používá stejnou architekturu pro připojení k Azure prostřednictvím soukromého partnerského vztahu ExpressRoute. Se provádí pomocí připojení Site-to-Site VPN z brány virtuální sítě v Azure zásobníku do směrovače ExpressRoute. Následující kroky v tomto článku ukazují, jak vytvořit připojení klient server mezi oběma virtuálními sítěmi z dva různé klienty v zásobníku Azure jejich odpovídajících virtuálních sítí v Azure. Můžete přidat, protože mnoho klienta virtuálních sítí a replikaci podle kroků pro každého klienta nebo použijte tento příklad k nasazení jednoho klienta virtuální sítě.

## <a name="configure-azure-stack"></a>Konfigurace Azure zásobníku
Nyní můžete vytvořit z prostředků, které je nutné nastavit prostředí zásobníku Azure jako klient. Následující kroky popisují, co musíte udělat. Tyto pokyny ukazují, jak vytvářet prostředky pomocí portálu Azure zásobníku, ale můžete také pomocí prostředí PowerShell.

![Kroky síťových prostředků](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Než začnete
Před zahájením konfigurace, potřebujete:
* Nasazení služby Azure zásobníku.

   Informace o nasazení Azure zásobníku Development Kit najdete v tématu [rychlý start Azure zásobníku Development Kit nasazení](azure-stack-deploy-overview.md).
* Nabídku v zásobníku Azure, který uživatel může přihlásit k odběru.

  Pokyny najdete v tématu [virtuální počítače zpřístupnit uživatelům Azure zásobníku](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Vytvoření síťovým prostředkům v Azure zásobníku

Chcete-li vytvořit k požadovaným síťovým zdrojům v zásobníku Azure pro každého klienta pomocí následujících postupů:

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů
1. Přihlaste se na portálu pro uživatele s účtem uživatele (klientů).

2. Na portálu, klikněte na tlačítko **nový**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. V nabídce Marketplace vyberte **Síť**.

4. V nabídce klikněte na **Virtuální síť**.

5. Zadejte hodnoty do příslušných polí v následující tabulce:

   |Pole  |Hodnota  |
   |---------|---------|
   |Název     |Tenant1VNet1         |
   |Adresní prostor     |10.1.0.0/16|
   |Název podsítě     |Tenant1-Sub1|
   |Rozsah adres podsítě     |10.1.1.0/24|

6. V poli **Předplatné** by se mělo zobrazovat předplatné, které jste vytvořili dřív.

    a. Pro skupinu prostředků, můžete buď vytvořit skupinu prostředků nebo pokud již účet máte, vyberte **použít existující**.

    b. Ověřte výchozí umístění.

    c. Klikněte na tlačítko **připnout na řídicí panel**.

    d. Klikněte na možnost **Vytvořit**.



#### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány
1. Otevřete prostředek virtuální sítě vytvořené (Tenant1VNet1) z řídicího panelu.
2. V části Nastavení vyberte **podsítě**.
3. Kliknutím na **Podsíť brány** přidejte k virtuální síti podsíť brány.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. Ve výchozím nastavení je název této podsítě nastavený na **GatewaySubnet**.
   Podsítě brány jsou speciální a musí mít tento konkrétní název, aby fungovaly správně.
5. V **rozsahu adres** pole, ověřte adresu **10.1.0.0/24**.
6. Klikněte na tlačítko **OK** vytvořit podsíť brány.

#### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě
1. V portálu user portal zásobník Azure, klikněte na tlačítko **nový**.
   
2. V nabídce Marketplace vyberte **Síť**.
3. V seznamu síťových prostředků vyberte **bránu virtuální sítě**.
4. Do pole **Název** zadejte **GW1**.
5. Pro výběr virtuální sítě klikněte na položku **Virtuální síť**.
   Vyberte **Tenant1VNet1** ze seznamu.
6. Klikněte na položku nabídky **Veřejná IP adresa**. Když **zvolte veřejnou IP adresu** části otevře, klikněte na tlačítko **vytvořit nový**.
7. Do pole **Název** zadejte **GW1-PiP** a klikněte na **OK**.
8. Jako **Typ VPN** by ve výchozím nastavení měla být vybraná možnost **Založená na trasách**.
    Toto nastavení zachovejte.
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Pokud chcete, můžete připnout prostředků na řídicí panel. Klikněte na možnost **Vytvořit**.

#### <a name="create-the-local-network-gateway"></a>Vytvoření brány místní sítě

Účelem prostředku brány místní sítě je k označení služby Brána vzdálené na druhém konci připojení k síti VPN. V tomto příkladu je na vzdálené straně podrozhraní LAN směrovače ExpressRoute. Pro klienta 1 v tomto příkladu Vzdálená adresa je 10.60.3.255, jak znázorňuje obrázek 2.

1. Přihlaste se k fyzickému počítači Azure Stack.
2. Přihlaste se k portálu user portal s vaším uživatelským účtem a klikněte na tlačítko **nový**.
3. V nabídce Marketplace vyberte **Síť**.
4. V seznamu prostředků vyberte **bránu místní sítě**.
5. V **název** pole typu **ER-směrovač-GW**.
6. Pro **IP adresu** pole, použijte Diagram 2. IP adresa směrovače ExpressRoute LAN podrozhraní pro klienta 1 je 10.60.3.255. Pro vlastní prostředí zadejte IP adresu odpovídající rozhraní směrovače.
7. V **adresní prostor** zadejte adresní prostor virtuální sítě, které se chcete připojit v Azure. Tento příklad najdete v části Diagram 2. Pro klienta 1, Všimněte si, že požadovaná podsítě jsou **192.168.2.0/24** (to je rozbočovače virtuální sítě v Azure) a **10.100.0.0/16** (to je příčkou virtuální sítě v Azure). Zadejte odpovídající podsítě pro vaše vlastní prostředí.
   > [!IMPORTANT]
   > Tento příklad předpokládá, že používáte statické trasy pro připojení Site-to-Site VPN mezi Azure zásobníku bránu a směrovač ExpressRoute.

8. Ověřte, že vaše **předplatné**, **skupiny prostředků**, a **umístění** správně a klikněte na tlačítko **vytvořit**.

#### <a name="create-the-connection"></a>Vytvoření připojení
1. V portálu user portal zásobník Azure, klikněte na tlačítko **nový**.
2. V nabídce Marketplace vyberte **Síť**.
3. V seznamu prostředků vyberte **Připojení**.
4. V **Základy** v oddílu nastavení, vyberte **Site-to-site (IPSec)** jako **typ připojení**.
5. Vyberte **předplatné**, **skupiny prostředků**, a **umístění** a klikněte na tlačítko **OK**.
6. V **nastavení** klikněte na tlačítko **Brána virtuální sítě** klikněte na tlačítko **GW1**.
7. Klikněte na tlačítko **brány místní sítě**a klikněte na tlačítko **ER směrovač GW**.
8. V **název připojení** zadejte **ConnectToAzure**.
9. V **sdílený klíč (PSK)** zadejte **abc123** a klikněte na tlačítko **OK**.
10. Na **Souhrn** klikněte na tlačítko **OK**.

    Po vytvoření připojení, uvidíte veřejnou IP adresu použitou bránou virtuální sítě. Pokud chcete vyhledat adresu na portálu Azure zásobníku, vyhledejte bránu virtuální sítě. V **přehled**, Najít **veřejnou IP adresu**. Poznámka: Tato adresa; Použijete jej jako *interní IP adresu* v další části (Pokud je k dispozici pro vaše nasazení).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Pro ověření dat při přenosu prostřednictvím připojení VPN, musíte virtuální počítače k odesílání a příjmu dat ve virtuální síti Azure zásobníku. Vytvoření virtuálního počítače teď a umístí jej v podsíť virtuálních počítačů ve virtuální síti.

1. V portálu user portal zásobník Azure, klikněte na tlačítko **nový**.
2. V nabídce Marketplace vyberte **Virtual Machines**.
3. Vyberte v seznamu bitové kopie virtuálních počítačů, **Windows Server 2016 Datacenter Eval** bitovou kopii a klikněte na tlačítko **vytvořit**.
4. Na **Základy** v části **název** pole typu **VM01**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet použijete pro přihlášení k virtuálnímu počítači, až se vytvoří.
6. Zadejte **předplatné**, **skupiny prostředků**, a **umístění** a pak klikněte na **OK**.
7. Na **velikost** části, klikněte na velikost virtuálního počítače pro tuto instanci a pak klikněte na tlačítko **vyberte**.
8. Na **nastavení** části můžete přijmout výchozí hodnoty. Ale zajistit vybranou virtuální síť **Tenant1VNet1** a podsíť je nastavena na **10.1.1.0/24**. Klikněte na **OK**.
9. Zkontrolujte nastavení na **Souhrn** části a klikněte na tlačítko **OK**.

Pro každého klienta se chcete připojit virtuální síť, opakujte předchozí kroky z **vytvořit virtuální síť a podsíť virtuálních počítačů** prostřednictvím **vytvoření virtuálního počítače** oddíly.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Nakonfigurujte virtuální počítač NAT pro bránu traversal
> [!IMPORTANT]
> Tato část se týká jenom nasazení Azure zásobníku Development Kit. Překlad síťových adres není vyžadován pro nasazení na víc uzlů.

Azure zásobníku Development Kit je úplný a samostatný a izolované od sítě, na kterém je nasazený na fyzickém hostiteli. Ano síť "Externí" VIP, která brány jsou připojené k není externí, ale místo toho je skrytý za směrovačem provádění překlad adres (NAT).
 
Směrovači je virtuálního počítače s Windows serverem (**AzS BGPNAT01**) spuštěná směrování a služby vzdálený přístup (RRAS) role v infrastruktuře Azure zásobníku Development Kit. NAT musíte nakonfigurovat na virtuálním počítači AzS BGPNAT01 umožňující připojení Site-to-Site VPN na obou koncích připojení.

#### <a name="configure-the-nat"></a>Nakonfigurujte zařízení NAT.

1. Přihlaste se k Azure zásobníku fyzický počítač pomocí účtu správce.
2. Zkopírujte a upravte následující skript prostředí PowerShell a spusťte v zvýšenými ISE Windows PowerShell. Nahraďte heslo správce. Vrácená adresu vaší *BGPNAT externí adresu*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. Ke konfiguraci překladu síťových adres, zkopírujte a upravte následující skript prostředí PowerShell a spusťte v zvýšenými ISE Windows PowerShell. Upravte skript, který chcete nahradit *BGPNAT externí adresu* a *interní IP adresu* (který jste si poznamenali dříve v **vytvoření připojení** části).

   V příkladu diagramy *adresu externího BGPNAT* je 10.10.0.62 a *interní IP adresu* je 192.168.102.1.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Konfigurace Azure
Teď, když jste dokončili konfigurace protokolů Azure, můžete nasadit některé prostředky Azure. Následující diagram znázorňuje klienta ukázka virtuální sítě v Azure. Můžete použít libovolný název a schéma adresování pro vaši virtuální síť v Azure. Rozsah adres virtuální sítě v Azure a Azure zásobníku však musí být jedinečné a není překrývat.

![Azure Vnets](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Diagram 3**

Prostředky, které můžete nasadit v Azure jsou podobné prostředky, které jste nasadili v zásobníku Azure. Podobně můžete nasadit:
* Virtuální sítě a podsítě
* Podsíť brány
* Brána virtuální sítě
* Připojení
* Okruh ExpressRoute

Příklad Azure síťové infrastruktury je nakonfigurovat následujícím způsobem:
* Standardní hub (192.168.2.0/24) a ramenem (10.100.0.0./16) modelu virtuální síť se používá.
* Úlohy jsou nasazeny v ramenem virtuální sítě a okruh ExpressRoute je připojené k rozbočovači virtuální sítě.
* Dvě virtuální sítě jsou propojeny pomocí funkce partnerského vztahu virtuální sítě.

### <a name="configure-vnets"></a>Konfigurace virtuální sítě
1. Přihlaste se k portálu Azure pomocí svých přihlašovacích údajů Azure.
2. Vytvoření centra pomocí 192.168.2.0/24 adresní prostor virtuální sítě. Vytvořte podsíť pomocí rozsah adres 192.168.2.0/25 a přidat podsíť brány pomocí 192.168.2.128/27 rozsah adres.
3. Vytvořte ramenem virtuální síť a podsíť pomocí zápisu 10.100.0.0/16 rozsahu adres.


Další informace o vytváření virtuálních sítí v Azure najdete v tématu [vytvořit virtuální síť](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Nakonfigurujte okruh ExpressRoute

1. Přečtěte si požadavky ExpressRoute v [ExpressRoute požadavky a kontrolní seznam](../expressroute/expressroute-prerequisites.md).
2. Postupujte podle kroků v [vytvoření a úprava okruhu ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) k vytvoření okruhu ExpressRoute pomocí vašeho předplatného Azure.
3. U svého poskytovatele/hostitel zřídit váš okruh ExpressRoute na jejich konci sdílet klíč služby z předchozího kroku.
4. Postupujte podle kroků v [vytvořit a upravit partnerský vztah pro okruh ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) nakonfigurovat soukromý partnerský vztah v okruhu ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

* Postupujte podle kroků v [nakonfigurovat bránu virtuální sítě pro ExpressRoute pomocí prostředí PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) vytvořit bránu virtuální sítě pro ExpressRoute v centru virtuální sítě.

### <a name="create-the-connection"></a>Vytvoření připojení

* Propojení okruhu ExpressRoute do centra virtuální sítě, postupujte podle kroků v [připojení virtuální sítě k okruhu ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Peer virtuální sítě

* Peer rozbočovače a příčkou virtuální sítě pomocí kroků v [vytvořit virtuální síť partnerský vztah pomocí portálu Azure](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Při konfiguraci virtuální sítě partnerský vztah, ujistěte se, že jste vybrali následující možnosti:
   * Z centra pro ramenem: **povolit brány přenosu**
   * Z ramenem do centra: **Brána vzdálené použití**

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

* Nasaďte virtuální počítače zatížení do ramenem virtuální sítě.

Tento postup opakujte pro všechny další klienta virtuální sítě se chcete připojit v Azure prostřednictvím jejich odpovídajících okruhy ExpressRoute.

## <a name="configure-the-router"></a>Nastavení směrovače

Následující diagram začátku do konce infrastruktury můžete Průvodce konfigurace směrovače ExpressRoute. Tento diagram zobrazuje dva klienty (klienta 1 a 2 klienta) s jejich příslušné okruhy Expressroute. Každý souvisí vlastní VRF (virtuální směrování a předávání) na straně LAN a WAN ExpressRoute směrovače k zajištění izolace začátku do konce mezi dvěma klienty. Všimněte si, používá rozhraní směrovače, jak budete postupovat podle ukázkové konfiguraci IP adresy.

![End na konec diagram](media/azure-stack-connect-expressroute/EndToEnd.png)

**Diagram 4**

Můžete použít všechny směrovač, který podporuje IKEv2 VPN a BGP ukončit připojení Site-to-Site VPN od Azure zásobníku. Stejné směrovač slouží k připojení do Azure pomocí okruhu ExpressRoute. 

Zde je ukázka konfigurace od 1000 Cisco automatické obnovení systému, který podporuje infrastrukturu rozhraní sítě znázorňuje ho obrázek 4:

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Otestování připojení

Vyzkoušejte připojení po navázání připojení Site-to-Site a ExpressRoute s okruhem expressroute. Tato úloha je jednoduché.  Přihlaste se do jednoho z virtuálních počítačů, které jste vytvořili ve vaší virtuální síti Azure a pomocí příkazu ping virtuálnímu počítači, který jste vytvořili v prostředí Azure zásobníku nebo naopak. 

K zajištění, že při odesílání provoz prostřednictvím připojení ExpressRoute a Site-to-Site, musíte odeslat příkaz ping vyhrazenou IP (DIP) adresu virtuálního počítače na obou koncích a ne VIP adresy virtuálního počítače. Ano je nutné vyhledat a poznamenejte si adresu na druhém konci připojení.

### <a name="allow-icmp-in-through-the-firewall"></a>Povolí protokol ICMP v přes bránu firewall
Ve výchozím nastavení Windows Server 2016 neumožňuje pakety protokolu ICMP v přes bránu firewall. Ano pro každý virtuální počítač, které můžete použít v testu, spusťte následující rutinu v okně Powershellu se zvýšenými oprávněními:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Příkaz ping virtuální počítač Azure zásobníku

1. Přihlaste se k portálu user portal zásobník Azure pomocí účtu klienta.
2. V levém navigačním panelu klikněte na **Virtual Machines**.
3. Najít virtuální počítač, který jste dříve vytvořili a klikněte na něj.
4. V části pro virtuální počítač, klikněte na tlačítko **Connect**.
5. Prostředí PowerShell se zvýšenými oprávněními otevřete okno a zadejte **ipconfig/all**.
6. Vyhledejte adresu IPv4 ve výstupu a poznamenejte si ho. Odeslat příkaz ping tuto adresu z virtuálního počítače ve virtuální síti Azure. V příkladu prostředí je adresu z podsítě 10.1.1.x/24. Ve vašem prostředí může být jinou adresu. Ale musí být v rámci podsítě, kterou jste vytvořili pro podsíť virtuální sítě klienta.


### <a name="view-data-transfer-statistics"></a>Statistika přenosu dat zobrazení

Pokud budete chtít vědět, kolik provozu je předávání přes připojení, můžete najít tyto informace v části připojení prostřednictvím uživatelského portálu Azure zásobníku. Tyto informace jsou také jiné dobrý způsob, jak ověřit, že příkazem ping, které jste poslali ve skutečnosti se prostřednictvím připojení VPN a ExpressRoute.

1. Přihlaste se k portálu user portal zásobníku Microsoft Azure pomocí svého účtu klienta.
2. Přejděte do skupiny prostředků, které bylo vytvořeno vaší brány sítě VPN a vyberte **připojení** typ objektu.
3. Klikněte **ConnectToAzure** připojení v seznamu.
4. Na **připojení** části lze zobrazit statistiku pro **Data v** a **Data**. Měly by se tady zobrazovat nenulové hodnoty.

   ![Data v Data](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Další postup
[Nasazení aplikací do Azure a Azure zásobníku](azure-stack-solution-pipeline.md)
