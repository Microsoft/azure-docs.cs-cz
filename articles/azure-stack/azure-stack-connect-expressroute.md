---
title: Připojení k Azure pomocí ExpressRoute Azure Stack
description: Zjistěte, jak propojit virtuální sítě ve službě Azure Stack k virtuálním sítím v Azure pomocí ExpressRoute.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/22/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: ee247dcadd7ba8574bbdae1e25b0e39ca1ebc2da
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246224"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Připojení k Azure pomocí Azure ExpressRoute Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek ukazuje, jak připojit virtuální síť Azure Stack ke službě Azure virtual network pomocí [Microsoft Azure ExpressRoute](/azure/expressroute/) přímé připojení.

Můžete použít v tomto článku o kurz a příklady použít k nastavení stejné testovací prostředí. Nebo můžete použít článek jako návod, který vás provede nastavením prostředí ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Přehled, předpoklady a požadavky

Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení, získáte ho od poskytovatele připojení. ExpressRoute není připojení k síti VPN přes veřejný internet.

Další informace o Azure ExpressRoute najdete v tématu [přehled ExpressRoute](../expressroute/expressroute-introduction.md).

### <a name="assumptions"></a>Předpoklady

Tento článek předpokládá, že:

* Máte praktické znalosti Azure.
* Máte základní znalosti o Azure Stack.
* Máte základní znalosti o sítě.

### <a name="prerequisites"></a>Požadavky

Pro připojení služby Azure Stack a Azure pomocí ExpressRoute, musí splňovat následující požadavky:

* Zřízenou [okruh ExpressRoute](../expressroute/expressroute-circuit-peerings.md) prostřednictvím [poskytovatele připojení](../expressroute/expressroute-locations.md).
* Předplatné Azure k vytvoření okruhu ExpressRoute a virtuálními sítěmi v Azure.
* Směrovač, který musí:
  * Podpora připojení Site-to-Site VPN mezi jeho rozhraní LAN a víceklientská brána Azure Stack.
  * Podpora vytváření více VRFs (virtuální směrování a předávání), pokud existuje více než jednoho tenanta ve vašem nasazení služby Azure Stack.
* Směrovač, který obsahuje:
  * Port sítě WAN připojení k okruhu ExpressRoute.
  * Portu LAN připojení k Azure Stack víceklientskou bránu.

### <a name="expressroute-network-architecture"></a>Architektura sítě ExpressRoute

Následující diagram znázorňuje Azure Stack a Azure prostředí po dokončení nastavení ExpressRoute pomocí příkladů v tomto článku:

*Obrázek 1. Síť ExpressRoute*

![Síť ExpressRoute](media/azure-stack-connect-expressroute/Conceptual.png)

Následující diagram znázorňuje, jak více tenantů připojení z infrastruktury služby Azure Stack prostřednictvím ExpressRoute směrovače do Azure na hraničních zařízeních společnosti Microsoft:

*Obrázek 2. Připojení více tenantů*

![Víceklientská připojení s ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

V příkladu v tomto článku používá pro připojení služby Azure Stack k Azure pomocí soukromého partnerského vztahu ExpressRoute stejné víceklientské architektury, které je znázorněno na obrázku 2. Připojení se provádí pomocí připojení site-to-site VPN z brány virtuální sítě ve službě Azure Stack směrovač ExpressRoute.

Kroky v tomto článku ukazují, jak vytvořit připojení k začátku do konce mezi dvěma virtuálními sítěmi ze dvou různých tenantech ve službě Azure Stack k odpovídajícím virtuálním sítím v Azure. Nastavení příkladu jsou dva tenanti je volitelná. Tyto kroky můžete také použít pro jednoho tenanta.

## <a name="configure-azure-stack"></a>Konfigurace služby Azure Stack

K nastavení prostředí Azure Stack pro první tenanta, postupujte podle kroků v následujícím diagramu a jako vodítko. Pokud nastavujete více než jednoho tenanta, opakujte tyto kroky:

>[!NOTE]
>Tyto kroky ukazují, jak vytvořit prostředky na portálu Azure Stack, ale můžete použít také prostředí PowerShell.

![Nastavení sítě Azure Stack](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Před zahájením

Předtím, než se pustíte do konfigurace služby Azure Stack, budete potřebovat:

* Při nasazování služby Azure Stack integrované systému nebo nasazení služby Azure Stack Development Kit (ASDK). Informace o nasazení ASDK, najdete v článku [rychlý úvod k nasazení Azure Stack Development Kit](azure-stack-deploy-overview.md).
* Nabídky ve službě Azure Stack, která vaši uživatelé mohou přihlásit k odběru. Další informace najdete v tématu [plány, nabídky a předplatné](azure-stack-plan-offer-quota-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Vytvoření síťových prostředků ve službě Azure Stack

Použijte následující postupy k vytvoření požadovaných síťových prostředků ve službě Azure Stack pro tenanta.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů

1. Přihlaste se k portálu user portal s účtem uživatele (tenant).

2. Na portálu vyberte **+ vytvořit prostředek**.

3. V části **Azure Marketplace**vyberte **sítě**.

4. V části **doporučené**vyberte **virtuální síť**.

5. V části **vytvořit virtuální síť**, hodnoty uvedené v následující tabulce do příslušných polí zadejte:

   |Pole  |Hodnota  |
   |---------|---------|
   |Název     |Tenant1VNet1         |
   |Adresní prostor     |10.1.0.0/16|
   |Název podsítě     |Tenant1 Sub1|
   |Rozsah adres podsítě     |10.1.1.0/24|

6. By měla zobrazovat předplatné, které jste vytvořili dřív v **předplatné** pole. Pro zbývající pole:

    * V části **skupiny prostředků**vyberte **vytvořit nový** a vytvořte novou skupinu prostředků nebo pokud ještě nemáte, vyberte **použít existující**.
    * Ověření výchozího **umístění**.
    * Klikněte na možnost **Vytvořit**.
    * (Volitelné) Klikněte na tlačítko **připnout na řídicí panel**.

#### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. V části **virtuální síť**vyberte **Tenant1VNet1**.
1. V části **NASTAVENÍ** vyberte **Podsítě**.
1. Vyberte **+ podsíť brány** přidat podsíť brány k virtuální síti.
1. Ve výchozím nastavení je název této podsítě nastavený na **GatewaySubnet**. Podsítě brány představují zvláštní případ a musí používat tento název fungovat správně.
1. Ověřte, že **rozsah adres** je **10.1.0.0/24**.
1. Klikněte na tlačítko **OK** vytvořit podsíť brány.

#### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Prostřednictvím uživatelského portálu služby Azure Stack, klikněte na tlačítko **+ vytvořit prostředek**.
1. V části **Azure Marketplace**vyberte **sítě**.
1. V seznamu síťových prostředků vyberte **bránu virtuální sítě**.
1. V **název** zadejte **GW1**.
1. Vyberte **Virtuální síť**.
1. Vyberte **Tenant1VNet1** z rozevíracího seznamu.
1. Vyberte **veřejnou IP adresu**, pak **zvolte veřejnou IP adresu**a potom klikněte na tlačítko **vytvořit nový**.
1. V **název** zadejte **GW1-PiP**a potom klikněte na tlačítko **OK**.
1. Jako **Typ VPN** by ve výchozím nastavení měla být vybraná možnost **Založená na trasách**. Toto nastavení zachovejte.
1. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Klikněte na možnost **Vytvořit**.

#### <a name="create-the-local-network-gateway"></a>Vytvoření brány místní sítě

Prostředku brány místní sítě určuje vzdálenou bránu na druhém konci připojení k síti VPN. V tomto příkladu je vzdáleným koncem připojení LAN podrozhraní ExpressRoute směrovače. Pro Tenanta 1, je znázorněno na obrázku 2 je Vzdálená adresa 10.60.3.255.

1. Přihlaste se k portálu user portal Azure Stack s vaším uživatelským účtem a vyberte **+ vytvořit prostředek**.
1. V části **Azure Marketplace**vyberte **sítě**.
1. V seznamu prostředků vyberte **bránu místní sítě**.
1. V **název** zadejte **ER-směrovač-GW**.
1. Pro **IP adresu** pole, viz obrázek 2. IP adresa podrozhraní ExpressRoute směrovače sítě LAN pro Tenanta 1 je 10.60.3.255. Pro konkrétní prostředí zadejte IP adresu směrovače odpovídající rozhraní.
1. V **adresní prostor** zadejte adresní prostor virtuální sítě, kterou chcete připojit v Azure. Podsítě pro Tenanta 1 v *obrázek 2* jsou následující:

   * 192.168.2.0/24 je centrum, virtuální síť v Azure.
   * 10.100.0.0/16 je paprsku virtuální síť v Azure.

   > [!IMPORTANT]
   > Tento příklad předpokládá, že používáte statické trasy pro připojení VPN Site-to-Site mezi bránou Azure Stack a ExpressRoute směrovače.

1. Ověřte, že vaše **předplatné**, **skupiny prostředků**, a **umístění** jsou správné. Poté klikněte na **Vytvořit**.

#### <a name="create-the-connection"></a>Vytvoření připojení

1. Prostřednictvím uživatelského portálu služby Azure Stack, vyberte **+ vytvořit prostředek**.
1. V části **Azure Marketplace**vyberte **sítě**.
1. V seznamu prostředků vyberte **Připojení**.
1. V části **Základy**, zvolte **Site-to-site (IPSec)** jako **typ připojení**.
1. Vyberte **předplatné**, **skupiny prostředků**, a **umístění**. Klikněte na **OK**.
1. V části **nastavení**vyberte **Brána virtuální sítě**a pak vyberte **GW1**.
1. Vyberte **bránu místní sítě**a pak vyberte **ER směrovače GW**.
1. V **název připojení** zadejte **ConnectToAzure**.
1. V **sdílený klíč (PSK)** zadejte **abc123** a pak vyberte **OK**.
1. V části **Souhrn**vyberte **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Získejte virtuální síť veřejné IP adresy brány

Po vytvoření brány virtuální sítě můžete získat veřejnou IP adresu brány. Poznamenejte si tuto adresu v případě, že ho budete potřebovat později pro vaše nasazení. V závislosti na nasazení, tato adresa slouží jako **interní IP adresa**.

1. Prostřednictvím uživatelského portálu služby Azure Stack, vyberte **všechny prostředky**.
1. V části **všechny prostředky**, vybrat bránu virtuální sítě, která je **GW1** v příkladu.
1. V části **Brána virtuální sítě**vyberte **přehled** ze seznamu prostředků. Alternativně můžete vybrat **vlastnosti**.
1. IP adresa, kterou chcete mějte na paměti, je uvedený v části **veřejnou IP adresu**. Tato adresa je pro příklad konfigurace 192.68.102.1.

#### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vyzkoušejte přenosy dat prostřednictvím připojení VPN, budete potřebovat virtuální počítače pro odesílání a přijímání dat ve virtuální síti Azure Stack. Vytvoření virtuálního počítače a nasaďte ji k podsíti virtuálních počítačů pro vaši virtuální síť.

1. Prostřednictvím uživatelského portálu služby Azure Stack, vyberte **+ vytvořit prostředek**.
1. V části **Azure Marketplace**vyberte **Compute**.
1. Vyberte v seznamu imagí virtuálních počítačů **systému Windows Server 2016 Datacenter Eval** bitové kopie.

   >[!NOTE]
   >Pokud obrázek použitý pro účely tohoto článku není k dispozici, požádejte vašeho operátory Azure stacku chcete zadat jinou image Windows serveru.

1. V **vytvořit virtuální počítač**vyberte **Základy**, zadejte **VM01** jako **název**.
1. Zadejte platné uživatelské jméno a heslo. Tento účet použijete pro přihlášení k virtuálnímu počítači po jeho vytvoření.
1. Zadejte **předplatné**, **skupiny prostředků**a **umístění**. Vyberte **OK**.
1. V části **zvolte velikost**, vyberte velikost virtuálního počítače pro tuto instanci a pak vyberte **vyberte**.
1. V části **nastavení**, ujistěte se, že:

   * Virtuální síť je **Tenant1VNet1**.
   * Že podsíť je nastavená na **10.1.1.0/24**.

   Použít výchozí nastavení a klikněte na tlačítko **OK**.

1. V části **Souhrn**, zkontrolujte konfiguraci virtuálních počítačů a potom klikněte na tlačítko **OK**.

Chcete-li přidat více tenantů, opakujte kroky, které jste udělali v těchto částech:

* [Vytvoření virtuální sítě a podsítě virtuálních počítačů](#create-the-virtual-network-and-vm-subnet)
* [Vytvoření podsítě brány](#create-the-gateway-subnet)
* [Vytvořte bránu virtuální sítě](#create-the-virtual-network-gateway)
* [Vytvořte bránu místní sítě](#create-the-local-network-gateway)
* [Vytvoření připojení](#create-the-connection)
* [Vytvoření virtuálního počítače](#create-a-virtual-machine)

Pokud používáte klienta 2 jako příklad, nezapomeňte změnit IP adres, aby se překrývají.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Konfigurace virtuálního počítače NAT pro přecházení přes bránu

> [!IMPORTANT]
> Tato část se týká pouze nasazení Azure Stack Development Kit (ASDK). Překlad síťových adres není potřeba pro nasazení na víc uzlů.

Azure Stack Development Kit je samostatné a izolované od sítě, ve kterém je nasazený fyzický hostitel. Síť virtuálních IP adres, které jsou brány připojené k není externí, je skrytá za směrovačem, který provádí překlad síťových adres (NAT).

Tímto směrovačem je virtuální počítač (AzS-BGPNAT01) Windows serveru spuštěná role Služba Směrování a vzdálený přístup (RRAS). Na virtuálním počítači povolit připojení VPN site-to-site pro připojení na obou koncích AzS-BGPNAT01 musíte nakonfigurovat překladu adres.

#### <a name="configure-the-nat"></a>Konfigurace zařízení NAT.

1. Přihlaste se k Azure Stack hostitelském počítači pomocí účtu správce.
1. Zkopírujte a upravte následující skript prostředí PowerShell. Nahraďte `"your administrator password"` se heslo správce a pak spusťte skript v se zvýšenými oprávněními ISE Powershellu. Tento skript vrátí vaše **adresu externího BGPNAT**.

   ```PowerShell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. Pokud chcete nakonfigurovat překlad síťových adres, zkopírujte a upravte následující skript prostředí PowerShell. Upravit skript, který chcete nahradit `'External BGPNAT address'` a `'Internal IP address'` pomocí následujícího příkladu:

   * Pro *adresu externího BGPNAT* použít 10.10.0.62
   * Pro *interní IP adresa* použít 192.168.102.1

   Spusťte následující skript z se zvýšenými oprávněními prostředí PowerShell ISE:

   ```PowerShell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

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
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
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

Po dokončení konfigurace služby Azure Stack, můžete nasadit prostředky Azure. Následující obrázek znázorňuje příklad virtuální síti tenanta v Azure. Pro vaši virtuální síť v Azure můžete použít libovolný název a schéma adresování. Rozsah adres virtuální sítě v Azure a Azure Stackem však musí být jedinečný a nesmí se překrývat.

*Obrázek 3. Virtuální sítě Azure*

![Virtuální sítě Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Prostředky, které nasazují v Azure jsou podobné prostředky, které jste nasadili ve službě Azure Stack. Budete nasazovat následující komponenty:

* Virtuální sítě a podsítě
* Podsíť brány
* Bránu virtuální sítě
* Připojení
* Okruh ExpressRoute

Azure síťovou infrastrukturu příklad je nakonfigurován takto:

* Standardní centra (192.168.2.0/24) a model virtuální sítě paprsků (10.100.0.0./16). Další informace o síťové topologie centra s paprsky, naleznete v tématu [implementace síťové topologie centra s rameny v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* Úlohy se nasazují do virtuální sítě paprsků a okruh ExpressRoute je připojen k virtuální síti centra.
* Dvě virtuální sítě jsou propojené pomocí VNet peering.

### <a name="configure-the-azure-vnets"></a>Konfigurace virtuálních sítí Azure

1. Přihlaste se k webu Azure portal pomocí přihlašovacích údajů Azure.
1. Vytvoření pomocí rozsahu adres 192.168.2.0/24 virtuální síti centra.
1. Vytvořit podsíť je rozsah adres 192.168.2.0/25 použitím a přidejte podsíť brány s použitím 192.168.2.128/27 rozsah adres.
1. Vytvoření paprsků virtuální síť a podsíť s použitím 10.100.0.0/16 rozsah adres.

Další informace o vytváření virtuálních sítí v Azure najdete v tématu [vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Nakonfigurujte okruh ExpressRoute

1. Zkontrolujte požadavky ExpressRoute v [ExpressRoute požadavky a kontrolní seznam](../expressroute/expressroute-prerequisites.md).

1. Postupujte podle kroků v [vytvoření a úprava okruhu ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) vytvoření okruhu ExpressRoute pomocí svého předplatného Azure.

   >[!NOTE]
   >Zadejte klíč služby pro váš okruh ke službě, můžete nastavit váš okruh ExpressRoute na jeho konci.

1. Postupujte podle kroků v [vytvořit a upravit partnerský vztah pro okruh ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) nakonfigurovat soukromý partnerský vztah v okruhu ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

Postupujte podle kroků v [konfigurace brány virtuální sítě pro ExpressRoute přes PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) vytvořit bránu virtuální sítě pro ExpressRoute ve virtuální síti centra.

### <a name="create-the-connection"></a>Vytvoření připojení

K propojení okruhu ExpressRoute k virtuální síti centra, postupujte podle kroků v [připojení virtuální sítě k okruhu ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Vytvoření partnerského vztahu virtuálních sítí

Vytvoření partnerského vztahu centra a virtuální sítě pomocí postupu v paprsků [vytvoření partnerského vztahu virtuálních sítí pomocí webu Azure portal](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Při konfiguraci partnerského vztahu virtuální sítě, ujistěte se, že pomocí následujících možností:

* V centru do paprsku **povolit průchod bránou**.
* Z paprsku k rozbočovači **používat vzdálenou bránu**.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Úlohy virtuálních počítačů nasaďte do virtuální sítě paprsků.

Tento postup opakujte pro všechny další tenanta virtuální sítě, které chcete připojit v Azure prostřednictvím jejich odpovídajících okruhy ExpressRoute.

## <a name="configure-the-router"></a>Konfigurace směrovače

Následující diagram konfigurace směrovače ExpressRoute můžete použít jako vodítko pro konfiguraci ExpressRoute směrovače. Tento diagram znázorňuje dvě klientů (Tenant 1 a 2 Tenanta) s jejich odpovídajících okruhy ExpressRoute. Každý tenant je propojen s vlastní VRF (virtuální směrování a předávání) v části ExpressRoute směrovače LAN a WAN. Tato konfigurace zajišťuje začátku do konce izolaci mezi dvěma klienty. Poznamenejte si IP adresy používané v rozhraní směrovačů, jak budete postupovat podle příklad konfigurace.

*Obrázek 4. Konfigurace směrovače pro ExpressRoute*

![Konfigurace směrovače pro ExpressRoute](media/azure-stack-connect-expressroute/EndToEnd.png)

Můžete použít libovolný směrovač, který podporuje IKEv2 VPN a protokol BGP k ukončení připojení site-to-site VPN z Azure Stack. Stejné směrovač slouží k připojení k Azure prostřednictvím okruhu ExpressRoute.

Následující příklad konfigurace Cisco Azure Site Recovery 1000 řady agregace služby směrovače podporuje infrastrukturu sítě je znázorněno *konfigurace směrovače pro ExpressRoute* diagramu.

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
 description PRIMARY ExpressRoute Link to AZURE over Equinix
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
 description BACKUP ExpressRoute Link to AZURE over Equinix
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

Po navázání připojení site-to-site a okruh ExpressRoute, vyzkoušejte připojení.

Proveďte následující testy:

* Přihlaste se k jednomu z virtuálních počítačů ve vaší virtuální síti Azure a odešlete zprávu ping virtuální počítač, který jste vytvořili ve službě Azure Stack.
* Přihlaste se k jednomu z virtuálních počítačů, které jste vytvořili v Azure stacku a ping virtuální počítač, který jste vytvořili ve virtuální síti Azure.

>[!NOTE]
>Pokud chcete mít jistotu, že odesíláte přenosy přes připojení ExpressRoute a Site-to-Site, musí příkaz ping použijte vyhrazenou IP (DIP) adresu virtuálního počítače na obou koncích a nikoli virtuální IP adresy virtuálního počítače.

### <a name="allow-icmp-in-through-the-firewall"></a>Povolit protokol ICMP v přes bránu firewall

Ve výchozím nastavení Windows Server 2016 nepovoluje příchozí pakety ICMP přes bránu firewall. Pro každý virtuální počítač, který používáte pro testy musíte také povolit příchozí ICMP pakety. Chcete-li vytvořit pravidlo brány firewall pro protokol ICMP, spusťte následující rutinu v okně PowerShell se zvýšenými oprávněními:

```PowerShell
# Create ICMP firewall rule.
New-NetFirewallRule `
  –DisplayName “Allow ICMPv4-In” `
  –Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Příkaz ping virtuálnímu počítači Azure Stack

1. Přihlaste se k portálu user portal Azure Stack pomocí účtu tenanta.

1. Vyhledejte virtuální počítače, který jste vytvořili a vyberte virtuální počítač.

1. Vyberte **Connect** (Připojit).

1. Windows PowerShell nebo příkazového řádku se zvýšenými oprávněními, zadejte **ipconfig/all**. Poznamenejte si IPv4 adresu vrácenou ve výstupu.

1. Odešlete zprávu ping na adresu IPv4 z virtuálního počítače ve virtuální síti Azure.

   V ukázkovém prostředí je adresa IPv4 z podsítě 10.1.1.x/24. Ve vašem prostředí adresa se může lišit, ale musí být v podsíti, ve které jste vytvořili pro podsíť virtuální sítě tenanta.

### <a name="view-data-transfer-statistics"></a>Zobrazení statistiky přenosu dat

Pokud chcete vědět, kolik přenos prochází přes připojení, najdete tyto informace na portálu pro uživatele Azure stacku. Toto je také vhodný způsob, jak zjistit, jestli se nepovedlo ping zkušebních dat prostřednictvím připojení VPN a ExpressRoute:

1. Přihlaste se k portálu user portal Azure Stack pomocí účtu tenanta a vyberte **všechny prostředky**.
1. Přejděte do skupiny prostředků pro bránu VPN a vyberte **připojení** typ objektu.
1. Vyberte **ConnectToAzure** připojení ze seznamu.
1. V části **připojení**>**přehled**, se zobrazují statistiky pro **Data v** a **výstupní Data**. Měli byste vidět některé nenulové hodnoty.

   ![Data a výstupní Data](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Další postup

[Nasazení aplikací do Azure a Azure Stack](azure-stack-solution-pipeline.md)
