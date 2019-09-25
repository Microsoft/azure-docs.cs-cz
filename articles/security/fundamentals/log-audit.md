---
title: Protokolování a auditování Azure | Microsoft Docs
description: Přečtěte si, jak můžete pomocí dat protokolování získat podrobné informace o vaší aplikaci.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: d64cdce34127b066aedc8a5fcd6ec3a891b38c5e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262854"
---
# <a name="azure-logging-and-auditing"></a>Protokolování a auditování Azure

Azure nabízí řadu konfigurovatelných možností auditování zabezpečení a protokolování, které vám pomůžou identifikovat mezery v zásadách a mechanismech zabezpečení. Tento článek pojednává o generování, shromažďování a analýze protokolů zabezpečení ze služeb hostovaných v Azure.

> [!Note]
> Některá doporučení v tomto článku můžou mít za následek zvýšení využití dat, sítě nebo výpočetních prostředků a zvyšují náklady na licence nebo předplatné.

## <a name="types-of-logs-in-azure"></a>Typy protokolů v Azure

Cloudové aplikace jsou komplexní, s mnoha pohybujícími se částmi. Protokoly poskytují data, která vám pomůžou udržet aplikace v provozu. Protokoly vám pomůžou vyřešit minulé problémy nebo zabránit potenciálním. A můžou pomoci zlepšit výkon a udržovatelnost aplikace nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

Protokoly Azure se kategorizují do následujících typů:
* **Protokoly řízení a správy** poskytují informace o Azure Resource Manager operacích vytváření, aktualizace a odstraňování. Další informace najdete v tématu [protokoly aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md).

* **Protokoly roviny dat** poskytují informace o událostech vyvolaných jako součást využití prostředků Azure. Příkladem tohoto typu protokolu jsou protokoly událostí systému Windows, zabezpečení a aplikace ve virtuálním počítači a [diagnostické protokoly](../../azure-monitor/platform/resource-logs-overview.md) , které jsou konfigurovány prostřednictvím Azure monitor.

* **Zpracované události** poskytují informace o analyzovaných událostech a výstrahách, které byly zpracovány vaším jménem. Příklady tohoto typu jsou [Azure Security Center výstrahy](../../security-center/security-center-managing-and-responding-alerts.md) , které [Azure Security Center](../../security-center/security-center-intro.md) zpracovaly a analyzovaly vaše předplatné a poskytuje stručné výstrahy zabezpečení.

V následující tabulce jsou uvedeny nejdůležitější typy protokolů, které jsou k dispozici v Azure:

| Kategorie protokolu | Typ protokolu | Využití | Integrace |
| ------------ | -------- | ------ | ----------- |
|[Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md)|Řízení událostí roviny na Azure Resource Managerch prostředcích|  Poskytuje přehled o operacích, které byly provedeny u prostředků v rámci vašeho předplatného.|    Rozhraní REST API [Azure monitor](../../azure-monitor/platform/activity-logs-overview.md)|
|[Protokoly diagnostiky Azure](../../azure-monitor/platform/resource-logs-overview.md)|Častá data o provozu prostředků Azure Resource Manager v předplatném|    Poskytuje přehled o operacích, které provedl váš prostředek.| Azure Monitor, [Stream](../../azure-monitor/platform/resource-logs-overview.md)|
|[Sestavy Azure AD](../../active-directory/reports-monitoring/overview-reports.md)|Protokoly a sestavy | Oznamuje aktivity přihlašování uživatelů a informace o aktivitách systému o správě uživatelů a skupin.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtuální počítače a cloudové služby](../../azure-monitor/learn/quick-collect-azurevm.md)|Služba protokolu událostí systému Windows a protokol syslog pro Linux|  Zachycuje systémová data a data protokolování na virtuálních počítačích a přenáší tato data do účtu úložiště podle vašeho výběru.|   Windows (pomocí úložiště Windows Azure Diagnostics [[wad](../../monitoring-and-diagnostics/azure-diagnostics.md)]) a Linux v Azure monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Protokolování úložiště poskytuje data metrik pro účet úložiště.|Poskytuje přehled o požadavcích trasování, analyzuje trendy využití a diagnostikuje problémy s vaším účtem úložiště.|   REST API nebo [Knihovna klienta](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Protokoly toku pro skupinu zabezpečení sítě (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Formát JSON, zobrazení odchozích a příchozích toků podle jednotlivých pravidel|Zobrazuje informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Přehled aplikací](../../azure-monitor/app/app-insights-overview.md)|Protokoly, výjimky a vlastní diagnostika|  Poskytuje službu APM (Application Performance Monitoring) pro webové vývojáře na různých platformách.| REST API [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Zpracování dat/výstrahy zabezpečení|    Výstrahy Azure Security Center Azure Monitor protokoly|    Poskytuje informace o zabezpečení a výstrahy.|  Rozhraní REST API, JSON|

### <a name="activity-logs"></a>Protokoly aktivit

[Protokoly aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích, které byly provedeny u prostředků v rámci vašeho předplatného. Protokoly aktivit se dřív nazývaly protokoly auditu nebo provozní protokoly, protože pro vaše předplatná sestavují [události řízení a roviny](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) . 

Protokoly aktivit vám pomůžou určit, kdo a kdy operace zápisu (tj., PUT, POST nebo DELETE). Protokoly aktivit vám také pomůžou pochopit stav operace a další související vlastnosti. Protokoly aktivit nezahrnují operace čtení (GET).

V tomto článku najdete informace o tom, jak vložit, zveřejnit a odstranit všechny operace zápisu, které protokol aktivit obsahuje pro prostředky. Například můžete pomocí protokolů aktivit najít chybu při odstraňování potíží nebo sledovat, jak uživatel ve vaší organizaci změnil prostředek.

![Diagram protokolu aktivit](./media/log-audit/azure-log-audit-fig1.png)

Události můžete načíst z protokolu aktivit pomocí Azure Portal, rozhraní příkazového [řádku Azure](../../storage/common/storage-azure-cli.md), rutin PowerShellu a [Azure monitor REST API](../../azure-monitor/platform/rest-api-walkthrough.md). Protokoly aktivit mají dobu uchování dat 90 dní.

Scénáře integrace pro událost protokolu aktivit:

* [Vytvořte e-mail nebo upozornění Webhooku, které aktivuje událost protokolu aktivit](../../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md).

* [Streamujte ho do centra událostí](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) pro ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu, jako je PowerBI.

* Analyzujte ho v PowerBI pomocí [balíčku obsahu PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Uložte ho do účtu úložiště pro archivaci nebo ruční kontrolu](../../azure-monitor/platform/archive-activity-log.md). Dobu uchování (ve dnech) můžete určit pomocí profilů protokolů.

* Dotazování a zobrazení v Azure Portal.

* Dotazování pomocí rutiny PowerShellu, rozhraní příkazového řádku Azure nebo REST API.

* Exportujte protokol aktivit pomocí profilů protokolů, aby se [protokoly Azure monitor](../../log-analytics/log-analytics-queries.md).

Můžete použít účet úložiště nebo [obor názvů centra událostí](../../event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-capture.md) , který není ve stejném předplatném jako ten, který tento protokol vyvolal. Když nakonfigurujete nastavení, musí mít pro oba odběry příslušné oprávnění [řízení přístupu na základě role (RBAC)](../../role-based-access-control/role-assignments-portal.md) .

### <a name="azure-diagnostics-logs"></a>Protokoly diagnostiky Azure

Protokoly diagnostiky Azure se generují pomocí prostředku, který poskytuje bohatou a častou datovou činnost tohoto prostředku. Obsah tyto protokoly se liší podle typu prostředku. Například [protokoly událostí systému Windows](../../azure-monitor/platform/data-sources-windows-events.md) jsou kategorií diagnostických protokolů pro virtuální počítače a [protokoly objektů blob, tabulek a front](../../storage/common/storage-monitor-storage-account.md) jsou kategorie diagnostické protokoly pro účty úložiště. Diagnostické protokoly se liší od protokolů aktivit, které poskytují přehled o operacích provedených u prostředků v rámci vašeho předplatného.

![Diagramy protokolů diagnostiky Azure](./media/log-audit/azure-log-audit-fig2.png)

Protokoly diagnostiky Azure nabízejí několik možností konfigurace, jako jsou Azure Portal, PowerShell, Azure CLI a REST API.

**Scénáře integrace**

* Uložte je do [účtu úložiště](../../azure-monitor/platform/archive-diagnostic-logs.md) pro auditování nebo ruční kontrolu. Čas uchování (ve dnech) můžete určit pomocí nastavení diagnostiky.

* [Streamujte je do centra událostí](../../azure-monitor/platform/resource-logs-stream-event-hubs.md) pro ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu, jako je [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analyzujte je pomocí [protokolů Azure monitor](../../log-analytics/log-analytics-queries.md).

**Podporované služby, schéma pro diagnostické protokoly a podporované kategorie protokolů na typ prostředku**


| Služba | Schéma a dokumentace | Typ prostředku | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Protokoly Azure Monitor pro Load Balancer (Preview)](../../load-balancer/load-balancer-monitor-log.md)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Network Security Groups (Skupiny zabezpečení sítě)|[Protokoly Azure Monitor pro skupiny zabezpečení sítě](../../virtual-network/virtual-network-nsg-manage-log.md)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Protokolování diagnostiky pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Protokoly Key Vault](../../key-vault/key-vault-logging.md)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Povolení a použití Analýza provozu vyhledávání](../../search/search-traffic-analytics.md)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Přístup k protokolům diagnostiky pro Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Auditování<br>Požadavky|
|Azure Data Lake Analytics|[Přístup k protokolům diagnostiky pro Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Auditování<br>Požadavky|
|Azure Logic Apps|[Vlastní schéma sledování B2B Logic Apps](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|Modul<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Protokoly diagnostiky Azure Batch](../../batch/batch-diagnostics.md)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Protokoly Azure Monitor pro Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md)|Microsoft. Automation/automationAccounts<br>Microsoft. Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Protokoly diagnostiky Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Protokoly diagnostiky úlohy](../../stream-analytics/stream-analytics-job-diagnostic-logs.md)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Spuštění<br>Vytváření obsahu|
|Azure Service Bus|[Protokoly diagnostiky služby Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Generování sestav v Azure Active Directory

Azure Active Directory (Azure AD) zahrnuje sestavy zabezpečení, aktivity a auditu pro adresář uživatele. [Sestava auditu Azure AD](../../active-directory/active-directory-reporting-azure-portal.md) vám pomůže identifikovat privilegované akce, k nimž došlo v instanci Azure AD uživatele. Mezi privilegované akce patří změny zvýšení úrovně (například vytvoření role nebo resetování hesla), změna konfigurace zásad (například zásady hesel) nebo změny v konfiguraci adresáře (například změny nastavení federace domén).

Sestavy poskytují záznam auditu pro název události, uživatele, který tuto akci provedl, cílový prostředek, který tato akce ovlivnila, a datum a čas (ve standardu UTC). Uživatelé mohou získat seznam událostí auditu pro Azure AD prostřednictvím [Azure Portal](https://portal.azure.com/), jak je popsáno v tématu [zobrazení protokolů auditu](../../active-directory/reports-monitoring/overview-reports.md). 

Zahrnuté sestavy jsou uvedeny v následující tabulce:

| Sestavy zabezpečení | Sestavy aktivit | Sestavy auditu |
| :--------------- | :--------------- | :------------ |
|Přihlášení z neznámých zdrojů| Využití aplikací: souhrn| Sestava auditu adresáře|
|Přihlášení po několika neúspěších|  Využití aplikací: podrobnosti||
|Přihlášení z více geografických poloh|    Řídicí panel aplikací||
|Přihlášení z IP adres s podezřelou aktivitou|   Chyby zřizování účtů||
|Nestandardní přihlašovací aktivita|    Zařízení jednotlivých uživatelů||
|Přihlášení z možných nakažených zařízení|   Aktivita jednotlivých uživatelů||
|Uživatelé s neobvyklou přihlašovací aktivitou| Sestava aktivit skupin||
||Sestava o aktivitě registrace resetování hesla||
||Aktivity resetování hesla||

Data v těchto sestavách můžou být užitečná pro vaše aplikace, jako jsou systémy SIEM (Security Information and Event Management), audit a business intelligence Tools. Rozhraní API pro generování sestav v Azure AD poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato [rozhraní API](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) můžete volat z různých programovacích jazyků a nástrojů.

Uchovávání událostí v sestavě auditu Azure AD se v závislosti na typu licence přidružené k tenantovi liší v rozmezí 7-90 dní. 

> [!Note]
> Další informace o uchovávání sestav najdete v tématu [zásady uchovávání sestav služby Azure AD](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

Pokud vás zajímá uchování událostí auditu, pomocí rozhraní API pro vytváření sestav pravidelně vynechejte [události auditu](../../active-directory/active-directory-reporting-activity-audit-logs.md) do samostatného úložiště dat.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Protokoly virtuálních počítačů, které používají Azure Diagnostics

[Azure Diagnostics](../../monitoring-and-diagnostics/azure-diagnostics.md) je schopnost v Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít z libovolného z několika zdrojů. Aktuálně se podporuje [Webová role Azure Cloud Service a pracovní procesy](../../cloud-services/cloud-services-choose-me.md).

![Protokoly virtuálních počítačů, které používají Azure Diagnostics](./media/log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabricservice-fabricservice-fabric-overviewmd"></a>[Virtuální počítače Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) se systémem Microsoft Windows a [Service Fabric](../../service-fabric/service-fabric-overview.md)

Azure Diagnostics můžete na virtuálním počítači povolit jedním z následujících způsobů:

* [Trasování virtuálních počítačů Azure pomocí sady Visual Studio](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Nastavení Azure Diagnostics vzdáleně na virtuálním počítači Azure](../../virtual-machines/virtual-machines-dotnet-diagnostics.md)

* [Použití PowerShellu k nastavení diagnostiky na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Vytvoření virtuálního počítače s Windows s monitorováním a diagnostikou pomocí šablony Azure Resource Manager](../../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics

[Analýza úložiště Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) protokoluje a poskytuje data metrik pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště. K dispozici je Analýza úložiště protokolování pro [Azure Blob, frontu Azure a služby Azure Table Storage](../../storage/common/storage-introduction.md). Analýza úložiště protokoluje podrobné informace o úspěšných a neúspěšných požadavcích službě úložiště.

Tyto informace můžete použít k monitorování jednotlivých požadavků a k diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí. Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu objektu blob, ale ne ve svých koncových bodech tabulky nebo fronty, vytvoří se jenom protokoly, které se vztahují ke službě BLOB Storage.

Pokud chcete použít Analýza úložiště, povolte ji individuálně pro každou službu, kterou chcete monitorovat. Můžete ji povolit v [Azure Portal](https://portal.azure.com/). Další informace najdete v tématu [monitorování účtu úložiště v Azure Portal](../../storage/common/storage-monitor-storage-account.md). Analýza úložiště můžete také povolit programově prostřednictvím REST API nebo klientské knihovny. Pomocí operace set Service Properties (nastavit vlastnosti služby) Povolte Analýza úložiště pro každou službu zvlášť.

Agregovaná data jsou uložená v dobře známém objektu BLOB (pro protokolování) a v známých tabulkách (pro metriky), ke kterým máte přístup pomocí rozhraní API služby Blob Storage a služby Table Storage.

Analýza úložiště má omezení 20 TB (TB) na množství uložených dat, která jsou nezávislá na celkovém limitu pro váš účet úložiště. Všechny protokoly se ukládají v objektech [blob bloku](../../storage/common/storage-analytics.md) v kontejneru s názvem $Logs, který se automaticky vytvoří, když povolíte analýza úložiště pro účet úložiště.

> [!Note]
> * Další informace o fakturaci a zásadách uchovávání dat najdete v tématu [Analýza úložiště a fakturace](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Další informace o limitech účtu úložiště najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](../../storage/common/storage-scalability-targets.md).

Analýza úložiště protokoluje následující typy ověřených a anonymních požadavků:

| Ověřeno  | Anonymní|
| :------------- | :-------------|
| Úspěšné požadavky | Úspěšné požadavky |
|Neúspěšné požadavky, včetně časového limitu, omezování, sítě, autorizace a dalších chyb | Žádosti s použitím sdíleného přístupového podpisu, včetně neúspěšných a úspěšných požadavků |
| Žádosti s použitím sdíleného přístupového podpisu, včetně neúspěšných a úspěšných požadavků |Chyby vypršení časového limitu pro klienta i server |
|   Požadavky na analytické údaje |    Neúspěšné žádosti o získání s kódem chyby 304 (nezměněno) |
| Požadavky vytvořené Analýza úložiště samotné, například vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat je popsán v [Analýza úložiště protokolované operace a stavové zprávy](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu analýza úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat je popsán v [Analýza úložiště protokolované operace a stavové zprávy](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu analýza úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Síťové protokoly Azure

Protokolování a sledování sítě v Azure je komplexní a zahrnuje dvě široké kategorie:

* [Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md): Monitorování sítě založené na scénářích se poskytuje s funkcemi v Network Watcher. Tato služba zahrnuje zachytávání paketů, další směrování, ověření toku protokolu IP, zobrazení skupiny zabezpečení a protokoly NSG Flow. Monitorování úrovně scénáře nabízí kompletní pohled na síťové prostředky na rozdíl od monitorování jednotlivých síťových prostředků.

* [Monitorování prostředků](../../network-watcher/network-watcher-monitoring-overview.md): Monitorování úrovně prostředků zahrnuje čtyři funkce, diagnostické protokoly, metriky, řešení potíží a stav prostředků. Všechny tyto funkce jsou postavené na úrovni síťových prostředků.

![Síťové protokoly Azure](./media/log-audit/azure-log-audit-fig4.png)

Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure. Nástroje pro diagnostiku a vizualizaci sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure.

### <a name="network-security-group-flow-logging"></a>Protokolování toku skupin zabezpečení sítě

[Protokoly toku NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) jsou funkcí Network Watcher, které můžete použít k zobrazení informací o příchozích a odchozích přenosech IP adres prostřednictvím NSG. Tyto protokoly toku jsou napsané ve formátu JSON a zobrazují se:
* Odchozí a příchozí toky na základě jednotlivých pravidel.
* Síťová karta, na kterou se tok vztahuje
* 5 – informace o tomto toku: zdrojová nebo cílová IP adresa, zdrojový nebo cílový port a protokol.
* Zda byl provoz povolen nebo odepřen.

I když Flow zaznamenává cílový skupin zabezpečení sítě, nezobrazí se stejným způsobem jako ostatní protokoly. Protokoly toku se ukládají pouze v rámci účtu úložiště.

Stejné zásady uchovávání informací, které se zobrazují na jiných protokolech, se vztahují na protokoly toků. V protokolech jsou zásady uchovávání informací, které můžete nastavit od 1 dne do 365 dnů. Pokud zásady uchovávání nejsou nastavené, protokoly se ukládají navždy.

**Protokoly diagnostiky**

Pravidelné a spontánní události jsou vytvářeny pomocí síťových prostředků a přihlášené do účtů úložiště a odesílány do centra událostí nebo Azure Monitor protokolů. Protokoly poskytují přehled o stavu prostředku. Můžou se zobrazit v nástrojích, jako jsou Power BI a protokoly Azure Monitor. Další informace o tom, jak zobrazit protokoly diagnostiky, najdete v tématu [protokoly Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![Protokoly diagnostiky](./media/log-audit/azure-log-audit-fig5.png)

Diagnostické protokoly jsou k dispozici pro [Load Balancer](../../load-balancer/load-balancer-monitor-log.md), [skupiny zabezpečení sítě](../../virtual-network/virtual-network-nsg-manage-log.md), trasy a [Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Network Watcher poskytuje zobrazení diagnostické protokoly. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto zobrazení můžete snadno a rychle povolit a zakázat síťové prostředky.


Kromě výše zmíněných možností protokolování Network Watcher v současné době následující možnosti:
- [Topologie](../../network-watcher/view-network-topology.md): Poskytuje zobrazení na úrovni sítě, které zobrazuje různá propojení a přidružení mezi síťovými prostředky ve skupině prostředků.

- [Zachytávání paketů proměnných](../../network-watcher/network-watcher-packet-capture-overview.md): Zachycuje data paketů na virtuálním počítači a z něj. Možnosti rozšířeného filtrování a jemné ladění ovládacích prvků, jako jsou například nastavení omezení času a velikosti, poskytují nenáročnost. Data paketů se můžou ukládat do úložiště objektů BLOB nebo na místní disk ve formátu *. Cap* .

- [Ověření toku protokolu IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md): Zkontroluje, jestli je paket povolený nebo zakázaný na základě informací o toku 5 – parametry paketů řazené kolekce členů (tj. cílová IP adresa, zdrojová adresa IP, cílový port, zdrojový port a protokol). Pokud je paket zamítnutý skupinou zabezpečení, vrátí se pravidlo a skupina, které paket zamítly.

- [Další segment směrování](../../network-watcher/network-watcher-next-hop-overview.md): Určuje další segment směrování pro pakety směrované do síťových prostředků Azure, abyste mohli diagnostikovat všechny nesprávně nakonfigurované trasy definované uživatelem.

- [Zobrazení skupiny zabezpečení](../../network-watcher/network-watcher-security-group-view-overview.md): Získá platná a použitá pravidla zabezpečení, která jsou použita na virtuálním počítači.

- [Řešení potíží s bránou virtuální sítě a připojením](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): Pomáhá při odstraňování potíží s bránami a připojeními virtuální sítě.

- [Omezení pro odběr sítě](../../network-watcher/network-watcher-monitoring-overview.md): Umožňuje zobrazit využití síťových prostředků proti limitům.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba APM pro webové vývojáře na různých platformách. Slouží k monitorování živých webových aplikací. Automaticky detekuje anomálie výkonu. Obsahuje výkonné analytické nástroje pro diagnostiku problémů a pomáhá porozumět, jak vlastně uživatelé vaši aplikaci používají.

Application Insights je navržený tak, aby vám pomohla průběžně zlepšovat výkon a použitelnost.

Funguje pro aplikace na nejrůznějších platformách, včetně .NET, Node. js a Java EE bez ohledu na to, jestli jsou hostované místně nebo v cloudu. Integruje se s vaším procesem DevOps a má spojovací body s různými nástroji pro vývoj.

![Diagram Application Insights](./media/log-audit/azure-log-audit-fig6.png)

Služba Application Insights je zaměřená na vývojový tým. Pomáhá pochopit, jaký je výkon vaší aplikace a jak se využívá. Monitoruje tyto parametry:

* **Sazby požadavků, doby odezvy a frekvence selhání**: Zjistěte, které stránky jsou nejoblíbenější a v jakém denní době a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud dojde k většímu počtu požadavků, může se stát, že dojde k potížím s refinancováním v případě, že doba odezvy a frekvence selhání vznikne vysoké

* **Sazby závislosti, doba odezvy a frekvence selhání**: Zjistěte, jestli se vám vaše externí služby zpomalují.

* **Výjimky**: Analyzovat agregované statistiky nebo vybrat konkrétní instance a přejít k podrobnostem o trasování zásobníku a souvisejících požadavcích. Hlásí se výjimky serveru i prohlížeče.

* **Zobrazení stránky a výkon zatížení**: Získejte sestavy z prohlížečů uživatelů.

* **Volání AJAX**: Získejte sazby za webové stránky, časy odezvy a míry selhání.

* **Počty uživatelů a relací**.

* **Čítače výkonu**: Získejte data z počítačů se systémem Windows nebo Linux, jako je například využití procesoru, paměti a sítě.

* **Diagnostika hostitele**: Získat data z Docker nebo Azure.

* **Protokoly trasování diagnostiky**: Získejte data z vaší aplikace, abyste mohli korelovat události trasování s požadavky.

* **Vlastní události a metriky**: Získejte data, která píšete sami v kódu klienta nebo serveru, abyste mohli sledovat obchodní události, jako jsou například prodávané položky nebo vyhrané hry.

Následující tabulka uvádí a popisuje scénáře integrace:

| Scénář integrace | Popis |
| --------------------- | :---------- |
|[Mapa aplikace](../../azure-monitor/app/app-map.md)|Komponenty vaší aplikace, včetně klíčových metrik a výstrah.|
|[Diagnostika – vyhledávání dat instancí](../../azure-monitor/app/diagnostic-search.md)| Události vyhledávání a filtrování, jako jsou třeba požadavky, výjimky, volání závislosti, trasování protokolů a zobrazení stránek.|
|[Průzkumník metrik pro agregovaná data](../../azure-monitor/app/metrics-explorer.md)|Prozkoumání, filtrování a segmentace agregovaných dat, jako jsou třeba frekvence požadavků, selhání a výjimek, doby odezvy a časy načtení stránek.|
|[Řídicí panely](../../azure-monitor/app/overview-dashboard.md)|Propojení dat z různých zdrojů a jejich sdílení s ostatními. Ideální pro aplikace s více komponentami a pro nepřetržité zobrazení v týmové místnosti.|
|[Live Metrics Stream](../../azure-monitor/app/live-stream.md)|Při nasazování nového sestavení sledujte tyto ukazatele výkonu téměř v reálném čase, abyste se ujistili, že všechno funguje podle očekávání.|
|[Analýzy](../../azure-monitor/app/analytics.md)|Tento výkonný dotazovací jazyk umožňuje odpovědět na složité dotazy týkající se využití a výkonu vaší aplikace.|
|[Automatické a ruční výstrahy](../../azure-monitor/app/alerts.md)|Automatické výstrahy se přizpůsobí normálním vzorům telemetrie vaší aplikace a aktivují se, pokud je něco mimo obvyklý vzor. Výstrahy se také dají nastavit pro konkrétní úrovně vlastních nebo standardních metrik.|
|[Visual Studio](../../azure-monitor/app/visual-studio.md)|Zobrazení údajů o výkonu v kódu. Ke kódu je možné přejít z trasování zásobníku.|
|[Power BI](../../azure-monitor/app/export-power-bi.md)|Integrujte metriky využití s ostatními funkcemi business intelligence.|
|[REST API](https://dev.applicationinsights.io/)|Napište kód pro spouštění dotazů nad daty metrik a nezpracovanými daty.|
|[Průběžný export](../../azure-monitor/app/export-telemetry.md)|Hromadný export nezpracovaných dat do úložiště při jejich doručení.|

### <a name="azure-security-center-alerts"></a>Výstrahy Azure Security Center

Azure Security Center detekce hrozeb funguje tak, že automaticky shromažďuje informace o zabezpečení z vašich prostředků Azure, sítě a připojených partnerských řešení. Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů. U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit. Další informace najdete v tématu [Azure Security Center](../../security-center/security-center-intro.md).

![Diagram Azure Security Center](./media/log-audit/azure-log-audit-fig7.png)

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Aplikuje převratné technologie pro velké objemy dat a [strojové učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) k vyhodnocení událostí v rámci celého cloudového prostředku infrastruktury. Tímto způsobem detekuje hrozby, které by nebylo možné identifikovat pomocí ručních přístupů a předvídání vývoje útoků. Do této analýzy zabezpečení patří:

* **Integrovaná analýza hrozeb**: Vyhledá známé chybné objekty actor použitím globální analýzy hrozeb z produktů a služeb společnosti Microsoft, jednotky digitálních zločinů společnosti Microsoft (DCU), centra MSRC (Microsoft Security Response Center) a externích informačních kanálů.

* **Analýza chování**: Použije známé vzorce pro zjištění škodlivého chování.

* **Detekce anomálií**: K sestavení historických standardních hodnot používá statistickou profilaci. Upozorní na odchylky od zavedených standardních hodnot, které mají potenciál útoku.

Řada operací zabezpečení a týmů reakce na incidenty spoléhají na řešení SIEM jako výchozí bod pro třídění a vyšetřování výstrah zabezpečení. Pomocí Azure Log Integration můžete synchronizovat Security Center výstrahy a události zabezpečení virtuálních počítačů shromážděné diagnostikou Azure a protokoly auditu, a to s využitím protokolů Azure Monitor nebo řešení SIEM téměř v reálném čase.

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Protokoly Azure Monitor jsou služby v Azure, které vám pomůžou shromáždit a analyzovat data vygenerovaná prostředky ve vašem cloudovém a místním prostředí. Poskytuje přehledy v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, díky kterým můžete snadno analyzovat miliony záznamů napříč všemi vašimi úlohami a servery bez ohledu na jejich fyzické umístění.

![Diagram Azure Monitorch protokolů](./media/log-audit/azure-log-audit-fig8.png)

V centru Azure Monitor protokolů je pracovní prostor Log Analytics, který je hostovaný v Azure. Protokoly Azure Monitor shromažďují data v pracovním prostoru z připojených zdrojů konfigurací zdrojů dat a přidáním řešení do vašeho předplatného. Zdroje dat a řešení každý z nich vytváří různé typy záznamů, z nichž každá má vlastní sadu vlastností. Ale zdroje a řešení je stále možné analyzovat společně v dotazech do pracovního prostoru. Tato funkce umožňuje používat stejné nástroje a metody pro práci s nejrůznějšími daty shromážděnými různými zdroji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Připojené zdroje jsou počítače a další prostředky, které generují data shromážděná protokolem Azure Monitor. Zdroje můžou zahrnovat agenty nainstalované v počítačích se [systémem Windows](../../log-analytics/log-analytics-agent-windows.md) a [Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) , které se připojují přímo, nebo agenti v [připojené skupině pro správu System Center Operations Manager](../../azure-monitor/platform/om-agents.md). Protokoly Azure Monitor můžou shromažďovat taky data z [účtu služby Azure Storage](../../azure-monitor/platform/resource-logs-collect-storage.md).

[Zdroje dat](../../azure-monitor/platform/agent-data-sources.md) jsou různé druhy dat, která jsou shromažďována z každého připojeného zdroje. Zdroje zahrnují události a [data o výkonu](../../azure-monitor/platform/data-sources-performance-counters.md) z agentů [Windows](../../azure-monitor/platform/data-sources-windows-events.md) a Linux, kromě zdrojů, jako jsou [protokoly IIS](../../azure-monitor/platform/data-sources-iis-logs.md) a [vlastní textové protokoly](../../azure-monitor/platform/data-sources-custom-logs.md). Nakonfigurujete každý zdroj dat, který chcete shromáždit, a konfigurace se automaticky distribuuje každému připojenému zdroji.

Existují čtyři způsoby, jak [shromažďovat protokoly a metriky pro služby Azure](../../azure-monitor/platform/resource-logs-collect-storage.md):

* Azure Diagnostics přímo do protokolů Azure Monitor (**Diagnostika** v následující tabulce)

* Azure Diagnostics do služby Azure Storage pro Azure Monitor protokolů (**úložiště** v následující tabulce)

* Konektory pro služby Azure (**konektor** v následující tabulce)

* Skripty pro shromažďování a odesílání dat do protokolů Azure Monitor (prázdné buňky v následující tabulce a pro služby, které nejsou uvedené)

| Služba | Typ prostředku | Logs | Metriky | Řešení |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostika|Diagnostika|    [Aplikace Azure](../../azure-monitor/insights/azure-networking-analytics.md) [Analýza brány](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     Spojovací čára|  Spojovací čára|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Konektor (Preview)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Účty Azure Automation| Microsoft.Automation/<br>automationAccounts|    Diagnostika||       [Další informace](../../automation/automation-manage-send-joblogs-log-analytics.md)|
|Účty Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnostika|    Diagnostika||
|Klasické cloudové služby||       Storage||       [Další informace](../../azure-monitor/platform/azure-storage-iis-table.md)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>účty|       Diagnostika|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>účty|   Diagnostika|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>účty|   Diagnostika|||
|Obor názvů centra událostí Azure| Microsoft.EventHub/<br>namespaces|  Diagnostika|    Diagnostika||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostika||
|Azure Key Vault|   Microsoft.KeyVault/<br>vaults|  Diagnostika  || [Analýza služby Key Vault](../../azure-monitor/insights/azure-key-vault.md)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnostika|||
|Azure Logic Apps|  Microsoft.Logic/<br>workflows|  Diagnostika|    Diagnostika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Skupiny zabezpečení sítě)|   Microsoft.Network/<br>networksecuritygroups|Diagnostika||   [Analýzy skupin zabezpečení sítě Azure](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-and-network-security-group-analytics)|
|Trezory služby Recovery|   Microsoft.RecoveryServices/<br>vaults|||[Analýza (Preview) služby Azure Recovery Services](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Služby hledání|   Microsoft.Search/<br>searchServices|    Diagnostika|    Diagnostika||
|Obor názvů služby Service Bus| Microsoft.ServiceBus/<br>namespaces|    Diagnostika|Diagnostika|    [Služba Service Bus Analytics (Náhled)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Analýza služby Service Fabric (verze Preview)](../../service-fabric/service-fabric-diagnostics-oms-setup.md)|
|SQL (v12)| Microsoft.Sql/<br>servery<br>databases||       Diagnostika||
||Microsoft.Sql/<br>servery<br>elasticPools||||
|Storage|||         Skript| [Analýza úložiště Azure (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft. COMPUTE/<br>virtualMachines|  Linka|  Linka||
||||Diagnostika||
|Virtual Machine Scale Sets|    Microsoft. COMPUTE/<br>virtualMachines    ||Diagnostika||
||Microsoft. COMPUTE/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Webové serverové farmy|Microsoft.Web/<br>serverfarms||   Diagnostika
|Weby|  Microsoft.Web/<br>sites ||      Diagnostika|    [Další informace](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>místa<br>slots||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integrace protokolu s místními SIEM systémy

Díky Azure Log Integration můžete integrovat nezpracované protokoly z prostředků Azure do místního systému SIEM (informace o zabezpečení a systém pro správu událostí). Stahování AzLog bylo zakázáno 27. června 2018. Další informace o tom, co dělat, najdete v příspěvku [pomocí Azure monitoru pro integraci s nástroji Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) .

![Diagram integrace protokolu](./media/log-audit/azure-log-audit-fig9.png)

Integrace protokolu shromažďuje diagnostiku Azure z vašich virtuálních počítačů s Windows, protokolů aktivit Azure, výstrah Azure Security Center a protokolů poskytovatele prostředků Azure. Tato integrace poskytuje jednotný řídicí panel pro všechny vaše prostředky, ať už jsou místní nebo v cloudu, takže můžete agregovat, sladit, analyzovat a upozorňovat na události zabezpečení.

Integrace protokolů v současné době podporuje integraci protokolů aktivit Azure, protokolů událostí systému Windows z virtuálních počítačů s Windows s vaším předplatným Azure, Azure Security Center výstrah, protokolů diagnostiky Azure a protokolů auditu Azure AD.

| Typ protokolu | Protokoly Azure Monitor podporující JSON (Splunk, ArcSight a IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Protokoly auditu Azure AD|   Ano|
|Protokoly aktivit| Ano|
|Výstrahy Security Center |Ano|
|Diagnostické protokoly (protokoly prostředků)|  Ano|
|Protokoly virtuálních počítačů|   Ano, prostřednictvím předaných událostí a nikoli přes JSON|

[Začínáme s Azure log Integration](azure-log-integration-get-started.md): Tento kurz vás provede instalací Azure Log Integration a integrací protokolů z Azure Storage, protokolů aktivit Azure, výstrah Azure Security Center a protokolů auditu Azure AD.

Scénáře integrace pro SIEM:

* [Kroky konfigurace partnerského serveru](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Tento Blogový příspěvek ukazuje, jak nakonfigurovat Azure Log Integration pro práci s partnerskými řešeními Splunk, HP ArcSight a IBM QRadar.

* [Nejčastější dotazy k Azure log Integration](azure-log-integration-faq.md): Tento článek obsahuje odpovědi na otázky týkající se Azure Log Integration.

* [Integrace výstrah Security Center s Azure log Integration](../../security-center/security-center-export-data-to-siem.md): Tento článek popisuje, jak synchronizovat výstrahy Security Center, události zabezpečení virtuálních počítačů shromažďované protokoly diagnostiky Azure a protokoly auditu Azure pomocí protokolů Azure Monitor nebo řešení SIEM.

## <a name="next-steps"></a>Další kroky

- [Auditování a protokolování](management-monitoring-overview.md): Chraňte data udržováním viditelnosti a rychlé reakce na včasné výstrahy zabezpečení.

- [Protokolování zabezpečení a audit – shromažďování protokolů v rámci Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Vyvynuťte tato nastavení, abyste zajistili, že vaše instance Azure shromažďují správné protokoly zabezpečení a auditu.

- [Nakonfigurujte nastavení auditování pro kolekci webů](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Pokud jste správce kolekce webů, načtěte historii akcí jednotlivých uživatelů a historii akcí prováděných v určitém rozsahu kalendářních dat. 

- [Vyhledejte v protokolu auditu v sadě Office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Pomocí sady Office 365 Centrum zabezpečení a dodržování předpisů můžete vyhledat jednotný protokol auditu a zobrazit aktivitu uživatelů a správců v organizaci Office 365.


