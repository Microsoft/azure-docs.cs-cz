---
title: Zabezpečená webová aplikace pro PCI DSS | Dokumenty společnosti Microsoft
description: Tato ukázková aplikace implementuje osvědčené postupy zabezpečení, které zlepšují vaši aplikaci a stav zabezpečení vaší organizace při vývoji v Azure.
keywords: není k dispozici
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992611"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Vývoj zabezpečené infrastruktury pro aplikaci PCI

## <a name="overview"></a>Přehled

Tato zabezpečená infrastruktura pro aplikaci PCI (Payment Card Industry) poskytuje pokyny pro nasazení platformy payment card industry jako služby (PaaS) vhodné pro shromažďování, ukládání a načítání dat držitele karty. Toto řešení automatizuje nasazení a konfiguraci prostředků Azure pro společnou referenční architekturu a ukazuje způsoby, jakými zákazníci mohou splňovat specifické požadavky na zabezpečení a dodržování předpisů, a slouží jako základ pro zákazníky, kteří mohou vytvářet a konfigurovat vlastní řešení v Azure. Řešení implementuje podmnožinu požadavků podobných standardům zabezpečení dat v odvětví platebních karet (PCI DSS 3.2).

Tato ukázka automaticky nasazuje referenční architekturu webových aplikací PaaS s předem nakonfigurovanými ovládacími prvky zabezpečení, které zákazníkům pomohou dosáhnout shody podobné požadavkům PCI DSS 3.2. Řešení se skládá ze šablon Azure Resource Manager a skriptů PowerShellu, které řídí nasazení a konfiguraci prostředků.

Měli byste postupovat podle kroků popsaných v tomto článku postupně, abyste zajistili, že součásti aplikace jsou správně nakonfigurovány. Databáze, služba Azure App Service, instance Azure Key Vault a instance Azure Application Gateway závisí na sobě navzájem.

Skripty nasazení nastavují infrastrukturu. Po spuštění skriptů nasazení budete muset provést nějakou ruční konfiguraci na webu Azure Portal, abyste propojili součásti a služby dohromady.

Tato ukázka je zaměřená na zkušené vývojáře v Azure, kteří pracují v maloobchodním odvětví a chtějí vytvořit maloobchodní aplikaci se zabezpečenou infrastrukturou Azure.

> [!NOTE]
> Tato architektura má sloužit jako základ pro zákazníky, aby se přizpůsobili svým specifickým požadavkům a neměla by být používána tak, jak je v produkčním prostředí.

Nasazení aplikace do tohoto prostředí bez úprav není dostatečné pro úplné splnění požadavků PCI DSS 3.2. Je třeba počítat s následujícím:

- Tato architektura poskytuje směrný plán, který zákazníkům pomáhá používat Azure způsobem kompatibilním s PCI DSS 3.2.
- Zákazníci jsou zodpovědní za provedení příslušného posouzení zabezpečení a dodržování předpisů jakéhokoli řešení vytvořeného pomocí této architektury, protože požadavky se mohou lišit v závislosti na specifikách implementace každého zákazníka.

Při vývoji a nasazování této aplikace se dozvíte, jak:

- Vytvořte instanci Azure Key Vault a uložte a načtěte z ní tajné klíče.
- Nasaďte Azure Database pro Azure SQL, nastavte zabezpečená data a autorizujte k nim přístup.
- Nasaďte webovou aplikaci Azure s prostředím App Service, které je vyhrazené izolované s front-end firewalla aEcess.
- Vytvořte a nakonfigurujte instanci Aplikační brány Azure pomocí brány firewall, která používá [sadu pravidel OWASP Top 10](https://coreruleset.org/).
- Povolte šifrování dat při přenosu a v klidovém stavu pomocí služeb Azure.
- Nastavení zásad Azure a modrých výtisků pro vyhodnocení dodržování předpisů

Po vývoji a nasazení této aplikace budete mít nastavena následující ukázková webová aplikace spolu s konfigurací a bezpečnostními opatřeními, která jsou popsána.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Aplikace je typická n-vrstvá aplikace se třemi vrstvami. Front-end, back-end a databázová vrstva s integrovanými komponentami monitorování a správy tajných klíčů jsou zobrazeny zde:

![Architektura aplikací](./media/secure-pci-web-app/architecture.png)

Architektura se skládá z těchto komponent:

- [Prostředí služby App V2](https://docs.microsoft.com/azure/app-service/environment/intro/). Poskytuje prostředí služby App Service a vyrovnávání zatížení pro naši architekturu aplikací.
- [Brána aplikací Azure](https://docs.microsoft.com/azure/application-gateway/). Poskytuje bránu a bránu firewall pro naši aplikační architekturu.
- [Přehledy aplikací](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Poskytuje rozšiřitelnou službu Správy výkonu aplikací (APM) na více platformách.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Ukládá a šifruje tajné kódy naší aplikace a spravuje vytváření zásad přístupu kolem nich.
- [Služba Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Poskytuje cloudovou službu správy identit a přístupu, přihlášení a přístup k prostředkům.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Poskytuje službu pro hostování domény.
- [Azure Load Balancer:](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/) Škáluje vaše aplikace a vytváří vysokou dostupnost pro vaše služby
- [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Poskytuje řešení pro moderní scénáře úložiště dat.
- [Azure Web App](https://docs.microsoft.com/azure/app-service/overview/).  Poskytuje službu založenou na protokolu HTTP pro hostování webových aplikací.
- [Azure Database pro AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Bezpečně ukládá data naší aplikace.
- [Azure Blueprints](https://docs.microsoft.com/azure/governance/blueprints/overview/). Poskytuje specifikace a soulad s určitými standardy a požadavky.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Zásady Azure](https://docs.microsoft.com/azure/governance/policy/overview). Vyhodnotí vaše prostředky z důvodu nedodržování přiřazených zásad.

## <a name="threat-model"></a>Model hrozby
Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši firmu a aplikaci a zajištění, že je zaveden správný plán zmírnění.

Tato ukázka používá [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) k implementaci modelování hrozeb pro zabezpečenou ukázkovou aplikaci. Pomocí diagramu komponent a toků dat můžete identifikovat problémy a hrozby v rané fázi procesu vývoje. To šetří čas a peníze později.

Toto je model ohrožení pro ukázkovou aplikaci:

![Model hrozby](./media/secure-pci-web-app/threat-model.png)

Některé ukázkové hrozby a potenciální chyby zabezpečení, které generuje nástroj pro modelování hrozeb, jsou zobrazeny na následujícím snímku obrazovky. Model hrozeb poskytuje přehled o vystaveném povrchu útoku a vyzve vývojáře, aby přemýšleli o tom, jak zmírnit problémy.

![Výstup modelu hrozby](./media/secure-web-app/threat-model-output.png)

Například vkládání SQL v předchozím výstupu modelu hrozeb je zmírněno dezinfekcí uživatelských vstupů a použitím uložených funkcí v Azure Database pro PostgreSQL. Toto zmírnění zabraňuje svévolné provádění dotazů během čtení a zápisy dat.

Vývojáři zlepšují celkové zabezpečení systému tím, že zmírňují každou hrozbu ve výstupu modelu hrozeb.

## <a name="deployment"></a>Nasazení
### <a name="prerequisites"></a>Požadavky
Chcete-li aplikaci zprovoznit, je třeba nainstalovat tyto nástroje:

- Editor kódu pro úpravu a zobrazení kódu aplikace. [Visual Studio Code](https://code.visualstudio.com/) je open source možnost.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) ve vývojovém počítači.
- [Git](https://git-scm.com/) ve vašem systému. Git se používá k místnímu klonování zdrojového kódu.
- [jq](https://stedolan.github.io/jq/), unixový nástroj pro dotazování JSON uživatelsky přívětivým způsobem.

Tato ukázka se skládá z konfiguračních souborů JSON a skriptů Prostředí PowerShell, které zpracovává služba rozhraní API Azure Resource Manageru k nasazení prostředků v rámci Azure. Podrobné pokyny k nasazení jsou k dispozici [zde](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Rychlý start

1.  Klonujte nebo si stáhněte [toto](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) úložiště GitHub na místní pracovní stanici.
2.  Zkontrolujte 0-Setup-AdministrativeAccountAndPermission.md a spusťte poskytnuté příkazy.
3.  Nasaďte testovací řešení s ukázkovými daty společnosti Contoso nebo pilotujte počáteční produkční prostředí.

    Tento skript nasazuje prostředky Azure pro demonstraci webového úložiště pomocí ukázkových dat Contoso.

V tomto příkladu spustíte skript nasazení, který nasazuje webovou aplikaci do služby App Service a vytváří prostředky.

Existuje mnoho způsobů nasazení aplikací v Azure, včetně:

- Šablony Azure Resource Manageru
- PowerShell
- Azure CLI
- portál Azure
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="network"></a>Network (Síť)
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Skupiny zabezpečeníhttps://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sítě( obsahují seznamy řízení přístupu (ACLs), které umožňují nebo odepírají provoz v rámci virtuální sítě. Skupiny zabezpečení sítě lze použít k zabezpečení provozu na úrovni podsítě nebo jednotlivých virtuálních aplikací. Existují následující skupiny zabezpečení sítě:

- 1 skupina zabezpečení sítě pro aplikační bránu
- 1 skupina zabezpečení sítě pro prostředí služby App Service
- 1 skupina zabezpečení sítě pro Azure SQL Database
- 1 skupina zabezpečení sítě pro hostitele bastionu

Každá ze skupin zabezpečení sítě má otevřené specifické porty a protokoly, takže řešení může pracovat bezpečně a správně. Kromě toho jsou pro každou skupinu zabezpečení sítě povoleny následující konfigurace:

- Diagnostické protokoly ahttps://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) události( jsou povoleny a uloženy v účtu úložiště
- Protokoly Azure Monitor jsou připojené k diagnostice skupiny zabezpečení sítě(https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>Konfigurace nsg
Konfigurace nsg pro prostředí služby App Service by měla být nakonfigurována tak, jak je znázorněno na obrázku níže.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Každá podsíť je přidružena k odpovídající skupině zabezpečení sítě.

### <a name="config"></a>Config
Podsítě jsou konfigurovány tak, jak je znázorněno na obrázku níže.
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
Dns (Domain Name System) je zodpovědný za překlad (nebo překlad) názvu webu nebo služby na jeho IP adresu. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Azure. Hostováním domén v Azure můžou uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, virtuálních api, nástrojů a fakturace jako ostatní služby Azure. Azure DNS také podporuje privátní domény DNS.

### <a name="protect-data"></a>Ochrana dat
Chcete-li chránit data v cloudu, je třeba účet pro možné stavy, ve kterých může dojít k datům a jaké ovládací prvky jsou k dispozici pro tento stav. Doporučené postupy pro zabezpečení a šifrování dat Azure se týkají následujících stavů dat:

- V klidovém stavu: To zahrnuje všechny objekty úložiště informací, kontejnery a typy, které existují staticky na fyzickém médiu, ať už magnetický nebo optický disk.
- Přenos: Při přenosu dat mezi součástmi, lokacemi nebo programy jsou data na cestě. Příklady jsou přenos přes síť, přes sběrnici (z místní do cloudu a naopak, včetně hybridních připojení, jako je ExpressRoute) nebo během procesu vstupu a výstupu.

### <a name="azure-storage"></a>Azure Storage
Aby všechny [Azure Storage](https://azure.microsoft.com/services/storage/) splnily šifrovaná data v klidovém stavu, používají Azure Key Vault k udržování kontroly nad klíči, které přistupují k datům a šifrují je. To pomáhá chránit a chránit data držitele karty na podporu závazků zabezpečení organizace a požadavků na dodržování předpisů definovaných v systému PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption využívá funkci Nástroje bitlockeru systému Windows k zajištění šifrování svazků pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůže řídit a spravovat klíče šifrování disku.

### <a name="azure-sql-database"></a>Azure SQL Database
Instance Azure SQL Database používá následující opatření zabezpečení databáze:

- [Ověřování a autorizace služby Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a dalších služeb společnosti Microsoft v jednom centrálním umístění.
- [Auditování databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
- Azure SQL Database je nakonfigurovaná tak, aby používala [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), které provádí šifrování a dešifrování databáze v reálném čase, přidružené zálohy a soubory protokolu transakcí k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje záruku, že uložená data nebyla vystavena neoprávněnému přístupu.
- [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují veškerému přístupu k databázovým serverům, dokud nejsou udělena správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
- [Rozpoznávání hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekci a reakci na potenciální hrozby, jak k nim dochází tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální chyby zabezpečení, útoky injektáže SQL a neobvyklé vzory přístupu k databázi.
- [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajišťují, že citlivá data se nikdy nezobrazí jako prostý text uvnitř databázového systému. Po povolení šifrování dat mají k datům ve formátu prostého textu přístup pouze klientské aplikace nebo aplikační servery s přístupem ke klíčům.
- [Maskování dynamických dat sql database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat maskováním dat uživatelům nebo aplikacím bez oprávnění. Dynamické maskování dat může automaticky zjišťovat potenciálně citlivá data a navrhovat vhodné masky, které mají být použity. To pomáhá identifikovat a omezit přístup k datům tak, aby neukončildatabázi prostřednictvím neoprávněného přístupu. Zákazníci jsou zodpovědní za úpravu nastavení dynamického maskování dat tak, aby dodržovala jejich schéma databáze.

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují funkce pro správu přístupu k datům držitele karty v prostředí Azure:

- Azure Active Directory je cloudová služba a služba správy identit společnosti Microsoft s více klienty. Všichni uživatelé pro toto řešení se vytvářejí ve službě Azure Active Directory, včetně uživatelů, kteří přistupují k databázi Azure SQL Database.
- Ověřování aplikace se provádí pomocí služby Azure Active Directory. Další informace najdete v článku [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Šifrování sloupců databáze navíc používá Azure Active Directory k ověření aplikace do Azure SQL Database. Další informace najdete v tématu [jak chránit citlivá data v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Řízení přístupu na základě rolí Azure umožňuje správcům definovat jemně odstupňovaná přístupová oprávnění udělit pouze množství přístupu, které uživatelé potřebují k provádění svých úloh. Místo toho, aby každý uživatel neomezená oprávnění pro prostředky Azure, správci můžete povolit pouze určité akce pro přístup k datům držitele karty. Přístup k předplatnému je omezen na správce předplatného.
- Správa privilegovaných identit služby Azure Active Directory umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určitým informacím, jako jsou data držitele karty. Správci můžou pomocí správy privilegovaných identit služby Azure Active Directory zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tuto funkci lze také použít k vynucení přístupu pro správu na vyžádání, který je v případě potřeby v čase.
- Azure Active Directory Identity Protection detekuje potenciální chyby zabezpečení ovlivňující identity organizace, konfiguruje automatické odpovědi na zjištěné podezřelé akce související s identitami organizace a vyšetřuje podezřelé k přijetí vhodných opatření k jejich řešení.

### <a name="secrets-management"></a>Správa tajných kódů
Řešení používá Azure Key Vault pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault pomáhají zákazníkům chránit a přistupovat k těmto datům:

- Rozšířené zásady přístupu jsou konfigurovány na základě potřeby.
- Zásady přístupu trezoru klíčů jsou definovány s minimálními požadovanými oprávněními ke klíčům a tajným klíčům.
- Všechny klíče a tajné klíče v trezoru klíčů mají data vypršení platnosti.
- Všechny klíče v trezoru klíčů jsou chráněny specializovanými moduly hardwarového zabezpečení. Typ klíče je 2048bitový klíč RSA chráněný hsm.
- Pomocí trezoru klíčů můžete šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, . PFX soubory a hesla) pomocí klíčů, které jsou chráněny moduly hardwarového zabezpečení (HSM)
- Pomocí RBAC přiřaďte oprávnění uživatelům, skupinám a aplikacím v určitém oboru.
- Pomocí trezoru klíčů můžete spravovat certifikáty TLS s automatickým obnovením.
- Protokoly diagnostiky pro trezor klíčů jsou povoleny s dobou uchování alespoň 365 dnů.
- Povolené kryptografické operace pro klíče jsou omezeny na ty, které jsou požadovány.

### <a name="azure-security-center"></a>Azure Security Center
Pomocí Azure Security Center můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozebm a zjišťovat útoky a reagovat na ně. Azure Security Center navíc přistupuje k existujícím konfiguracím služeb Azure, aby vám pomohla zlepšit stav zabezpečení a chránit data.

Azure Security Center používá celou řadu funkcí detekce k upozornění zákazníků na potenciální útoky zaměřené na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných výstrah zabezpečení, které se aktivují, když dojde k ohrožení nebo podezřelé aktivity. Vlastní pravidla výstrah v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která jsou již shromažďovány z jejich prostředí.

Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, takže je pro zákazníky jednodušší zjistit a řešit potenciální problémy se zabezpečením. Pro každou zjištěnou hrozbu je generována zpráva o zpravodajské službě hrozeb, která pomáhá týmům pro reakci na incidenty při vyšetřování a nápravě hrozeb.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Architektura snižuje riziko ohrožení zabezpečení pomocí brány aplikace Azure s nakonfigurovanou bránou firewall webové aplikace a povolenou sadou pravidel OWASP. Mezi další možnosti patří:

- Ssl od konce do konce
- Zakázání tls v1.0 a v1.1
- Povolit TLSv1.2
- Brána firewall webové aplikace (režim prevence)
- Režim prevence se sadou pravidel OWASP 3.0
- Povolení diagnostického protokolování
- Vlastní sondy stavu
- Azure Security Center a Azure Advisor poskytují další ochranu a oznámení. Azure Security Center také poskytuje systém reputace.

### <a name="logging-and-auditing"></a>Protokolování a auditování
Služby Azure rozsáhle zaznamenávají aktivity systému a uživatelů a také stav systému:

- [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operacích prováděných s prostředky v předplatném. Protokoly aktivit mohou pomoci určit iniciátor operace, čas výskytu a stav.
- [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnují všechny protokoly vyzařované každý prostředek. Tyto protokoly zahrnují protokoly systému událostí systému Windows, protokoly úložiště Azure, protokoly auditu trezoru klíčů a protokoly přístupu k bráně aplikace a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu úložiště Azure pro archivaci. Uchovávání informací lze konfigurovat uživatelem až 730 dní tak, aby splňovalo požadavky na uchovávání informací specifické pro danou organizaci.

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
Tyto protokoly jsou konsolidovány v [protokolech Azure Monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění jsou data uspořádána do samostatných tabulek pro každý datový typ v rámci pracovních prostorů Log Analytics, což umožňuje analyzovat všechna data společně bez ohledu na původní zdroj. Azure Security Center se navíc integruje s protokoly Azure Monitor, které zákazníkům umožňují používat dotazy Kusto k přístupu k datům událostí zabezpečení a kombinovat je s daty z jiných služeb.

Následující [řešení monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure jsou zahrnuty jako součást této architektury:

- [Vyhodnocení služby Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Řešení kontroly stavu služby Active Directory v pravidelných intervalech vyhodnocuje rizika a stav serverových prostředí a poskytuje seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Sql Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Řešení SQL Health Check vyhodnocuje rizika a stav serverových prostředí v pravidelných intervalech a poskytuje zákazníkům seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Řešení stavu agenta hlásí, kolik agentů je nasazeno a jejich geografické rozložení, a kolik agentů neodpovídá a počet agentů, kteří odesílají provozní data.
- [Analýza protokolu aktivit](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure ve všech předplatných Azure pro zákazníka.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňuje organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledování volání rozhraní API v jejich prostředcích Azure.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba Application Performance Management pro webové vývojáře na více platformách. Application Insights detekuje anomálie výkonu a zákazníci ji můžou použít ke sledování živé webové aplikace. Obsahuje výkonné analytické nástroje, které zákazníkům pomáhají diagnostikovat problémy a pochopit, co uživatelé se svou aplikací skutečně dělají. Je navržen tak, aby pomohl zákazníkům neustále zlepšovat výkon a použitelnost.

### <a name="azure-key-vault"></a>Azure Key Vault
Vytvořte úschovnu pro organizaci, do které chcete ukládat klíče, a udržujte odpovědnost za provozní úkoly, jako je následující:

- Data uložená v trezoru klíčů zahrnují:

   - Klíč přehledu aplikace
   - Klíč přístupu k úložišti dat
   - Připojovací řetězec
   - Název tabulky dat
   - Pověření uživatele

- Rozšířené zásady přístupu jsou konfigurovány na základě potřeby
- Zásady přístupu trezoru klíčů jsou definovány s minimálními požadovanými oprávněními ke klíčům a tajným klíčům.
- Všechny klíče a tajné klíče v trezoru klíčů mají data vypršení platnosti
- Všechny klíče v trezoru klíčů jsou chráněny hsm [Typ klíče = 2048bitový klíč RSA chráněný hsm]
- Všem uživatelům/identitám jsou udělena minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí (RBAC).
- Aplikace nesdílejí trezor klíčů, pokud si vzájemně nedůvěřují a nepotřebují přístup ke stejným tajným kódům za běhu
- Protokoly diagnostiky pro trezor klíčů jsou povoleny s dobou uchování alespoň 365 dnů.
- Povolené kryptografické operace pro klíče jsou omezeny na ty, které jsou požadovány

### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
Zabezpečené tunelové propojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurováno tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást této referenční architektury webové aplikace PaaS. Vhodným nastavením sítě VPN nebo ExpressRoute mohou zákazníci přidat vrstvu ochrany dat při přenosu.

Implementací zabezpečeného tunelového propojení VPN s Azure lze vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelovat" informace uvnitř šifrovaného propojení mezi sítí zákazníka a Azure. Site-to-Site VPN je bezpečná, vyspělá technologie, která byla nasazena podniky všech velikostí po celá desetiletí. Režim tunelového propojení IPsec se používá v této možnosti jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internet pomocí sítě VPN site-to-site, společnost Microsoft nabízí další, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute neprocházejí přes Internet, tato připojení nabízejí vyšší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než běžná připojení přes Internet. Navíc vzhledem k tomu, že se jedná o přímé spojení poskytovatele telekomunikačních služeb zákazníka, data necestují přes internet, a proto mu nejsou vystavena.

K [dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou doporučené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť na Azure .

## <a name="cost-considerations"></a>Důležité informace o nákladech
Pokud ještě nemáte účet Azure, můžete si vytvořit bezplatný účet. Na [stránce bezplatného účtu](https://azure.microsoft.com/free/) můžete začít, zjistit, co můžete dělat s bezplatným účtem Azure, a zjistit, které produkty jsou zdarma po dobu 12 měsíců.

Chcete-li nasadit prostředky v ukázkové aplikaci s funkcemi zabezpečení, musíte zaplatit za některé prémiové funkce. Vzhledem k tomu, že aplikace se škáluje a bezplatné úrovně a zkušební verze nabízené Azure je třeba upgradovat tak, aby splňovaly požadavky aplikací, vaše náklady se mohou zvýšit. K odhadu nákladů použijte [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/) Azure.

## <a name="next-steps"></a>Další kroky
Následující články vám mohou pomoci navrhovat, vyvíjet a nasazovat zabezpečené aplikace.

- [Návrh](secure-design.md)
- [Rozvíjet](secure-develop.md)
- [Nasadit](secure-deploy.md)
