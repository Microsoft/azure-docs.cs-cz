---
title: Protokolování, auditování a viditelnost brány v Azure Austrálie
description: Postup konfigurace protokolování, auditování a viditelnosti v rámci australských oblastí pro splnění konkrétních požadavků na zásady, předpisy a právní předpisy australské vlády.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 153b9d503dfece404455fbb7e8cb51c51686ec57
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824294"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Protokolování, auditování a viditelnost brány v Azure Austrálie

Zjišťování a reakce na počítačové bezpečnostní hrozby se spoléhá na generování, shromažďování a analýzu dat souvisejících s provozem systému.

Microsoft má integrované nástroje v Azure, které vám pomůžou implementovat protokolování, auditování a viditelnost při správě zabezpečení vašich systémů nasazených v Azure. K dispozici je také referenční architektura, která je v souladu se zákaznickým průvodcem ACSC (australských Internet Security Center) a záměrem příručky Information Security Manual (ISM).

Brány působí jako mechanismy řízení toku dat ve vrstvě sítě a můžou také řídit informace na vyšších vrstvách modelu Open System Interconnect (OSI). Brány jsou nezbytné ke kontrole datových toků mezi doménami zabezpečení a zabránění neoprávněnému přístupu z externích sítí. Vzhledem k závažnosti bran při řízení toku informací mezi doménami zabezpečení může mít jakákoli chyba, zejména u vyšších klasifikací, vážné důsledky. V takovém případě se jedná o robustní mechanismy pro upozorňování zaměstnanců na situace, které můžou způsobit, že se pro brány budou obzvláště důležité počítačové bezpečnostní incidenty.

Implementace možností protokolování a upozorňování pro brány může pomoct při zjišťování incidentů v počítačové bezpečnosti, pokusu o vniknutí a neobvyklých vzorech použití. Kromě toho je ukládání protokolů událostí na samostatném serveru protokolu zabezpečení vyšší než nežádoucí osoba, aby odstranila informace o protokolování, aby bylo možné zničit důkaz o neoprávněném neoprávněném přístupu na Internet.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Požadavky na středisko ACSC (australských Internet Security Center)

Celkové požadavky na zabezpečení pro systémy Společenství jsou definované v ACSC Information Security Manual (ISM). Pomoc subjektům Společenství, aby splnily tyto požadavky v rámci Azure, *Příručka pro příjemce acsc – Microsoft Azure v chráněné* a *acsc zprávě o certifikaci – Microsoft Azure* publikace podrobností následujících specifických požadavků Protokolování, auditování a viditelnost:

1. Aby bylo možné zmírnit rizika vyplývající z používání sdílených prostředků v cloudu, musí entity Společenství souhlasit s Microsoft Azure poskytovanými možnostmi, včetně služby Azure Security Center, Azure Monitor, Azure Policy a Azure Advisor, které pomáhají subjektům provádět. monitorování svých úloh Azure v reálném čase

2. ACSC také doporučuje, aby entity Společenství přenesly všechny přihlášené protokoly zabezpečení do ACSC pro celé monitorování australské vlády.

3. Pro pomoc při zmírnění rizik by se entity Společenství měly konfigurovat v rámci předplatných Azure:

    * Povolit Azure Security Center
    * Upgrade na úroveň Standard
    * Povolení automatického zřizování Microsoft Monitoring Agent pro podporované virtuální počítače Azure
    * Pravidelně kontrolujte, prioritise a zmírňujte doporučení a výstrahy zabezpečení na řídicím panelu Security Center.

4. Entity státní správy musí povolit předávání protokolů a událostí z předplatného Azure do ACSC a poskytnout tak ACSC přehled o nedodržení předpisů s těmito pokyny. Azure Event Hubs poskytuje možnost provádět externí streamování protokolů do ACSC nebo místních systémů vlastněných entitou Společenství.

5. Entity Společenství by měly zarovnat protokolování, které v Azure umožňují, do požadavků uvedených v rámci ISM.

6. Společnost Microsoft uchovává protokoly v rámci Azure po dobu 90 dnů. Entity zákazníka musí implementovat režim archivace protokolu, aby bylo možné uchovávat protokoly po dobu sedmi let, které jsou požadovány v rámci NAA AFDA

7. Entity služby řízení bezpečnosti, které mají místní funkce nebo informace o zabezpečení založené na Azure (SIEM), můžou do těchto systémů taky přesílat protokoly.

8. Entity Společenství by měly implementovat protokoly toku Network Watcher pro skupiny zabezpečení sítě (skupin zabezpečení sítě) a Virtual Machines. Tyto protokoly by se měly ukládat do vyhrazeného účtu úložiště obsahujícího jenom protokoly zabezpečení a přístup k účtu úložiště by měl být zabezpečený pomocí řízení přístupu na základě rolí.

9. Entity Společenství musí implementovat Rady ACSC Consumer, aby se zajistilo, že úlohy Azure budou splňovat požadavky ISM na protokolování a monitorování. Entity Společenství musí také vyjádřit výslovný souhlas s funkcemi Azure, které pomáhají ACSC přijímat monitorování, upozorňování a protokoly spojené s australským využitím Azure v reálném čase.

## <a name="architecture"></a>Architektura

Aby bylo možné bez obav pochopit síťový provoz, který vstupuje do prostředí Azure a opustí ho, musí být v pravé sadě součástí povolené potřebné protokolování. Tím zajistíte kompletní viditelnost prostředí a poskytnete potřebná data k provedení analýzy.

![Architektura monitorování Azure](media/visibility.png)

## <a name="components"></a>Komponenty

Výše uvedená architektura je tvořená diskrétními součástmi, které poskytují funkce obou zdrojů protokolů, shromažďování protokolů, uchovávání protokolů, analýzu protokolů nebo odpovědi na incidenty. Tato architektura zahrnuje jednotlivé komponenty, které jsou obvykle součástí nasazení Azure přístupných k Internetu.

|Funkce|Komponenty|
|---|---|
|Zdroje protokolů|<ul><li>Application Gateway</li><li>VPN Gateway</li><li>Brána Azure Firewall</li><li>Síťová virtuální zařízení</li><li>Azure Load Balancer</li><li>Virtuální počítače</li><li>Servery DNS (Domain Naming System)</li><li>Protokol syslog nebo servery shromažďování protokolů</li><li>skupin NSG</li><li>Protokol aktivit Azure</li><li>Diagnostický protokol Azure</li><li>Azure Policy</li></ul>|
|Shromažďování protokolů|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|Uchovávání protokolů|<ul><li>Azure Storage</li></ul>|
|Analýza protokolů|<ul><li>Azure Security Center (ASC)</li><li>Azure Advisor</li><li>Log Analytics řešení<ul><li>Analýza provozu</li><li>DNS Analytics (Náhled)</li><li>Activity Log Analytics</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|Reakce na incidenty|<ul><li>Upozornění Azure</li><li>Azure Automation</li></ul>|
|

Architektura funguje tak, že nejdřív generuje protokoly z nezbytných zdrojů a potom je shromažďuje do centralizovaných úložišť. Po shromáždění protokolů můžou být:

* používá Azure Analysis Services k získání přehledu,
* předávané externím systémům nebo
* Získejte archivované úložiště pro dlouhodobé uchovávání.

Aby bylo možné reagovat na klíčové události nebo incidenty identifikované nástroji pro analýzu, můžete nakonfigurovat výstrahy a automatizovat automatizaci, aby bylo možné provést nezbytné akce pro proaktivní správu a reakci.

## <a name="general-guidance"></a>Obecné pokyny

Při implementaci součástí uvedených v tomto článku platí následující obecné pokyny:

* Ověřte dostupnost oblasti služeb a zajistěte, aby všechna data zůstala v povolených umístěních, a jako první předvolby pro chráněné úlohy nasaďte na AU Central nebo AU Central 2.

* Seznamte se s *certifikací certifikace Azure-acsc – chráněná 2018* pro stav certifikace jednotlivých služeb a provádět vlastní posouzení všech relevantních komponent, které nejsou součástí sestavy, podle *příručky pro příjemce acsc – Microsoft Azure v CHRÁNĚNých*

* Pro součásti, na které se neodkazuje v tomto článku, by měly entity Společenství postupovat podle principů, které se týkají generování, zachytávání, analýzy a uchovávání protokolů.

* Identifikujte a prioritise protokolování, auditování a viditelnost systémů s vysokými hodnotami a také všechny síťové příchozí a výstupní body pro systémy hostované v Azure.

* Konsolidujte protokoly a minimalizujte počet instancí nástrojů protokolování, jako jsou účty úložiště, Log Analytics pracovní prostory a Event Hubs

* Omezení oprávnění správce prostřednictvím řízení přístupu na základě rolí

* Použití vícefaktorového ověřování (MFA) pro účty, které spravují nebo konfigurují prostředky v Azure

* Při centralizovaném shromažďování protokolů mezi několika předplatnými se ujistěte, že správci mají v každém předplatném potřebná oprávnění.

* Zajistěte, aby připojení k síti a veškerá nutná konfigurace proxy serveru Virtual Machines, včetně síťových virtuálních zařízení (síťová virtuální zařízení), serverů shromažďování protokolů a serverů DNS, aby se připojovaly k potřebným službám Azure, jako jsou pracovní prostory Log Analytics, Event Hubs a úložiště

* Nakonfigurujte Microsoft Monitoring Agent (MMA) pro použití TLS verze 1,2.

* Použití Azure Policy k monitorování a prosazování dodržování předpisů pomocí požadavků

* Vynutili šifrování u všech úložišť dat, jako jsou úložiště a databáze.

* Použití místně redundantního úložiště (LRS) a snímků pro dostupnost účtů úložiště a přidružených dat

* Zvažte použití geograficky redundantního úložiště (GRS) nebo úložiště mimo lokalitu pro zarovnávání se strategiemi zotavení po havárii.

|Resource|URL|
|---|---|
|Australské dokumenty a předpisy o dodržování předpisů v australském pořádku|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Produkty Azure – australské oblasti a jiné než regionální|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Austrálie – střed, Austrálie – střed – 2, Austrálie – východ, Austrálie – jihovýchod](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Dokument white paper o správě protokolů Microsoft Azure Security and Audit|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Konfigurace Microsoft Monitoring Agent|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>Doprovodné materiály k komponentám

Tato část poskytuje informace o účelu každé součásti a její roli v rámci celkové architektury protokolování, auditování a viditelnosti. K dispozici jsou další odkazy pro přístup k užitečným prostředkům, jako jsou referenční dokumentace, příručky a kurzy.

## <a name="log-sources"></a>Zdroje protokolů

Aby bylo možné provést jakoukoli analýzu, upozorňování nebo vytváření sestav, musí být vygenerovány potřebné protokoly. Protokoly Azure jsou rozdělené do kategorií řídicích a řídicích protokolů, protokolů roviny dat a zpracovaných událostí.

|type|Popis|
|---|---|
|Protokoly řízení a správy|Zadání informací o Azure Resource Managerch operacích|
|Protokoly roviny dat|Poskytněte informace o událostech vyvolaných v rámci využití prostředků Azure, jako jsou protokoly ve virtuálním počítači a diagnostické protokoly dostupné prostřednictvím Azure Monitor|
|Zpracované události|Poskytněte informace o analyzovaných událostech a výstrahách, které zpracovala Azure, například kde Azure Security Centera zpracovala a analyzovala odběry k poskytování výstrah zabezpečení.|
|

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway je jedním z možných vstupních bodů do prostředí Azure, takže potřebujete zachytit informace související s příchozími připojeními, která komunikují s webovými aplikacemi. Application Gateway může poskytnout zásadní informace týkající se využití webové aplikace a pomáhat při zjišťování incidentů v oblasti zabezpečení sítě. Application Gateway odesílá metadata do protokolu aktivit a diagnostické protokoly v Azure Monitor, kde je lze využít v Log Analytics nebo distribuované do centra událostí nebo účtu úložiště.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace ke službě Application Gateway|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|Průvodce rychlým startem Application Gateway|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>VPN Gateway

VPN Gateway je potenciální vstupní bod pro rozsáhlou škálu komunikace do prostředí Azure, například připojení k místnímu prostředí a provozu správy. Přihlášení k bránám sítě VPN poskytuje přehled a sledovatelnost pro připojení prováděná v prostředí Azure. Protokolování může poskytovat auditování a analýzu a také pomáhat při detekci nebo vyšetřování škodlivých nebo neobvyklé připojení. Protokoly VPN Gateway jsou odesílány do protokolu aktivit Azure Monitor, kde je lze využít v Log Analytics nebo distribuované do centra událostí nebo účtu úložiště.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace služby VPN Gateway|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|Doprovodné materiály pro VPN Gateway australské vlády|[Konfigurace Azure VPN Gateway](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Brána Azure Firewall

Azure Firewall poskytuje řízený bod ukončení z prostředí Azure a vygenerované protokoly, které obsahují informace o pokusůch a úspěšných odchozích připojeních, jsou důležitým prvkem ve vaší strategii protokolování. Tyto protokoly můžou ověřit, že systémy fungují tak, jak jsou navržené, a pomáhají při zjišťování škodlivého kódu nebo objektů Actor, které se pokoušejí připojit k neautorizovaným externím systémům. Azure Firewall zapisuje protokoly do protokolů aktivit a diagnostické protokoly v Azure Monitor, kde se dají použít v Log Analytics, nebo distribuované do centra událostí nebo účtu úložiště.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace brány Azure Firewall|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|Kurz: Monitorovat protokoly Azure Firewall a metriky|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>Síťová virtuální zařízení (síťové virtuální zařízení)

Síťová virtuální zařízení se dá použít k doplnění možností zabezpečení dostupných nativně v Azure. Protokoly generované v síťová virtuální zařízení můžou být cennými prostředky při zjišťování incidentů zabezpečení počítače a jsou klíčovou součástí celkové strategie protokolování, auditování a viditelnosti. K zachycení protokolů z síťová virtuální zařízení se využívá Microsoft Monitoring Agent (MMA). Pro síťová virtuální zařízení, které nepodporují instalaci MMA, zvažte použití protokolu syslog nebo jiného serveru shromažďování protokolů k přenosu protokolů.

|Zdroje a prostředky|Odkaz|
|---|---|
|Přehled síťových virtuálních zařízení|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|Dokumentace k síťové virtuální zařízení|Informace o implementaci relevantního síťové virtuální zařízení v Azure najdete v dokumentaci dodavatele.|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Protokoly Azure Load Balancer slouží k získání užitečných informací o připojeních a využití v souvislosti s systémy nasazenými v Azure. Tato možnost se dá použít k monitorování stavu a dostupnosti, ale také vytvoří další klíčovou komponentu pro získání potřebného přehledu o komunikačních komunikacích a zjištění škodlivých nebo neobvykléch vzorců provozu. Azure Load Balancer protokoly protokolu aktivit a diagnostické protokoly v Azure Monitor, kde je lze využít v Log Analytics nebo distribuované do centra událostí nebo účtu úložiště.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k Azure Load Balancer|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|Metriky a Diagnostika stavu pro Standard Load Balancer|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>Virtuální počítače

Virtual Machines jsou koncové body, které odesílají a přijímají síťovou komunikaci, zpracovávají data a poskytují služby. Jak Virtual Machines může hostovat data nebo klíčové systémové služby, aby se zajistilo správné fungování a detekce případných incidentů zabezpečení sítě může být kritická. Virtual Machines shromažďovat různé protokoly událostí a auditu, které mohou sledovat provoz systému a akce prováděné v tomto systému. Protokoly shromážděné v Virtual Machines lze přeslat do pracovního prostoru Log Analytics pomocí Microsoft Monitoring Agent, kde je lze analyzovat pomocí Azure Security Center a příslušných Log Analytics řešení. Virtual Machines taky můžete integrovat přímo se službou Azure Event Hubs nebo SIEM, a to buď přímo, nebo prostřednictvím serveru shromažďování protokolů.

|Zdroje a prostředky|Odkaz|
|---|---|
|Virtuální počítače|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|Shromažďování dat z Virtual Machines|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|Streamování protokolů virtuálních počítačů do Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>Servery DNS (Domain Name Services)

Protokoly serveru DNS poskytují klíčové informace týkající se služeb, ke kterým se systémy snaží získat přístup, a to interně nebo externě. Zachytávání protokolů DNS vám může pomoci identifikovat incident zabezpečení v rámci počítače a poskytnout přehled o typu incidentu a systémy, které to mohou být ovlivněny. Na serverech DNS se dá použít agent MMA (Microsoft Management Agent) k předávání protokolů do Log Analytics pro použití v DNS Analytics (Preview).

|Zdroje a prostředky|Odkaz|
|---|---|
|Překlad názvů Azure pro virtuální sítě|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Protokol syslog a servery shromažďování protokolů

Pokud chcete přijímat protokoly ze síťových virtuálních zařízení nebo vlastní protokoly zabezpečení z jiných systémů pro použití v rámci SIEM, můžete v rámci Azure virtuální sítě nasadit vyhrazené servery. Protokoly syslog se dají shromažďovat na serveru syslog a předají se do Log Analytics pro účely analýzy. Server shromažďování protokolů je obecný termín pro všechny agregace protokolů a možnosti distribuce používané centralizovanými monitorovacími systémy nebo systémů Siem. Je možné je využít ke zjednodušení síťové architektury a zabezpečení a k filtrování a agregaci protokolů před distribucí na centralizované možnosti.

|Zdroje a prostředky|Odkaz|
|---|---|
|Zdroje dat protokolu Syslog v Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|Server shromažďování protokolů|Podrobnosti o architektuře monitoring a SIEM najdete v dokumentaci od dodavatele.|
|

### <a name="network-security-groups-nsgs"></a>Skupiny zabezpečení sítě (skupin zabezpečení sítě)

Skupin zabezpečení sítě provoz mezi virtuálními sítěmi v Azure a mimo ně. Skupin zabezpečení sítě použít pravidla pro přenosová data, která jsou povolená nebo zakázaná, což zahrnuje provoz v rámci Azure a mezi Azure a externími sítěmi, jako je místní nebo Internet. Skupin zabezpečení sítě se aplikují na podsítě v rámci virtuální sítě nebo na jednotlivá síťová rozhraní. K zaznamenání informací o provozu vstupujících do systémů v Azure a jejich chodu je možné protokoly NSG povolit prostřednictvím funkce Network Watcher protokolů Flow NSG. Tyto protokoly slouží k vytvoření směrného plánu pro standardní fungování systému a jsou zdrojem dat pro Analýza provozu, který poskytuje podrobné přehledy o vzorcích provozu systémů hostovaných v Azure.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace ke skupině zabezpečení sítě|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Úvod do protokolování toků pro skupiny zabezpečení sítě|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|Kurz: Protokolování síťového provozu do a z virtuálního počítače pomocí Azure Portal|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Protokol aktivit Azure

Protokol aktivit Azure, který je součástí Azure Monitor, je protokol předplatného, který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Protokol aktivit vám může pomáhat určit, co, kdo a kdy je potřeba pro všechny operace zápisu (PUT, POST, DELETE) u prostředků v rámci předplatného. Protokol aktivit je zásadní pro sledování změn konfigurace provedených v prostředí Azure. Protokoly aktivit Azure jsou automaticky dostupné pro použití v Log Analytics řešení a je možné je odeslat do Event Hubs nebo Azure Storage pro zpracování nebo uchování.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k protokolu aktivit Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|Streamování protokolu aktivit Azure do služby Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Diagnostický protokol Azure

Protokoly diagnostiky Azure Monitor jsou protokoly emitované službou Azure, která poskytuje bohatou a častou data o provozu této služby. Diagnostické protokoly poskytují přehled o fungování prostředku na detailní úrovni a dají se použít pro řadu požadavků, jako je auditování nebo řešení potíží. Diagnostické protokoly Azure jsou automaticky dostupné pro použití v Log Analytics řešení a je možné je odeslat do Event Hubs nebo Azure Storage pro zpracování nebo uchování.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k diagnostickému protokolu Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|Služby podpory pro diagnostické protokoly|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy vynutila pravidla týkající se nasazení prostředků, jako je typ, umístění a konfigurace. Azure Policy lze nakonfigurovat tak, aby bylo zajištěno, že prostředky lze nasadit pouze v případě, že jsou kompatibilní s požadavky. Azure Policy je základní součástí pro zachování integrity prostředí Azure. Události související s Azure Policy jsou protokolovány do protokolu aktivit Azure a jsou automaticky dostupné pro použití v řešeních Log Analytics nebo je lze odeslat do Event Hubs nebo Azure Storage pro zpracování nebo uchování.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k Azure Policy|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|Využití šablon Azure Policy a Správce prostředků pomocí Azure modrotisky|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>Shromažďování protokolů

Po vygenerování z více zdrojů protokolů se protokoly musí ukládat do centralizovaného umístění pro průběžný přístup a analýzu. Azure poskytuje několik metod a možností pro shromažďování protokolů, které je možné využívat v závislosti na typu a požadavcích protokolu.

### <a name="event-hubs"></a>Event Hubs

Účelem centra událostí je agregovat data protokolu pro různé zdroje pro distribuci. Z centra událostí můžete data protokolu odesílat do SIEM, ACSC pro dodržování předpisů a úložiště pro dlouhodobé uchovávání.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace ke službě Event Hubs|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|Pokyny k Event Hubs a externím nástrojům|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics je součástí Azure Monitor a slouží k analýze protokolů. Log Analytics používá pracovní prostor jako mechanizmus úložiště, ve kterém je možné zpřístupnit data protokolu pro nejrůznější analytické nástroje a řešení dostupná v rámci Azure. Log Analytics se integruje s nejrůznějšími komponentami Azure přímo a také Virtual Machines prostřednictvím Microsoft Monitoring Agent.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace ke službě Log Analytics|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|Kurz: Analyzovat data v Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>Network Watcher

ACSC doporučuje použití Network Watcher, které vám pomůžou pochopit a zachytit síťový provoz v rámci předplatného Azure. Protokoly toku NSG poskytují vstup do řešení Analýza provozu v Log Analytics, které poskytuje rozšířenou viditelnost, analýzu a generování sestav prostřednictvím Azure. Network Watcher taky nabízí možnost zachycení paketů přímo z Azure Portal bez nutnosti přihlašovat se k virtuálnímu počítači. Zachycení paketů umožňuje vytvořit relace zachytávání paketů pro sledování provozu do a z virtuálního počítače.

|Zdroje a prostředky|Odkaz|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|Přehled zachytávání paketů|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>Uchovávání protokolů

V případě organizací australské vlády se protokoly zachycené v rámci Azure musí uchovávat v souladu s národními archivy [autority pro vyřazení správních funkcí Austrálie (AFDA)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx), která určuje zachování protokolů až po dobu sedmi let.

|Umístění protokolu|Doba uchování|
|---|---|
|Protokol aktivit Azure|Až 90 dní|
|Pracovní prostor Log Analytics|Až dva roky|
|Centrum událostí|Až sedm dní|
|

Je vaší zodpovědností zajistit, aby byly protokoly vhodně archivovány, aby dodržovaly AFDA a jiné legislativní požadavky.

### <a name="azure-storage"></a>Azure Storage

Azure Storage je úložiště pro ukládání protokolů pro dlouhodobé uchovávání v Azure. Azure Storage můžete použít k archivaci protokolů z Azure včetně Event Hubs, protokolu aktivit Azure a diagnostických protokolů Azure. Doba uchovávání dat v úložišti může být nastavena na hodnotu nula nebo může být zadána jako počet dnů. Doba uchování 0 dnů znamená, že se protokoly uchovávají trvale, jinak může být libovolný počet dní mezi 1 a 2147483647.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k Azure Storage|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|Zachycení událostí pomocí Azure Event Hubs v Azure Blob Storage nebo Azure Data Lake Storage|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Kurz: Archivace metrik Azure a dat protokolů s použitím Azure Storage|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Replikace Azure Storage|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|Vytvoření snímku objektu BLOB|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Analýza protokolů

Po vygenerování a uložení v centralizovaném umístění musí být protokoly analyzovány, aby bylo možné zjistit pokusy o incidenty zabezpečení nebo úspěšné. Pokud se zjistí incidenty zabezpečení, musí agentura reagovat na tyto incidenty a sledovat, obsahovat a opravovat jakékoli hrozby.

### <a name="azure-security-center-asc"></a>Azure Security Center (ASC)

Azure Security Center poskytuje jednotnou správu zabezpečení a rozšířenou ochranu před internetovými útoky. Azure Security Center můžou používat zásady zabezpečení napříč úlohami, omezovat vystavení hrozbám a detekovat a reagovat na útoky. Azure Security Center poskytuje řídicí panely a analýzy v rámci široké škály komponent Azure. Použití Azure Security Center je zadáno jako požadavek v pokynech pro spotřebitele ACSC.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace ke službě Azure Security Center|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|Rychlý start: Připojení předplatného Azure k Security Center Standard|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>Analýza provozu

Analýza provozu je cloudové řešení, které poskytuje přehled o aktivitách uživatelů a aplikací v Azure. Analýzy provozu Network Watcher protokoly toku NSG a poskytují přehled o toku provozu v Azure. Analýza provozu slouží k poskytování řídicích panelů, sestav, analýz a schopností reakce na události související se síťovými přenosy, které jsou v rámci virtuálních sítí viditelné. Analýza provozu poskytuje významný přehled a pomůže vám identifikovat a vyřešit incidenty zabezpečení na Internet.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k Analýza provozu|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor analýzy prostředků a další data k doporučením řešení, která vám pomůžou zlepšit výkon, zabezpečení a vysokou dostupnost prostředků a současně hledat příležitosti pro snížení celkové útraty Azure. Azure Advisor doporučuje ACSC a nabízí snadno dostupné a podrobné poradenství týkající se konfigurace prostředí Azure.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace ke službě Azure Advisor|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Začínáme se službou Azure Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (Náhled)

DNS Analytics je Log Analytics řešení, které shromažďuje, analyzuje a koreluje protokoly Windows DNS pro analýzu a audit a další související data. DNS Analytics identifikuje klienty, kteří se pokoušejí přeložit názvy škodlivých domén, zastaralých záznamů prostředků, často dotazovaných názvů domén a klientů DNS prahová. DNS Analytics taky nabízí přehled o zatížení serverů DNS a chybách při dynamických registrech DNS. DNS Analytics slouží k poskytování řídicích panelů, sestav, analýz a schopností reakce na události související s dotazy DNS provedenými v prostředí Azure. DNS Analytics poskytuje významný přehled a pomůže vám identifikovat a vyřešit incidenty zabezpečení na Internet.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k DNS Analytics|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Activity Log Analytics

Activity Log Analytics je Log Analytics řešení, které pomáhá analyzovat a prohledávat protokol aktivit Azure napříč několika předplatnými Azure. Activity Log Analytics slouží k poskytování centralizovaných řídicích panelů, sestav, analýz a schopností reakce na události související s akcemi provedenými na prostředcích celého prostředí Azure. Activity Log Analytics může pomáhat s auditováním a vyšetřováním.

|Zdroje a prostředky|Odkaz|
|---|---|
|Shromažďování a analýza protokolů aktivit Azure do Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>Správa událostí a informací o zabezpečení (SIEM)

SIEM je systém, který poskytuje centralizované úložiště, auditování a analýzu protokolů zabezpečení s definovanými mechanismy pro ingestování široké škály dat protokolů a inteligentních nástrojů pro analýzu, generování sestav a zjišťování incidentů a reakci. K doplnění funkcí zabezpečení poskytovaných nativně v Azure můžete použít funkce SIEM, které obsahují informace o protokolování Azure. Entity Společenství můžou využívat SIEM hostované na Virtual Machines v Azure, v místním prostředí nebo jako schopnost SaaS (software jako služba) v závislosti na konkrétních požadavcích.

|Zdroje a prostředky|Odkaz|
|---|---|
|Sentinel Azure (Preview)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|Dokumentace k SIEM|Informace o architektuře a zásadách SIEM najdete v dokumentaci dodavatele.|
|Integrace s nástroji SIEM pomocí Azure Monitor|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Australský Internet Security Center

Australský Internet Security Center (ACSC) je vedoucí australské vlády o národním internetovém zabezpečení. Spojuje možnosti internetového zabezpečení od australské vlády za účelem zlepšení počítačové odolnosti australské komunity a podporu hospodářské a sociální prosperity Austrálie v digitálním věku. ACSC doporučuje, aby entity Společenství přenesly všechny přihlášené soubory protokolů, události a protokoly generované systémem do ACSC pro celé monitorování australské vlády.

|Zdroje a prostředky|Odkaz|
|---|---|
|Webový server australského centra zabezpečení pro Internet|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Reakce na incidenty

Generování příslušných protokolů, jejich shromáždění do centralizovaných úložišť a provádění analýz zvyšuje porozumění systémům a poskytuje mechanismy pro detekci případných problémů se zabezpečením na Internet. Po zjištění incidentů nebo událostí je dalším krokem reakce na tyto události a provádění akcí, které udržují stav systému a chrání služby a data před ohrožením. Azure poskytuje kombinaci služeb, které umožňují efektivně reagovat na všechny události, ke kterým dojde.

### <a name="azure-alerts"></a>Upozornění Azure

Výstrahy Azure je možné použít k oznámení o podpoře a personálu zabezpečení v reakci na konkrétní události. To umožňuje entitě Společenství aktivně reagovat na detekci relevantních událostí vyvolaných službami Analysis Services uvedenými v tomto článku.

|Zdroje a prostředky|Odkaz|
|---|---|
|Přehled výstrah v Microsoft Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|Správa a zpracování výstrah zabezpečení v Azure Security Center|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|Reakce na události s upozorněními služby Azure Monitor|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Azure Automation

Azure Automation umožňuje entitám Společenství aktivovat akce v reakci na události. Může to být spuštění zachytávání paketů při Virtual Machines, spuštění pracovního postupu, zastavení nebo spuštění Virtual Machines nebo služeb nebo řada jiných úloh. Automatizace umožňuje rychlou odezvu na výstrahy bez ručních zásahů, takže zkracuje dobu odezvy a závažnost incidentu nebo události.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k Azure Automation|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|Návod: Aktivace sady Runbook Azure Automation pomocí výstrahy|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>Další postup

Podrobné informace o bezpečné správě prostředí brány v Azure najdete v článku o [zabezpečení vzdálené správy brány](gateway-secure-remote-administration.md) .
