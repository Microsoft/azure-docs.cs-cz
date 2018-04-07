---
title: Protokolování a auditování Azure | Microsoft Docs
description: Informace o tom, jak můžete data protokolování a získáte přehled o hloubkové o vaší aplikaci.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: c82b56cdf0fc2cb288986cf8fbf43c2dab5eacb6
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-logging-and-auditing"></a>Azure protokolování a auditování
## <a name="introduction"></a>Úvod
### <a name="overview"></a>Přehled
Aktuální a potenciální zákazníci Azure pomáhá vysvětlující Princip fungování a pomocí různých související se zabezpečením možnosti dostupné v a které obaluje platformě Azure, společnost Microsoft vyvinula řadu dokumenty white paper, zabezpečení přehledy, osvědčené postupy a kontrolní seznamy. Témata v rozsahu z hlediska spektra a hloubky a jsou pravidelně aktualizovány. Tento dokument je součástí této řady dle souhrnu v následující části abstraktní.
### <a name="azure-platform"></a>Platformy Azure
Azure je platforma otevřené a flexibilní cloudové služby, která podporuje nejširší výběr operačních systémů, programovací jazyky, rozhraní, nástroje, databází a zařízení.

Můžete například provést následující věci:
-   Spusťte Linux kontejnery s integrace Dockeru.

-   Vývoj aplikací pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js

-   Sestavení back EndY pro iOS, Android a Windows zařízení.

Služby veřejného cloudu Azure podporují stejné technologie miliony vývojářů a IT profesionály již spoléhají na a vztah důvěryhodnosti.

Pokud sestavení nebo IT prostředky se mají migrovat, poskytovatele cloudových služeb, můžete se spoléhat na dané organizace dalo k ochraně vašich aplikací a dat se službami a ovládací prvky poskytují ke správě zabezpečení vaše cloudové prostředky.

Infrastruktura Azure je od zařízení až po aplikace navržena tak, aby umožňovala hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro splnění potřeb zabezpečení jednotlivých podniků. Azure navíc poskytuje širokou škálu konfigurovatelných možností zabezpečení a umožňuje jejich nastavování, takže můžete zabezpečení přizpůsobit jedinečným požadavkům svého nasazení. Tento dokument umožňuje splňovat tyto požadavky.

### <a name="abstract"></a>Abstraktní
Auditování a protokolování událostí souvisejících se zabezpečením a související výstrahy, jsou důležité součásti v strategie ochrany počáteční datum. Protokoly zabezpečení a sestavy poskytují záznam elektronické podezřelé aktivity a nápovědu zjišťování struktur, které můžou signalizovat úspěšné nebo neúspěšné pokusy o externí průnikům sítě, jakož i interní útoky. Auditování můžete použít k monitorování aktivity uživatelů, dodržování legislativních dokumentu, provádět forenzní analýzu a další. Výstrahy zadejte okamžité odeslání oznámení, když dojde k události zabezpečení.

Služby Microsoft Azure a produkty poskytují konfigurovat zabezpečení, auditování a protokolování možnosti vám pomohou zjistí nesrovnalosti v vaše zásady a mechanismy zabezpečení a vyřešte tyto mezery, aby se zabránilo narušení. Služby Microsoft nabízejí některé (a v některých případech všechny) z následujících možností: centralizované sledování, protokolování a analýza systémy zajistit nepřetržitou viditelnost; včas výstrah; a sestavy, které vám pomohou spravovat velké množství informací, které jsou generované zařízení a služeb.

Data protokolu Microsoft Azure je možné exportovat do systémů incidentu zabezpečení a událostí správy (SIEM) pro analýzu a umožňuje integraci s řešeními třetích stran auditování.

Tento dokument White Paper je úvodem pro generování, shromažďování a analýzu protokolů zabezpečení ze služby hostované v Azure a umožní vám získat přehled o zabezpečení do Azure nasazení. Rozsah tento dokument je omezený na aplikace a služby vytvořené a nasazené v Azure.

> [!Note]
> Některá doporučení v nich obsažených může vést k vyšší data, síťové nebo využití výpočetních prostředků a zvýšit náklady na licence nebo předplatné.

## <a name="types-of-logs-in-azure"></a>Typy protokolů v Azure
Cloudové aplikace jsou komplexní s mnoha přesunutí částmi. Protokoly poskytují data a ujistěte se, že vaše aplikace zůstává nahoru a spuštěna v dobrém stavu. Také pomáhá stave vypnout potenciální problémy nebo vyřešit potíže s uplynulou těch, které jsou. Kromě toho můžete data protokolování a získáte přehled o hloubkové o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

Azure vytvoří rozsáhlé protokolování pro všechny služby Azure. Tyto protokoly jsou rozdělené podle těchto hlavních typů:
-   **Řízení nebo protokoly** poskytují přehled o činnosti Azure Resource Manager vytvoření, aktualizace a odstranění. [Azure protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) je příkladem tohoto typu protokolu.

-   **Data roviny protokoly** poskytují přehled o události vyvolané jako součást využití prostředek služby Azure. Příkladů tohoto typu protokolu událostí systému Windows systém, zabezpečení, a protokoly aplikace ve virtuálním počítači a [protokolů diagnostiky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) nakonfigurovaný pomocí Azure monitorování


-   **Zpracování události** poskytnout informace o analyzovaných události a výstrahy, které byly zpracovány vaším jménem. Příkladem tohoto typu jsou [Azure Security Center výstrahy](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) kde [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) je zpracován a analyzovat vaše předplatné a poskytuje výstrahy stručným zabezpečení

V následující tabulce uveden nejdůležitější typ protokolů dostupných v Azure.

| Kategorie protokolu | Log Type | Použití | Integrace |
| ------------ | -------- | ------ | ----------- |
|[Protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Události rovině řízení na prostředky Azure Resource Manager|   Získáte přehled o činnosti, které byly provedeny v prostředky ve vašem předplatném.| REST API & [Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure diagnostických protokolů](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|časté data o operaci prostředky Azure Resource Manager v rámci předplatného| Získat přehled o operace, aby prostředku provedeny sám sebe| Azure Monitor, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Vytváření sestav AAD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Protokoly a sestavy|Uživatelské přihlašovací aktivity & systému aktivity informace o uživatelích a Správa skupin|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuální počítač & cloudové služby](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Protokolu událostí systému Windows a Linux Syslog|    Zaznamená data systému a data protokolování na virtuálních počítačích a přenosy dat do účtu úložiště podle vašeho výběru.|   Pomocí Windows [WAD](https://docs.microsoft.com/azure/azure-diagnostics) (Windows Azure Diagnostics úložiště) a Linux v nástroji Azure sledování|
|[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Protokolování úložiště a poskytuje data metriky pro účet úložiště|Poskytuje vhled do trasování požadavků, analyzovat trendy využití a diagnostikovat problémy s vaším účtem úložiště.|    Rozhraní API REST nebo [klientské knihovny](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Tok protokolů NSG (skupina zabezpečení sítě)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formát JSON a zobrazuje toky odchozí a příchozí pravidlo za den|Zobrazení informací o příchozí a odchozí provoz IP prostřednictvím skupinu zabezpečení sítě|[Sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Přehled aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Protokoly, výjimky a vlastní diagnostiky|    Služba správy výkonu (APM) aplikace pro web vývojářů ve více platformách.| Rozhraní REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Zpracování dat nebo výstrahy zabezpečení| Azure Security Center Alert, Log Analytics Alert|   Informace o zabezpečení a výstrahy.|   Rozhraní REST API, JSON|

### <a name="activity-log"></a>Protokol aktivit
[Protokol činnosti Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), poskytuje vhled do činnosti, které byly provedeny v prostředky ve vašem předplatném. Protokol aktivit se dřív označovala jako "Protokoly auditu" nebo "Provozní protokoly," vzhledem k tomu, že se hlásí [rovině řízení události](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) pro vaše předplatné. Pomocí protokolu činnosti, můžete určit ", kdo a kdy" pro všechny zápisu operace (PUT, POST, DELETE) na prostředky v rámci vašeho předplatného. Můžete také chápou stav operace a další relevantní vlastnosti. Protokol aktivit nezahrnuje operace čtení (GET).

Zde PUT, POST, DELETE odkazuje na všechny obsahuje protokol aktivit operací zápisu na prostředky. Můžete například protokoly aktivity najít k chybě při odstraňování problémů s nebo k monitorování, jak upravit uživatele ve vaší organizaci prostředek.

![Protokol aktivit](./media/azure-log-audit/azure-log-audit-fig1.png)


Můžete načíst události z protokolu aktivit pomocí portálu Azure [rozhraní příkazového řádku](https://docs.microsoft.com/azure/storage/storage-azure-cli), rutiny prostředí PowerShell a [REST API služby Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Protokoly aktivity mají období uchovávání dat dne 19.

Scénáře integrace
-   [Vytvořte výstrahu e-mailu nebo webhooku, která aktivuje vypnout aktivity protokolu události.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Stream do centra událostí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) pro přijímání službu třetí strany nebo řešení vlastní analýzy, jako je například PowerBI.

-   Analyzovat v Power BI pomocí [balíček obsahu Power BI.](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)

-   [Uložte na účet úložiště pro archivaci nebo ruční kontroly.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) Můžete zadat dobu uchování (ve dnech) pomocí protokolu profilů.

-   Dotaz a zobrazit na portálu Azure.

-   Dotaz je pomocí rutiny prostředí PowerShell, rozhraní příkazového řádku nebo REST API.

-   Export protokolu aktivit s profily protokolu, aby [protokolu analýzy](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Můžete použít účet úložiště nebo [názvový prostor události rozbočovače](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) , není ve stejném předplatném jako jeden generování protokolu. Uživatel, který konfiguruje nastavení, musí mít odpovídající [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) přístup k oba odběry
### <a name="azure-diagnostic-logs"></a>Azure diagnostických protokolů
Azure diagnostické protokoly jsou vysílaných prostředků, které poskytují bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku (například [protokoly událostí systému Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)jsou jednu kategorii protokolů diagnostiky pro virtuální počítače a [objektů blob, tabulky a fronty protokoly](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) kategorií diagnostické protokoly pro účty úložiště) a liší od protokol aktivit, které poskytuje vhled do činnosti, které byly provedeny v prostředky ve vašem předplatném.

![Azure diagnostických protokolů](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure Diagnostics protokoly nabízí několik možností konfigurace, které je, portál Azure, pomocí prostředí PowerShell, rozhraní příkazového řádku (CLI) a rozhraní REST API.

**Scénáře integrace**
-   Uložte je do [účet úložiště](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) pro auditování nebo ruční kontroly. Můžete zadat dobu uchování (ve dnech) používá nastavení pro diagnostiku.

-   [Stream je do centra událostí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) pro přijímání službu třetí strany nebo řešení vlastní analýzy, jako [PowerBI.](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

-   Analyzovat, s [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

**Podporované služby, schéma pro diagnostické protokoly a kategorie podporované protokolu na typ prostředku**


| Služba | Schéma & dokumentace | Typ prostředku | Kategorie |
| ------- | ------------- | ------------- | -------- |
|Load Balancer| [Analýzy protokolů pro vyrovnávání zatížení Azure (Preview)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Network Security Groups (Skupiny zabezpečení sítě)|[Analýza protokolu pro skupiny zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Brány Application Gateway|[Protokolování diagnostiky pro službu Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Key Vault|[Protokolování v Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Povolení a používání Analýza provozu vyhledávání](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[Přístup k diagnostickým protokolům pro Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Auditování|
|Data Lake Analytics|[Přístup k protokolům diagnostiky pro Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Auditování|
|||Microsoft.DataLakeAnalytics/accounts|Požadavky|
|||Microsoft.DataLakeStore/accounts|Požadavky|
|Logic Apps|[Vlastní schéma sledování B2B Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|Modul runtime pracovního postupu|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Protokolování diagnostiky Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Analýzy protokolů pro Azure Automation.](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Event Hubs|[Diagnostické protokoly služby Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[Diagnostické protokoly úlohy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Spouštěcí|
|||Microsoft.StreamAnalytics/streamingjobs|Vytváření obsahu|
|Service Bus|[Diagnostické protokoly služby Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Generování sestav Azure Active Directory
Azure Active Directory (Azure AD) obsahuje různé sestavy zabezpečení, aktivit a auditu pro váš adresář. [Azure Active Directory sestava auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomáhá zákazníkům k identifikaci privilegovaných akcí, které došlo k chybě v Azure Active Directory. Privilegované akce zahrnují změny zvýšení oprávnění (například role vytvoření nebo resetování hesla), změna konfigurace zásad (třeba zásady pro hesla) nebo změny konfigurace adresáře (například změny nastavení federace domén).

Sestavy poskytují záznam auditu z názvu události objektu actor, který provedl akci, cílový prostředek vliv na změny a datum a čas (ve formátu UTC). Zákazníci mohou načíst seznam událostí auditu pro Azure Active Directory pomocí [portál Azure](https://portal.azure.com/), jak je popsáno v [zobrazit protokoly auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Zde je seznam zahrnutých sestav:

| Sestavy zabezpečení | Sestavy aktivit | Sestavy auditu |
| :--------------- | :--------------- | :------------ |
|Přihlášení z neznámých zdrojů| Využití aplikací: souhrn| Sestava auditu adresáře|
|Přihlášení po několika neúspěších|  Využití aplikací: podrobnosti||
|Přihlášení z více geografických poloh|    Řídicí panel aplikací||
|Přihlášení z IP adres s podezřelou aktivitou|   Chyby zřizování účtů||
|Nestandardní přihlašovací aktivita|    Zařízení jednotlivých uživatelů||
|Přihlášení z možných nakažených zařízení|   Aktivity jednotlivých uživatelů||
|Uživatelé s neobvyklou přihlašovací aktivitou| Sestava aktivit skupin||
||Sestava aktivit registrace resetování hesla||
||Aktivity resetování hesla|||

Data z nich může být užitečné pro vaše aplikace, jako je například systémů SIEM, auditování a nástroje business intelligence. Rozhraní API pro generování sestav v Azure AD poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Můžete volat tyto [rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) z různých programovacích jazyků a nástroje.

Události sestavy auditování Azure AD jsou uchovány na 180 dní.

> [!Note]
> Další informace o uchovávání dat v sestavách najdete v tématu [zásady uchování sestav Azure ve službě Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)

Pro zákazníky ukládat svoje události auditu delší dobu uchovávání dat, lze použít rozhraní API pro vytváření sestav pravidelně vyžádání [události auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) do samostatné datové úložiště.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Virtuální počítač protokolů pomocí Azure Diagnostics
[Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) je funkce v rámci Azure, která umožňuje shromažďování diagnostických dat na nasazené aplikace. Můžete použít rozšíření diagnostiky z několika různých zdrojů. Aktuálně podporované jsou [webové služby Azure Cloud a rolí pracovního procesu](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me),

![Virtuální počítač protokolů pomocí Azure Diagnostics](./media/azure-log-audit/azure-log-audit-fig3.png)

[Virtuální počítače Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) systémem Microsoft Windows a [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

Můžete povolit diagnostiky Azure na virtuální počítač pomocí následující:

-   Pomocí sady Visual Studio, najdete v části [pomocí aplikace Visual Studio trasování virtuální počítače Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [Nastavení Azure Diagnostics na služby Azure Virtual Machine vzdáleně](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Nastavení diagnostiky ve virtuálních počítačích Azure pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Vytvoření Windows virtuálního počítače s monitorování a Diagnostika pomocí šablony Azure Resource Manageru](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště. Je k dispozici pro úložiště Analytics protokolování [službám Blob, fronty a tabulky.](https://docs.microsoft.com/azure/storage/storage-introduction) Analytika úložiště protokoly podrobné informace o úspěšných a nezdařených požadavků a služba úložiště.

Tyto informace slouží k monitorování jednotlivých požadavků a diagnostikovat problémy s služby úložiště. Na základě typu best effort protokolované požadavky. Položky protokolu se vytvoří pouze v případě, že jsou požadavky na koncový bod služby. Například pokud má aktivita v svůj koncový bod objektu Blob účet úložiště, ale není v jeho tabulku nebo frontu koncových bodů, pouze protokoly, která se týkají služby objektů Blob je vytvořen.

Pokud chcete používat analytika úložiště, musíte povolit ji jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ho povolit [portál Azure](https://portal.azure.com/); podrobnosti najdete v tématu [monitorování účtu úložiště na portálu Azure.](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) Můžete také povolit Storage Analytics programově pomocí rozhraní REST API nebo knihovny klienta. Pomocí operace nastavit vlastnosti služby tak, aby Storage Analytics samostatně pro každou službu.

Agregovaná data se ukládají v dobře známé objektu blob (pro protokolování) a dobře známé tabulky (pro metriky), které můžete získat přístup pomocí služby objektů Blob a služby Table rozhraní API.

Analytika úložiště může mít 20 TB na množství uložených dat, která je nezávislá celkový limit pro váš účet úložiště. Všechny protokoly jsou uloženy v [objekty BLOB bloků](https://docs.microsoft.com/azure/storage/storage-analytics) v kontejneru nazvaném $logs, které automaticky vytvářejí, když je pro účet úložiště povolená analytika úložiště.

> [!Note]
> Další informace o fakturaci a zásad uchovávání dat najdete v tématu [Storage Analytics a fakturace.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)
>
> [!Note]
> Další informace o limity účtu úložiště najdete v tématu [Azure úložiště škálovatelnost a cíle výkonnosti.](https://docs.microsoft.com/azure/storage/storage-scalability-targets)

Následující typy požadavků na ověření a anonymní přihlášeni.



| Ověřeno  | Anonymní|
| :------------- | :-------------|
| Úspěšné požadavky | Úspěšné požadavky |
|Neúspěšné požadavky, včetně vypršení časového limitu, omezení šířky pásma, sítě, autorizace a dalších chyb | Požadavky pomocí sdíleného přístupového podpisu (SAS), včetně žádostí úspěšné a neúspěšné |
| Požadavky pomocí sdíleného přístupového podpisu (SAS), včetně žádostí úspěšné a neúspěšné |Chyby časového limitu pro klienta a serveru |
|   Požadavky na analytická data |    Neúspěšné požadavky GET s kódem chyby 304 (upraveno) |
| Požadavky na úložiště Analytics samostatně, jako je například protokol vytvoření nebo odstranění, se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolované](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [úložiště analýzy protokolů formátu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) témata. | Všechny ostatní selhání anonymních požadavků se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolované](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [úložiště analýzy protokolů formátu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Azure síťové protokoly
Sítě protokolování a monitorování v Azure je komplexní a zahrnuje dvě rozsáhlé kategorie:

-   [Sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) -monitorování na základě scénáře sítě se poskytuje s funkcí v sledovací proces sítě. Tato služba obsahuje zachytáváním paketů, další směrování, IP tok ověření, zobrazení skupiny zabezpečení, tok protokolů NSG. Scénář úrovně monitorování poskytuje pohledu koncové síťových prostředků, na rozdíl od monitorování jednotlivých síťových prostředků.

-   [Sledování prostředků](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) -sledování na úrovni prostředků se skládá ze čtyř funkce, diagnostické protokoly, metriky, řešení potíží s a stav prostředků. Všechny tyto funkce jsou postavené na úrovni prostředků sítě.

![Azure síťové protokoly](./media/azure-log-audit/azure-log-audit-fig4.png)

Sledovací proces sítě je místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni scénář sítě, do a z Azure. Diagnostika sítě a k dispozici sledovací proces sítě vizualizace nástroje vám pomůžou pochopit, diagnostikovat a získáte přehled o k síti v Azure.

**Protokolování toku NSG** -toku protokoly pro skupinu zabezpečení sítě umožňují zaznamenat protokoly související s přenosy, které jsou povolené nebo zakázané pravidla zabezpečení ve skupině. Tyto protokoly toku jsou zapsané ve formátu JSON a zobrazit příchozí a odchozí toky na základě pravidla na síťový adaptér tok se vztahuje na 5 řazené kolekce členů informace o toku (protokol IP zdroj nebo cíl, zdrojový nebo cílový Port), a pokud se povoluje nebo odepírá provoz.

### <a name="network-security-group-flow-logging"></a>Protokolování toku skupinu zabezpečení sítě

[Skupina zabezpečení sítě toku protokoly](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) jsou funkce sledovací proces sítě, která vám umožní zobrazit informace o příchozí a odchozí provoz IP prostřednictvím skupinu zabezpečení sítě. Tyto protokoly toku jsou zapsané ve formátu JSON a zobrazit příchozí a odchozí toky na základě pravidla na síťový adaptér tok se vztahuje na 5 řazené kolekce členů informace o toku (protokol IP zdroj nebo cíl, zdrojový nebo cílový Port), a pokud se povoluje nebo odepírá provoz.

I když toku protokoluje cílové skupiny zabezpečení sítě, nejsou zobrazí stejné jako další protokoly. Tok protokoly se ukládají pouze v rámci účtu úložiště.

Do protokolů toku použít stejné zásady uchovávání informací jako zobrazené na další protokoly. Protokoly mít zásady uchovávání informací, můžete nastavit od 1 den do 365 dní. Pokud zásady uchovávání nejsou nastavené, protokoly se ukládají navždy.

**Diagnostické protokoly**

Pravidelné a spontánních události jsou vytvořené síťové prostředky a protokolovány v účtech úložiště, odeslané do centra událostí, nebo analýzy protokolů. Tyto protokoly poskytují přehled o stavu prostředku. Tyto protokoly můžete zobrazit v nástrojů, jako je Power BI a analýzy protokolů. Další postup zobrazení diagnostických protokolů najdete [analýzy protokolů.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)

![Diagnostické protokoly](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnostické protokoly jsou k dispozici pro [nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy a [Application Gateway.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

Sledovací proces sítě poskytuje že diagnostické protokoly zobrazení. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto hlediska můžete povolit nebo zakázat síťových prostředků, snadno a rychle.


Kromě předchozích možnosti protokolování, sledovací proces sítě aktuálně má následující možnosti:
- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -poskytuje zobrazení úrovně sítě zobrazuje různé propojení a přidružení mezi síťovým prostředkům ve skupině prostředků.

- [Proměnné zachytáváním paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -zaznamená data paketů do/z virtuálního počítače. Pokročilé možnosti filtrování a podrobně nastavit ovládací prvky, jako je například moct nastavit čas a velikost omezení poskytují univerzálnost. Paketu data mohou být uložena v úložišti objektů blob nebo na místní disk ve formátu CAP.

-   [Ověřuje IP toku](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) – ověří, zda je paket povolený nebo zakázaný na základě toku informace 5 řazené kolekce členů paketu parametrů (cílovou IP adresu, zdrojové IP adresy, cílový Port, zdrojový Port a protokol). Pokud paketu je zakázané skupiny zabezpečení, je vrácena pravidlo a skupiny, který odepřen paketu.

-   [Další směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) -určí další segment pro pakety směrovány v prostředcích infrastruktury sítě Azure umožňuje diagnostikovat žádné špatně nakonfigurovaný trasy definované uživatelem.

-   [Zobrazení skupiny zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -získá zabezpečení efektivní a použitých pravidel, která se použijí na virtuálním počítači.

-   [Brána virtuální sítě a řešení potíží s připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) -poskytuje možnost Poradce při potížích brány virtuální sítě a připojení.

-   [Sítě limity předplatného](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) -umožňuje zobrazit využití prostředků sítě proti omezení.

### <a name="application-insight"></a>Přehled aplikace

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelný služba Správa výkonu aplikace (APM) pro vývojáře, kteří ve více platformách. Slouží k monitorování živé webové aplikace. Je automaticky zjišťovat anomálie výkonu. Obsahuje výkonné analytické nástroje pro diagnostiku problémů a pomáhá porozumět, jak vlastně uživatelé vaši aplikaci používají.

 Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

 Funguje u aplikací na široké škále platforem, jako jsou .NET, Node.js a J2EE, a to hostovaných lokálně i v cloudu. Se integruje s váš proces devOps a má spojovací body na různé nástroje pro vývoj.

![Přehled aplikace](./media/azure-log-audit/azure-log-audit-fig6.png)

Služba Application Insights je zaměřená na vývojový tým. Pomáhá pochopit, jaký je výkon vaší aplikace a jak se využívá. Monitoruje tyto parametry:

-   **Frekvence požadavků, doby odezvy a míra selhání** – Zjistěte, které stránky jsou nejoblíbenější a v kterou denní dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud se při zvýšení počtu požadavků zvýší i doba odezvy a míra selhání, máte pravděpodobně potíže s prostředky.

-   **Míra závislosti, doby odezvy a míra selhání** – Zjistěte, jestli vás nezpomalují externí služby.

-   **Výjimky** – analyzovat souhrnných statistik, nebo vyberte určité instance a přejít k podrobnostem trasování zásobníku a související požadavky. Hlásí se výjimky serveru i prohlížeče.

-   **Zobrazení a načítání stránek** – Tyto informace hlásí prohlížeče uživatelů.

-   **Volání AJAX** z webových stránek – frekvence, doby odezvy a míry selhání.

-   **Počty uživatelů a relací**.

-   **Čítače výkonu** ze serverových počítačů s Windows nebo Linuxem, jako je třeba CPU, paměť a využití sítě.

-   **Diagnostika hostitele** z Dockeru nebo Azure.

-   **Protokoly trasování diagnostiky** z vaší aplikace – umožňují zjistit korelaci mezi požadavky a událostmi trasování.

-   **Vlastní události a metriky**, které napíšete sami v kódu klienta nebo serveru, ke sledování obchodních událostí, jako například jsou třeba prodané položky nebo vyhrané hry.

**Seznam scénáře integrace a popis:**

| Scénáře integrace | Popis |
| --------------------- | :---------- |
|[Mapa aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Komponenty vaší aplikace, včetně klíčových metrik a výstrah.||
|[Diagnostické vyhledávání pro instanci dat](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Události vyhledávání a filtrování, jako jsou třeba požadavky, výjimky, volání závislosti, trasování protokolů a zobrazení stránek.||
|[Průzkumníku metrik pro agregovaná data](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Prozkoumání, filtrování a segmentace agregovaných dat, jako jsou třeba frekvence požadavků, selhání a výjimek, doby odezvy a časy načtení stránek.||
|[Řídicí panely](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Propojení dat z různých zdrojů a jejich sdílení s ostatními. Ideální pro aplikace s více komponentami a pro nepřetržité zobrazení v týmové místnosti.||
|[Živý datový proud metriky](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Při nasazování nového sestavení sledujte tyto ukazatele výkonu téměř v reálném čase, abyste se ujistili, že všechno funguje podle očekávání.||
|[Analýzy](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Tento výkonný dotazovací jazyk umožňuje odpovědět na složité dotazy týkající se využití a výkonu vaší aplikace.||
|[Automatickou a ruční výstrahy](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatické výstrahy se přizpůsobí běžným vzorům telemetrie vaší aplikace a aktivují se, když se stane něco neobvyklého. Výstrahy se také dají nastavit pro konkrétní úrovně vlastních nebo standardních metrik.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Viz údaje o výkonu v kódu. Ke kódu je možné přejít z trasování zásobníku.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrujte metriky využití s ostatními funkcemi business intelligence.||
|[REST API](https://dev.applicationinsights.io/)|Napište kód pro spouštění dotazů nad daty metrik a nezpracovanými daty.||
|[Průběžný export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Export nezpracovaná data do úložiště hromadně, jakmile bude přijata.||

### <a name="azure-security-center-alerts"></a>Výstrahy Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a endpoint protection řešení, zjistily skutečné hrozby a snížit false. položky. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.

Detekce hrozeb ve službě Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení ze všech prostředků Azure, ze sítě a připojených partnerských řešení. Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů. U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit.

![Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Změnám ve velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologie, které se použijí k vyhodnocení události napříč celou cloudových prostředků infrastruktury – zjišťování hrozeb, které by bylo možné zjistit pomocí ruční přístupy a predikci vývoj útoků. Do této analýzy zabezpečení patří:

-   **Integrované analýzou hrozeb:** hledá známé nesprávnými účastníky použitím globální analýzou hrozeb z produktů společnosti Microsoft a služby, Microsoft digitální činů jednotky (přicházejí týmu DCU), Microsoft Security Response Center (MSRC) a externích informačních kanálů.

-   **Pro vypracování analýzy:** platí známé vzorce ke zjištění škodlivé chování.

-   **Detekce anomálií:** vytvoří pomocí statistické profilace historických směrného plánu. Upozorní na odchylky od zavedených standardních hodnot, které mají potenciál útoku.


Mnoho operace zabezpečení a reakce na incidenty týmy závisí na informace o zabezpečení a událostí správy (SIEM) řešení jako výchozí bod pro triaging a prošetřování výstrah zabezpečení. S integrací protokolů Azure můžou zákazníci synchronizovat výstrahy Security Center a události zabezpečení virtuálního počítače, shromážděné pomocí diagnostiky Azure a protokoly auditu Azure pomocí jejich analýzy protokolů nebo řešení SIEM skoro v reálném čase.


## <a name="log-analytics"></a>Log Analytics

Analýzy protokolů je služba v Azure, který vám pomůže shromažďovat a analyzovat data generována prostředky ve vašem cloudu a místní prostředí. Nabízí přehledy v reálném čase pomocí integrovaného hledání a vlastní řídicí panely, které snadno analyzovat miliony záznamů ve všech úloh a servery bez ohledu na jejich fyzické umístění.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

V center Log Analytics je pracovní prostor analýzy protokolů, který je hostován v cloudu Azure. Data jsou shromažďována do pracovního prostoru z připojených zdrojů tak, že konfigurace zdroje dat a řešení pro přidání do vašeho předplatného. Zdroje dat a řešení každý vytvoří typy různých záznamů, které mají své vlastní sadu vlastností, ale může být analyzován společně v dotazech do pracovního prostoru. To vám umožňuje používat stejné nástroje a metody pro práci s různými druhy dat shromážděných různými prostředky.

Propojené zdroje jsou počítače a další prostředky, které generují data shromážděná službou Log Analytics. To může zahrnovat agentů nainstalovaných na [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) a [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) počítače, které se připojují přímo nebo agentů v [připojené skupiny pro správu System Center Operations Manager.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) Analýzy protokolů může taky shromažďovat data z [úložiště Azure.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)

[Zdroje dat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) jsou různé druhy dat shromážděných z každého připojeného zdroje. To zahrnuje události a [údaje o výkonu](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) z [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) a agenty Linux kromě zdrojů, jako [protokoly služby IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs), a [protokoly vlastní text.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs) Nakonfigurujete každý zdroj dat, který chcete shromáždit, a konfigurace se automaticky distribuuje každému připojenému zdroji.

Existují čtyři různé způsoby [shromažďování protokolů a metriky pro služby Azure:](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)
1.  Azure diagnostics přímo k Log Analytics (Diagnostika v následující tabulce)

2.  Azure diagnostics do úložiště Azure k analýze protokolů (úložiště v následující tabulce)

3.  Konektory pro služby Azure (konektory v následující tabulce)

4.  Skripty ke sběru a následně je publikovat data do analýzy protokolů (prázdné buňky v následující tabulce a pro služby, které nejsou uvedené)

| Služba | Typ prostředku | Logs | Metriky | Řešení |
| :------ | :------------ | :--- | :------ | :------- |
|Application Gateway|  Microsoft.Network/<br>applicationGateways|  Diagnostika|Diagnostika|    [Aplikace Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Analytics brány](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Konektor|  Konektor|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Connectoru (Preview)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Účty Automation|   Microsoft.Automation/<br>AutomationAccounts|    Diagnostika||       [Další informace](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Účty Batch|    Microsoft.Batch/<br>batchAccounts|  Diagnostika|    Diagnostika||
|Classic cloudové služby||       Úložiště||       [Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Kognitivní služby|    Microsoft.CognitiveServices/<br>accounts|       Diagnostika|||
|Data Lake analytics|   Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostika|||
|Úložiště data Lake store|   Microsoft.DataLakeStore/<br>accounts|   Diagnostika|||
|Obor názvů centra událostí|   Microsoft.EventHub/<br>namespaces|  Diagnostika|    Diagnostika||
|IoT Huby|  Microsoft.Devices/<br>IotHubs||     Diagnostika||
|Key Vault| Microsoft.KeyVault/<br>trezory|  Diagnostika  || [KeyVault Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Služby vyrovnávání zatížení|    Microsoft.Network/<br>loadBalancers|    Diagnostika|||
|Logic Apps|    Microsoft.Logic/<br>Pracovní postupy|  Diagnostika|    Diagnostika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Skupiny zabezpečení sítě)|   Microsoft.Network/<br>networksecuritygroups|Diagnostika||   [Skupina zabezpečení sítě Azure Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Trezory zotavení|   Microsoft.RecoveryServices/<br>trezory|||[Azure Recovery Services Analytics (Preview)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Služby hledání|   Microsoft.Search/<br>searchServices|    Diagnostika|    Diagnostika||
|Obor názvů služby Service Bus| Microsoft.ServiceBus/<br>namespaces|    Diagnostika|Diagnostika|    [Service Bus Analytics (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Úložiště||    [Služba Fabric Analytics (Preview)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>databáze||       Diagnostika||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Úložiště|||         Skript| [Azure Storage Analytics (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Virtuální počítače|  Microsoft.Compute/<br>virtuálních počítačů|  Linka|  Linka||
||||Diagnostika||
|Sady škálování virtuálních počítačů|   Microsoft.Compute/<br>virtuálních počítačů    ||Diagnostika||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtuálních počítačů||||
|Webové serverové farmy|Microsoft.Web/<br>serverfarms||   Diagnostika
|Weby| Microsoft.Web/<br>weby ||      Diagnostika|    [Další informace](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>weby nebo<br>sloty|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integrace protokolu s místních systémů SIEM
[Integrace Azure protokolu](https://www.microsoft.com/download/details.aspx?id=53324) umožňuje integraci nezpracovaná protokoly z vašich prostředků Azure v místní **systémy informace o zabezpečení a událostí správy (SIEM)**.

![Integrace protokolu](./media/azure-log-audit/azure-log-audit-fig9.png)

Integrace Azure protokolu shromažďuje Azure Diagnostics ze systému Windows (WAD) virtuálních počítačů, protokoly aktivity Azure, Azure Security Center výstrahy, a protokoly poskytovatel prostředků Azure. Tato integrace poskytuje jednotný řídicí panel pro všechny vaše prostředky, místně nebo v cloudu, takže můžete agregovat, korelovat, analyzovat a výstrahy pro události zabezpečení.



Integrace protokolů Azure aktuálně podporuje integraci Azure aktivity protokolů, protokolu událostí systému Windows z virtuálního počítače Windows ve vašem předplatném Azure, Azure Security Center výstrahy, Azure diagnostické protokoly a Azure Active Directory protokoly auditu.

| Typ protokolu | Podpora formátu JSON (Splunk, ArcSight, Qradar) analýzy protokolů |
| :------- | :-------------------------------------------------------- |
|Protokoly auditu AAD|    ano|
|Protokoly aktivit| Ano|
|ASC výstrahy |Ano|
|Diagnostické protokoly (protokoly prostředků)|  Ano|
|Protokoly virtuálních počítačů|   Ano prostřednictvím předané události a ne prostřednictvím formátu JSON|


Následující tabulka popisuje kategorie protokolu a podrobnosti integrace systému SIEM.

[Začínáme s Azure protokolu integrace](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) – kurz vás provede procesem instalace integrace protokolů Azure a integrace protokoly z úložiště Azure WAD, protokoly aktivity Azure, Azure Security Center výstrahy a Azure Active Directory protokoly auditu.

Scénáře integrace

-   [Partner kroky konfigurace](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – tento příspěvek blogu ukazuje, jak nakonfigurovat integraci Azure protokolu pro práci s partnerských řešení Splunk, HP ArcSight a IBM QRadar.

-   [Protokolů Azure integrace nejčastější dotazy (FAQ)](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) – nejčastější dotazy týkající se tento odpovědi dotazy týkající se integrace protokolů Azure.

-   [Integrace Security Center výstrahy s Azure protokolu integrace](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) – tento dokument ukazuje, jak synchronizovat výstrahy Security Center spolu s shromážděných pomocí diagnostiky Azure a protokoly auditu Azure s vaší analýzy protokolů událostí zabezpečení virtuálního počítače nebo Řešení SIEM.

## <a name="next-steps"></a>Další kroky

- [Auditování a protokolování](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Ochrana dat údržbou viditelnost a rychle reagovat na výstrahy včas zabezpečení

- [Protokolování zabezpečení a kolekce protokolu auditování v rámci Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Nastavení, které potřebujete vynutit zajistit vaší instancí Azure jsou shromažďování správné zabezpečení a protokoly auditu.

- [Konfigurovat nastavení auditování pro kolekci webů](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

Jako správce kolekce webů jeden můžete načíst historii akcí provedených konkrétním uživatelem a můžete také načíst historii akcí během určité období. 

- [Vyhledávat protokol auditu zabezpečení Office 365 & centru dodržování předpisů](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Zabezpečení Office 365 & centru dodržování předpisů jeden můžete použít k vyhledání jednotná auditu protokolu, který chcete zobrazit aktivity uživatele a správce v organizaci služeb Office 365.


