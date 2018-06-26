---
title: Protokolování a auditování Azure | Microsoft Docs
description: Informace o tom, jak můžete data protokolování a získáte přehled o hloubkové o vaší aplikaci.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: e4144ca0d87abda3d9f8de47e56af59d0e4af312
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938362"
---
# <a name="azure-logging-and-auditing"></a>Protokolování a auditování Azure

Azure nabízí širokou škálu konfigurovat zabezpečení, auditování a protokolování možnosti můžete zjistí nesrovnalosti v vaše zásady a mechanismy zabezpečení. Tento článek popisuje generování, shromažďování a analýzy protokolů zabezpečení ze služby hostované v Azure.

> [!Note]
> Některá doporučení v tomto článku může vést k vyšší data, síťové nebo využití výpočetních prostředků a zvýšit náklady na licence nebo předplatné.

## <a name="types-of-logs-in-azure"></a>Typy protokolů v Azure
Cloudové aplikace jsou komplexní, s mnoha přesunutí částmi. Protokoly poskytují data zajistit, aby byl aplikace fungovaly. Protokoly pomůžou zabránit potenciální ty, které jsou nebo odstranit potíže s problémy. A můžou pomoct zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

Azure protokoly jsou rozdělené do následujících typů:
* **Řízení nebo protokoly** poskytují informace o operacích Azure Resource Manager vytvoření, aktualizace a odstranění. Další informace najdete v tématu [protokoly Azure aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Data roviny protokoly** poskytují informace o události vyvolané jako část využití prostředků Azure. Příkladů tohoto typu protokolu událostí systému Windows, zabezpečení, a protokoly aplikace ve virtuálním počítači (VM) a [protokolů diagnostiky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) které jsou nakonfigurovány pomocí sledování Azure.

* **Zpracování události** poskytují informace o analyzovaných události a výstrahy, které byly zpracovány vaším jménem. Příkladem tohoto typu jsou [výstrahy Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) kde [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) je zpracován a analyzovat vaše předplatné a poskytuje výstrahy stručným zabezpečení.

Následující tabulka uvádí nejdůležitější typy protokolů dostupných v Azure:

| Kategorie protokolu | Typ protokolu | Využití | Integrace |
| ------------ | -------- | ------ | ----------- |
|[Protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Události rovině řízení na prostředky Azure Resource Manager|   Poskytuje náhled do činnosti, které byly provedeny v prostředky ve vašem předplatném.|    REST API, [Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Protokoly diagnostiky Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Časté data o operaci prostředky Azure Resource Manager v rámci předplatného|    Poskytuje náhled do operací, které provádí samotné prostředku.| Azure monitorování [datového proudu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Sestavy Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Protokoly a sestavy | Sestavy aktivit přihlášení uživatelů a informace o systému aktivity týkající se uživatelů a Správa skupin.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuální počítače a cloudové služby](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-quick-collect-azurevm)|Služba Protokol událostí systému Windows a Linux Syslog|    Zaznamená data systému a data protokolování na virtuálních počítačích a přenosy dat do účtu úložiště podle vašeho výběru.|   Windows (pomocí Windows Azure Diagnostics [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)] úložiště) a Linux v nástroji Sledování Azure|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Protokolování úložiště, poskytuje data metriky pro účet úložiště|Poskytuje přehled o trasování požadavků, analyzuje trendy využití a diagnostikuje problémy s vaším účtem úložiště.|   Rozhraní API REST nebo [klientské knihovny](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Protokoly toku skupinu zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formát JSON, ukazuje příchozí a odchozí toky na základě pravidla|Zobrazí informace o příchozí a odchozí provoz IP prostřednictvím skupinu zabezpečení sítě.|[Sledovací proces sítě Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Přehled aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Protokoly, výjimky a vlastní diagnostiky|   Poskytuje funkce application performance monitoring (APM) služby vývojářům webů ve více platformách.| Rozhraní REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Zpracování dat nebo výstrahy zabezpečení|    Azure Security Center oznámení, oznámení Azure Log Analytics|   Poskytuje informace o zabezpečení a výstrahy.|  Rozhraní REST API, JSON|

### <a name="activity-logs"></a>Protokoly aktivit
[Aktivita Azure protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o činnosti, které byly provedeny v prostředky ve vašem předplatném. Protokoly aktivity se dřív označovala jako "protokoly auditu" nebo "provozní protokoly,", protože ohlásí [rovině řízení události](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) pro vaše předplatné. 

Aktivita protokoly vám pomáhají určit, ", kdo a kdy" pro operace zápisu (to znamená, PUT, POST nebo odstranit). Aktivita zaznamená také nápovědy porozumění stavům operaci a další relevantní vlastnosti. Protokoly aktivity nezahrnují operace čtení (GET).

V tomto článku PUT, POST a odstranění najdete všechny operace zápisu, které obsahuje protokol aktivity s prostředky. Můžete například protokoly aktivity k Najít chybu, když jste potíží nebo k monitorování, jak upravit uživatele ve vaší organizaci prostředek.

![Diagram protokolu aktivit](./media/azure-log-audit/azure-log-audit-fig1.png)

Události z protokolu aktivit můžete načíst pomocí portálu Azure [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), rutiny prostředí PowerShell a [REST API služby Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Protokoly aktivity mají období uchovávání dat dne 19.

Scénáře integrace pro aktivitu protokolu události:

* [Vytvořit výstrahu e-mailu nebo webhooku se aktivuje aktivity protokolu události](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Stream do centra událostí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) pro přijímání službu třetí strany nebo řešení vlastní analýzy, jako je například PowerBI.

* Analyzovat v Power BI pomocí [balíček obsahu Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Uložit na účet úložiště pro archivaci nebo ruční kontroly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Můžete zadat dobu uchování (ve dnech) s použitím protokolu profilů.

* Dotaz a zobrazit na portálu Azure.

* Dotaz je pomocí rutiny prostředí PowerShell, rozhraní příkazového řádku Azure nebo REST API.

* Export protokolu aktivit s profily protokolu, aby [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Můžete použít účet úložiště nebo [názvový prostor události rozbočovače](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) , není ve stejném předplatném jako ten, který je generování protokolu. Kdo nakonfiguruje nastavení musí mít odpovídající [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) přístup k oba odběry.

### <a name="azure-diagnostics-logs"></a>Diagnostické protokoly Azure
Azure diagnostické protokoly jsou vysílaných na prostředek, který poskytuje bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku. Například [protokoly událostí systému Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) jsou kategorii protokolů diagnostiky pro virtuální počítače, a [objektů blob, tabulky a fronty protokoly](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) kategorií protokolů diagnostiky pro účty úložiště. Diagnostické protokoly se liší od protokoly aktivity, které poskytují přehled o činnosti, které byly provedeny v prostředky ve vašem předplatném.

![Azure diagnostics protokoly diagramy](./media/azure-log-audit/azure-log-audit-fig2.png)

Protokoly Azure diagnostics nabízejí různé možnosti konfigurace, jako je například portál Azure, prostředí PowerShell, rozhraní příkazového řádku Azure a rozhraní REST API.

**Scénáře integrace**

* Uložte je do [účet úložiště](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) pro auditování nebo ruční kontroly. Doba uchování (ve dnech) můžete zadat pomocí nastavení diagnostiky.

* [Stream je do centra událostí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) pro přijímání službu třetí strany nebo řešení vlastní analýzy, jako například [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analyzovat, s [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Podporované služby, schématu pro podporované protokolu kategorií podle typu prostředku a protokolů diagnostiky**


| Služba | Schéma a dokumentace | Typ prostředku | Kategorie |
| ------- | ------------- | ------------- | -------- |
|Nástroj pro vyrovnávání zatížení Azure| [Analýzy protokolů pro vyrovnávání zatížení (Preview)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Network Security Groups (Skupiny zabezpečení sítě)|[Analýzy protokolů pro skupinu zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Protokolování diagnostiky pro službu Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Protokoly Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Povolení a používání Analýza provozu vyhledávání](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Přístup k protokolům diagnostiky pro Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Auditování<br>Požadavky|
|Azure Data Lake Analytics|[Přístup k protokolům diagnostiky pro Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Auditování<br>Požadavky|
|Azure Logic Apps|[Vlastní schéma sledování B2B Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|Modul runtime pracovního postupu<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Protokoly diagnostiky Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Analýzy protokolů pro služby Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Protokoly událostí centra diagnostiky](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Protokoly úlohy diagnostiky](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Spouštěcí<br>Vytváření obsahu|
|Azure Service Bus|[Diagnostické protokoly služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Generování sestav v Azure Active Directory
Azure Active Directory (Azure AD) obsahuje zabezpečení, aktivity a zprávy o auditu pro adresáře uživatele. [Sestava auditu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomáhá identifikovat privilegovaných akcí, které došlo k chybě v instanci Azure AD uživatele. Privilegované akce zahrnují změny zvýšení oprávnění (například role vytvoření nebo resetování hesla), změna konfigurace zásad (například zásady pro hesla) nebo změny v konfiguraci adresáře (například změny nastavení federace domén).

Sestavy poskytují záznam auditu pro název události, uživatele, který provedl akci, cílový prostředek vliv na změny a datum a čas (ve formátu UTC). Uživatele můžete načíst seznam událostí auditu pro Azure AD prostřednictvím [portál Azure](https://portal.azure.com/), jak je popsáno v [zobrazit vaše protokoly auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Zahrnuté sestavy jsou uvedené v následující tabulce:

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

Data v těchto sestavách mohou být užitečné pro vaše aplikace, například informace o zabezpečení a událostí správy (SIEM) systémy, auditování a nástroje business intelligence. Rozhraní API pro generování sestav v Azure AD poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Můžete volat tyto [rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) z různých programovacích jazyků a nástroje.

Události sestavy auditování Azure AD jsou uchovány na 180 dní.

> [!Note]
> Další informace o uchování sestav najdete v tématu [zásady uchování sestav Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Pokud vás zajímá uchování delší událostí auditu, používají rozhraní API pro vytváření sestav pravidelně vyžádání [události auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) do samostatné datové úložiště.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Virtuální počítač protokoly, které používají Azure Diagnostics
[Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) je funkce v rámci Azure, která umožňuje shromažďování dat diagnostiky na nasazené aplikace. Můžete použít rozšíření diagnostiky z jakéhokoli z několika zdrojů. Aktuálně podporované jsou [Azure cloud service webové a pracovní role](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Virtuální počítač protokoly, které používají Azure Diagnostics](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineshttpsazuremicrosoftcomdocumentationlearning-pathsvirtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Virtuální počítače Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) se systémem Microsoft Windows a [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Azure Diagnostics na virtuálním počítači můžete povolit pomocí některého z následujících akcí:

* [Trasování virtuální počítače Azure pomocí sady Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Nastavení Azure Diagnostics vzdáleně na virtuální počítač Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Nastavení diagnostiky na virtuálních počítačích Azure pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Vytvoření virtuálního počítače s Windows pomocí monitorování a Diagnostika pomocí šablony Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) protokoly a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště. Je k dispozici pro úložiště Analytics protokolování [služby úložiště Azure Blob, fronty Azure a Azure Table](https://docs.microsoft.com/azure/storage/storage-introduction). Analytika úložiště protokoly podrobné informace o úspěšných a nezdařených požadavků a služba úložiště.

Tyto informace můžete použít k monitorování jednotlivých požadavků a diagnostikovat problémy s služby úložiště. Na základě typu best effort protokolované požadavky. Položky protokolu se vytvoří pouze v případě, že jsou požadavky na koncový bod služby. Například pokud je účet úložiště svůj koncový bod objektu blob, ale není ve své koncové body tabulku nebo frontu, se vytvoří pouze protokoly, které se týkají služby Blob storage.

Pokud chcete používat Storage Analytics, ji povolte jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ho povolit [portál Azure](https://portal.azure.com/). Další informace najdete v tématu [monitorování účtu úložiště na portálu Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Můžete také povolit Storage Analytics programově pomocí rozhraní REST API nebo knihovny klienta. Pomocí operace nastavit vlastnosti služby tak, aby Storage Analytics samostatně pro každou službu.

Agregovaná data se ukládají v dobře známé objektu blob (pro protokolování) a dobře známé tabulky (pro metriky), které mají přístup k pomocí služby úložiště objektů Blob a služby úložiště Table rozhraní API.

Analytika úložiště limit terabajt 20 (TB) je na množství uložených dat, která je nezávislá celkový limit pro váš účet úložiště. Všechny protokoly jsou uloženy v [objekty BLOB bloků](https://docs.microsoft.com/azure/storage/storage-analytics) v kontejneru nazvaném $logs, který se automaticky vytvoří při povolení analytika úložiště pro účet úložiště.

> [!Note]
> * Další informace o fakturaci a zásad uchovávání dat najdete v tématu [Storage Analytics a fakturace](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Další informace o omezeních účtu úložiště najdete v tématu [Azure Storage škálovatelnosti a cílech výkonnosti](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Analytika úložiště protokoluje následující typy požadavků na ověření a anonymní:

| Ověření  | Anonymní|
| :------------- | :-------------|
| Úspěšné požadavky | Úspěšné požadavky |
|Neúspěšné požadavky, včetně vypršení časového limitu, omezení šířky pásma, sítě, autorizace a dalších chyb | Požadavky pomocí sdíleného přístupového podpisu, včetně žádostí úspěšné a neúspěšné |
| Požadavky pomocí sdíleného přístupového podpisu, včetně žádostí úspěšné a neúspěšné |Chyby časového limitu pro klienta a serveru |
|   Požadavky na analytická data |    Neúspěšné požadavky GET s kódem chyby 304 (nedojde ke změně) |
| Požadavky na úložiště Analytics samostatně, jako je například protokol vytvoření nebo odstranění, se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [Storage Analytics protokolovat stavové zprávy a operace](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [formát protokolu Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Všechny ostatní selhání anonymních požadavků se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [Storage Analytics protokolovat stavové zprávy a operace](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [formát protokolu Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Azure síťové protokoly
Sítě protokolování a monitorování v Azure je komplexní a zahrnuje dvě rozsáhlé kategorie:

* [Sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher): monitorování na základě scénáře sítě se poskytuje s funkcí v sledovací proces sítě. Tato služba obsahuje zachytáváním paketů, další směrování, IP tok ověření, zobrazení skupiny zabezpečení, tok protokolů NSG. Scénář úrovně monitorování poskytuje pohledu koncové síťových prostředků, na rozdíl od monitorování jednotlivých síťových prostředků.

* [Sledování prostředků](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring): sledování na úrovni prostředků obsahuje čtyři funkce, diagnostické protokoly, metriky, řešení potíží a stav prostředků. Všechny tyto funkce jsou postavené na úrovni prostředků sítě.

![Azure síťové protokoly](./media/azure-log-audit/azure-log-audit-fig4.png)

Sledovací proces sítě je místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni scénář sítě, do a z Azure. Diagnostika sítě a k dispozici sledovací proces sítě vizualizace nástroje vám pomůžou pochopit, diagnostikovat a získáte přehled o k síti v Azure.

### <a name="network-security-group-flow-logging"></a>Protokolování toku skupinu zabezpečení sítě

[Tok protokolů NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) jsou funkce sledovací proces sítě, který můžete použít k zobrazení informací o příchozí a odchozí provoz IP prostřednictvím skupinu NSG. Tyto protokoly toku jsou zapsány ve formátu JSON a zobrazit:
* Odchozí a příchozí tok na základě pravidla.
* Síťové adaptéry, které toku platí pro.
* 5 řazené kolekce členů informace o toku: zdrojová nebo cílová IP adresa, zdrojový nebo cílový port a protokol.
* Jestli se provoz povolené nebo zakázané.

I když toku protokoluje cílové skupiny Nsg, nezobrazí se stejným způsobem jako jiné protokoly. Tok protokoly se ukládají pouze v rámci účtu úložiště.

Do protokolů toku použít stejné zásady uchovávání informací, které se zobrazují na další protokoly. Protokoly mít zásady uchovávání, které můžete nastavit od 1 den do 365 dní. Pokud zásady uchovávání nejsou nastavené, protokoly se ukládají navždy.

**Protokoly diagnostiky**

Pravidelné a spontánních události jsou vytvořené síťové prostředky a zaznamenávají v účtech úložiště a odeslaných do centra událostí nebo analýzy protokolů. Protokoly poskytují přehled o stavu prostředku. Může se zobrazit v nástrojů, jako je Power BI a analýzy protokolů. Informace o tom, které chcete zobrazit protokoly diagnostiky, najdete v části [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Protokoly diagnostiky](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnostické protokoly jsou k dispozici pro [nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy a [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Sledovací proces sítě poskytuje že zobrazení protokolů diagnostiky. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto hlediska můžete povolit nebo zakázat síťových prostředků, snadno a rychle.


Kromě výše uvedených protokolování možností sledovací proces sítě aktuálně má následující možnosti:
- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): poskytuje zobrazení úrovni sítě, které obsahuje různé propojení a přidružení mezi síťovým prostředkům ve skupině prostředků.

- [Zachytáváním paketů proměnné](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): zaznamená data paketů do/z virtuálního počítače. Pokročilé možnosti filtrování a upřesnění ovládací prvky, jako je například nastavení času a velikost omezení, zadejte univerzálnost. Paket dat může být uložená v úložišti objektů blob nebo na místním disku v *CAP* formát souboru.

* [Tok ověření IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): zkontroluje, jestli je paket povolený nebo zakázaný na základě toku informace 5 řazené kolekce členů paketu parametrů (to znamená, cílová adresa IP, zdrojové IP adresy, cílový port, zdrojový port a protokol). Pokud paketu je zakázané skupiny zabezpečení, je vrácena pravidlo a skupiny, který odepřen paketu.

* [Další směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Určuje další směrování pro pakety směrovány v prostředcích infrastruktury síť Azure, takže lze diagnostikovat žádné špatně nakonfigurovaný trasy definované uživatelem.

* [Zobrazení skupiny zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): získá zabezpečení efektivní a použitých pravidel, která se použijí na virtuálním počítači.

* [Brána virtuální sítě a řešení potíží s připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): vám pomůže vyřešit brány virtuální sítě a připojení.

* [Sítě limity předplatného](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): umožňuje zobrazit využití prostředků sítě proti omezení.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelnou službu APM vývojářům webů ve více platformách. Použijte jej k monitorování za chodu webových aplikací. Automaticky rozpozná anomálie výkonu. Obsahuje výkonné analytické nástroje pro diagnostiku problémů a pomáhá porozumět, jak vlastně uživatelé vaši aplikaci používají.

Application Insights je navržená tak, abyste neustále průběžně zlepšují výkon a použitelnost.

Funguje pro aplikace na širokou škálu platformy, včetně .NET, Node.js a J2EE, zda jsou hostovaný místně nebo v cloudu. Se integruje s váš proces DevOps a má spojovací body pomocí různých nástrojů pro vývoj.

![Application Insights diagram](./media/azure-log-audit/azure-log-audit-fig6.png)

Služba Application Insights je zaměřená na vývojový tým. Pomáhá pochopit, jaký je výkon vaší aplikace a jak se využívá. Monitoruje tyto parametry:

* **Žádosti o rychlostí, doby odezvy a selhání sazby**: Zjistěte, které stránky jsou nejoblíbenější, den, kdy a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud vaše doby odezvy a selhání sazby přejít vysoké, kdy další požadavky, bude pravděpodobně resourcing problém.

* **Sazby závislostí, doby odezvy a selhání sazby**: Zjistěte, jestli externích služeb zpomalují můžete.

* **Výjimky**: Analýza souhrnných statistik, nebo vyberte určité instance a přejít k podrobnostem trasování zásobníku a související požadavky. Hlásí se výjimky serveru i prohlížeče.

* **Zobrazení stránky a zatížení výkonu**: získání sestavy z prohlížečů uživatelů.

* **Volání AJAX**: získání sazby webovou stránku, doby odezvy a selhání sazby.

* **Počty uživatelů a relací**.

* **Čítače výkonu**: získání dat z vašeho systému Windows nebo Linux počítače serveru, například CPU, paměť a využívání sítě.

* **Hostitel diagnostiky**: získat data z Docker nebo Azure.

* **Diagnostické protokoly trasování**: získat data z aplikace, takže události trasování mohou korelovat s požadavky.

* **Vlastní události a metriky**: získat data, která sami zápisu v kódu klienta nebo serveru ke sledování obchodní události, jako například položek prodaných nebo hry won.

Následující tabulka uvádí a popisuje scénáře integrace:

| Scénáře integrace | Popis |
| --------------------- | :---------- |
|[Mapa aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Komponenty vaší aplikace, včetně klíčových metrik a výstrah.||
|[Diagnostické vyhledávání pro instanci dat](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Události vyhledávání a filtrování, jako jsou třeba požadavky, výjimky, volání závislosti, trasování protokolů a zobrazení stránek.||
|[Průzkumníku metrik pro agregovaná data](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Prozkoumání, filtrování a segmentace agregovaných dat, jako jsou třeba frekvence požadavků, selhání a výjimek, doby odezvy a časy načtení stránek.||
|[Řídicí panely](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Propojení dat z různých zdrojů a jejich sdílení s ostatními. Ideální pro aplikace s více komponentami a pro nepřetržité zobrazení v týmové místnosti.||
|[Živý datový proud metriky](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Při nasazování nového sestavení sledujte tyto ukazatele výkonu téměř v reálném čase, abyste se ujistili, že všechno funguje podle očekávání.||
|[Analýzy](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Tento výkonný dotazovací jazyk umožňuje odpovědět na složité dotazy týkající se využití a výkonu vaší aplikace.||
|[Automatickou a ruční výstrahy](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatické výstrahy se vaše aplikace normální vzory telemetrie přizpůsobit a se aktivují, pokud má něco mimo obvykle vzor. Výstrahy se také dají nastavit pro konkrétní úrovně vlastních nebo standardních metrik.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Zobrazení dat výkonu v kódu. Ke kódu je možné přejít z trasování zásobníku.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrujte metriky využití s ostatními funkcemi business intelligence.||
|[REST API](https://dev.applicationinsights.io/)|Napište kód pro spouštění dotazů nad daty metrik a nezpracovanými daty.||
|[Průběžný export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Export nezpracovaná data do úložiště hromadně, jakmile bude přijata.||

### <a name="azure-security-center-alerts"></a>Výstrahy Azure Security Center
Detekce hrozeb Azure Security Center funguje tak, že automaticky shromažďování informací o zabezpečení z vašich prostředků Azure, sítě a připojených partnerských řešení. Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů. U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit. Další informace najdete v tématu [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagram Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Platí změnám ve velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologie vyhodnotit události napříč celou cloudových prostředků infrastruktury. Tímto způsobem zjistí hrozeb, které by bylo možné zjistit pomocí ruční přístupy a predikci vývoj útoků. Do této analýzy zabezpečení patří:

* **Integrované analýzou hrozeb**: hledá známé nesprávnými účastníky použitím globální analýzou hrozeb z produktů společnosti Microsoft a služby, Microsoft digitální činů jednotky (přicházejí týmu DCU), Microsoft Security Response Center (MSRC) a externích informačních kanálů.

* **Pro vypracování analýzy chování**: platí známé vzorce ke zjištění škodlivé chování.

* **Detekce anomálií**: vytvoří pomocí statistické profilace historických směrného plánu. Upozorní na odchylky od zavedených standardních hodnot, které mají potenciál útoku.

Mnoho operace zabezpečení a reakce na incidenty týmy závisí na řešení SIEM jako výchozí bod pro triaging a prošetřování výstrah zabezpečení. Díky integraci protokolu Azure můžete synchronizovat výstrahy Security Center a událostí zabezpečení virtuálního počítače, shromážděných za Azure protokoly auditu a Diagnostika, s vaším řešením analýzy protokolů nebo SIEM skoro v reálném čase.

## <a name="log-analytics"></a>Log Analytics 

Analýzy protokolů je služba v Azure, který vám pomůže shromažďovat a analyzovat data, která je generován prostředky ve vašem cloudu a místní prostředí. Nabízí přehledy v reálném čase pomocí integrovaného hledání a vlastní řídicí panely snadno analyzovat miliony záznamů ve všech úloh a serverů, bez ohledu na jejich fyzické umístění.

![Diagram analýzy protokolů](./media/azure-log-audit/azure-log-audit-fig8.png)

V center Log Analytics je pracovní prostor analýzy protokolů, který je hostován v Azure. Analýzy protokolů shromažďuje data v pracovním prostoru z připojeného zdroje konfigurace zdroje dat a přidáním řešení do vašeho předplatného. Zdroje dat a řešení pro každou vytvořit různé zaznamenejte typů, které mají svůj vlastní sadu vlastností. Ale zdrojů a řešení můžete stále analyzovat společně v dotazech do pracovního prostoru. Díky této funkci můžete používat stejné nástroje a metody pro práci s různými data shromažďovaná společností různých zdrojů.

Počítače a další prostředky, které generují data shromážděná nástrojem analýzy protokolů jsou připojené zdroje. Zdroje mohou zahrnovat agentů, které jsou nainstalované na [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) a [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) počítače, které se připojují přímo nebo agentů v [připojené skupiny pro správu System Center Operations Manager](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Analýzy protokolů může taky shromažďovat data ze [účtu úložiště Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Zdroje dat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) jsou různé typy dat, která se shromažďují z každého připojeného zdroje. Zahrnout zdroje událostí a [údaje o výkonu](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) z [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) a agenty Linux, kromě zdrojů, jako [protokoly služby IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) a [vlastní textové protokoly](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Nakonfigurujete každý zdroj dat, který chcete shromáždit, a konfigurace se automaticky distribuuje každému připojenému zdroji.

Existují čtyři způsoby [shromažďovat protokoly a metriky pro služby Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):
* Azure Diagnostics přímo k Log Analytics (**diagnostiky** v následující tabulce)

* Azure Diagnostics do úložiště Azure k analýze protokolů (**úložiště** v následující tabulce)

* Konektory pro služby Azure (**konektor** v následující tabulce)

* Skripty ke sběru a následně je publikovat data do analýzy protokolů (prázdné buňky v následující tabulce a pro služby, které nejsou uvedené)

| Služba | Typ prostředku | Logs | Metriky | Řešení |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostika|Diagnostika|    [Aplikace Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Analytics brány](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Spojovací čára|  Spojovací čára|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Connectoru (Preview)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Účty služby Azure Automation| Microsoft.Automation/<br>AutomationAccounts|    Diagnostika||       [Další informace](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Účty Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnostika|    Diagnostika||
|Classic cloudové služby||       Úložiště||       [Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Diagnostika|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostika|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnostika|||
|Obor názvů Azure centra událostí| Microsoft.EventHub/<br>obory názvů|  Diagnostika|    Diagnostika||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostika||
|Azure Key Vault|   Microsoft.KeyVault/<br>trezory|  Diagnostika  || [Analýza služby Key Vault](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Nástroj pro vyrovnávání zatížení Azure|   Microsoft.Network/<br>loadBalancers|    Diagnostika|||
|Azure Logic Apps|  Microsoft.Logic/<br>Pracovní postupy|  Diagnostika|    Diagnostika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Skupiny zabezpečení sítě)|   Microsoft.Network/<br>networksecuritygroups|Diagnostika||   [Skupina zabezpečení sítě Azure analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Trezory zotavení|   Microsoft.RecoveryServices/<br>trezory|||[Azure Recovery Services Analytics (Preview)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Služby hledání|   Microsoft.Search/<br>searchServices|    Diagnostika|    Diagnostika||
|Obor názvů služby Service Bus| Microsoft.ServiceBus/<br>obory názvů|    Diagnostika|Diagnostika|    [Service Bus Analytics (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Úložiště||    [Služba Fabric Analytics (Preview)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servery nebo<br>databáze||       Diagnostika||
||Microsoft.Sql/<br>servery nebo<br>elasticPools||||
|Úložiště|||         Skript| [Azure Storage Analytics (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>virtuálních počítačů|  Linka|  Linka||
||||Diagnostika||
|Škálovací sady virtuálních počítačů|    Microsoft.Compute/<br>virtuálních počítačů    ||Diagnostika||
||Microsoft.Compute/<br>virtualMachineScaleSets /<br>virtuálních počítačů||||
|Webové serverové farmy|Microsoft.Web/<br>serverfarms||   Diagnostika
|Websites|  Microsoft.Web/<br>weby ||      Diagnostika|    [Další informace](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>weby nebo<br>sloty|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integrace protokolu s místních systémů SIEM
S [integrace se službou Azure protokolu](https://www.microsoft.com/download/details.aspx?id=53324), nezpracovaná protokoly z vašich prostředků Azure lze integrovat s místní systém SIEM.

![Diagram integrace protokolu](./media/azure-log-audit/azure-log-audit-fig9.png)

Integrace protokolu shromažďuje Azure diagnostics z virtuální počítače s Windows, protokoly aktivita Azure, Azure Security Center výstrahy a protokol zprostředkovatele prostředků Azure. Tato integrace poskytuje jednotný řídicí panel pro všechny prostředky, ať už místní nebo v cloudu, takže můžete agregovat, korelovat, analyzovat a výstrahy pro události zabezpečení.

Integrace protokolu aktuálně podporuje integraci Azure aktivity protokoly, protokoly auditu protokoly událostí systému Windows z virtuálních počítačů s Windows pomocí předplatného Azure, Azure Security Center výstrahy, protokolů diagnostiky Azure a Azure AD.

| Typ protokolu | Podpora formátu JSON (Splunk, ArcSight a IBM QRadar) analýzy protokolů |
| :------- | :-------------------------------------------------------- |
|Protokoly auditu Azure AD|   Ano|
|Protokoly aktivit| Ano|
|Výstrahy Security Center |Ano|
|Diagnostické protokoly (protokoly prostředků)|  Ano|
|Protokoly virtuálních počítačů|   Ano, prostřednictvím předané události a ne prostřednictvím formátu JSON|

[Začínáme s Azure protokolu integrace](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): v tomto kurzu vás provede procesem instalace protokolu integrace se službou Azure a integrace protokoly ze služby Azure storage, protokoly aktivita Azure, Azure Security Center výstrahy a Azure AD protokoly auditu.

Scénáře integrace pro SIEM:

* [Partner kroky konfigurace](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Tento příspěvek blogu ukazuje, jak nakonfigurovat integraci Azure protokolu pro práci s partnerských řešení Splunk, HP ArcSight a IBM QRadar.

* [Nejčastější dotazy k integraci Azure protokolu](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Tento článek obsahuje odpovědi na otázky týkající se integrace se službou Azure protokolu.

* [Výstrahy Security Center integrování integrace se službou Azure protokolu](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Tento článek popisuje, jak synchronizovat výstrahy Security Center, shromážděných Azure diagnostics protokoly událostí zabezpečení virtuálního počítače a protokoly auditu Azure Log Analytics nebo SIEM řešení.

## <a name="next-steps"></a>Další postup

- [Auditování a protokolování](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): ochrana dat údržbou viditelnost a rychle reagovat na výstrahy zabezpečení včas.

- [Shromažďování protokolování a auditování protokolu zabezpečení v rámci Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): vynucení těchto nastavení se ujistěte, že vaše Azure instance jsou shromažďování správné protokolů zabezpečení a audit.

- [Konfigurovat nastavení auditování pro kolekci webů](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Pokud jste správce kolekce webů, načíst historii akcí jednotlivých uživatelů a historii akcí během určité období. 

- [Hledání protokol auditu v Office 365 zabezpečení a dodržování předpisů Center](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): použití zabezpečení Office 365 & centru dodržování předpisů k vyhledávání protokol auditu jednotné a zobrazit činnosti uživatelů a správce v organizaci služeb Office 365.


