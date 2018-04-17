---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - Spojené království – oficiální třívrstvá webové aplikace automatizace
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - Spojené království – oficiální třívrstvá webové aplikace automatizace
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: bb0a667c28e4ed0be3e67a7d89f10903be2c9d2a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---uk-offical-three-tier-web-applications-automation"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého - UK OFICIÁLNÍHO třívrstvá webové aplikace automatizace

## <a name="overview"></a>Přehled

 Tento článek obsahuje pokyny a automatizace skripty k poskytování architektura třívrstvá webové Microsoft Azure, která je vhodné pro zpracování mnoho úloh, které jsou klasifikovány jako oficiální ve Spojeném království.

 Pomocí infrastruktury jako kód přístupu sadu [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) šablony nasazení prostředí, aby k Spojené království National Kybernetického zabezpečení Centre (NCSC) 14 [zásady zabezpečení cloudu](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) Centrum pro zabezpečení Internetu (SNS) a [ovládací prvky zabezpečení](https://www.cisecurity.org/critical-controls.cfm).

 NCSC doporučujeme použít jejich zásady zabezpečení cloudu zákazníci k vyhodnocení vlastnosti zabezpečení služby a pro lepší porozumění tomu dělení zodpovědnosti mezi zákazníkem a dodavatele. Nabízíme informace pro každý z těchto zásad, které vám pomohou pochopit rozdělení odpovědnosti.

 Tato architektura a odpovídající šablony Azure Resource Manager jsou podporovány v dokumentu White Paper Microsoft [14 cloudu kontrolních mechanismů pro zabezpečení pro velkou Británii cloudu pomocí Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Tento dokument v katalozích jak Azure services zarovnat se UK NCSC 14 cloudu zabezpečení zásadami, a umožňuje organizacím rychlého možnost jejich dodržování předpisů povinnosti na Microsoft Azure pomocí globálně a ve Spojeném království cloudové služby cloud.

 Tato šablona nasadí infrastrukturu pro pracovní vytížení. Kód aplikace a podpůrné obchodní vrstvou a softwaru úroveň dat musí být nainstalovaná a nakonfigurovaná. Podrobné pokyny jsou k dispozici [zde](https://aka.ms/ukwebappblueprintrepo).

 Pokud nemáte předplatné Azure, pak si můžete zaregistrovat snadno a rychle - [Začínáme s Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

 Šablony Azure poskytování architektura třívrstvá webové aplikace v prostředí cloudu Azure, které podporuje Spojené království – oficiální úlohy. Architektura poskytuje zabezpečené hybridní prostředí, které rozšiřuje k místní síti na Azure povolení webové úlohy do bezpečně přistupovat pomocí podnikovým uživatelům nebo z Internetu.

![alternativní text](images/diagram.png?raw=true "Azure architektura UK OFICIÁLNÍHO tři vrstvy")

 Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení jsou umístěné v [architektura nasazení služby](#deployment-architecture) části.

((1) /16 virtuální síť – provozní virtuální sítě
- (3) /24 podsítě – vrstvy 3 (Web, Biz, Data)
- (1) /27 podsíť – přidá
- ((1) /27 podsíť - podsíť brány
- ((1) /29 podsíť - podsítě Application Gateway
- Používá výchozí (DNS poskytnutých platformou Azure)
- Partnerský vztah povoleno na virtuální síť pro správu
- Skupina zabezpečení sítě (NSG) pro správu tok přenosů dat

((1) /24 virtuální sítě - virtuální síť pro správu
- ((1) /27 podsíť
- Přidá DNS používá (2) a položek Azure DNS (1)
- Partnerský vztah povoleno na provozní virtuální sítě
- Skupina zabezpečení sítě (NSG) pro správu tok přenosů dat

(1) aplikační brány
- Firewall webových aplikací – povoleno
- Režim firewall webových aplikací – prevence
- Sady pravidel: OWASP 3.0
- Naslouchací proces protokolu HTTP na portu 80
- Připojení nebo provoz upraveno prostřednictvím NSG
- Koncový bod veřejné IP adresy definované (Azure)

(1) VPN – na základě trasy, tunelové propojení VPN typu Site-2-Site protokolu IPSec
- Koncový bod veřejné IP adresy definované (Azure)
- Připojení nebo provoz upraveno prostřednictvím NSG
- (1) místní síťová brána (místní koncový bod)
- (1) azure brány (koncového bodu Azure)

(9) nasazení virtuálních počítačů – všechny virtuální počítače s nastavením Azure IaaS antimalwarových DSC

- (2) active Directory Domain Services řadiče domény (Windows Server 2012 R2)
  - (2) role serveru DNS - 1 na virtuální počítač
  - (2) síťové adaptéry připojené k provozní VNet - 1 na virtuální počítač
  - Jsou obě domény připojený k doméně v šabloně definovánu
    - Vytvořené jako součást nasazení domény


- (1) Jumpbox (Bastionu hostitel) správu virtuálních počítačů
  - 1 síťovou kartu v správy sítě VNet s veřejnou IP adresu
    - Skupina NSG se používá pro omezení provozu (vstup/výstup) pro konkrétní zdroje
  - Není připojený k doméně


- (2) web virtuálními počítači vrstvy
  - (2) role serveru služby IIS - 1 na virtuální počítač
  - (2) síťové adaptéry připojené k provozní VNet - 1 na virtuální počítač
  - Není připojený k doméně


- (2) virtuální počítače vrstvy biz
  - (2) síťové adaptéry připojené k provozní VNet - 1 na virtuální počítač
  - Není připojený k doméně


- (2) data vrstvy virtuální počítače
  - (2) síťové adaptéry připojené k provozní VNet - 1 na virtuální počítač
  - Není připojený k doméně

Skupiny dostupnosti
- (1) active Directory virtuálního počítače řadiče domény nastavte - 2 virtuální počítače
- (1) webovou vrstvu virtuálního počítače nastavit – 2 virtuální počítače
- (1) biz vrstvy virtuálního počítače nastavit – 2 virtuální počítače
- (1) datové vrstvy virtuálního počítače nastavit – 2 virtuální počítače

Load Balancer
- (1) nástroj pro vyrovnávání zatížení webové vrstvy
- (1) nástroj pro vyrovnávání zatížení vrstvy biz
- (1) nástroj pro vyrovnávání zatížení vrstvy data

Úložiště
- (14) účty úložiště celkový počet
  - Skupina dostupnosti řadiče domény služby Active Directory
    - (2) primární účty místně redundantní úložiště (LRS) - 1 pro každý virtuální počítač  
    - (1) diagnostiky účet místně redundantní úložiště (LRS) pro sady přidá dostupnosti
  - Jumpbox správu virtuálních počítačů
    - (1) účet primární místně redundantní úložiště (LRS) pro virtuální počítač Jumpbox
    - (1) diagnostiky účet místně redundantní úložiště (LRS) pro virtuální počítač Jumpbox
  - Virtuální web vrstvy počítače
    - (2) primární účty místně redundantní úložiště (LRS) - 1 pro každý virtuální počítač  
    - (1) diagnostiky účet místně redundantní úložiště (LRS) pro skupiny dostupnosti webové vrstvy
  - Virtuální počítače vrstvy Biz
    - (2) primární účty místně redundantní úložiště (LRS) - 1 pro každý virtuální počítač  
    - (1) diagnostiky účet místně redundantní úložiště (LRS) pro skupiny dostupnosti Biz vrstvy
  - Virtuální počítače vrstvy dat
    - (2) primární účty místně redundantní úložiště (LRS) - 1 pro každý virtuální počítač  
    - (1) diagnostiky účet místně redundantní úložiště (LRS) pro sadu dat úroveň dostupnosti

### <a name="deployment-architecture"></a>Architektura nasazení:

**Místní síť**: privátní síti místní oblasti implementována v organizaci.

**Virtuální síť produkční**: produkčním [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (virtuální sítě) je hostitelem aplikace a další provozní prostředky, které běží v Azure. Každý virtuální sítě může obsahovat několik podsítí, které jsou používány k izolování a správu síťového provozu.

**Webové vrstvy**: zpracovává příchozí požadavky HTTP. Jsou odpovědi vrácené prostřednictvím této vrstvy.

**Obchodní vrstva**: implementuje obchodní procesy a další funkční logiku pro systém.

**Databáze vrstvy**: poskytuje úložiště dat pomocí [SQL serveru skupin dostupnosti Always On](https://msdn.microsoft.com/library/hh510230.aspx) pro vysokou dostupnost. Zákazníci mohou používat [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) jako alternativní PaaS.

**Brána**: [brány VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) poskytuje připojení mezi směrovači v místní síti a provozní virtuální sítě.

**Internetová brána a veřejnou IP adresu**: Internetová brána zpřístupní služby aplikace uživatelům prostřednictvím Internetu. Provoz přístup k tyto služby zabezpečené pomocí [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) nabídky vrstvy 7 směrování a možnosti s ochranou brány firewall (firewall webových aplikací) webové aplikace Vyrovnávání zatížení.

**Virtuální síť pro správu**: to [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) obsahuje prostředky, které implementují Správa a sledování možnosti pro úlohy běžící v produkčním prostředí virtuální sítě.

**Jumpbox**: označuje taky jako [bastionu hostitele](https://en.wikipedia.org/wiki/Bastion_host), což je zabezpečeného virtuálního počítače v síti, která správcům umožňuje připojení k virtuálním počítačům v produkčním prostředí virtuální sítě. Jumpbox má skupinu NSG, která umožňuje vzdálenou komunikaci pouze z jedné veřejné IP adresy na seznamu bezpečných adres. Zdroj provoz pro povolení vzdálené plochy (RDP) komunikace, musí být definován v této skupině. Správa prostředků produkční je prostřednictvím protokolu RDP pomocí zabezpečené Jumpbox virtuálního počítače.

**Trasy definované uživatelem**: [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) slouží k určení tok provozu IP v rámci virtuálních sítí Azure.

**Sítě Peered virtuálních sítí**: produkční a správu virtuálních sítí jsou připojené pomocí [VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Tyto virtuální sítě se pořád spravují jako samostatné prostředky, ale zobrazí jako jedna pro všechny účely připojení pro tyto virtuální počítače. Tyto sítě vzájemně komunikovat přímo pomocí privátních IP adres. VNet peering podléhá virtuální sítě se ve stejné oblasti Azure.

**Skupin zabezpečení sítě**: [skupiny Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, která povolí nebo zakážou provoz v rámci virtuální sítě. Skupiny Nsg můžete použít k zabezpečení přenosů dat na podsíť nebo jednotlivé úroveň virtuálního počítače.

**Active Directory Domain Services (AD DS)**: Tato architektura poskytuje vyhrazená [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) nasazení.

**Protokolování a auditování**: [protokol činnosti Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zaznamená operace, s prostředky v rámci vašeho předplatného, jako je například kdo inicioval operace, při operaci došlo k chybě, stav operace a hodnoty Další vlastnosti, které vám můžou pomoct výzkum operaci. Azure protokol aktivit je služba platformy Azure, která zaznamená všechny akce na předplatném. Protokoly dá archivovat nebo exportovali v případě potřeby.

**Síťové monitorování a výstraha**: [sledovací proces sítě Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) je platforma služba poskytuje zachytáváním síťových paketů, protokolování toku, topologie nástroje a diagnostiky pro traffics sítě v rámci vaší virtuální sítě.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost**: úlohy serveru jsou seskupené v [sadu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Tato konfigurace pomáhá zajistit, že během plánované i neplánované údržby alespoň jeden virtuální počítač bude k dispozici a splňují podmínku 99.95 % smlouva Azure SLA.

### <a name="logging-and-audit"></a>Protokolování a auditování

**Monitorování**: [Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) je služba platformy, která poskytuje jednoho zdroje pro monitorování protokol aktivit, metriky a diagnostické protokoly všech vašich prostředků Azure. Azure monitorování lze nakonfigurovat pro vizualizaci, dotazování, směrování, archivaci a fungovat na metriky a protokoly pocházejících z prostředků ve službě Azure. Doporučuje se, že řízení přístupu na základě prostředků slouží k zabezpečení záznam pro audit k zajištění, že uživatelé nemají možnost upravit v protokolech.

**Protokoly aktivity**: konfigurace [protokoly aktivity Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zajistit přehled o činnosti, které byly provedeny v prostředky ve vašem předplatném.

**Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou všechny protokoly vygenerované prostředkem. Tyto protokoly mohou zahrnovat protokoly událostí systému Windows, objektů blob, table a protokoly fronty.

**Brány firewall protokoly**: Application Gateway poskytuje úplných protokolů diagnostiky a přístup. Protokoly brány firewall jsou dostupné pro prostředky služby Application Gateway, které mají povolený Firewall webových aplikací.

**Přihlaste se archivaci**: úložiště dat může být nakonfigurován k zápisu do centralizované úložiště Azure protokolu účet pro archivaci a dobu uchování definované. Protokoly můžete zpracovat pomocí Azure Log Analytics nebo SIEM systémy jiných výrobců.

### <a name="identity"></a>Identita

**Active Directory Domain Services**: Tato architektura zajišťuje nasazení služby Active Directory Domain Services v Azure. Konkrétní doporučení ohledně implementace Active Directory v Azure najdete v následujících článcích:

[Rozšíření do Azure Active Directory Domain Services (AD DS)](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Pokyny pro nasazení systému Windows Server Active Directory na virtuálních počítačích Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integrace služby Active Directory**: jako alternativu k vyhrazené architektura služby AD DS, se zákazníci mohou chtít používat [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) integrace nebo [služby Active Directory v Azure připojené k místní doménové struktury](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Zabezpečení

**Správa zabezpečení**: Tento plán, podle kterého umožňuje správcům připojit k správu virtuální sítě a Jumpbox pomocí protokolu RDP z důvěryhodného zdroje. Síťových přenosů pro správu virtuální sítě je řízena pomocí skupin Nsg. Přístup k portu 3389 je omezen na provoz z důvěryhodných rozsahu IP, kterým může přistupovat podsíť obsahující Jumpbox.

Zákazníci mohou také zvažte použití [model správy rozšířené zabezpečení](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) zabezpečit prostředí při připojování k management virtuální sítě a Jumpbox. Doporučuje se, že pro zvýšení zabezpečení zákazníci používat [privilegovaného přístupu pracovní stanice](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) a konfigurace RDGateway. Použití virtuálních zařízení sítě a veřejného a privátního zóny DMZ nabídne další vylepšení zabezpečení.

**Zabezpečení sítě**: [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Nsg) – se doporučují pro každou podsíť zajistit druhou úroveň ochrany proti obcházení brány nesprávně nakonfigurován nebo zakázané příchozí přenosy. Příklad: [šablony Resource Manageru pro nasazování skupinu NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Zabezpečení veřejné koncové body**: Internetová brána zpřístupní služby aplikace uživatelům prostřednictvím Internetu. Provoz přístup k tyto služby zabezpečené pomocí [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), který poskytuje protokol správy brány Firewall webových aplikací a HTTPS.

**Rozsahy IP adres**: rozsahy IP v architektuře, která jsou navržené oblasti. Zákazníci by měli vzít v úvahu svoje vlastní prostředí a použijte příslušné rozsahy.

**Hybridní připojení**: cloudové úlohy jsou připojené k místního datového centra prostřednictvím sítě VPN IPSEC pomocí služby Azure VPN Gateway. Zákazníci zkontrolujte, že používají bránu příslušné sítě VPN pro připojení k Azure. Příklad: [šablony Resource Manageru brány VPN](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Zákazníci, kteří používají ve velkém měřítku, zvažte možnost použití architektura hybridní sítě může přát kritické úlohy s velkými objemy dat požadavky [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) pro připojení k privátní síti společnosti Microsoft cloudových služeb.

**Oddělené oblasti zájmu**: tuto referenční architekturu odděluje virtuální sítě pro operace správy a provoz firmy. Samostatné virtuální sítě a podsítě povolit správu provoz, včetně omezení příchozí a odchozí přenosy, pomocí skupin Nsg mezi segmenty sítě následující [zabezpečení služeb a sítě Microsoft cloud](https://docs.microsoft.com/azure/best-practices-network-security) osvědčené postupy.

**Správa prostředků**: spravuje prostředky Azure, jako jsou virtuální počítače, virtuální sítě a nástroje pro vyrovnávání zatížení seskupení společně do [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource). Řízení přístupu na základě role prostředků můžete omezit přístup jenom autorizovaným uživatelům přiřadit pak ke každé skupině prostředků.

**Přístup k omezení řízení**: použití [řízení přístupu na základě Role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) ke správě prostředků ve aplikace pomocí [vlastní role](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) RBAC lze použít k omezení operací, DevOps provádět na jednotlivých úrovních. Při udělování oprávnění, použijte [Princip nejnižších nutných oprávnění](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Protokolujte všechny operace správy a provádějte pravidelné audity, abyste měli jistotu, že byly všechny změny konfigurace plánované.

**Přístup k Internetu**: využívá tuto referenční architekturu [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) jako internetové brány a zatížení vyrovnávání. Někteří zákazníci mohou také zvažte použití virtuálních zařízení třetích stran sítě pro další vrstvy zabezpečení jako alternativu k síťových [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Azure Security Center**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) poskytuje centrální zobrazení stavu zabezpečení prostředků v předplatném a poskytuje doporučení, které pomáhají zabránit ohroženými prostředky. Je také slouží k povolení podrobnější zásad. Například můžete do skupin konkrétní prostředek, což umožňuje enterprise podle jeho postavení k riziku použijí zásady. Doporučujeme, aby zákazníci Azure Security Center v rámci svého předplatného Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC cloudu zabezpečení zásady dodržování předpisů dokumentace

Crown komerční služby (agentura, který lze použít ke zlepšení činnost komerční a nákup vládou) obnovit klasifikaci Microsoft enterprise v oboru cloudové služby na G-Cloud v6 pokrývajících všech nabídek na oficiální úrovni. Podrobnosti o Azure a G-Cloud nachází v [souhrn hodnocení zabezpečení cloudu se službou Azure UK G](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Tento plán, podle kterého zarovnává zásadám zabezpečení 14 cloudu, které jsou dokumentovány v NCSC [zásady zabezpečení cloudu](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) k zajištění prostředí, které podporuje úlohy, které jsou klasifikovány jako oficiální UK.

[Matice odpovědnosti zákazníka](https://aka.ms/blueprintuk-gcrm) (sešitu aplikace Excel) obsahuje všechny zásady zabezpečení 14 cloudu a matice označuje, pro každou zásadu (nebo její Princip komponenty), zda implementace princip má na starosti Microsoft, zákazník nebo sdílené mezi nimi.

[Princip implementace matice](https://aka.ms/ukwebappblueprintpim) (sešitu aplikace Excel) zobrazí všechny zásady zabezpečení 14 cloudu a matice označuje, pro každou zásadu (nebo její Princip komponenty), který je vybraný odpovědnosti zákazníka v zákazníka Matice odpovědnosti, 1) Pokud plán, podle kterého automatizace implementuje princip a 2) popis způsobu implementace zarovnaná s uvažovaný zásadu. Tento obsah je k dispozici [zde](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

Kromě toho Alliance zabezpečení cloudu (CSA) publikovat matice řízení cloudu pro podporu zákazníci při vyhodnocení poskytovatelů cloudu a k identifikaci otázky, které by měl být zodpovězena, před přesunutím cloudových služeb. V odpovědi, Microsoft Azure odpovědi Initiative dotazník CSA Konsenzu Assessment ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), který popisuje, jak Microsoft adresy navrhované zásady.

## <a name="deploy-the-solution"></a>Nasazení řešení.

Existují dvě metody, které uživatelé nasazení může použít k nasazení tento plán, podle kterého automatizace. První metoda používá skript prostředí PowerShell, zatímco druhý způsob využívá portál Azure k nasazení referenční architektura. Podrobné pokyny jsou k dispozici [zde](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.
 - Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
 - Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.
