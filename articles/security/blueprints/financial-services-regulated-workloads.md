---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - FFIEC finančních služeb regulovaná úlohy
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - FFIEC finančních služeb regulovaná úlohy
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: jomolesk
ms.openlocfilehash: f1339af22132d19f14ea8ebb72fe0e6bd45b7fad
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého - FFIEC finančních služeb regulovaná úlohy

## <a name="overview"></a>Přehled

Azure zabezpečení a dodržování předpisů plán, podle kterého - pomáhá FFIEC finančních služeb Regulated zatížení, nasazení zabezpečení a dodržování platforma jako služba (PaaS) webovou aplikaci určený k řešení citlivá data v cloudu. Plán, podle kterého se skládá z automatizované skripty a pokyny, které prezentují jednoduché referenční architektura a návrh, který pomáhá zjednodušit přijetí řešení Microsoft Azure. Tento plán, podle kterého je znázorněný začátku do konce řešení ke splnění potřeb organizace, které hledají způsoby, jak snížit zatížení a náklady na nasazení v cloudu.

Tento plán slouží ke splnění přísných kompatibilní norem nastavit pomocí American Institute z Certified veřejné účetní například - SOC 1, SOC 2, council Payment Card Industry Data Security Standards DSS 3.2 a FFIEC pro shromažďování, ukládání a načítání finanční citlivá data. Po nasazení řešení, které spravuje finanční data v prostředí zabezpečené, kompatibilní, vícevrstvé ukazuje správné zpracování těchto údajů. Je řešení nasazeno jako koncoví na end řešení Azure založené PaaS. 

Plán, podle kterého se má sloužit jako základ pro zákazníky sestavení a pochopit požadavky na správu zabezpečených dat v cloudu. Řešení se nesmí použít v produkčním nasazení jako-se, ale chcete porozumět, návrh a nasazení služby Azure; slouží jako základ pro zákazníky, zabezpečení a dodržování způsobem použít Microsoft Azure.

Auditor akreditované musí potvrdit řešení zákazníků produkční založený na tento plán. Řešení může lišit v závislosti na podrobnosti implementace a zeměpisnou každého zákazníka.

Pro rychlý přehled o tom, jak toto řešení funguje, si pusťte toto [video](https://aka.ms/fsiblueprintvideo) , které vysvětluje a ukazuje jeho nasazení.

## <a name="solution-components"></a>Součásti řešení

Architektura se skládá z následujících součástí a používá možnosti nasazení z řešení Azure PCI DSS dodržování předpisů.

- **Diagram architektury**. Diagram znázorňuje referenční architektura použít pro řešení Contoso Webstore.
- **Nasazení šablony**. V tomto nasazení [šablon Azure Resource Manageru](/azure/azure-resource-manager/resource-group-overview#template-deployment) se používají k automaticky nasazení komponent architektury do Microsoft Azure tak, že zadáte parametry konfigurace během instalace.
- **Automatizované skripty nasazení**. Tyto skripty pomoct nasadit řešení začátku do konce. Skripty se zabývají:
    - Instalace modulu a [globálního správce](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) instalační skript se používá k instalaci a ověřte, zda jsou správně nakonfigurovány požadované moduly prostředí PowerShell a rolí globálního správce. 
    - Instalace skriptu prostředí PowerShell slouží k nasazení řešení začátku do konce, poskytnuto prostřednictvím soubor .zip a souboru .bacpac, které obsahují předdefinovaných ukázkovou webovou aplikaci s [ukázkové databáze SQL](https://github.com/Microsoft/azure-sql-security-sample). obsah. Zdrojový kód pro toto řešení je k dispozici ke kontrole [úložiště kódu platebních zpracování plán, podle kterého] [kódu úložišti]. 

## <a name="architectural-diagram"></a>Diagram architektury

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Uživatelský scénář

Plán, podle kterého řeší následující případ použití níže.

> Tento scénář ukazuje, jak přesunout fiktivní webstore, že citlivá data do PaaS cloudové řešení založené na Azure. Řešení příklad znázorňuje zpracování a kolekce základní uživatelské informace a vybrané citlivá data. Tento pracovní vychází z Azure zabezpečení a dodržování předpisů plán, podle kterého - vyhovující PCI DSS zpracování platebních prostředí jazyků. Další informace najdete na rozšíření na tento pracovní ["Zkontrolujte a pokyny pro implementaci"](https://aka.ms/pciblueprintprocessingoverview) dokumentu poskytuje kontrolu kompatibilní se standardem PCI DSS prostředí.

### <a name="use-case"></a>Případ použití
Malé webstore názvem *Contoso Webstore* lze přesunout finanční data, která obsahuje informace o platebních zákazníka do cloudu. 

Správce společnosti Contoso Webstore hledá řešení, které lze rychle nasadit, abyste dosáhli svých cílů. Pomocí této testování--konceptu (POC) mu diskuse s jeho zúčastněným v prostředí Azure je možné použít jak pro shromažďování, ukládání a načítání finanční údaje v souladu s požadavky přísné dodržování předpisů.

> Bude provádějící příslušné zabezpečení a dodržování předpisů recenze řešení vytvořená s architekturou používá tento POC jako požadavky se mohou lišit podle specifických vlastností implementace a geography. 

### <a name="elements-of-the-foundational-architecture"></a>Elementy základní architektury

Základní architektura je navržen s fiktivní následující prvky:

Domény lokality `contosowebstore.com`

Role uživatele jsou vzhledem k objasnění případ použití a získat přehled o uživatelském rozhraní.

#### <a name="role-site-and-subscription-admin"></a>Role: Správce serveru a předplatné

|Položka      |Příklad:|
|----------|------|
|Uživatelské jméno: |`adminXX@contosowebstore.com`|
| Název: |`Global Admin Azure PCI Samples`|
|Typ uživatele:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- Účet správce nemůže přečíst finanční informace odmaskováno. Všechny akce jsou protokolovány.
- Účet správce nelze spravovat nebo Přihlaste se k databázi SQL.
- Účet správce můžete spravovat služby Active Directory a odběry.

#### <a name="role-sql-administrator"></a>Role: Správce SQL

|Položka      |Příklad:|
|----------|------|
|Uživatelské jméno: |`sqlAdmin@contosowebstore.com`|
| Název: |`SQLADAdministrator PCI Samples`|
| Jméno: |`SQL AD Administrator`|
|Příjmení: |`PCI Samples`|
|Typ uživatele:| `Administrator`|

- Účet sqladmin nefiltrované finanční informace nelze zobrazit. Všechny akce jsou protokolovány.
- Účet sqladmin umožňuje spravovat databáze SQL.

#### <a name="role-clerk"></a>Role: Fulltextu

|Položka      |Příklad:|
|----------|------|
|Uživatelské jméno:| `receptionist_EdnaB@contosowebstore.com`|
| Název: |`Edna Benson`|
| Jméno:| `Edna`|
|Příjmení:| `Benson`|
| Typ uživatele: |`Member`|

Edna Benson je např. recepční a obchodní manager. Je zodpovědná za zajištění, že je přesné informace o zákazníkovi a dokončení fakturace. Edna je uživatel přihlášen pro veškerou komunikaci s ukázkový web společnosti Contoso Webstore. Edna má následující oprávnění: 

- Edna můžete vytvořit a přečtěte si informace o zákazníkovi.
- Edna můžete upravit informace o zákazníkovi.
- Edna můžete přepsat finanční informace.
- Účet edna nefiltrované finanční informace nelze zobrazit.



### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - odhadované ceny

Tato základní architektura a příklad webové aplikace mají měsíční poplatek struktura a využití cena za jednu hodinu, které je potřeba zvážit při dimenzování řešení. Tyto náklady se dá odhadnout pomocí [Azure cenou kalkulačky](https://azure.microsoft.com/pricing/calculator/). Od září 2017 odhadované měsíční náklady pro toto řešení je ~ $ 2 500 to zahrnuje využití poplatek 1 000 USD/měsíc v2 App Service Environment. Tyto poplatky budou lišit v závislosti na velikosti využití a se mohou změnit. Je úlohou zákazníka k výpočtu jejich odhadované náklady na měsíční v době nasazení pro přesnější odhad. 

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení jsou umístěné v [architektura nasazení služby](#deployment-architecture) části.

>- Application Gateway
>- Azure Active Directory
>- V2 prostředí služby App Service
>- Log Analytics
>- Azure Key Vault
>- Network Security Groups (Skupiny zabezpečení sítě)
>- Azure SQL DB
>- Nástroj pro vyrovnávání zatížení Azure
>- Application Insights
>- Azure Security Center
>- Webové aplikace Azure
>- Azure Automation
>- Runbooky služby Azure Automation
>- Azure DNS
>- Azure Virtual Network
>- Virtuální počítače Azure
>- Skupina prostředků Azure a zásady
>- Azure Blob Storage
>- Řízení přístupu na základě Role Azure Active Directory (RBAC)

## <a name="deployment-architecture"></a>Architektura nasazení služby

V následující části Podrobné informace o vývoji a implementaci elementy.

### <a name="network-segmentation-and-security"></a>V případě segmentace sítě a zabezpečení

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

Základní architektura snižuje riziko ohrožení zabezpečení služby Application Gateway pomocí brány firewall webových aplikací (firewall webových aplikací) a ruleset OWASP povolena. Další možnosti patří:

- [End-to-End-SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Přesměrování zpracování SSL](/azure/application-gateway/application-gateway-ssl-portal) povoleno
- [TLS verze 1.0 a v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) zakázáno
- [Brány firewall webových aplikací](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (firewall webových aplikací režim)
- [Prevence režimu](/azure/application-gateway/application-gateway-web-application-firewall-portal) s ruleset OWASP 3.0
- [Protokolování diagnostiky](/azure/application-gateway/application-gateway-diagnostics) povoleno
- [Testy vlastní stavu](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisor](/azure/advisor/advisor-security-recommendations), které poskytují zvýšenou ochranu a oznámení. Azure Security Center nabízí také ztrátou dobré pověsti systému.

#### <a name="virtual-network"></a>Virtuální síť

Základní architektura definuje privátní virtuální síť s adresním prostorem 10.0.0.0/16.

#### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Každý z úrovně sítě má skupina zabezpečení vyhrazené sítě (NSG):

- Skupina zabezpečení sítě hraniční sítě pro brány firewall a aplikace brány firewall webových aplikací
- Skupina NSG pro správu jumpbox (bastionu hostitel)
- Skupina NSG pro app service environment

Každý z skupin Nsg mít určité porty a protokoly otevřené pro operaci zabezpečení a správné řešení. 

Kromě toho tyto konfigurace jsou povolené pro jednotlivé skupiny NSG:

- Povolit [diagnostické protokoly a události](/azure/virtual-network/virtual-network-nsg-manage-log) jsou uložené v účtu úložiště 
- Připojení analýzy protokolů pro [NSG na diagnostiky](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

 
#### <a name="subnets"></a>Podsítě
 Zajistěte, aby že každá podsíť je přidružen jeho odpovídající skupina NSG.

#### <a name="custom-domain-ssl-certificates"></a>Certifikáty SSL vlastní doménu.
 Provoz HTTPS je povolit pomocí certifikát SSL a vlastní doménu.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém stavu pomocí šifrování, auditování databáze a jiných opatření.

#### <a name="azure-storage"></a>Azure Storage

Aby splňoval požadavky šifrovaná data na rest, všechny [Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby úložiště](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Azure SQL Database

Instance databáze SQL Azure používá následující bezpečnostní opatření databáze:

- [AD ověřování a autorizace](/azure/sql-database/sql-database-aad-authentication)
- [Auditování databáze SQL](/azure/sql-database/sql-database-auditing-get-started)
- [Transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Pravidla brány firewall](/azure/sql-database/sql-database-firewall-configure), což pro správu IP klienta a fondy pracovních procesů App Service Environment
- [Detekce hrozeb SQL](/azure/sql-database/sql-database-threat-detection-get-started)
- [Vždy šifrované sloupce](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [Databáze SQL dynamická data maskování](/azure/sql-database/sql-database-dynamic-data-masking-get-started), pomocí skriptu prostředí PowerShell po nasazení

### <a name="logging-and-auditing"></a>Protokolování a auditování

[Analýza protokolu](https://azure.microsoft.com/services/log-analytics) můžete poskytnout Contoso Webstore rozsáhlé protokolování veškerou aktivitu systému a uživatele, zahrnují protokolování finanční údaje. Změny můžete zkontrolovat a ověřit přesnost. 

- **Protokoly aktivity.**  [Protokoly aktivity](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o činnosti, které byly provedeny v prostředky ve vašem předplatném.
- **Diagnostické protokoly.**  [Diagnostické protokoly](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou všechny protokoly vygenerované každých prostředkem. Tyto protokoly patří protokoly událostí systému Windows, protokol úložiště objektů Azure Blob, tabulek a protokoly fronty.
- **Protokoly brány firewall.**  Application Gateway poskytuje úplné diagnostiky a přístup k protokolům. Brány firewall protokoly jsou k dispozici pro aplikační bránu prostředky, které mají povolen firewall webových aplikací.
- **Archivace protokolu.**  Všechny diagnostické protokoly jsou nakonfigurovány k zápisu centralizovaný a šifrované účtu úložiště Azure pro archivaci s dobou uchování definované (2 dny). Protokoly jsou připojena k analýze protokolů Azure pro zpracování, ukládání a dashboarding. [Analýza protokolu](https://azure.microsoft.com/services/log-analytics) je služba, která pomáhá shromažďovat a analyzovat data generována prostředky ve vašem cloudu a místní prostředí.

### <a name="encryption-and-secrets-management"></a>Správa šifrování a tajné klíče

Contoso Webstore šifruje všechny citlivá data a používat Azure Key Vault Správa klíčů a zabránit načtení CHD.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pomáhá zabezpečit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. 
- [Šifrování TDE SQL](/sql/relational-databases/security/encryption/transparent-data-encryption) se používá k šifrování veškeré finanční údaje zákazníka.
- Data se ukládají na disku pomocí [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) a nástroje BitLocker.

### <a name="identity-management"></a>Správa identit

Následujících technologií poskytovat identitu možnosti správy v prostředí Azure.

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je Microsoft víceklientské cloudové adresáře a identity management service. Všechny uživatele pro řešení byly vytvořeny v Azure Active Directory, včetně uživatelů přístup k databázi SQL.
- Ověřování do aplikace se provádí pomocí služby Azure AD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho sloupce šifrování databáze také používá Azure AD k ověřování aplikace do Azure SQL Database. Další informace najdete v tématu [funkce Always Encrypted: chrání citlivá data v databázi SQL](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které mohou ovlivnit identity ve vaší organizaci, nakonfiguruje automatické odpovědi na zjištěné podezřelé akcí souvisejících s identity ve vaší organizaci, a prozkoumá podezřelé incidenty a provede příslušnou akci jejich řešení.
- [Azure na základě rolí řízení přístupu (RBAC)](/azure/role-based-access-control/role-assignments-portal) umožňuje přesněji správu cílených přístupu k Azure. Předplatné přístup je omezen na správce předplatného a Azure Key Vault přístup je omezen na všechny uživatele.

Další informace o používání funkcí zabezpečení databáze SQL Azure, najdete v článku [Contoso Klinika ukázkové aplikace](https://github.com/Microsoft/azure-sql-security-sample) ukázka.
   
### <a name="web-and-compute-resources"></a>Webové a výpočetní prostředky

#### <a name="app-service-environment"></a>App Service Environment

[Aplikační služba Azure](/azure/app-service/) je spravovaná služba pro nasazení webových aplikací. Nasazení aplikace Contoso Webstore jako [webové aplikace App Service](/azure/app-service-web/app-service-web-overview).

[Azure App Service Environment (App Service Environment v2)](/azure/app-service/app-service-environment/intro) je funkce služby App Service, která poskytuje plně izolovaném a vyhrazeném prostředí pro zabezpečené spouštění aplikací App Service ve velkém rozsahu. je plán služeb Premium používá tuto základní architekturu umožňující PCI DSS dodržování předpisů.

ASEs izolují spouštění jenom jednoho zákazníka aplikací a vždy nasazených do virtuální sítě. Zákazníci mají jemně odstupňovanou kontrolu nad obě aplikace příchozí a odchozí síťový provoz a aplikace může vytvořit vysokorychlostní zabezpečené připojení přes virtuální sítě k firemním prostředkům místně.

Použití ASEs pro tato architektura povolené pro následující ovládací prvky nebo konfigurace:

- Hostování v zabezpečené virtuální sítě a pravidel zabezpečení sítě
- App Service Environment nakonfigurovaný s ILB certifikát podepsaný svým držitelem pro komunikaci pomocí protokolu HTTPS
- [Interní režimu Vyrovnávání zatížení](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (režim 3)
- [Protokol TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) zakázáno
- [Šifer TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings) změnit
- Ovládací prvek [příchozí provoz N/W porty](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [Firewall webových aplikací – omezit Data](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Databáze SQL provoz](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) povoleno


#### <a name="jumpbox-bastion-host"></a>Jumpbox (bastionu hostitel)

Protože služba App Service Environment je zabezpečená a uzamčené, je potřeba povolit pro všechny verze DevOps nebo změny, které může být nezbytné, jako je například možnost monitorování webové aplikace pomocí modulu Kudu mechanismus. Virtuální počítač, je zabezpečena za NAT Vyrovnávání zatížení, která poskytuje možnost připojit se k virtuálnímu počítači na jiný port než TCP 3389. 

Virtuální počítač byl vytvořen jako jumpbox (bastionu hostitel) s následující konfigurace:

-   [Rozšíření proti malwaru](/azure/security/azure-security-antimalware)
-   [Rozšíření analýzy protokolů](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozšíření Azure Diagnostics](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) pomocí Azure Key Vault 
-   [Vypnutí automatického zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) snížit spotřebu prostředků virtuálního počítače, když není používán

### <a name="security-and-malware-protection"></a>Zabezpečení a malware ochrany

[Azure Security Center](https://azure.microsoft.com/services/security-center/) poskytuje centralizovanou zobrazení stavu zabezpečení prostředků Azure, všechny. Na první pohled můžete ověřit příslušná bezpečnostní prvky jsou na místě a správně nakonfigurovaný, a můžete rychle zjistit všechny prostředky, které vyžadují pozornost.  

[Azure Advisor](/azure/advisor/advisor-overview) je konzultantem přizpůsobené cloudu, který vám pomůže dodržujte doporučené postupy, chcete-li optimalizovat nasazení Azure. Ho analyzuje konfigurace prostředků a telemetrii využití a pak doporučuje řešení, které vám pomohou zlepšit efektivitu nákladů, výkon, vysokou dostupnost a zabezpečení vašich prostředků Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) pro cloudové služby Azure a virtuální počítače je ochrana v reálném čase funkci, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software s konfigurovat výstrahy, když známé škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v Azure systémech.

### <a name="operations-management"></a>Řízení provozu

#### <a name="application-insights"></a>Application Insights

Použití [Application Insights](https://azure.microsoft.com/services/application-insights/) k získání prakticky uplatnitelných informací prostřednictvím správy výkonu aplikací a rychlé analýzy.

#### <a name="log-analytics"></a>Log Analytics

[Analýza protokolu](https://azure.microsoft.com/services/log-analytics/) je služba, která vám pomůže shromažďovat a analyzovat data generována prostředky ve vašem cloudu a místní prostředí.

#### <a name="managment-solutions"></a>Řešení pro správu

Tyto další správu řešení by měl být a konfigurovány: 
- [Activity Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Analýza sítí Azure](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Sledování změn](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Analýza služby Key Vault](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Mapa služeb](/azure/operations-management-suite/operations-management-suite-service-map)
- [Zabezpečení a audit](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Antimalware](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Správa aktualizací](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Integrace Security Center

Výchozí nasazení slouží jako základní úroveň doporučení Security Center, které označují stav v pořádku a zabezpečené konfigurace. Můžete povolit shromažďování dat z Azure Security Center. Další informace najdete v tématu [Azure Security Center – Začínáme](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Nasazení řešení

Součásti pro nasazení tohoto řešení jsou k dispozici v [kód úložišti plán, podle kterého] [kódu úložišti]. Nasazení základní architektury vyžaduje několik kroků proveden prostřednictvím v5 Microsoft PowerShell. Pro připojení k webu, zadejte vlastní název domény (například contoso.com). Je to určeno, pomocí `-customHostName` přepínače v kroku 2. Další informace najdete v tématu [koupit vlastní název domény pro Azure Web Apps](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Vlastní název domény není nutné úspěšně nasadit a provozovat řešení, ale bude možné připojit se k webu pro demonstrační účely.

Skripty přidávat uživatele domény klienta služby Azure AD, který určíte. Microsoft doporučuje vytvoření nové služby Azure AD klienta pro použití jako testu.

Pokud narazíte na problémy při nasazení, přečtěte si téma [– nejčastější dotazy a řešení potíží s](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

Společnost Microsoft důrazně doporučuje, aby čistou instalaci prostředí PowerShell použít k nasazení řešení. Případně ověřte, že používáte nejnovější moduly potřebnými pro správné spuštění skriptů instalace. Tento příklad přihlásí k jumpbox (u chráněných hostitelů) a spouští následující příkazy. Všimněte si, že to umožňuje příkaz vlastní doménu.


1. Nainstalujte požadované moduly a správně nastavena role správce.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Podrobné informace o použití pokyny najdete v tématu [skriptu pokyny – instalační program účet správce a oprávnění](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. Instalace správy aktualizací řešení. 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Podrobné informace o použití pokyny najdete v tématu [pokyny skriptu – nasazení a konfigurace prostředků Azure](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. Log Analytics protokolování a monitorování. Při nasazení řešení, můžete otevřít pracovní prostor analýzy protokolů a ukázkové šablony součástí řešení úložiště lze použít k objasnění, jak lze nakonfigurovat monitorování řídicího panelu. Ukázka šablony, najdete v části [omsDashboards složky](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Všimněte si, že data se shromažďují ve analýzy protokolů pro šablony nasazení správně. To může trvat až hodinu nebo déle v závislosti na aktivitě lokality.
 
    Při nastavování vaší protokolování analýzy protokolů, zvažte, včetně těchto prostředků:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Model hrozeb

Diagram toku dat (diagramu toku dat) a ukázkový model hrozeb pro Contoso Webstore [Model hrozeb plán, podle kterého](https://aka.ms/pciblueprintthreatmodel).

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Matice odpovědnosti zákazníka

Je zodpovědností ponechá kopii zákazníků [matice souhrn odpovědnost](https://aka.ms/fsiblueprintcrm), který popisuje FFIEC požadavky, které zodpovídají zákazníka a ty, které zodpovídají Microsoft Azure.



## <a name="disclaimer-and-acknowledgments"></a>Právní omezení a potvrzování

*2017 září*

- Tento dokument je pouze informativní charakter. MICROSOFT A AVYAN PROVÉST ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.  
- Tento dokument neposkytuje zákazníkům žádná zákonná práva týkající se jakékoli produkty společnosti Microsoft nebo Avyan nebo řešení duševního vlastnictví.  
- Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.  

  > [!NOTE]
  > Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.  

- Řešení v tomto dokumentu slouží jako základní architektura a nesmí se používat jako-je pro produkční účely. Dosažení dodržování předpisů vyžaduje, aby zákazníci poraďte se s jejich auditor ověření řešení konečné zákazníka.  
- Všechny názvy zákazníků, záznamy transakce a všechna související data na této stránce jsou smyšlené, vytvoří pro účely této základní architektury a jenom ilustrativní. Je určen žádný skutečný vztah nebo připojení a žádné vyvozovat.  
- Toto řešení společně vyvinula společnost Microsoft a konzultace ohledně Avyan a je k dispozici v části [licencí MIT](https://opensource.org/licenses/MIT).

