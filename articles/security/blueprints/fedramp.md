---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - automatizace FedRAMP webové aplikace
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - automatizace FedRAMP webové aplikace
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 10ed297180f68fcaf006f2778990879be02f994d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---fedramp-web-applications-automation"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého - automatizace FedRAMP webové aplikace

## <a name="overview"></a>Přehled

[Federal rizika a autorizace správu Program (FedRAMP)](https://www.fedramp.gov), je program government celou USA, který poskytuje standardizovaná přístup k zabezpečení, autorizace a nepřetržité monitorování produktů cloudu a služby. Tento zabezpečení Azure a dodržování předpisů plán, podle kterého automatizace obsahuje pokyny pro nasazení kompatibilní se standardem FedRAMP infrastruktury jako služby (IaaS) prostředí, který je vhodný pro jednoduchou webovou aplikaci internetové brány. Toto řešení umožňuje automatizovat nasazení a konfigurace prostředků Azure pro běžné referenční architektura, ukázka způsoby, ve kterém zákazníkům můžete splňovat určité požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníkům umožňují vytvářet a Nakonfigurujte vlastní řešení v Azure. Řešení implementuje podmnožinu ovládacích prvků ze směrného plánu FedRAMP vysoká, podle SP NIST 800-53. Další informace o požadavcích FedRAMP vysokou a řešení najdete v tématu [FedRAMP vysoké požadavky - přehled vysoké úrovni](fedramp-controls-overview.md). ***Poznámka: Toto řešení se nasadí do Azure Government.***

Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav není dostatečná pro úplně splnění požadavků FedRAMP vysokou standardních hodnot. Je třeba počítat s následujícím:
- Tato architektura poskytuje směrný plán pro zákazníky, používat Azure kompatibilní se standardem FedRAMP způsobem.
- Zákazníci jsou zodpovědní za provedení příslušné zabezpečení a hodnocení dodržování předpisů řešení vytvořená s využitím této architektury, protože požadavky se mohou lišit podle specifických vlastností implementace každého zákazníka. 

Pro rychlý přehled o tom, jak toto řešení funguje, si pusťte toto [video](https://aka.ms/fedrampblueprintvideo) vysvětlením a předvedení jeho nasazení.

Klikněte na tlačítko [sem](https://aka.ms/fedrampblueprintrepo) pokyny pro nasazení.

## <a name="solution-components"></a>Součásti řešení

Tento zabezpečení Azure a dodržování předpisů plán, podle kterého automatizace automaticky nasadí IaaS webové aplikace referenční architektura s ovládacími prvky pro zákazníky, dosažení souladu s požadavky FedRAMP předem nakonfigurovaný zabezpečení. Řešení se skládá z šablon Azure Resource Manageru a skriptů prostředí PowerShell, které průvodce prostředků nasazení a konfigurace. Doplňujícími [dodržování předpisů dokumentace](#compliance-documentation) je k dispozici, označující dědičnosti řízení zabezpečení z Azure a nasazené prostředky a konfigurace, které zarovnané s NIST SP 800-53 ovládacích prvků zabezpečení, zpřístupňují organizaci rychlého povinnosti dodržování předpisů.

## <a name="architecture-diagram"></a>Diagram architektury

Toto řešení nasadí referenční architektura pro webovou aplikaci IaaS s databáze back-end. Architektura zahrnuje webovou vrstvu, datové vrstvy, infrastrukturu služby Active Directory, aplikační bránu a nástroj pro vyrovnávání zatížení. Virtuální počítače nasazené na úrovně web a data jsou nakonfigurované v nastavení dostupnosti, a instance systému SQL Server jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost. Virtuální počítače jsou připojené k doméně a zásady skupiny služby Active Directory se používají k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému. Správa jumpbox (bastionu hostitel) poskytuje zabezpečené připojení pro Správci nasazení přístup k prostředkům.

![alternativní text](images/fedramp-architectural-diagram.png?raw=true "zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP webové aplikace automatizace")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení jsou umístěné v [architektura nasazení služby](#deployment-architecture) části.

* **Azure Virtual Machines**
    - (1) správy/bastionu (Windows Server 2016 Datacenter)
    - (2) řadič domény active Directory (Windows Server 2016 Datacenter)
    - (2) uzel clusteru SQL serveru (SQL Server 2016 na Windows Server 2012 R2)
    - (1) SQL serveru s kopií clusteru (Windows Server 2016 Datacenter)
    - (2) web nebo IIS (Windows Server 2016 Datacenter)
* **Skupiny dostupnosti**
    - (1) řadiče domény active Directory
    - (1) uzly clusteru SQL a s kopií clusteru
    - (1) webového nebo IIS
* **Azure Virtual Network**
    - ((1) /16 virtuální sítě
    - (5) /24 podsítě
    - Nastavení DNS jsou nastavené pro oba řadiče domény
* **Nástroj pro vyrovnávání zatížení Azure**
    - (1) nástroj pro vyrovnávání zatížení SQL
* **Azure Application Gateway**
    - (1) Aplikační brána firewall webových aplikací povoleno
      - Brány firewall režim: prevence
      - Sady pravidel: OWASP 3.0
      - Naslouchacího procesu: Port 443
* **Azure Storage**
    - (7) účty geograficky redundantní úložiště
* **Azure Backup**
    - (1) trezor služeb zotavení
* **Azure Key Vault**
    - (1) klíče trezoru
* **Azure Active Directory**
* **Azure Resource Manager**
* **Azure Log Analytics**
    - (1) pracovní prostor analýzy protokolů
* **Azure Automation**
    - (1) účet automation

## <a name="deployment-architecture"></a>Architektura nasazení služby

V následující části Podrobné informace o vývoji a implementaci elementy.

### <a name="network-segmentation-and-security"></a>V případě segmentace sítě a zabezpečení

#### <a name="application-gateway"></a>Application Gateway

Architektura snižuje riziko ohrožení zabezpečení pomocí služby Application Gateway pomocí brány firewall webových aplikací (firewall webových aplikací) a ruleset OWASP povolena. Další možnosti patří:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brány firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (firewall webových aplikací režim)
- [Prevence režimu](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) s ruleset OWASP 3.0

#### <a name="virtual-network"></a>Virtuální síť

Architektura definuje privátní virtuální síť s adresním prostorem z 10.200.0.0/16.

#### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Toto řešení nasadí prostředky v architekturu s podsíť samostatný webový, databáze podsíť, podsíť služby Active Directory a podsítě správu uvnitř virtuální sítě. Podsítě jsou logicky odděleny použít na jednotlivé podsítě omezit přenos dat mezi podsítě, které jenom to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě.

Najdete v části konfigurace [skupin zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazení s tímto řešením. Organizace skupiny zabezpečení sítě můžete nakonfigurovat úpravou souboru nad pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako vodítko.

Každý podsítí má skupina zabezpečení vyhrazené sítě (NSG):
- 1 skupina NSG pro aplikační brána (LBNSG)
- 1 skupina NSG pro Jumpbox (MGTNSG)
- 1 skupina NSG pro primární a záložní řadiče domény (ADNSG)
- 1 skupina NSG pro servery SQL a určující sdílenou složku (SQLNSG)
- 1 skupina NSG pro webová vrstva (WEBNSG)

#### <a name="subnets"></a>Podsítě

Každá podsíť je přidružen jeho odpovídající skupina NSG.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém stavu pomocí několika míry šifrování.

#### <a name="azure-storage"></a>Azure Storage

Chcete-li splnit požadavky na šifrování dat na rest, použijte všechny účty úložiště [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>Databáze SQL

Databáze SQL je nakonfigurovaný na použití [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), která provede v reálném čase šifrování a dešifrování dat a souborů protokolu ochrany informací v klidovém stavu. Šifrování TDE poskytuje záruku, že data uložena nebyla neoprávněnému přístupu k. 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption se používá k šifrované disky virtuálních počítačů IaaS ve Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkce nástroje BitLocker systému Windows k poskytování šifrování svazku operačního systému a dat disků. Řešení jsou integrované s Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

### <a name="logging-and-auditing"></a>Protokolování a auditování

[Analýza protokolu](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) poskytuje rozsáhlé protokolování systému a uživatelské aktivity a také stav systému. 

- **Protokoly aktivity:**[protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o činnosti, které byly provedeny v prostředky ve vašem předplatném.
- **Diagnostické protokoly:**[diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou všechny protokoly vygenerované každých prostředkem. Tyto protokoly zahrnují protokoly událostí systému Windows, protokoly úložiště Azure, protokoly Key Vault auditu a protokolů Application Gateway přístup a brány firewall.
- **Archivaci protokolu:** diagnostické protokoly a protokoly Azure aktivity může být připojen k analýze protokolů Azure pro zpracování, ukládání a dashboarding. Doba uchování je uživatelsky konfigurovatelného až 730 den splnění uchování specifické pro organizaci.

### <a name="secrets-management"></a>Tajné klíče správy

Řešení používá Azure Key Vault pro správu klíčů a tajných klíčů.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pomáhá zabezpečit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. 
- Řešení jsou integrované s Azure Key Vault pro správu klíčů šifrování disku virtuálního počítače IaaS a tajných klíčů.

### <a name="identity-management"></a>Správa identit

Následujících technologií poskytovat identitu možnosti správy v prostředí Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft víceklientské cloudové adresáře a identity management service.
- Lze provést ověření do zákazníka nasazené webové aplikace pomocí služby Azure AD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje přesněji správu cílených přístupu k Azure. Předplatné přístup je omezen na správce předplatného, a přístup k prostředkům může být omezen na základě role uživatele.
- Nasazenou instanci služby Active Directory IaaS poskytuje identity management na úrovni operačního systému pro nasazené virtuální počítače IaaS.
   
### <a name="compute-resources"></a>Výpočet prostředků

#### <a name="web-tier"></a>Webová vrstva

Řešení nasadí webovou vrstvu virtuálních počítačů v [sadu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti Ujistěte se, že virtuální počítače distribuované ve více clusterů izolované hardwaru ke zlepšení dostupnosti.

#### <a name="database-tier"></a>Databázové vrstvy

Nasadí virtuální počítače vrstvy databáze v skupiny dostupnosti jako řešení [skupiny dostupnosti AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Poskytuje funkci dostupnosti skupin Always On pro vysokou dostupnost a zotavení po havárii možnosti. 

#### <a name="active-directory"></a>Active Directory

Všechny virtuální počítače nasazené řešení jsou připojené k doméně a zásady skupiny služby Active Directory se používají k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému. Active Directory virtuálních počítačů nasazených v skupiny dostupnosti.


#### <a name="jumpbox-bastion-host"></a>Jumpbox (bastionu hostitel)

Správa jumpbox (bastionu hostitel) poskytuje zabezpečené připojení pro Správci nasazení přístup k prostředkům. Skupina NSG přidružených k podsíti správy, kde je umístěn virtuální počítač jumpbox umožňuje připojení pouze na TCP port 3389 pro protokol RDP. 

### <a name="malware-protection"></a>Ochrana proti malwaru

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro virtuální počítače poskytuje funkci Ochrana v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software, se dají konfigurovat výstrahy, když známé škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit ve chráněných virtuálních počítačů.

### <a name="patch-management"></a>Opravy správy

Windows virtuálních počítačů nasazených v tomto zabezpečení Azure a dodržování předpisů plán, podle kterého Automation jsou nakonfigurované ve výchozím nastavení příjem automatických aktualizací ze služby Windows Update. Toto řešení taky nasadí řešení Azure Automation, pomocí kterého lze vytvořit nasazení aktualizací na servery Windows v případě potřeby nasadit opravy.

### <a name="operations-management"></a>Řízení provozu

#### <a name="log-analytics"></a>Log Analytics

[Analýza protokolu](https://azure.microsoft.com/services/log-analytics/) je služba, která umožňuje shromažďování a analýza dat vygenerovaných sadami prostředky ve službě Azure a místní prostředí.

#### <a name="management-solutions"></a>Řešení pro správu

Následující řešení pro správu jsou předem nainstalován jako součást tohoto řešení:
- [Posouzení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Posouzení antimalwaru](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Zabezpečení a audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Protokoly Azure aktivity](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Sledování změn](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Dokumentace dodržování předpisů

### <a name="customer-responsibility-matrix"></a>Matice odpovědnosti zákazníka

[Matice odpovědnosti zákazníka](https://aka.ms/blueprinthighcrm) (sešitu aplikace Excel) zobrazí všechny kontrolní mechanismy zabezpečení, které vyžadují vysokou FedRAMP směrného plánu. Matice označuje pro každý ovládací prvek (nebo její ovládací prvek komponenty), zda implementace jeho zodpovědné pro ovládací prvek zodpovídá Microsoft, zákazník nebo sdílené mezi nimi. 

### <a name="control-implementation-matrix"></a>Matice implementaci ovládacího prvku

[Matice implementaci ovládacího prvku](https://aka.ms/blueprintwacim) (sešitu aplikace Excel) zobrazí všechny kontrolní mechanismy zabezpečení, které vyžadují vysokou FedRAMP směrného plánu. Matice označuje, pro každý ovládací prvek (nebo její ovládací prvek komponenty), je určen zákazníka-jeho zodpovědné v matici odpovědnosti zákazníka, 1) Pokud plán, podle kterého automatizace implementuje ovládacího prvku a 2) a popis způsobu implementace zarovnaná s ovládací prvek uvažovaný. Tento obsah je k dispozici [zde](fedramp-controls-overview.md).

## <a name="deploy-the-solution"></a>Nasazení řešení

Tento zabezpečení Azure a dodržování předpisů automatizace plán, podle kterého se skládá z JSON konfigurační soubory a skripty prostředí PowerShell, které jsou zpracovávány službou rozhraní API Správce Azure Resource Manager k nasazení prostředků v rámci Azure. Podrobné pokyny jsou k dispozici [zde](https://aka.ms/fedrampblueprintrepo). ***Poznámka: Toto řešení se nasadí do Azure Government.***

#### <a name="quickstart"></a>Rychlý start
1. Klonovat nebo stáhnout [to](https://aka.ms/fedrampblueprintrepo) úložiště GitHub do místní pracovní stanici.

2. Spustit skript prostředí PowerShell před nasazením: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Klikněte na tlačítko níže, přihlaste se k portálu Azure, zadejte požadované parametry šablony ARM a klikněte na **nákupu**.

    [![Nasazení do Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Právní omezení

- Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.  
- Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.  
- Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.  
- Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.  
- Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
- Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.
