---
title: Koncept – integrace nasazení řešení Azure VMware v architektuře hub a paprsků
description: Přečtěte si o doporučeních pro integraci nasazení řešení Azure VMware do existující nebo nové architektury hub a paprsků v Azure.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: bfd0da4f03eedaf215ddb55facffc2296a9d0b85
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579891"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrace řešení Azure VMware v architektuře hub a paprsků

V tomto článku poskytujeme doporučení pro integraci nasazení řešení Azure VMware do existující nebo nové [architektury hub a paprsků](/azure/architecture/reference-architectures/hybrid-networking/shared-services) v Azure. 

Scénář centra a paprsků předpokládá hybridní cloudové prostředí s úlohami na:

* Nativní Azure s využitím služeb IaaS nebo PaaS
* Azure VMware Solution 
* místní vSphere

## <a name="architecture"></a>Architektura

*Centrum* je Virtual Network Azure, která funguje jako centrální bod připojení k místnímu a privátnímu cloudu řešení Azure VMware. *Paprsky* jsou virtuální sítě, které jsou v partnerském vztahu s rozbočovačem a umožňují komunikaci mezi virtuálními sítěmi.

Přenosy dat mezi místním datacentrem, privátním cloudem řešení Azure VMware a centrem procházejí prostřednictvím připojení Azure ExpressRoute. Paprskové virtuální sítě obvykle obsahují úlohy založené na IaaS, ale můžou mít PaaS služby, jako je [App Service Environment](../app-service/environment/intro.md), které mají přímou integraci s Virtual Network nebo jiné PaaS služby s povoleným [privátním propojením Azure](../private-link/index.yml) .

Diagram znázorňuje příklad nasazení centra a paprsků v Azure připojeného k místnímu a řešení Azure VMware prostřednictvím ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Nasazení integrace centra řešení a paprsků Azure VMware" border="false":::

Architektura má následující hlavní součásti:

-   **Místní lokalita:** Místní datacentrum (y) zákazníka připojené k Azure prostřednictvím připojení ExpressRoute

-   **Privátní cloud řešení Azure VMware:** Řešení Azure VMware SDDC vytvořené jedním nebo několika clustery vSphere, přičemž každý z nich má maximálně 16 uzlů.

-   **ExpressRoute brána:** Umožňuje komunikaci mezi privátním cloudem řešení Azure VMware, sdílenými službami v centrální virtuální síti a úlohami spuštěnými ve virtuálních sítích s paprsky.

-   **ExpressRoute Global REACH:** Umožňuje připojení mezi místním prostředím a privátním cloudem řešení Azure VMware.


  > [!NOTE]
  > **Předpoklady S2S VPN:** Pro nasazení v produkčním prostředí Azure VMware se Azure S2S VPN nepodporuje kvůli požadavkům sítě pro VMware HCX. Dá se ale použít pro nasazení podle ověření koncepce.


-   **Virtuální síť centra:** Funguje jako centrální bod připojení k vaší místní síti a privátnímu cloudu řešení Azure VMware.

-   **Virtuální síť paprsků**

    -   **IaaS paprsk:** IaaS uzel hostuje úlohy založené na Azure IaaS, včetně skupin dostupnosti virtuálních počítačů a virtuálních počítačů s měřítkem, a odpovídajících síťových komponent.

    -   **PaaS paprsk:** PaaS uzel hostuje služby Azure PaaS pomocí privátního [koncového bodu s privátním koncovým bodem](../private-link/private-endpoint-overview.md) a [soukromým odkazem](../private-link/private-link-overview.md).

-   **Azure firewall:** Funguje jako centrální kus pro segmentování provozu mezi paprsky a řešením Azure VMware.

-   **Application Gateway:** Zpřístupňuje a chrání webové aplikace, které běží na Azure IaaS/PaaS nebo virtuálních počítačích řešení Azure VMware. Integruje se s dalšími službami, jako je API Management.

## <a name="network-and-security-considerations"></a>Otázky k síti a zabezpečení

Připojení ExpressRoute umožňují tok dat do toku mezi místním prostředím, řešením Azure VMware a síťovými prostředky infrastruktury Azure. Řešení Azure VMware používá [Global REACH ExpressRoute](../expressroute/expressroute-global-reach.md) k implementaci tohoto připojení.

Vzhledem k tomu, že brána ExpressRoute neposkytuje tranzitivní směrování mezi připojenými okruhy, místní připojení musí ke komunikaci mezi místním prostředím vSphere a řešením Azure VMware použít taky ExpressRoute Global Reach. 

* **Tok provozu řešení VMware z místního prostředí do Azure**

  :::image type="content" source="media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Nasazení integrace centra řešení a paprsků Azure VMware" border="false":::


* **Tok provozu řešení Azure VMware do centra přenosů virtuální sítě**

  :::image type="content" source="media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Nasazení integrace centra řešení a paprsků Azure VMware" border="false":::


Další podrobnosti o sítích řešení Azure VMware a konceptech připojení najdete v dokumentaci k [produktu řešení Azure VMware](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentace provozu

[Azure firewall](../firewall/index.yml) je střední část topologie hub a paprsků, která je nasazená ve virtuální síti rozbočovače. Pomocí Azure Firewall nebo jiného síťového virtuálního zařízení s podporou Azure navažte pravidla provozu a segmentujte komunikaci mezi různými paprsky a úlohami řešení Azure VMware.

Vytvořte směrovací tabulky a nasměrujte provoz na Azure Firewall.  U virtuálních sítí paprsků vytvořte trasu, která nastaví výchozí trasu k internímu rozhraní Azure Firewall. Tento způsob, kdy zatížení v Virtual Network potřebuje přístup k adresnímu prostoru řešení Azure VMware, ho může Brána firewall vyhodnotit a použít odpovídající pravidlo provozu pro povolení nebo zamítnutí.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Nasazení integrace centra řešení a paprsků Azure VMware" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> Trasa s předponou adresy 0.0.0.0/0 není v nastavení **GatewaySubnet** podporována.

Nastavte trasy pro konkrétní sítě v odpovídající směrovací tabulce. Například trasy pro přístup ke správě řešení Azure VMware a úlohám předpony IP adres z úloh paprsků a naopak.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Nasazení integrace centra řešení a paprsků Azure VMware" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Druhá úroveň segmentace přenosu dat pomocí skupin zabezpečení sítě v Paprskech a v centru pro vytváření podrobnějších přenosových zásad.

> [!NOTE]
> **Řešení provozu z místního prostředí do Azure VMware:** Provoz mezi místními úlohami, ať už vSphere nebo jinými, je povolený Global Reach, ale přenos neprojde Azure Firewall na rozbočovači. V tomto scénáři musíte implementovat mechanismy segmentace provozu buď místně, nebo v řešení Azure VMware.

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway V1 a v2 byly testovány pomocí webových aplikací, které běží na virtuálních počítačích řešení VMware Azure jako fond back-end. Application Gateway je aktuálně jedinou podporovanou metodou pro vystavení webových aplikací běžících na virtuálních počítačích řešení VMware Azure na internetu. Můžou aplikace bezpečně zveřejnit i pro interní uživatele.

Podrobnosti a požadavky najdete v článku týkajícím se řešení Azure VMware pro [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md) .

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Nasazení integrace centra řešení a paprsků Azure VMware" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox a Azure bastionu

Přístup k prostředí řešení Azure VMware pomocí JumpBox, což je virtuální počítač s Windows 10 nebo Windows Server nasazený v podsíti sdílené služby v rámci centrální virtuální sítě.

Z hlediska zabezpečení je nejvhodnější nasadit službu [Microsoft Azure bastionu](../bastion/index.yml) v rámci virtuální sítě rozbočovače. Azure bastionu poskytuje bezproblémové připojení RDP a SSH k virtuálním počítačům nasazeným v Azure bez nutnosti zřídit pro tyto prostředky veřejné IP adresy. Po zřízení služby Azure bastionu můžete k vybranému virtuálnímu počítači přistupovat z Azure Portal. Po navázání připojení se otevře nová karta, kde se zobrazí plocha JumpBox a z této plochy můžete získat přístup k rovině správy privátního cloudu řešení Azure VMware.

> [!IMPORTANT]
> Neposkytněte virtuálnímu počítači s JumpBox veřejnou IP adresu nebo zveřejňujte port 3389/TCP veřejnému Internetu. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Nasazení integrace centra řešení a paprsků Azure VMware" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS – požadavky na řešení

Pro řešení Azure DNS jsou k dispozici dvě možnosti:

-   Použijte řadiče domény Azure Active Directory (Azure AD) nasazené v centru (popsané v tématu [požadavky na identitu](#identity-considerations)) jako názvové servery.

-   Nasaďte a nakonfigurujte Azure DNS privátní zóna.

Nejlepším způsobem, jak zajistit spolehlivé rozlišení názvů pro řešení Azure VMware, místní a Azure, je kombinovat.

Jako obecné doporučení k návrhu použijte stávající infrastrukturu Azure DNS (v tomto případě službu DNS integrovanou službou Active Directory) nasazenou do aspoň dvou virtuálních počítačů Azure nasazených ve virtuální síti centrální sítě a nakonfigurované ve virtuálních sítích s paprsky tak, aby používaly servery Azure DNS v nastavení DNS.

Azure Privátní DNS se pořád používá tam, kde je zóna Azure Privátní DNS propojená s virtuálními sítěmi a servery DNS se používají jako hybridní překladače s podmíněným předáváním do místního nebo Azure VMware, které používají DNS s využitím zákaznické infrastruktury Azure Privátní DNS.

Je potřeba vzít v úvahu několik důležitých Azure DNS privátních zón:

* Automatická registrace by měla být povolená pro Azure DNS k automatické správě životního cyklu záznamů DNS pro virtuální počítače nasazené v rámci virtuálních sítí s paprsky.
* Maximální počet privátních zón DNS, ke kterým může být virtuální síť propojená, je povolená Automatická registrace jenom jedna.
* Maximální počet privátních zón DNS, ke kterým může být virtuální síť propojená, je 1000 bez povolení automatické registrace.

Místní a servery řešení Azure VMware je možné nakonfigurovat s použitím podmíněného serveru pro překládání na virtuální počítače v Azure pro zónu Azure Privátní DNS.

## <a name="identity-considerations"></a>Požadavky na identitu

Pro účely identity je nejlepší přístup k nasazení aspoň jednoho řadiče domény služby Active Directory v centru pomocí podsítě sdílené služby, která je v ideálním případě dvě v rámci distribuované zóny nebo skupiny dostupnosti virtuálního počítače. Další informace najdete v tématu [cetrum architektury Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain) pro rozšíření místní domény AD do Azure.

Navíc můžete nasadit jiný řadič domény na straně řešení Azure VMware, aby fungoval jako identita a zdroj DNS v prostředí vSphere.

Jako osvědčený postup doporučujeme integrovat [doménu AD s Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
