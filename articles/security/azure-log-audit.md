---
title: Protokolování a auditování Azure | Dokumentace Microsoftu
description: Další informace o použití protokolování dat získat podrobný přehled o vaší aplikaci.
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
ms.openlocfilehash: e2dabc66a906d35525f51bf9a1c09b62430ded55
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434003"
---
# <a name="azure-logging-and-auditing"></a>Protokolování a auditování Azure

Azure nabízí širokou škálu konfigurovatelných zabezpečení, auditování a protokolování možnosti, abyste mohli snadno identifikovat mezery v zabezpečení zásady a mechanismy. Tento článek popisuje generování, shromažďování a analýze protokolů zabezpečení ze služeb hostovaných v Azure.

> [!Note]
> Některá doporučení v tomto článku může způsobit zvýšenou dat, sítě nebo využití výpočetních prostředků a zvýšit náklady na licence nebo předplatné.

## <a name="types-of-logs-in-azure"></a>Typy protokolů v Azure
Cloudové aplikace jsou složité a řadu pohyblivých částí. Protokoly obsahují data, aby provoz, zajištění provozu vašich aplikací. Protokoly vám pomůžou vyřešit problémy nebo zabránit potenciální značky. A může pomoct zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

Protokoly Azure jsou rozdělené do následujících typů:
* **Protokoly/řízení** poskytují informace o operacích vytvořte Azure Resource Manageru, UPDATE a DELETE. Další informace najdete v tématu [protokolů aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Protokoly RP roviny dat** poskytují informace o události vyvolané jako součást využití prostředků Azure. Příklady tohoto typu protokolu událostí systému Windows, zabezpečení, a na virtuálním počítači (VM) protokoly aplikací a [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) , které jsou nakonfigurované v nástroji Azure Monitor.

* **Zpracování události** poskytují informace o analýze události nebo výstrahy, které byly zpracovány za vás. Příkladem tohoto typu jsou [výstrahy Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) kde [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) má zpracovat a analyzovat vaše předplatné a poskytuje výstrahy stručné zabezpečení.

V následující tabulce jsou uvedeny nejdůležitější typy protokolů dostupných v Azure:

| Kategorie protokolu | Typ protokolu | Využití | Integrace |
| ------------ | -------- | ------ | ----------- |
|[Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Rovina řízení událostí na prostředky Azure Resource Manageru|   Poskytuje podrobné informace o operacích provedených na prostředky ve vašem předplatném.|    Rozhraní REST API, [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Protokoly diagnostiky Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Časté data o operaci prostředky Azure Resource Manageru v rámci předplatného|    Poskytuje přehled o operacích, které provedly vaše prostředky, samotného.| Azure Monitor, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Sestavy Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Protokoly a sestavy | Sestavy aktivit přihlašování uživatele a informace aktivit systému o uživatelích a Správa skupin.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuální počítače a cloudové služby](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Služba Protokol událostí Windows a protokolu Syslog v Linuxu|  Zaznamenává systémová data a data protokolování na virtuální počítače a přenese data do účtu úložiště Azure podle vašeho výběru.|   Windows (pomocí Windows Azure Diagnostics [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)] úložiště) a Linuxu ve službě Azure Monitor|
|[Analýza úložiště Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Protokolování úložiště poskytuje data metriky pro účet úložiště|Poskytuje podrobné informace o trasování požadavků, analyzuje trendy využití a Diagnostika problémů s vaším účtem úložiště.|   Rozhraní REST API nebo [klientské knihovny](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Protokolů toků skupin zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formát JSON ukazuje příchozí a odchozí toky na základě podle pravidla|Zobrazí informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Protokoly, výjimky a vlastní Diagnostika|   Poskytuje application performance monitoring (APM) služby pro webové vývojáře ve více platformách.| Rozhraní REST API [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Zpracování dat a výstrah zabezpečení|    Azure Security Center oznámení, oznámení Azure Log Analytics|   Poskytuje informace o zabezpečení a upozornění.|  Rozhraní REST API, JSON|

### <a name="activity-logs"></a>Protokoly aktivit
[Protokoly aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operacích provedených na prostředky ve vašem předplatném. Protokoly aktivit byly dříve označovanou jako "protokolů auditu" nebo "provozní protokoly,", protože ohlásí [rovina řízení událostí](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) pro vaše předplatná. 

Protokoly aktivit vám pomáhají určit, "co, kdo a kdy" pro operace zápisu (to znamená, PUT, publikovat nebo odstranění). Taky vám pomůžou pochopit, stav operace a další relevantní vlastnosti protokoly aktivit. Protokoly aktivit nezahrnují operace čtení (GET).

V tomto článku PUT, POST a DELETE najdete všechny operace zápisu, které obsahuje protokol aktivit na prostředky. Můžete například použít protokolů aktivit najdete chybu, pokud řešíte problémy a sledovat, jak uživatel ve vaší organizaci změnil prostředek.

![Diagram protokolu aktivit](./media/azure-log-audit/azure-log-audit-fig1.png)

Události z protokolu aktivita můžete načíst pomocí webu Azure portal, [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), rutin prostředí PowerShell a [REST API služby Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Protokoly aktivit mají období uchovávání dat 90 dní.

Scénáře integrace pro událost protokolu aktivit:

* [Vytvořit upozornění e-mailu nebo webhooku, který se aktivuje událost protokolu aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Stream do centra událostí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) za účelem ingestování datových vlastní analýzy řešení, jako je například Power BI nebo služby třetích stran.

* Analýza ve službě Power BI s použitím [balíček obsahu Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Uložte ho do účtu úložiště pro archivaci nebo ruční kontrolu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Doba uchování (ve dnech) můžete určit pomocí profily protokolů.

* Dotaz a zobrazit na webu Azure Portal.

* Zjistit pomocí rutiny Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API.

* Exportovat protokol aktivit s profily protokolů do [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Můžete použít účet úložiště nebo [obor názvů centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) , který není ve stejném předplatném jako ten, který generuje protokol. Kdo konfiguruje nastavení, musí mít odpovídající [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) přístup k oběma předplatným.

### <a name="azure-diagnostics-logs"></a>Diagnostické protokoly Azure
Protokoly diagnostiky Azure jsou emitovány prostředek, který poskytuje bohaté a časté data o fungování tohoto prostředku. Obsah tyto protokoly se liší podle typu prostředku. Například [protokoly událostí systému Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) jsou kategorie protokolů diagnostiky pro virtuální počítače, a [objektů blob, tabulky a fronty protokoly](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) kategorie diagnostické protokoly pro účty úložiště. Diagnostické protokoly se liší od protokolů aktivit, které poskytují přehled o operacích provedených na prostředky ve vašem předplatném.

![Diagramy protokoly diagnostiky Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Protokoly diagnostiky Azure nabízí více možností konfigurace, jako je například na webu Azure portal, Powershellu, rozhraní příkazového řádku Azure a rozhraní REST API.

**Scénáře integrace**

* Uložte je do [účtu úložiště](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) pro auditování nebo ruční kontrolu. Doba uchování (ve dnech) můžete určit pomocí nastavení diagnostiky.

* [Stream do služby event hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) za účelem ingestování datových služby třetích stran nebo vlastních analytického řešení, jako například [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analyzovat pomocí [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Podporované služby, schéma pro diagnostické protokoly a podporovaných protokolů kategorií podle typu prostředku**


| Služba | Schéma a dokumentace | Typ prostředku | Kategorie |
| ------- | ------------- | ------------- | -------- |
|Nástroj pro vyrovnávání zatížení Azure| [Log Analytics pro nástroj pro vyrovnávání zatížení (Preview)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Network Security Groups (Skupiny zabezpečení sítě)|[Log Analytics pro skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Diagnostika protokolování pro službu Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Protokoly Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Povolení a používání prohledání analýzy provozu](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Přístup k protokolům diagnostiky pro Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Auditování<br>Požadavky|
|Azure Data Lake Analytics|[Přístup k protokolům diagnostiky pro Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Auditování<br>Požadavky|
|Azure Logic Apps|[Vlastní schéma sledování B2B Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|Modul runtime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Protokoly diagnostiky Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Log Analytics pro Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Protokoly diagnostiky centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Protokoly úlohy diagnostiky](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Spouštěcí<br>Vytváření obsahu|
|Azure Service Bus|[Protokoly diagnostiky služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Generování sestav v Azure Active Directory
Azure Active Directory (Azure AD) zahrnuje zabezpečení, aktivit a zprávy o auditu pro adresáře uživatele. [Sestavy auditu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomáhá identifikovat privilegovaných akcí, ke kterým došlo v instanci Azure AD daného uživatele. Privilegované akce zahrnují změny zvýšení oprávnění (například role vytvoření nebo resetování hesla), změny konfigurace zásad (například zásady pro hesla) nebo změny v konfiguraci adresáře (například změny nastavení federace domény).

Sestavy poskytují záznam auditu uživatele, který provedl akci, cílový prostředek měla vliv na změnu, datum a čas (ve standardu UTC) jako název události. Uživatelům můžete načíst seznam událostí auditu, které pro Azure AD prostřednictvím [webu Azure portal](https://portal.azure.com/), jak je popsáno v [zobrazit protokoly auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Součástí sestavy jsou uvedeny v následující tabulce:

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

Data v těchto sestavách může být užitečná pro vaše aplikace, jako je například informace o zabezpečení a správu událostí (SIEM) systémů, audit a nástroje pro business intelligence. Rozhraní API pro generování sestav v Azure AD poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Můžete volat tyto [rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) z různé programovací jazyky a nástroje.

Události sestavy auditování Azure AD se uchovávají po dobu 180 dnů.

> [!Note]
> Další informace o uchovávání sestav, naleznete v tématu [zásady uchování sestav Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Pokud vás zajímá uchování delší událostí auditu, pomocí rozhraní API pro generování sestav pravidelně o přijetí změn [události auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) do samostatného úložiště dat.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Protokolů virtuálního počítače, které používají Azure Diagnostics
[Diagnostika Azure](https://docs.microsoft.com/azure/azure-diagnostics) je funkce v Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít z některého z několika zdrojů. V tuto chvíli nepodporuje jsou [cloudu Azure, služby, webové a pracovní role](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Protokolů virtuálního počítače, které používají Azure Diagnostics](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineshttpsazuremicrosoftcomdocumentationlearning-pathsvirtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Virtuální počítače Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) se systémem Microsoft Windows a [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Azure Diagnostics na virtuálním počítači můžete povolit pomocí některého z následujících akcí:

* [Trasování virtuálních počítačů Azure pomocí sady Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Vzdálené nastavení Azure Diagnostics na virtuálním počítači Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Použití Powershellu k nastavení diagnostiky na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Vytvoření virtuálního počítače s Windows s monitorováním a diagnostikou pomocí šablony Azure Resource Manageru](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) protokoly a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště. Je k dispozici pro Storage Analytics protokolování [služby Azure Blob, fronty Azure a Azure Table storage](https://docs.microsoft.com/azure/storage/storage-introduction). Analýza úložiště protokoluje podrobné informace o úspěšné i neúspěšné požadavky do služby úložiště.

Tyto informace můžete použít ke sledování jednotlivých požadavků a Diagnostikujte problémy pomocí služby storage. Žádosti o přihlášení snažíme maximálně vyhovět. Položky protokolu se vytvoří pouze v případě, že jsou požadavky na koncový bod služby. Například pokud účet úložiště je v její koncový bod služby blob, ale ne v jeho tabulek nebo front koncové body, jsou vytvořeny pouze protokoly, které se týkají služby Blob storage.

Použití Storage Analytics, povolte jej jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ho povolit [webu Azure portal](https://portal.azure.com/). Další informace najdete v tématu [monitorování účtu úložiště na webu Azure Portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Můžete také povolit Storage Analytics prostřednictvím kódu programu přes rozhraní REST API nebo knihovny klienta. Pomocí operace nastavit vlastnosti služby můžete povolit Storage Analytics jednotlivě pro každou službu.

Agregovaná data se ukládají v dobře známý objekt blob (pro protokolování) a dobře známých tabulek (pro metriky), které dostanete pomocí služby Blob storage a služba Table storage API.

Analýza úložiště má limit 20terabajtů (TB) na množství uložených dat, která je nezávislá celkový limit pro váš účet úložiště. Všechny protokoly se ukládají v [objekty BLOB bloku](https://docs.microsoft.com/azure/storage/storage-analytics) v kontejneru nazvaném $logs, které se automaticky vytvoří při povolení analýzy úložiště pro účet úložiště.

> [!Note]
> * Další informace o fakturaci a zásad uchovávání dat najdete v tématu [analytika úložiště a fakturace](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Další informace o omezeních účtů úložišť najdete v tématu [škálovatelnost a výkonnostní cíle Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Analýza úložiště protokoluje následující typy ověřený a anonymních požadavků:

| Ověřeno  | Anonymní|
| :------------- | :-------------|
| Úspěšné požadavky | Úspěšné požadavky |
|Neúspěšné požadavky, včetně vypršení časového limitu, omezování, sítě, autorizace a dalších chyb | Požadavky pomocí sdíleného přístupového podpisu, včetně úspěšné a neúspěšné požadavky |
| Požadavky pomocí sdíleného přístupového podpisu, včetně úspěšné a neúspěšné požadavky |Chyby časového limitu klienta a serveru |
|   Požadavky na analytických dat |    Neúspěšné požadavky GET s kódem chyby 304 (Neupraveno) |
| Storage Analytics, jako je protokol vytvoření nebo odstranění, žádosti se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [Storage Analytics protokoluje operace a stavové zprávy](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [formát protokolu Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Všechny ostatní neúspěšné anonymní žádosti se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [Storage Analytics protokoluje operace a stavové zprávy](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [formát protokolu Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Síťových protokolů Azure
Síť protokolování a monitorování v Azure je komplexní a zahrnuje dvě rozsáhlé kategorie:

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher): monitorování sítě založené na scénářích se poskytuje zároveň s funkcí ve službě Network Watcher. Tato služba zahrnuje zachycení paketu, další směrování, IP tok ověřit, zobrazení skupin zabezpečení, protokoly toků NSG. Scénář úrovně monitorování poskytuje komplexní přehled síťové prostředky, na rozdíl od sledování jednotlivých síťových prostředků.

* [Sledování prostředků](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring): sledování na úrovni prostředků obsahuje čtyři funkce, diagnostické protokoly, metriky, řešení potíží a stav prostředku. Všechny tyto funkce jsou sestaveny na úrovni síťových prostředků.

![Síťových protokolů Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v aplikaci, do a z Azure. Nástroj Diagnostika sítě a vizualizačních nástrojů, které jsou k dispozici pomocí služby Network Watcher pomáhají porozumět, diagnostice a získání přehledu o vaší síti v Azure.

### <a name="network-security-group-flow-logging"></a>Protokolování toků skupin zabezpečení sítě

[Protokoly toků NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) jsou funkce služby Network Watcher, můžete použít k zobrazení informací o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě. Tyto protokoly toku jsou napsané ve formátu JSON a zobrazit:
* Odchozí a příchozí toků na základě podle pravidla.
* Síťové rozhraní, která se použije tok do.
* 5 řazené kolekce členů informace o toku: zdrojová nebo cílová IP adresa, zdrojový nebo cílový port a protokol.
* Určuje, zda přenos byl povolen nebo odepřen.

I když protokoly toku Nsg cíl, nezobrazují se stejným způsobem jako ostatní protokoly. Protokoly toku se ukládají pouze v rámci účtu úložiště.

Platí stejné zásady uchovávání informací, které se zobrazují na jiné protokoly pro protokoly toků. Protokoly mají zásady uchovávání informací, které můžete nastavit od 1 den až 365 dnů. Pokud zásady uchovávání nejsou nastavené, protokoly se ukládají navždy.

**Protokoly diagnostiky**

Pravidelné a spontánních události jsou vytvořené síťové prostředky a protokolovány v účtech úložiště a odeslaných do Log Analytics nebo centra událostí. Protokoly poskytují přehled o stavu prostředku. Může se zobrazit v nástrojů, jako je Power BI a Log Analytics. Zjistěte, jak zobrazit diagnostické protokoly, najdete v článku [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Protokoly diagnostiky](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnostické protokoly jsou k dispozici pro [nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy, a [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher dává zobrazení protokolů Diagnostika. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto hlediska můžete povolit nebo zakázat síťové prostředky, jednoduše a rychle.


Kromě výše uvedených protokolování možností Network Watcher aktuálně má následující možnosti:
- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): poskytuje přehled úrovni sítě, který zobrazuje různá propojení a přidružení mezi síťové prostředky ve skupině prostředků.

- [Proměnné zachytávání paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): zaznamená data o paketů do a z virtuálního počítače. Rozšířené možnosti filtrování a upřesnění ovládací prvky, jako je například nastavení omezení času a velikost, poskytují flexibility. Paket data mohou být uloženy v úložišti objektů blob nebo na místním disku v *CAP* formát souboru.

* [Ověření IP toku](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): zkontroluje, jestli je paket povolený nebo zakázaný podle informace 5 řazené kolekce členů paketů parametry toku (to znamená, cílová IP adresa, Zdrojová IP adresa, cílový port, zdrojový port a protokol). Pokud paketu je skupina zabezpečení, je vrácena pravidlo a skupiny, které paket zamítlo.

* [Další směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Určuje další segment pro pakety směrovány v prostředcích infrastruktury sítě Azure, takže můžete diagnostikovat všechny správně nakonfigurovaný. trasy definované uživatelem.

* [Zobrazení skupin zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): získá pravidla efektivní a použité zabezpečení, které se použijí na virtuálním počítači.

* [Brána virtuální sítě a řešení potíží s připojením](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): vám pomohou s řešením brány virtuální sítě a připojení.

* [Sítě limity předplatného](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): umožňuje zobrazit využití prostředků sítě proti omezení.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba APM pro webové vývojáře ve více platformách. Použijte ho k monitorování živé webové aplikace. Automaticky zjišťuje anomálie výkonu. Obsahuje výkonné analytické nástroje pro diagnostiku problémů a pomáhá porozumět, jak vlastně uživatelé vaši aplikaci používají.

Application Insights je určena k pomáhala průběžně vylepšovat výkon a použitelnost.

Funguje pro aplikace na nejrůznějších platformách, včetně .NET, Node.js a J2EE, ať už jsou hostované místně nebo v cloudu. Integruje se s vaším procesem DevOps a obsahuje spojovací body pomocí různých vývojových nástrojů.

![Application Insights diagram](./media/azure-log-audit/azure-log-audit-fig6.png)

Služba Application Insights je zaměřená na vývojový tým. Pomáhá pochopit, jaký je výkon vaší aplikace a jak se využívá. Monitoruje tyto parametry:

* **Požádat o sazby, doby odezvy a míra selhání**: Zjistěte, které stránky jsou nejoblíbenější a v kterou denní, dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud doba odezvy a míra selhání zvýší když existují další požadavky, bude pravděpodobně pravděpodobně potíže.

* **Míra závislosti, doby odezvy a míra selhání**: Zjistěte, zda externích služeb vás zpomalují.

* **Výjimky**: Analýza souhrnné statistiky nebo vyberte konkrétní instance a přejít k podrobnostem trasování zásobníku a souvisejících požadavcích. Hlásí se výjimky serveru i prohlížeče.

* **Zobrazení stránek a výkonu načítání**: získání sestav z prohlížečů uživatelů.

* **Volání AJAX**: sazby webovou stránku, doby odezvy a míra selhání.

* **Počty uživatelů a relací**.

* **Čítače výkonu**: získání dat z Windows nebo Linuxem serverový počítač, například CPU, paměť a využití sítě.

* **Diagnostika hostitele**: získání dat z Dockeru nebo Azure.

* **Protokoly trasování diagnostiky**: získání dat z vaší aplikace tak, aby události trasování můžete porovnat s požadavky.

* **Vlastní události a metriky**: získání dat, které napíšete sami v kódu klienta nebo serveru, ke sledování obchodních událostí, například prodané položky nebo Vyhrané hry.

Následující tabulka uvádí a popisuje scénáře integrace:

| Scénáře integrace | Popis |
| --------------------- | :---------- |
|[Mapa aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Komponenty vaší aplikace, včetně klíčových metrik a výstrah.||
|[Diagnostické vyhledávání pro instanci dat](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Události vyhledávání a filtrování, jako jsou třeba požadavky, výjimky, volání závislosti, trasování protokolů a zobrazení stránek.||
|[Průzkumník metrik pro agregovaná data](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Prozkoumání, filtrování a segmentace agregovaných dat, jako jsou třeba frekvence požadavků, selhání a výjimek, doby odezvy a časy načtení stránek.||
|[Řídicí panely](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Propojení dat z různých zdrojů a jejich sdílení s ostatními. Ideální pro aplikace s více komponentami a pro nepřetržité zobrazení v týmové místnosti.||
|[Live Metrics Stream](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Při nasazování nového sestavení sledujte tyto ukazatele výkonu téměř v reálném čase, abyste se ujistili, že všechno funguje podle očekávání.||
|[Analýzy](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Tento výkonný dotazovací jazyk umožňuje odpovědět na složité dotazy týkající se využití a výkonu vaší aplikace.||
|[Automatické a ruční výstrahy](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatické výstrahy se přizpůsobí běžným vzorům telemetrie vaší aplikace a se zobrazí, když něco neobvyklého. Výstrahy se také dají nastavit pro konkrétní úrovně vlastních nebo standardních metrik.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Zobrazit data o výkonu v kódu. Ke kódu je možné přejít z trasování zásobníku.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrujte metriky využití s ostatními funkcemi business intelligence.||
|[REST API](https://dev.applicationinsights.io/)|Napište kód pro spouštění dotazů nad daty metrik a nezpracovanými daty.||
|[Průběžný export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Jakmile bude přijata se dají hromadně exportovat nezpracovaná data do úložiště.||

### <a name="azure-security-center-alerts"></a>Upozornění Azure Security Center
Detekce hrozeb služby Azure Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení z vašich prostředků Azure, sítě a připojených partnerských řešení. Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů. U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit. Další informace najdete v tématu [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Azure Security Center diagramu](./media/azure-log-audit/azure-log-audit-fig7.png)

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Použije prorazit ve velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologie k vyhodnocování událostí v rámci prostředků cloudové infrastruktury. Tímto způsobem detekuje hrozby, které by jinak nebylo možné identifikovat pomocí manuálních metod a předvídání vývoje útoků. Do této analýzy zabezpečení patří:

* **Integrovaná analýza hrozeb**: hledá známých útočníků použitím globální analýzu hrozeb z produktů společnosti Microsoft a služby, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) a externích informačních kanálů.

* **Behaviorální analýza**: používá známá schémata k odhalování škodlivého chování.

* **Detekce anomálií**: pomocí statistické profilace sestavení historické standardní hodnoty. Upozorní na odchylky od zavedených standardních hodnot, které mají potenciál útoku.

Mnoho operací zabezpečení a reakce na incidenty týmy využívají řešení SIEM jako výchozí bod pro třídění a prošetřování výstrah zabezpečení. S integrací protokolů Azure můžete synchronizovat výstrahy Security Center a události zabezpečení virtuálních počítačů, shromážděné Azure protokolů diagnostiky a auditu, pomocí řešení Log Analytics nebo systému SIEM téměř v reálném čase.

## <a name="log-analytics"></a>Log Analytics 

Log Analytics je služba v Azure, která pomáhá shromažďovat a analyzovat data, která je vygenerovaná prostředky ve vašem cloudovém a místním prostředí. Poskytuje přehledy v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, díky kterým můžete analyzovat miliony záznamů napříč vašimi úlohami a servery bez ohledu na jejich fyzické umístění.

![Log Analytics diagram](./media/azure-log-audit/azure-log-audit-fig8.png)

V System center Log Analytics je pracovní prostor Log Analytics, která je hostovaná v Azure. Log Analytics shromažďuje data v pracovním prostoru z připojených zdrojů tak, že konfigurace zdroje dat a přidání řešení do vašeho předplatného. Zdroje dat a řešení, každý vytvoření různých typech záznamů, každý s vlastní sadou vlastností. Ale zdroje a řešení lze přesto analyzovat společně v dotazech na pracovní prostor. Díky této funkci můžete používat stejné nástroje a metody pro práci s širokou škálu data shromážděná z různých zdrojů.

Připojené zdroje jsou počítače a další prostředky, které generují data shromážděná službou Log Analytics. Zdroje mohou zahrnovat agenty, kteří jsou nainstalováni na [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) a [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) počítačů, které se připojují přímo, nebo agenti v [připojené skupiny pro správu System Center Operations Manager](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics může také shromažďovat data ze [účtu služby Azure storage](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Zdroje dat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) jsou různé druhy dat shromážděných z každého připojeného zdroje. Zahrnout zdroje události a [údaje o výkonu](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) z [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) a agenty Linux spolu se zdroji, jako [protokoly služby IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) a [vlastní textové protokoly](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Nakonfigurujete každý zdroj dat, který chcete shromáždit, a konfigurace se automaticky distribuuje každému připojenému zdroji.

Existují čtyři způsoby, jak [shromažďovat protokoly a metriky pro služby Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):
* Diagnostika Azure směrovat do služby Log Analytics (**diagnostiky** v následující tabulce)

* Diagnostika Azure do služby Azure storage do služby Log Analytics (**úložiště** v následující tabulce)

* Konektory pro služby Azure (**konektor** v následující tabulce)

* Skripty, které shromažďovat a následně je publikovat data do Log Analytics (prázdné buňky v následující tabulce a pro služby, které nejsou uvedené)

| Služba | Typ prostředku | Logs | Metriky | Řešení |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostika|Diagnostika|    [Aplikace Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [analýzy brány](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Spojovací čára|  Spojovací čára|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [konektoru (Preview)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Účty Azure Automation| Microsoft.Automation/<br>AutomationAccounts|    Diagnostika||       [Další informace](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Účty služby Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnostika|    Diagnostika||
|Klasické cloudové služby||       Úložiště||       [Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Diagnostika|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostika|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnostika|||
|Azure – obor názvů centra událostí| Microsoft.EventHub/<br>Obory názvů|  Diagnostika|    Diagnostika||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostika||
|Azure Key Vault|   Microsoft.KeyVault/<br>trezory služby|  Diagnostika  || [Analýza služby Key Vault](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Nástroj pro vyrovnávání zatížení Azure|   Microsoft.Network/<br>Load Balancerech|    Diagnostika|||
|Azure Logic Apps|  Microsoft.Logic/<br>Pracovní postupy|  Diagnostika|    Diagnostika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Skupiny zabezpečení sítě)|   Microsoft.Network/<br>networksecuritygroups|Diagnostika||   [Analýzy skupin zabezpečení sítě Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Trezory služby Recovery|   Microsoft.RecoveryServices/<br>trezory služby|||[Analýza (Preview) služby Azure Recovery Services](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Služby hledání|   Microsoft.Search/<br>searchServices|    Diagnostika|    Diagnostika||
|Obor názvů služby Service Bus| Microsoft.ServiceBus/<br>Obory názvů|    Diagnostika|Diagnostika|    [Služba Service Bus Analytics (Náhled)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Úložiště||    [Analýza služby Service Fabric (verze Preview)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servery pro /<br>databáze||       Diagnostika||
||Microsoft.Sql/<br>servery pro /<br>elasticPools||||
|Úložiště|||         Skript| [Analýza úložiště Azure (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>virtuálních počítačů|  Linka|  Linka||
||||Diagnostika||
|Škálovací sady virtuálních počítačů|    Microsoft.Compute/<br>virtuálních počítačů    ||Diagnostika||
||Microsoft.Compute/<br>virtualMachineScaleSets /<br>virtuálních počítačů||||
|Farmy webových serverů|Microsoft.Web/<br>serverfarms||   Diagnostika
|Websites|  Microsoft.Web/<br>Weby ||      Diagnostika|    [Další informace](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>servery /<br>sloty|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integrace protokolů s místními systémy SIEM
S [Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324), můžete integrovat nezpracovaných protokolů z vašich prostředků Azure v místním systému SIEM (zabezpečení informací a událostí systému pro správu).

![Diagram integrace protokolu](./media/azure-log-audit/azure-log-audit-fig9.png)

Integrace protokolů Azure diagnostics shromažďuje z virtuálních počítačů Windows, protokoly aktivit Azure, výstrahy Azure Security Center a protokolech poskytovatele prostředků Azure. Tato integrace poskytuje jednotný řídicí panel pro všechny vaše prostředky, ať už místní nebo v cloudu, takže můžete shromažďovat, korelovat, analyzovat a výstrahy pro události zabezpečení.

Integrace protokolů aktivit Azure v současné době podporuje integrace protokolů, protokoly auditu protokoly událostí Windows z virtuálních počítačů s Windows pomocí předplatného Azure, Azure Security Center upozornění, protokoly diagnostiky Azure a Azure AD.

| Typ protokolu | Log Analytics podporuje JSON (Splunk ArcSight a IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Protokoly auditu Azure AD|   Ano|
|Protokoly aktivit| Ano|
|Výstrahy služby Security Center |Ano|
|Protokoly diagnostiky (protokoly prostředku)|  Ano|
|Protokolů virtuálního počítače|   Ano, prostřednictvím předané události a ne prostřednictvím formátu JSON|

[Začínáme s Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Tento kurz vás provede instalací Azure Log Integration a integrace protokolů ze služby Azure storage, protokoly aktivit Azure, Azure Security Center oznámení a Azure AD protokoly auditu.

Scénáře integrace SIEM:

* [Partner kroky konfigurace](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Tento příspěvek na blogu se dozvíte, jak konfigurovat integraci protokolů Azure pro práci s partnerskými řešeními Splunk, HP ArcSight a IBM QRadar.

* [Nejčastější dotazy k Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Tento článek obsahuje odpovědi na otázky týkající se integrace protokolů Azure.

* [Integrace výstrah služby Security Center s integrací protokolů Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Tento článek popisuje, jak synchronizovat výstrahy Security Center, události zabezpečení virtuálních počítačů shromažďuje protokoly diagnostiky Azure a protokolů auditu Azure pomocí Log Analytics nebo systému SIEM řešení.

## <a name="next-steps"></a>Další postup

- [Auditování a protokolování](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): ochrana dat tak, že Údržba viditelnost a rychle reagovat na výstrahy zabezpečení včas.

- [Shromažďování protokolování a auditování protokolu zabezpečení v rámci Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): vynuťte tato nastavení zajistit, že vaše instance Azure jsou shromažďování správné protokolů zabezpečení a audit.

- [Konfigurace nastavení auditu pro kolekci webů](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Pokud jste správcem kolekce webů, načíst historii akcí jednotlivých uživatelů a historii akcí provedených během určité období. 

- [Prohledání protokolu auditování v centru dodržování předpisů a zabezpečení Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): použít centru dodržování předpisů a zabezpečení Office 365 k vyhledání protokolu auditu jednotné a zobrazit aktivit uživatelů a správce ve vaší organizaci Office 365.


