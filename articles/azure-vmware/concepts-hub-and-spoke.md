---
title: Koncept – integrace nasazení řešení Azure VMware v architektuře hub a paprsků
description: Přečtěte si o integraci nasazení řešení Azure VMware v architektuře hub a paprsků v Azure.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 0d511c8d6a96ffb6fa666bcb7c989764f398bdc9
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901381"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrace řešení Azure VMware v architektuře hub a paprsků

Tento článek poskytuje doporučení pro integraci nasazení řešení Azure VMware do existující nebo nové [architektury hub a paprsků](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) v Azure. 


Scénář centra a paprsků předpokládá hybridní cloudové prostředí s úlohami na:

* Nativní Azure s využitím služeb IaaS nebo PaaS
* Azure VMware Solution 
* místní vSphere

## <a name="architecture"></a>Architektura

*Centrum* je Virtual Network Azure, která funguje jako centrální bod připojení k místnímu a privátnímu cloudu řešení Azure VMware. *Paprsky* jsou virtuální sítě, které jsou v partnerském vztahu s rozbočovačem a umožňují komunikaci mezi virtuálními sítěmi.

Přenosy dat mezi místním datacentrem, privátním cloudem řešení Azure VMware a centrem procházejí prostřednictvím připojení Azure ExpressRoute. Paprskové virtuální sítě obvykle obsahují úlohy založené na IaaS, ale můžou mít PaaS služby, jako je [App Service Environment](../app-service/environment/intro.md), které mají přímou integraci s Virtual Network nebo jiné PaaS služby s povoleným [privátním propojením Azure](../private-link/index.yml) .

>[!IMPORTANT]
>Existující bránu ExpressRoute můžete použít pro připojení k řešení Azure VMware, pokud nepřekračuje limit čtyř ExpressRoute okruhů na jednu virtuální síť.  Pro přístup k řešení Azure VMware z místního prostředí prostřednictvím ExpressRoute ale musíte mít ExpressRoute Global Reach, protože brána ExpressRoute neposkytuje přechodné směrování mezi připojenými okruhy.

Diagram znázorňuje příklad nasazení centra a paprsků v Azure připojeného k místnímu a řešení Azure VMware prostřednictvím ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Nasazení integrace centra řešení a paprsků Azure VMware" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

Architektura má následující hlavní součásti:

- **Místní lokalita:** Místní datacentrum (y) zákazníka připojené k Azure prostřednictvím připojení ExpressRoute

- **Privátní cloud řešení Azure VMware:** Řešení Azure VMware SDDC vytvořené jedním nebo několika clustery vSphere, přičemž každý z nich má maximálně 16 hostitelů.

- **ExpressRoute brána:** Umožňuje komunikaci mezi privátním cloudem řešení Azure VMware, sdílenými službami v centrální virtuální síti a úlohami spuštěnými ve virtuálních sítích s paprsky.

- **ExpressRoute Global REACH:** Umožňuje připojení mezi místním prostředím a privátním cloudem řešení Azure VMware. Připojení mezi řešením Azure VMware a prostředky infrastruktury Azure se provádí jenom ExpressRoute Global Reach. Nemůžete vybrat žádnou možnost kromě rychlé cesty ExpressRoute.  ExpressRoute Direct není podporován.


- **Předpoklady S2S VPN:** Pro nasazení v produkčním prostředí Azure VMware se Azure S2S VPN nepodporuje kvůli požadavkům sítě pro VMware HCX. Můžete ho ale použít pro nasazení podle ověření koncepce.


- **Virtuální síť centra:** Funguje jako centrální bod připojení k vaší místní síti a privátnímu cloudu řešení Azure VMware.

- **Virtuální síť paprsků**

    - **IaaS paprsk:** IaaS uzel hostuje úlohy založené na Azure IaaS, včetně skupin dostupnosti virtuálních počítačů a virtuálních počítačů s měřítkem, a odpovídajících síťových komponent.

    - **PaaS paprsk:** PaaS uzel hostuje služby Azure PaaS pomocí privátního [koncového bodu s privátním koncovým bodem](../private-link/private-endpoint-overview.md) a [soukromým odkazem](../private-link/private-link-overview.md).

- **Azure firewall:** Funguje jako centrální kus pro segmentování provozu mezi paprsky a řešením Azure VMware.

- **Application Gateway:** Zpřístupňuje a chrání webové aplikace, které běží na Azure IaaS/PaaS nebo virtuálních počítačích řešení Azure VMware. Integruje se s dalšími službami, jako je API Management.

## <a name="network-and-security-considerations"></a>Otázky k síti a zabezpečení

Připojení ExpressRoute umožňují tok dat do toku mezi místním prostředím, řešením Azure VMware a síťovými prostředky infrastruktury Azure. Řešení Azure VMware používá [Global REACH ExpressRoute](../expressroute/expressroute-global-reach.md) k implementaci tohoto připojení.

Vzhledem k tomu, že brána ExpressRoute neposkytuje tranzitivní směrování mezi připojenými okruhy, místní připojení musí ke komunikaci mezi místním prostředím vSphere a řešením Azure VMware použít taky ExpressRoute Global Reach. 

* **Tok provozu řešení VMware z místního prostředí do Azure**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Tok provozu řešení VMware z místního prostředí do Azure" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Tok provozu řešení Azure VMware do centra přenosů virtuální sítě**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Tok provozu řešení Azure VMware do centra přenosů dat virtuální sítě" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Další podrobnosti o sítích řešení Azure VMware a konceptech připojení najdete v dokumentaci k [produktu řešení Azure VMware](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentace provozu

[Azure firewall](../firewall/index.yml) je centrální část topologie rozbočovače a paprsků, která je nasazená ve virtuální síti rozbočovače. Pomocí Azure Firewall nebo jiného síťového virtuálního zařízení s podporou Azure navažte pravidla provozu a segmentujte komunikaci mezi různými paprsky a úlohami řešení Azure VMware.

Vytvořte směrovací tabulky a nasměrujte provoz na Azure Firewall.  Pro virtuální sítě paprsků vytvořte trasu, která nastaví výchozí trasu na interní rozhraní Azure Firewall. Tímto způsobem platí, že pokud je potřeba, aby zatížení v Virtual Network dosáhlo adresního prostoru řešení Azure VMware, firewall ho může vyhodnotit a použít odpovídající přenosové pravidlo, aby ho povolil nebo zakázal.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Vytvoření směrovacích tabulek pro směrování provozu do Azure Firewall" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> Trasa s předponou adresy 0.0.0.0/0 není v nastavení **GatewaySubnet** podporována.

Nastavte trasy pro konkrétní sítě v odpovídající směrovací tabulce. Například trasy pro přístup ke správě řešení Azure VMware a úlohám z úloh paprsků a jiným způsobem.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Nastavte trasy pro konkrétní sítě v odpovídající tabulce směrování." lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Druhá úroveň segmentace přenosu dat pomocí skupin zabezpečení sítě v Paprskech a v centru pro vytváření podrobnějších přenosových zásad.

> [!NOTE]
> **Řešení provozu z místního prostředí do Azure VMware:** Provoz mezi místními úlohami, ať už vSphere nebo jinými, je povolený Global Reach, ale přenos neprojde Azure Firewall na rozbočovači. V tomto scénáři musíte implementovat mechanismy segmentace provozu, a to buď místně, nebo v řešení Azure VMware.

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway V1 a v2 byly testovány pomocí webových aplikací, které běží na virtuálních počítačích řešení VMware Azure jako fond back-end. Application Gateway je aktuálně jedinou podporovanou metodou pro vystavení webových aplikací běžících na virtuálních počítačích řešení VMware Azure na internetu. Můžou aplikace bezpečně zveřejnit i pro interní uživatele.

Podrobnosti a požadavky najdete v článku týkajícím se [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md) pro řešení Azure VMware.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Druhá úroveň segmentace provozu pomocí skupin zabezpečení sítě" border="false":::


### <a name="jump-box-and-azure-bastion"></a>Pole pro přesun a Azure bastionu

Přístup k prostředí řešení Azure VMware pomocí pole pro skok, což je virtuální počítač s Windows 10 nebo Windows Server nasazený v podsíti sdílené služby ve virtuální síti centrální sítě.

>[!IMPORTANT]
>Služba Azure bastionu se doporučuje připojit k poli s odkazem, abyste zabránili odhalení řešení Azure VMware na Internet. Azure bastionu se nedá použít pro připojení k virtuálním počítačům řešení VMware Azure, protože se nejedná o objekty Azure IaaS.  

Z hlediska zabezpečení je nejvhodnější nasadit službu [Microsoft Azure bastionu](../bastion/index.yml) v rámci virtuální sítě rozbočovače. Azure bastionu poskytuje bezproblémové připojení RDP a SSH k virtuálním počítačům nasazeným v Azure bez nutnosti zřídit pro tyto prostředky veřejné IP adresy. Po zřízení služby Azure bastionu můžete k vybranému virtuálnímu počítači přistupovat z Azure Portal. Po navázání připojení se otevře nová karta, kde se zobrazí plocha rozevíracího seznamu, a z této plochy můžete získat přístup k rovině správy privátního cloudu řešení Azure VMware.

> [!IMPORTANT]
> Nepřiřazujte veřejnou IP adresu virtuálnímu počítači s odkazem, nebo zveřejňujte port 3389/TCP veřejnému Internetu. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Virtuální síť Azure bastionu hub" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS – požadavky na řešení

Pro Azure DNS rozlišení jsou k dispozici dvě možnosti:

-   Použijte řadiče domény nasazené na rozbočovači (popsané v tématu [požadavky na identitu](#identity-considerations)) jako názvové servery.

-   Nasaďte a nakonfigurujte Azure DNS privátní zóna.

Nejlepším způsobem, jak zajistit spolehlivé rozlišení názvů pro řešení Azure VMware, místní a Azure, je kombinovat.

Jako obecné doporučení k návrhu použijte stávající infrastrukturu Azure DNS (v tomto případě službu DNS integrovanou službou Active Directory) nasazenou do aspoň dvou virtuálních počítačů Azure nasazených ve virtuální síti centrální sítě a nakonfigurované ve virtuálních sítích s paprsky tak, aby používaly servery Azure DNS v nastavení DNS.

Můžete použít Azure Privátní DNS, kde se zóna Azure Privátní DNS odkazuje na virtuální síť.  Servery DNS se používají jako hybridní překladače s podmíněným předáváním do místního prostředí nebo řešení Azure VMware se systémem DNS pomocí zákaznické infrastruktury Azure Privátní DNS. 

Pokud chcete automaticky spravovat životní cyklus záznamů DNS pro virtuální počítače nasazené v rámci virtuálních sítí paprsků, povolte automatickou registraci. Pokud je tato možnost povolená, je maximální počet privátních zón DNS jenom jeden. Pokud je tato hodnota zakázaná, pak je maximální počet 1000.

Místní a servery řešení Azure VMware je možné nakonfigurovat s použitím podmíněného serveru pro překládání na virtuální počítače v Azure pro zónu Azure Privátní DNS.

## <a name="identity-considerations"></a>Požadavky na identitu

Pro účely identity je nejlepším přístupem nasazení aspoň jednoho řadiče domény v centru. Použijte dvě podsítě sdílené služby v rámci distribuované zóny nebo skupiny dostupnosti virtuálních počítačů. Další informace o rozšíření místní domény služby Active Directory (AD) do Azure najdete v tématu [cetrum architektury Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

Navíc můžete nasadit jiný řadič domény na straně řešení Azure VMware, aby fungoval jako identita a zdroj DNS v prostředí vSphere.

Jako osvědčený postup doporučujeme integrovat [doménu AD s Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
