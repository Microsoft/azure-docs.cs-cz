---
title: Podrobný plán zabezpečení a dodržování předpisů Azure – IaaS webová aplikace pro Spojené království
description: Podrobný plán zabezpečení a dodržování předpisů Azure – IaaS webová aplikace pro Spojené království
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 602e4356ccd9eb45855462a7a25e0966dc176b4f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899942"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Podrobný plán zabezpečení a dodržování předpisů Azure – IaaS webová aplikace pro Spojené království

## <a name="overview"></a>Přehled

 Tento článek poskytuje pokyny a skripty pro automatizaci, které vám umožní doručovat Microsoft Azure trojrozměrné architektury založené na webu, která je vhodná pro zpracování mnoha úloh klasifikovaných jako oficiální ve Spojeném království.

 Pomocí infrastruktury jako přístupu ke kódu Sada šablon [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) nasadí prostředí, které se bude přizpůsobovat národním internetovým bezpečnostním střediskům (NCSC) [](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) 14 v oblasti cloudového zabezpečení a centrem pro Internet Security (CIS) [. Kritické ovládací prvky zabezpečení](https://www.cisecurity.org/critical-controls.cfm).

 NCSC doporučuje svým zákazníkům používat zásady zabezpečení cloudu k vyhodnocení vlastností zabezpečení služby a k pochopení rozdělení zodpovědnosti mezi zákazníkem a dodavatelem. Pro každý z těchto principů poskytujeme informace, které vám pomůžou pochopit rozdělení odpovědností.

 Tato architektura a odpovídající šablony Azure Resource Manager podporují Microsoft White Paper, [14 kontroly zabezpečení cloudu pro Velká Británie v cloudu pomocí Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). V tomto dokumentu najdete informace o tom, jak se služby Azure rovnají se zásadami cloudového zabezpečení Spojené království NCSC 14, což umožňuje organizacím rychle sledovat jejich schopnost plnit své povinnosti dodržování předpisů pomocí cloudových služeb globálně a na Velká Británie na Microsoft Azure cloudu.

 Tato šablona nasadí infrastrukturu pro úlohu. Je nutné nainstalovat a nakonfigurovat kód aplikace a podporu softwaru obchodní vrstvy a datové vrstvy. Podrobné pokyny k nasazení jsou k dispozici [zde](https://aka.ms/ukwebappblueprintrepo).

 Pokud předplatné Azure nemáte, můžete se zaregistrovat rychle a snadno – začít [s Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

 Šablony Azure poskytují architekturu webové aplikace se třemi vrstvami v cloudovém prostředí Azure, které podporuje oficiální úlohy Spojené království. Tato architektura zajišťuje zabezpečené hybridní prostředí, které rozšiřuje místní síť do Azure a umožňuje tak zabezpečený webovým úlohám, které využívají podnikoví uživatelé nebo Internet.

![IaaS webová aplikace pro Velká Británie – diagram pro oficiální referenční architekturu](images/ukofficial-iaaswa-architecture.png?raw=true "IaaS webová aplikace pro Velká Británie – diagram pro oficiální referenční architekturu")

 Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .

(1)/16 Virtual Network – provozní virtuální síť
- (3)/24 podsítí – 3 – úroveň (web, příčtení, data)
- (1)/27 podsíť – přidá
- (1)/27 podsíť brány
- (1)/29 podsíť-Application Gateway podsíť
- Používá výchozí DNS (poskytovaný Azure)
- Partnerský vztah povolený pro virtuální síť pro správu
- Skupina zabezpečení sítě (NSG) pro správu toku provozu

(1)/24 Virtual Network – virtuální síť pro správu
- (1)/27 podsítí
- Použití (2) přidá do Azure DNS záznamů DNS a (1).
- Partnerský vztah povolený pro provozní virtuální síť
- Skupina zabezpečení sítě (NSG) pro správu toku provozu

(1) Application Gateway
- WAF – povoleno
- Režim WAF – prevence
- Sada pravidel: OWASP 3,0
- Naslouchací proces HTTP na portu 80
- Připojení/provoz regulovaný prostřednictvím NSG
- Definováno koncovým bodem veřejné IP adresy (Azure)

(1) VPN-based – tunelové propojení sítě VPN sítě-2-site
- Definováno koncovým bodem veřejné IP adresy (Azure)
- Připojení/provoz regulovaný prostřednictvím NSG
- (1) brána místní sítě (místní koncový bod)
- (1) síťová brána Azure (koncový bod Azure)

(9) Virtual Machines – všechny virtuální počítače se nasazují s nastavením antimalwarového DSC Azure IaaS

- (2) Active Directory Domain Services řadičů domény (Windows Server 2012 R2)
  - (2) role serveru DNS – 1 na virtuální počítač
  - (2) síťové adaptéry připojené k provozní virtuální síti VNet-1 na virtuálním počítači
  - Obě domény jsou připojené k doméně definované v šabloně.
    - Doména vytvořená jako součást nasazení


- (1) virtuální počítač pro správu JumpBox (bastionu Host)
  - 1 síťová karta ve virtuální síti pro správu s veřejnou IP adresou
    - NSG se používá pro omezení provozu (v/v) na konkrétní zdroje.
  - Nepřipojeno k doméně


- (2) virtuální počítače webové vrstvy
  - (2) role serveru IIS – 1 na virtuální počítač
  - (2) síťové adaptéry připojené k provozní virtuální síti VNet-1 na virtuálním počítači
  - Nepřipojeno k doméně


- (2) virtuální počítače na úrovni
  - (2) síťové adaptéry připojené k provozní virtuální síti VNet-1 na virtuálním počítači
  - Nepřipojeno k doméně


- (2) virtuální počítače datové vrstvy
  - (2) síťové adaptéry připojené k provozní virtuální síti VNet-1 na virtuálním počítači
  - Nepřipojeno k doméně

Sady dostupnosti
- (1) Doména služby Active Directory virtuálního počítače set-2 virtuálních počítačů řadiče
- (1) sada virtuálních počítačů na webové vrstvě – 2 virtuální počítače
- (1) sada virtuálních počítačů pro virtuální počítače na úrovni: 2
- (1) virtuální počítače sady virtuálních počítačů – 2

Nástroj pro vyrovnávání zatížení
- (1) Load Balancer webové vrstvy
- (1) Load Balancer vrstvy
- (1) Load Balancer datové vrstvy

Storage
- (14) celkový počet účtů úložiště
  - Skupina dostupnosti kontroleru Doména služby Active Directory
    - (2) primární účty místně redundantního úložiště (LRS) – 1 pro každý virtuální počítač  
    - (1) účet místní redundantní úložiště (LRS) pro přidání skupiny dostupnosti
  - JumpBox Management VM
    - (1) primární účet místně redundantního úložiště (LRS) pro virtuální počítač JumpBox
    - (1) účet pro diagnostiku místně redundantního úložiště (LRS) pro virtuální počítač JumpBox
  - Virtuální počítače webové vrstvy
    - (2) primární účty místně redundantního úložiště (LRS) – 1 pro každý virtuální počítač  
    - (1) účet pro diagnostiku místně redundantního úložiště (LRS) pro skupinu dostupnosti webové vrstvy
  - Virtuální počítače vrstvy
    - (2) primární účty místně redundantního úložiště (LRS) – 1 pro každý virtuální počítač  
    - (1) účet pro diagnostiku místně redundantního úložiště (LRS) pro skupinu dostupnosti na úrovni paměti
  - Virtuální počítače datové vrstvy
    - (2) primární účty místně redundantního úložiště (LRS) – 1 pro každý virtuální počítač  
    - (1) účet pro diagnostiku místně redundantního úložiště (LRS) pro skupinu dostupnosti datové vrstvy

### <a name="deployment-architecture"></a>Architektura nasazení:

**Místní síť**: Soukromá místní síť implementovaná v organizaci.

**Virtuální síť**v produkčním prostředí: Produkční [virtuální síť](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (Virtual Network) hostuje aplikaci a další provozní prostředky běžící v Azure. Každá virtuální síť může obsahovat několik podsítí, které se používají k izolaci a správě síťového provozu.

**Webová vrstva**: Zpracovává příchozí požadavky HTTP. Odpovědi jsou vráceny prostřednictvím této vrstvy.

**Obchodní vrstva**: Implementuje obchodní procesy a další funkční logiku pro systém.

**Databázová vrstva**: Poskytuje trvalé úložiště dat pomocí [skupin dostupnosti Always On SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) pro zajištění vysoké dostupnosti. Zákazníci můžou použít [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) jako alternativu PaaS.

**Brána**: [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) poskytuje připojení mezi směrovači v místní síti a provozní virtuální sítí.

**Internetová brána a veřejná IP adresa**: Internetová brána zpřístupňuje aplikační služby uživatelům prostřednictvím Internetu. Provoz, který přistupuje k těmto službám, je zabezpečený pomocí [Application Gateway](../../application-gateway/overview.md) nabízí možnost směrování a vyrovnávání zatížení vrstvy 7 s ochranou firewallu webových aplikací (WAF).

**Virtuální síť pro správu**: Tato [virtuální síť](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) obsahuje prostředky, které implementují funkce správy a monitorování pro úlohy běžící v produkční virtuální síti.

**JumpBox**: Označuje se taky jako [hostitel bastionu](https://en.wikipedia.org/wiki/Bastion_host), což je zabezpečený virtuální počítač v síti, který správci používají pro připojení k virtuálním počítačům v produkční virtuální síti. Jumpbox má skupinu NSG, která umožňuje vzdálenou komunikaci pouze z jedné veřejné IP adresy na seznamu bezpečných adres. Pokud chcete povolit provoz vzdálené plochy (RDP), musí být zdroj provozu definovaný v NSG. Správa produkčních prostředků přes protokol RDP používá zabezpečený virtuální počítač s JumpBox.

**Trasy definované uživatelem**: [Trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) slouží k definování toku přenosů IP adres v rámci služby Azure virtuální sítě.

**Virtuální sítě s partnerským vztahem sítě**: Virtuální sítě pro produkční prostředí a správu se připojují pomocí [partnerského vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)virtuálních sítí.
Tyto virtuální sítě se pořád spravují jako samostatné prostředky, ale zobrazí se jako jedna pro všechny účely připojení pro tyto virtuální počítače. Tyto sítě spolu komunikují přímo pomocí privátních IP adres. Partnerský vztah virtuálních sítí podléhá virtuální sítě ve stejné oblasti Azure.

**Skupiny zabezpečení sítě**: [Skupin zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) obsahují Access Control seznamy, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupin zabezpečení sítě se dá použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače.

**Active Directory Domain Services (služba AD DS)** : Tato architektura poskytuje vyhrazené nasazení [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) .

**Protokolování a audit**: [Protokol aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md) zachycuje operace prováděné s prostředky ve vašem předplatném, jako je například uživatel, který operaci zahájil, stav operace a hodnoty dalších vlastností, které vám mohou při průzkumu operace. Protokol aktivit Azure je služba platformy Azure, která zachycuje všechny akce v rámci předplatného. V případě potřeby lze protokoly archivovat nebo exportovat.

**Monitorování sítě a výstrahy**: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) je služba platformy, která poskytuje Zachytávání síťových paketů, protokolování toků, nástroje topologie a diagnostiku pro síťové přenosy v rámci virtuální sítě.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost**: Úlohy serveru jsou seskupené do [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , které vám pomůžou zajistit vysokou dostupnost virtuálních počítačů v Azure. Tato konfigurace pomáhá zajistit, že během plánované nebo neplánované události údržby bude k dispozici alespoň jeden virtuální počítač a bude vyhovovat 99,95% Azure SLA.

### <a name="logging-and-audit"></a>Protokolování a audit

**Monitorování**: [Azure monitor](../../azure-monitor/overview.md) je služba platformy, která poskytuje jeden zdroj pro monitorování protokolu aktivit, metrik a diagnostických protokolů všech vašich prostředků Azure. Azure Monitor je možné nakonfigurovat tak, aby vizualizuje metriky a protokoly přicházející z prostředků v Azure, dotazování, směrování, archivaci a jednání s nimi. Doporučuje se, aby se k zabezpečení záznamu auditu používala Access Control založená na prostředku, aby bylo možné zajistit, že uživatelé nebudou moct měnit protokoly.

**Protokoly aktivit**: Nakonfigurujte [protokoly aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md) a poskytněte vám přehled o operacích, které byly provedeny u prostředků v rámci vašeho předplatného.

**Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md) jsou všechny protokoly emitované prostředkem. Tyto protokoly můžou zahrnovat protokoly systému událostí Windows, objekty blob, tabulky a fronty.

**Protokoly brány firewall**: Application Gateway poskytuje úplnou diagnostiku a protokoly přístupu. Protokoly brány firewall jsou dostupné pro prostředky služby Application Gateway, které mají povolený Firewall webových aplikací.

**Archivace protokolu**: Úložiště dat protokolu se dá nakonfigurovat tak, aby se do centralizovaného účtu Azure Storage napsalo pro archivaci a definovanou dobu uchování. Protokoly lze zpracovávat pomocí protokolů Azure Monitor nebo jinými systémy SIEM třetích stran.

### <a name="identity"></a>Identita

**Active Directory Domain Services**: Tato architektura zajišťuje nasazení Active Directory Domain Services v Azure. Konkrétní doporučení ohledně implementace Active Directory v Azure najdete v následujících článcích:

[Rozšíření Active Directory Domain Services (služba AD DS) do Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain)

[Pokyny pro nasazení služby Windows Server Active Directory v Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integrace služby Active Directory**: Jako alternativu k vyhrazené služba AD DS architektuře můžou zákazníci chtít použít integraci [Azure Active Directory](/azure/architecture/reference-architectures/identity) nebo [službu Active Directory v Azure, která je připojená k místní doménové struktuře](/azure/architecture/reference-architectures/identity).

### <a name="security"></a>Zabezpečení

**Zabezpečení správy**: Tento podrobný plán umožňuje správcům připojit se k virtuální síti pro správu a JumpBox pomocí protokolu RDP z důvěryhodného zdroje. Síťový provoz virtuální sítě pro správu se řídí pomocí skupin zabezpečení sítě. Přístup k portu 3389 je omezený na přenosy z důvěryhodného rozsahu IP adres, který má přístup k podsíti obsahující JumpBox.

Zákazníci taky můžou zvážit použití [rozšířeného modelu správy zabezpečení](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) k zabezpečení prostředí při připojování k virtuální síti pro správu a JumpBox. Doporučujeme, aby pro zákazníky s vyšším zabezpečením používali [privilegovaný přístup k pracovní stanici](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) a konfiguraci RDGateway. Používání síťových virtuálních zařízení a veřejných a privátních zóny DMZ bude nabízet další vylepšení zabezpečení.

**Zabezpečení sítě**: [Skupiny zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (Skupin zabezpečení sítě) se doporučuje, aby každá podsíť poskytovala druhou úroveň ochrany proti příchozímu provozu, která obchází nesprávně nakonfigurovanou nebo zakázanou bránu. Příklad – [Správce prostředků šablona pro nasazení rozhraní NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Zabezpečení veřejných koncových bodů**: Internetová brána zpřístupňuje aplikační služby uživatelům prostřednictvím Internetu. Provoz, který přistupuje k těmto službám, se zabezpečuje pomocí [Application Gateway](../../application-gateway/overview.md), která poskytuje bránu firewall webových aplikací a správu protokolu HTTPS.

**Rozsahy IP adres**: Rozsahy IP adres v architektuře jsou navržené rozsahy. Zákazníkům doporučujeme vzít v úvahu vlastní prostředí a použít příslušné rozsahy.

**Hybridní připojení**: Cloudové úlohy jsou připojené k místnímu datovému centru prostřednictvím protokolu IPSEC VPN pomocí VPN Gateway Azure. Zákazníci by měli mít jistotu, že používají vhodný VPN Gateway pro připojení k Azure. Příklad – [VPN Gateway správce prostředků šabloně](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Zákazníci, kteří využívají rozsáhlé a kritické úlohy s požadavky na velké objemy dat, můžou zvážit hybridní síťovou architekturu s využitím [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) pro připojení privátních sítí k cloudovým službám Microsoftu.

**Oddělení obav**: Tato referenční architektura odděluje virtuální sítě pro operace správy a obchodní operace. Samostatné virtuální sítě a podsítě umožňují správu provozu, včetně omezení příchozího a odchozího přenosu, pomocí skupin zabezpečení sítě mezi segmenty sítě, které následují v rámci osvědčených postupů pro [cloudové služby Microsoftu a zabezpečení sítě](/azure/architecture/vdc/networking-virtual-datacenter) .

**Správa prostředků**: Prostředky Azure, jako jsou virtuální počítače, virtuální sítě a nástroje pro vyrovnávání zatížení, se spravují seskupením dohromady do [skupin prostředků Azure](../../azure-resource-manager/resource-group-overview.md). Role Access Control na základě prostředků se pak dají přiřadit ke každé skupině prostředků, aby se omezil přístup jenom na autorizované uživatele.

**Omezení Access Control**: Pomocí [Access Control na základě rolí](../../role-based-access-control/role-assignments-portal.md) (RBAC) spravujte prostředky v aplikaci pomocí [vlastních rolí](../../role-based-access-control/custom-roles.md) RBAC, které můžete použít k omezení operací, které DevOps můžou provádět na jednotlivých úrovních. Při udělování oprávnění použijte [Princip](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1)nejnižších oprávnění. Protokolujte všechny operace správy a provádějte pravidelné audity, abyste měli jistotu, že byly všechny změny konfigurace plánované.

**Přístup k Internetu**: Tato referenční architektura využívá [Azure Application Gateway](../../application-gateway/overview.md) jako internetovou bránu a nástroj pro vyrovnávání zatížení. Někteří zákazníci taky můžou zvážit použití virtuálních zařízení třetích stran pro další vrstvy zabezpečení sítě jako alternativu k [Azure Application Gateway](../../application-gateway/overview.md).

**Azure Security Center**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) poskytuje centrální pohled na stav zabezpečení prostředků v rámci předplatného a poskytuje doporučení, která vám pomůžou zabránit napadeným prostředkům. Dá se taky použít k povolení podrobnějších zásad. Zásady můžete například použít na konkrétní skupiny prostředků, což podniku umožní přizpůsobit své stav riziku. Doporučuje se, aby zákazníci ve svém předplatném Azure povolili Azure Security Center.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentace k dodržování předpisů pro NCSC Cloud Security

Komerční služba pro koruna (agentura, která funguje ke zlepšení obchodních a podnikových aktivit), obnovila klasifikaci podnikových cloudových služeb Microsoftu do G-cloudu V6, které pokrývají všechny své nabídky na oficiální úrovni. Podrobnosti o Azure a G-cloudu najdete v souhrnu [posouzení zabezpečení cloudu pro Azure Británie G-Cloud](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Tento podrobný plán se zařadí ke 14 zásadám cloudového zabezpečení, které jsou popsány v tématu [Principy](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) NCSC cloudového zabezpečení, které vám pomohou zajistit prostředí, které podporuje úlohy klasifikované jako státní království.

[Matice zodpovědnosti zákazníka](https://aka.ms/ukofficial-crm) (excelový sešit) obsahuje seznam všech 14 principů zabezpečení cloudu a matrice pro každý princip (neboli princip subpart), zda je základní implementace zodpovědností od společnosti Microsoft, zákazníka nebo sdílí se mezi těmito dvěma.

Základní [matice implementace](https://aka.ms/ukofficial-iaaswa-pim) (excelový sešit) obsahuje seznam všech 14 principů zabezpečení cloudu a matrici pro každý princip (neboli princip subpart), který je určen zodpovědností zákazníka v matrici zodpovědnosti zákazníka 1), pokud automatizace podrobného plánu implementuje princip a 2) popis způsobu, jakým implementace zarovnává s principem požadavku.

Cloud Security Alliance (CSA) navíc publikovala matrici řízení cloudu pro podporu zákazníků při hodnocení poskytovatelů cloudu a k identifikaci otázek, které by měly být zodpovězeny před přechodem na cloudové služby. V odpovědi Microsoft Azure odpovědět na dotazník CSA konsensu Assessment Initiative ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), který popisuje, jak Microsoft řeší navržené principy.

## <a name="deploy-the-solution"></a>Nasazení řešení

Existují dvě metody, které mohou uživatelé nasazení použít k nasazení této automatizace podrobného plánu. První metoda používá skript prostředí PowerShell, zatímco druhá metoda využívá Azure Portal k nasazení referenční architektury. Podrobné pokyny k nasazení jsou k dispozici [zde](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
