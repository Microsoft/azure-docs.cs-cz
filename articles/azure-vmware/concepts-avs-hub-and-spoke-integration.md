---
title: Koncept – integrace nasazení řešení Azure VMware (AVS) do architektury hub a paprsků
description: Přečtěte si o doporučeních pro integraci nasazení řešení Azure VMware (AVS) v existující nebo nové architektuře hub a paprsků v Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 82937e04fc0a5101c353702b92b6b068d027d7ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85375033"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>Integrace řešení Azure VMware (AVS) v architektuře hub a paprsků

V tomto článku poskytujeme doporučení pro integraci nasazení řešení Azure VMware (AVS) do existující nebo nové [architektury hub a paprsků](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services) v Azure. 

Scénář centra a paprsků předpokládá hybridní cloudové prostředí s úlohami na:

* Nativní Azure s využitím služeb IaaS nebo PaaS
* FUNKCI 
* místní vSphere

## <a name="architecture"></a>Architektura

*Centrum* je Virtual Network Azure, která funguje jako centrální bod připojení k místnímu a síťovému privátnímu cloudu. *Paprsky* jsou virtuální sítě, které jsou v partnerském vztahu s rozbočovačem a umožňují komunikaci mezi virtuálními sítěmi.

Přenosy dat mezi místním datacentrem, privátním cloudem AVS a centrem procházejí ExpressRoute připojeními. Paprskové virtuální sítě obvykle obsahují úlohy založené na IaaS, ale můžou mít PaaS služby, jako je [App Service Environment](../app-service/environment/intro.md), které mají přímou integraci s Virtual Network nebo jiné PaaS služby s povoleným [privátním propojením Azure](https://docs.microsoft.com/azure/private-link/) . 

Diagram znázorňuje příklad nasazení centra a paprsků v Azure připojeného k místním prostředím a funkci AVS přes ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Nasazení integrace z rozbočovače a paprsků v prostředí AVS":::




Architektura má následující hlavní součásti:

-   **Místní lokalita:** Místní datacentra zákazníka připojené k Azure prostřednictvím připojení Express Route.

-   **Privátní cloud pro funkci AVS:** Služba AVS SDDC vytvořená jedním nebo více clustery vSphere, přičemž každý z nich má maximálně 16 uzlů.

-   **ExpressRoute brána:** Umožňuje komunikaci mezi privátním cloudem služby AVS, místní sítí, sdílenými službami v centrální virtuální síti a úlohami spuštěnými ve virtuálních sítích s paprsky.

    > [!NOTE]
    > **Předpoklady S2S VPN:** U produkčních nasazení služby AVS není Azure S2S podporován z důvodu požadavků na síť pro HCX. Pro nasazení ověření koncepce nebo neprodukční nasazení, které nevyžaduje HCX, je ale možné ho použít.

-   **Virtuální síť centra:** Funguje jako centrální bod připojení k místní síti a privátnímu cloudu služby AVS.

-   **Virtuální síť paprsků**

    -   **IaaS paprsk:** IaaS paprsk bude hostovat úlohy založené na Azure IaaS, včetně sad dostupnosti virtuálních počítačů a virtuálních počítačů s měřítkem, a odpovídajících síťových komponent.

    -   **PaaS paprsk:** PaaS uzel hostuje služby Azure PaaS pomocí privátního [koncového bodu s privátním koncovým bodem](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) a [soukromým odkazem](https://docs.microsoft.com/azure/private-link/private-link-overview).

-   **Azure firewall:** Slouží jako centrální kus pro segmentování provozu mezi paprsky, Prem a AVS.

-   **Application Gateway:** Zveřejňuje a chrání webové aplikace, které běží na virtuálních počítačích Azure IaaS/PaaS nebo AVS (VM). Integruje se s dalšími službami, jako je API Management.

## <a name="network-and-security-considerations"></a>Otázky k síti a zabezpečení

Připojení ExpressRoute umožňují provoz do toku mezi místními, službou AVS a prostředky infrastruktury sítě Azure. K implementaci tohoto připojení používá službu AVS [Global REACH ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) .

K místnímu připojení můžou ExpressRoute Global Reach také použít, ale není to povinné.

* **Tok přenosů z místního prostředí do prostředí AVS**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Tok přenosů z místního prostředí do prostředí AVS":::


* **Tok přenosů mezi VIRTUÁLNÍmi sítěmi ze sítě do rozbočovače**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Tok přenosů ze síťové komunikace ze sítě na rozbočovač":::


Další podrobnosti o funkci AVS Networking a vzájemné propojení koncepty najdete v [dokumentaci k produktu služby AVS](https://docs.microsoft.com/azure/azure-vmware/concepts-networking).

### <a name="traffic-segmentation"></a>Segmentace provozu

[Azure firewall](https://docs.microsoft.com/azure/firewall/) je střední část topologie hub a paprsků, která je nasazená ve virtuální síti rozbočovače. Pomocí Azure Firewall nebo jiného síťového virtuálního zařízení s podporou Azure navažte pravidla provozu a segmentujte komunikaci mezi různými paprsky, místními procesy a úlohami služby AVS.

Vytvořte směrovací tabulky a nasměrujte provoz na Azure Firewall.  U virtuálních sítí paprsků vytvořte trasu, která nastaví výchozí trasu na vnitřní rozhraní Azure Firewall. tímto způsobem se v případě, že zatížení v Virtual Network potřebuje spojit s adresním prostorem služby AVS, může ji brána firewall vyhodnotit a použít odpovídající pravidlo provozu pro povolení nebo zamítnutí.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Vytvoření směrovacích tabulek pro směrování provozu do Azure Firewall":::


> [!IMPORTANT]
> Trasa s předponou adresy 0.0.0.0/0 není v nastavení **GatewaySubnet** podporována.

Nastavte trasy pro konkrétní sítě v odpovídající směrovací tabulce. Například trasy, které mají přístup ke službě AVS Management a úlohám IP, z místních a naopak, směrují veškerý provoz z místního prostředí do služby AVS privátní Cloud prostřednictvím Azure Firewall.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Nastavte trasy pro konkrétní sítě v odpovídající tabulce směrování.":::

Druhá úroveň segmentace přenosu dat pomocí skupin zabezpečení sítě v Paprskech a v centru pro vytváření podrobnějších přenosových zásad. 


### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway V1 a v2 byly testovány pomocí webových aplikací, které běží na virtuálních počítačích služby AVS jako back-end fond. Application Gateway je aktuálně jedinou podporovanou metodou pro vystavení webových aplikací běžících na virtuálních počítačích se službou AVS na Internet. Můžou aplikace bezpečně zveřejnit i pro interní uživatele.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Druhá úroveň segmentace provozu pomocí skupin zabezpečení sítě":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox a Azure bastionu

Přístup k prostředí služby AVS pomocí JumpBox, což je virtuální počítač s Windows 10 nebo Windows Server nasazený v podsíti sdílené služby ve virtuální síti centrální sítě.

Z hlediska zabezpečení je nejvhodnější nasadit službu [Microsoft Azure bastionu](https://docs.microsoft.com/azure/bastion/) v rámci virtuální sítě rozbočovače. Azure bastionu poskytuje bezproblémové připojení RDP a SSH k virtuálním počítačům nasazeným v Azure bez nutnosti zřídit pro tyto prostředky veřejné IP adresy. Po zřízení služby Azure bastionu můžete k vybranému virtuálnímu počítači přistupovat z Azure Portal. Po navázání připojení se otevře nová karta, kde se zobrazí plocha JumpBox a z této plochy můžete získat přístup k rovině správy privátního cloudu služby AVS.

> [!IMPORTANT]
> Neposkytněte virtuálnímu počítači s JumpBox veřejnou IP adresu nebo zveřejňujte port 3389/TCP veřejnému Internetu. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Virtuální síť Azure bastionu hub":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS – požadavky na řešení

Pro řešení Azure DNS jsou k dispozici dvě možnosti:

-   Použijte řadiče domény Azure Active Directory (Azure AD) nasazené v centru (popsané v tématu [požadavky na identitu](#identity-considerations)) jako názvové servery.

-   Nasaďte a nakonfigurujte Azure DNS privátní zóna.

Nejlepším řešením je sloučit obojí a zajistit spolehlivé rozlišení názvů pro funkci AVS, místní i Azure.

Jako obecné doporučení k návrhu použijte stávající infrastrukturu Azure DNS (v tomto případě službu DNS integrovanou službou Active Directory) nasazenou do aspoň dvou virtuálních počítačů Azure nasazených ve virtuální síti centrální sítě a nakonfigurované ve virtuálních sítích s paprsky tak, aby používaly servery Azure DNS v nastavení DNS.

Azure Privátní DNS se pořád používá tam, kde je zóna Azure Privátní DNS propojená s virtuálními sítěmi a servery DNS se používají jako hybridní překladače s podmíněným předáváním do místních/AVS se spuštěných názvů DNS s využitím zákaznické infrastruktury Azure Privátní DNS.

Je potřeba vzít v úvahu několik důležitých Azure DNS privátních zón:

* Automatická registrace by měla být povolená pro Azure DNS k automatické správě životního cyklu záznamů DNS pro virtuální počítače nasazené v rámci virtuálních sítí s paprsky.
* Maximální počet privátních zón DNS, ke kterým může být virtuální síť propojená, je povolená Automatická registrace jenom jedna.
* Maximální počet privátních zón DNS, ke kterým může být virtuální síť propojená, je 1000 bez povolení automatické registrace.

Místní a službu AVS servery je možné nakonfigurovat s použitím podmíněného serveru pro překládání na virtuální počítače v Azure pro zónu Azure Privátní DNS.

## <a name="identity-considerations"></a>Požadavky na identitu

Pro účely identity je nejlepší přístup k nasazení aspoň jednoho řadiče domény služby Active Directory v centru pomocí podsítě sdílené služby, která je v ideálním případě dvě v rámci distribuované zóny nebo skupiny dostupnosti virtuálního počítače. Další informace najdete v tématu [cetrum architektury Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain) pro rozšíření místní domény AD do Azure.

Navíc můžete nasadit další řadič domény na straně služby AVS, který bude fungovat jako identita a zdroj DNS v prostředí vSphere.

V případě vCenter a jednotného přihlašování nastavte zdroj identity v Azure Portal pro **správu \> \> zdrojů identity identity**.

Jako osvědčený postup doporučujeme integrovat [doménu AD s Azure Active Directory](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: https://docs.microsoft.com/azure/architecture/

[Hub & Spoke topology]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: https://docs.microsoft.com/azure/networking/

<!-- LINKS - internal -->


