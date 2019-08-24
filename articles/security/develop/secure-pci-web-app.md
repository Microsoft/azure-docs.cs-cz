---
title: Zabezpečená webová aplikace pro PCI DSS | Microsoft Docs
description: Tato ukázková aplikace implementuje osvědčené postupy zabezpečení, které zlepšují vaši aplikaci a zabezpečení stav vaší organizace při vývoji v Azure.
keywords: ná
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992611"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Vývoj zabezpečené infrastruktury pro aplikaci PCI

## <a name="overview"></a>Přehled

Tato zabezpečená infrastruktura pro aplikaci pro odvětví platebních karet (PCI) poskytuje pokyny pro nasazení prostředí oborové platformy jako služby (PaaS) platební karty vhodné pro shromažďování, ukládání a načítání dat z karty. Toto řešení automatizuje nasazení a konfiguraci prostředků Azure pro společnou referenční architekturu, která demonstruje způsoby, kterými zákazníci můžou splnit konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky k sestavení a Nakonfigurujte vlastní řešení v Azure. Řešení implementuje podmnožinu požadavků, které jsou podobné standardům zabezpečení dat v odvětví platebních karet (PCI DSS 3,2).

Tato ukázka automaticky nasadí referenční architekturu webové aplikace v PaaS s předem nakonfigurovanými ovládacími prvky zabezpečení, aby zákazníkům pomohla splnit dodržování předpisů, které jsou podobné požadavkům PCI DSS 3,2. Toto řešení se skládá z Azure Resource Manager šablon a skriptů PowerShellu, které provedou nasazení a konfiguraci prostředků.

Postupujte podle kroků popsaných v tomto článku postupně a ujistěte se, že součásti aplikace jsou správně nakonfigurovány. Databáze, Azure App Service, instance Azure Key Vault a instance služby Azure Application Gateway, jsou na sobě navzájem závislé.

Skripty nasazení nastavily infrastrukturu. Po spuštění skriptů nasazení budete muset v Azure Portal provést několik ručních konfigurací, aby se komponenty a služby vzájemně procházely.

Tato ukázka je zaměřená na zkušené vývojáře v Azure, kteří pracují v rámci maloobchodního průmyslu a chtějí vytvořit maloobchodní aplikaci s zabezpečenou infrastrukturou Azure.

> [!NOTE]
> Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.

Nasazení aplikace do tohoto prostředí bez úprav není dostačující k úplnému splnění požadavků PCI DSS 3,2. Vezměte na vědomí následující:

- Tato architektura poskytuje směrný plán, který zákazníkům umožňuje využívat Azure ve vyhovujícím způsobem PCI DSS 3,2.
- Zákazníci zodpovídají za provádění vhodného posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

Při vývoji a nasazení této aplikace se naučíte:

- Vytvořte instanci Azure Key Vault a uložte si z ní tajné kódy a načtěte je z ní.
- Nasaďte Azure Database for Azure SQL, nastavte zabezpečená data a udělte k ní přístup.
- Nasazení webové aplikace Azure pomocí App Service prostředí, které je vyhrazené s front-end firewall aEcess.
- Vytvořte a nakonfigurujte instanci Azure Application Gateway s bránou firewall, která používá [OWASP prvních 10 RuleSet](https://coreruleset.org/).
- Povolí šifrování dat při přenosu a v klidovém provozu pomocí služeb Azure.
- Nastavení zásad Azure a modrých výtisků pro vyhodnocení dodržování předpisů

Po vývoji a nasazení této aplikace budete mít k dispozici následující ukázkovou webovou aplikaci spolu s popsanými opatřeními konfigurace a zabezpečení.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Aplikace je typickou n-vrstvou aplikací se třemi úrovněmi. Rozhraní front-end, back-end a databázová vrstva s integrovanými součástmi pro monitorování a správu tajného kódu se zobrazují tady:

![Architektura aplikací](./media/secure-pci-web-app/architecture.png)

Tato architektura se skládá z těchto součástí:

- [App Service Environment v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Poskytuje App Service Environment a nástroj pro vyrovnávání zatížení pro naši architekturu aplikace.
- [Application Gateway Azure](https://docs.microsoft.com/azure/application-gateway/). Poskytuje bránu a bránu firewall pro naši aplikační architekturu.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Poskytuje rozšiřitelnou službu správy výkonu aplikací (APM) na různých platformách.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Ukládá a šifruje tajné kódy naší aplikace a spravuje vytváření zásad přístupu.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Poskytuje cloudovou službu pro správu identit a přístupu, přihlašování a přístup k prostředkům.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Umožňuje službě hostovat doménu.
- [Azure Load Balancer:](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/) Škálujte aplikace a vytvoří vysokou dostupnost pro vaše služby.
- [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/) Poskytuje řešení pro scénáře moderních úložišť dat.
- [Webová aplikace Azure](https://docs.microsoft.com/azure/app-service/overview/).  Poskytuje službu založenou na protokolu HTTP pro hostování webových aplikací.
- [Azure Database for AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Bezpečně ukládá data naší aplikace.
- [Plány Azure](https://docs.microsoft.com/azure/governance/blueprints/overview/) Poskytuje specifikace a dodržování předpisů pomocí určitých standardů a požadavků.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview). Vyhodnocuje prostředky pro nedodržování předpisů pomocí přiřazených zásad.

## <a name="threat-model"></a>Model hrozeb
Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši firmu a aplikaci a následnému zajištění správného plánu zmírnění.

Tato ukázka používá [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) k implementaci modelování hrozeb pro zabezpečenou ukázkovou aplikaci. Díky vytváření diagramů komponent a toků dat můžete identifikovat problémy a hrozby včas v procesu vývoje. Tím ušetříte čas i peníze později.

Toto je model hrozeb pro ukázkovou aplikaci:

![Model hrozeb](./media/secure-pci-web-app/threat-model.png)

Některé ukázkové hrozby a potenciální ohrožení zabezpečení, které nástroj pro modelování hrozeb generuje, se zobrazují na následujícím snímku obrazovky. Model hrozeb poskytuje přehled o zpřístupnění plochy pro útok a vyzývá vývojáře, aby si myslí, jak tyto problémy zmírnit.

![Výstup modelu hrozeb](./media/secure-web-app/threat-model-output.png)

Například injektáže SQL na předchozím výstupu modelu hrozeb se omezuje úpravou uživatelských vstupů a pomocí uložených funkcí v Azure Database for PostgreSQL. Toto zmírnění brání libovolnému spuštění dotazů během čtení a zápisu dat.

Vývojáři zlepšují celkové zabezpečení systému tím, že zmírnit jednotlivé hrozby ve výstupu modelu hrozeb.

## <a name="deployment"></a>Nasazení
### <a name="prerequisites"></a>Požadavky
Chcete-li aplikaci nainstalovat a spustit, je nutné nainstalovat tyto nástroje:

- Editor kódu pro úpravu a zobrazení kódu aplikace. [Visual Studio Code](https://code.visualstudio.com/) je open-source možnost.
- Rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) ve vývojovém počítači.
- V systému [Git](https://git-scm.com/) . Git se používá ke klonování zdrojového kódu místně.
- [JQ](https://stedolan.github.io/jq/), nástroj pro systém UNIX pro dotazování formátu JSON uživatelsky přívětivým způsobem.

Tato ukázka se skládá z konfiguračních souborů JSON a skriptů PowerShellu, které jsou zpracovávány službou rozhraní API Azure Resource Manager k nasazení prostředků v rámci Azure. Podrobné pokyny k nasazení jsou k dispozici [zde](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Rychlý start

1.  Naklonujte nebo stáhněte [Toto](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) úložiště GitHub na místní pracovní stanici.
2.  Zkontrolujte 0-Setup-AdministrativeAccountAndPermission.md a spusťte poskytnuté příkazy.
3.  Nasaďte testovací řešení s ukázkovými daty společnosti Contoso nebo pilotem počáteční provozní prostředí.

    Tento skript nasadí prostředky Azure pro ukázku webového úložiště pomocí ukázkových dat společnosti Contoso.

V tomto příkladu spustíte skript nasazení, který nasadí webovou aplikaci do App Service a vytvoří prostředky.

Existuje mnoho způsobů, jak nasadit aplikace v Azure, včetně těchto:

- Šablony Azure Resource Manageru
- PowerShell
- Azure CLI
- portál Azure
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="network"></a>Síť
Architektura definuje privátní Virtual Network s adresním prostorem 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Skupiny zabezpečení sítě (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy Access Control (seznamy ACL), které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě se dají použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače. Existují následující skupiny zabezpečení sítě:

- 1 skupina zabezpečení sítě pro Application Gateway
- 1 skupina zabezpečení sítě pro App Service Environment
- 1 skupina zabezpečení sítě pro Azure SQL Database
- 1 skupina zabezpečení sítě pro hostitele bastionu

Každá ze skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každou skupinu zabezpečení sítě povolené tyto konfigurace:

- Diagnostické protokoly a události (https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště
- Protokoly Azure Monitor jsou připojené k diagnostice skupiny zabezpečení sítě (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG konfigurace
Konfigurace NSG pro App Service Environment by měla být nakonfigurovaná tak, jak je znázorněno na obrázku níže.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Každá podsíť je přidružená ke své odpovídající skupině zabezpečení sítě.

### <a name="config"></a>Konfigurace
Podsítě jsou konfigurovány tak, jak je znázorněno na obrázku níže.
 ![Konfigurace](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
Služba DNS (Domain Name System) zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Azure. Díky hostování domén v Azure můžou uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jiné služby Azure. Azure DNS podporuje i privátní domény DNS.

### <a name="protect-data"></a>Ochrana dat
Aby bylo možné chránit data v cloudu, musíte se přihlásit k možným stavům, ve kterých mohou být data provedena, a jaké ovládací prvky jsou pro daný stav k dispozici. Osvědčené postupy zabezpečení a šifrování dat Azure souvisejí s následujícími stavy dat:

- V klidovém umístění: To zahrnuje všechny informace o objektech úložiště, kontejnerech a typech, které existují staticky na fyzických médiích, ať už magnetickém nebo optickým diskem.
- Při přenosu: Při přenosu dat mezi součástmi, umístěními nebo programy se jedná o přenos. Příklady jsou přenosy přes síť, přes službu Service Bus (z místního prostředí do cloudu a naopak, včetně hybridních připojení, jako je ExpressRoute) nebo během procesu vstupu a výstupu.

### <a name="azure-storage"></a>Azure Storage
Pro splnění požadavků na uchovávání šifrovaných dat používá všechny [Azure Storage](https://azure.microsoft.com/services/storage/) Azure Key Vault k zachování kontroly nad klíči, které mají přístup k datům a jejich šifrování. To pomáhá chránit a chránit data držitele v podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů definovaných PCI DSS 3,2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

### <a name="azure-sql-database"></a>Azure SQL Database
Instance Azure SQL Database používá následující bezpečnostní opatření databáze:

- [Ověřování a autorizace služby Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
- [Auditování služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
- Azure SQL Database je nakonfigurované pro použití [transparentního šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), které provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase, aby chránila informace v klidovém znění. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
- [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
- [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekci a reakci na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
- [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajišťují, že citlivá data se v databázovém systému nikdy neobjeví v podobě prostého textu. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
- [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat tím, že se data maskují na uživatele nebo aplikace bez oprávnění. Dynamické maskování dat může automaticky zjišťovat potenciálně citlivá data a navrhovat vhodné masky pro použití. Pomůže vám identifikovat a snížit přístup k datům tak, že neukončí databázi prostřednictvím neoprávněného přístupu. Zákazníci zodpovídají za úpravu nastavení dynamického maskování dat, aby odpovídaly schématu databáze.

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují možnosti pro správu přístupu k datům držitele v prostředí Azure:

- Azure Active Directory je víceklientské cloudová služba Microsoftu a služba pro správu identit, která je založená na víceklientské architektuře. Všichni uživatelé tohoto řešení jsou vytvořeni v Azure Active Directory, včetně uživatelů, kteří přistupují k Azure SQL Database.
- Ověřování pro aplikaci se provádí pomocí Azure Active Directory. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Kromě toho šifrování sloupce databáze používá Azure Active Directory k ověření aplikace pro Azure SQL Database. Další informace najdete v tématu [jak chránit citlivá data v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Řízení přístupu na základě role v Azure umožňuje správcům definovat jemně odstupňovaná přístupová oprávnění, která jim umožňují udělit jenom přístup, který uživatelé potřebují k provádění svých úloh. Místo udělení oprávnění pro všechny uživatele bez omezení přístupu k prostředkům Azure můžou správci pro přístup k datům na držiteli aplikace použít jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- Azure Active Directory Privileged Identity Management zákazníkům umožňuje minimalizovat počet uživatelů, kteří mají přístup k určitým informacím, jako jsou například data o držiteli. Správci můžou pomocí Azure Active Directory Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- Azure Active Directory Identity Protection detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace, nakonfiguruje automatizované odezvy na zjištěné podezřelé akce související s identitami organizace a prošetří podezřelé incidenty, které mají přijmout vhodná opatření k jejich vyřešení.

### <a name="secrets-management"></a>Správa tajných kódů
Řešení používá Azure Key Vault pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující možnosti Azure Key Vault můžou zákazníkům chránit a přistupovat k těmto datům:

- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typ klíče je 32bitový klíč RSA s ochranou 2048.
- Pomocí Key Vault můžete šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat). Soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM)
- Pomocí RBAC můžete přiřadit oprávnění uživatelům, skupinám a aplikacím v určitém oboru.
- Pomocí Key Vault můžete spravovat certifikáty TLS s automatickým obnovením.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

### <a name="azure-security-center"></a>Azure Security Center
Díky Azure Security Center můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Kromě toho Azure Security Center přistupuje k existujícím konfiguracím služeb Azure a poskytuje doporučení ke konfiguraci a službám, které vám pomůžou zlepšit zabezpečení stav a chránit data.

Azure Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných výstrah zabezpečení, které se aktivují při výskytu hrozby nebo podezřelé aktivity. Vlastní pravidla výstrah v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která už jsou z jejich prostředí shromážděná.

Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, které zákazníkům zjednodušují zjišťování a řešení potenciálních problémů se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje sestava analýzy hrozeb, která pomáhá týmům reakce na incidenty při zkoumání a opravai hrozeb.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Architektura snižuje riziko ohrožení zabezpečení pomocí Application Gateway Azure s nakonfigurovanou bránou firewall webových aplikací a povoleným rulesetem OWASP. Mezi další možnosti patří:

- Od začátku do konce-SSL
- Zakázat TLS v 1.0 a v 1.1
- Povolit TLSv 1.2
- Firewall webových aplikací (režim prevence)
- Režim prevence s OWASP 3,0 RuleSet
- Povolení diagnostického protokolování
- Vlastní sondy stavu
- Azure Security Center a Azure Advisor poskytují dodatečnou ochranu a oznámení. Azure Security Center také nabízí systém reputace.

### <a name="logging-and-auditing"></a>Protokolování a auditování
Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:

- [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly Azure Storage, Key Vault protokoly auditu a protokoly Application Gateway přístupu a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uchovávání dat je uživatelsky konfigurovatelné, až 730 dní, aby se splnily požadavky na uchovávání specifické pro konkrétní organizaci.

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
Tyto protokoly jsou konsolidovány v [protokolech Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění jsou data uspořádaná do samostatných tabulek pro každý datový typ v rámci Log Analyticsch pracovních prostorů, což umožňuje analyzovat všechna data bez ohledu na její původní zdroj. Kromě toho Azure Security Center integruje s protokoly Azure Monitor a umožňuje zákazníkům používat dotazy Kusto pro přístup k datům událostí zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure:

- [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Řešení Agent Health oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet agentů, kteří nereagují a kolik agentů odesílá provozní data.
- [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

### <a name="azure-monitor"></a>Azure Monitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňují organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledovacích volání rozhraní API ve svých prostředcích Azure.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba pro správu výkonu aplikací pro webové vývojáře na různých platformách. Application Insights detekuje anomálie výkonu a zákazníci je můžou použít k monitorování živé webové aplikace. Obsahuje výkonné analytické nástroje, které zákazníkům pomohou diagnostikovat problémy a porozumět tomu, co uživatelé skutečně dělají s aplikací. Je navržený tak, aby pomohla zákazníkům neustále zlepšovat výkon a použitelnost.

### <a name="azure-key-vault"></a>Azure Key Vault
Vytvořte trezor pro organizaci, do které se mají ukládat klíče, a udržujte zodpovědnost za provozní úlohy, jako jsou uvedené níže:

- Data uložená v Key Vault zahrnují:

   - Klíč Application Insight
   - Přístupový klíč k úložišti dat
   - Připojovací řetězec
   - Název tabulky dat
   - Přihlašovací údaje uživatele

- Zásady pokročilého přístupu jsou nakonfigurované podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněné modulem HSM [typ klíče = HSM Protected 2048-bit RSA Key].
- Všem uživatelům/identitám se uděluje minimální požadovaná oprávnění pomocí Access Control na základě rolí (RBAC).
- Aplikace nesdílejí Key Vault, pokud mezi sebou vzájemně nedůvěřují a potřebují přístup ke stejným tajným klíčům za běhu.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) je třeba nakonfigurovat tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást referenční architektury webové aplikace PaaS. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a službou Azure Virtual Network. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "Tunnel" informace v rámci šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. Režim tunelového propojení IPsec se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, společnost Microsoft nabízí jinou, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Vzhledem k tomu, že se jedná o přímé připojení zprostředkovatele telekomunikačních komunikací zákazníka, data necestují přes Internet, a proto se k nim nezveřejňují.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

## <a name="cost-considerations"></a>Důležité informace o nákladech
Pokud ještě nemáte účet Azure, můžete si vytvořit nějaký bezplatný. Přejděte na [stránku bezplatný účet](https://azure.microsoft.com/free/) , abyste mohli začít, podívejte se, co můžete dělat s bezplatným účtem Azure, a zjistěte, které produkty jsou zdarma po dobu 12 měsíců.

Pokud chcete nasadit prostředky v ukázkové aplikaci s funkcemi zabezpečení, musíte platit za některé prémiové funkce. Vzhledem k tomu, že se aplikace škáluje a úrovně Free a zkušební verze, které nabízí Azure, je potřeba upgradovat, aby splňovaly požadavky na aplikace, může dojít ke zvýšení nákladů. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) Azure můžete odhadnout náklady.

## <a name="next-steps"></a>Další postup
Následující články vám pomůžou při návrhu, vývoji a nasazení zabezpečených aplikací.

- [Vytvořit](secure-design.md)
- [Vývoj](secure-develop.md)
- [Nasazení](secure-deploy.md)
