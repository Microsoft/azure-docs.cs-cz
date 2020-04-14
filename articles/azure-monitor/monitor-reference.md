---
title: Co monitor Azure Monitor monitoru
description: Odkaz na všechny služby a další prostředky monitorované službou Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: e0e98b87cf3612bf01f90f806ea64ef06d08c60a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255324"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Co monitor umonitoruje?
Tento článek popisuje různé aplikace a služby, které jsou monitorovány azure monitoru. 

## <a name="insights-and-core-solutions"></a>Přehledy a základní řešení
Základní přehledy a řešení jsou považovány za součást Azure Monitoru a postupujte podle smluv o podpoře a úrovni služeb pro Azure. Jsou podporované ve všech oblastech Azure, kde je Azure Monitor k dispozici.

### <a name="insights"></a>Insights

Přehledy poskytují přizpůsobené prostředí monitorování pro konkrétní aplikace a služby. Shromažďují a analyzují protokoly i metriky.

| Přehled | Popis |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Rozšiřitelná služba Správy výkonu aplikací (APM) pro sledování vaší živé webové aplikace na libovolné platformě. |
| [Azure Monitor pro kontejnery](insights/container-insights-overview.md) | Monitoruje výkon úloh kontejnerů nasazených buď do instancí kontejnerů Azure, nebo do spravovaných clusterů Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). |
| [Azure Monitor pro Cosmos DB (preview)](insights/cosmosdb-insights-overview.md) | Poskytuje zobrazení celkového výkonu, selhání, kapacity a provozního stavu všech prostředků Azure Cosmos DB v jednotném interaktivním prostředí. |
| [Azure Monitor pro sítě (preview)](insights/network-insights-overview.md) | Poskytuje komplexní přehled o stavu a metriky pro všechny síťové prostředky. Rozšířené vyhledávací funkce vám pomohou identifikovat závislosti prostředků a umožní vám scénáře, jako je identifikace prostředků, které hostují váš web, pouhým vyhledáním názvu webu. |
[Azure Monitor pro skupiny prostředků (preview)](insights/resource-group-insights.md) |  Třídění a diagnostikovat všechny problémy, které vaše jednotlivé prostředky narazí, a zároveň nabízí kontext, pokud jde o stav a výkon skupiny prostředků jako celku. |
| [Azure Monitor pro úložiště (preview)](insights/storage-insights-overview.md) | Poskytuje komplexní monitorování vašich účtů Azure Storage tím, že poskytuje jednotné zobrazení výkonu, kapacity a dostupnosti služeb Azure Storage. |
| [Azure Monitor pro virtuální počítače](insights/container-insights-overview.md) | Monitoruje vaše virtuální počítače (VM) Azure a škálovací sady virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech. |

### <a name="core-solutions"></a>Základní řešení

Řešení jsou založena na dotazech protokolu a zobrazeních přizpůsobených pro konkrétní aplikaci nebo službu. Shromažďují a analyzují pouze protokoly a jsou zastaralé v průběhu času ve prospěch přehledů.

| Řešení | Popis |
|:---|:---|
| [Stav agenta](insights/solution-agenthealth.md) | Analyzujte stav a konfiguraci agentů Log Analytics. |
| [Správa výstrah](platform/alert-management-solution.md) | Analyzujte výstrahy shromážděné od nástroje System Center Operations Manager, Nagios nebo Zabbix. |
| [Mapa služeb](insights/service-map.md) | Automaticky zjišťuje součásti aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. |



## <a name="azure-services"></a>Služby Azure
V následující tabulce jsou uvedeny služby Azure a data, která shromažďují do Azure Monitoru. 

- Metriky – služba automaticky shromažďuje metriky do metrik Azure Monitor. 
- Protokoly – služba podporuje diagnostická nastavení, která můžou shromažďovat protokoly platformy a metriky do protokolů monitorování Azure.
- Insight – k dispozici je přehled pro službu, která poskytuje přizpůsobené monitorování prostředí pro službu.

| Služba | Metriky | Protokoly | Přehled | Poznámky |
|:---|:---|:---|:---|:---|
|Active Directory | Ne | Ano | [Ano](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Ne | Ne | Ne |  |
|Active Directory Domain Services | Ne | Ano | Ne |  |
|Protokol aktivit | Ne | Ano | Ne | |
|Rozšířená ochrana před internetovými útoky | Ne | Ne | Ne |  |
|Advisor | Ne | Ne | Ne |  |
|AI Builder | Ne | Ne | Ne |  |
|Analysis Services | Ano | Ano | Ne |  |
|API pro FHIR | Ne | Ne | Ne |  |
|API Management | Ano | Ano | Ne |  |
|App Service | Ano | Ano | Ne |  |
|AppConfig | Ne | Ne | Ne |  |
|Application Gateway | Ano | Ano | Ne |  |
|Atestační služba | Ne | Ne | Ne |  |
|Automation | Ano | Ano | Ne |  |
|Správce služeb Azure (RDFE) | Ne | Ne | Ne |  |
|Backup | Ne | Ano | Ne |  |
|Bastion | Ne | Ne | Ne |  |
|Batch | Ano | Ano | Ne |  |
|Batch AI | Ne | Ne | Ne |  |
|Služba Blockchain | Ne | Ano | Ne |  |
|Blueprint | Ne | Ne | Ne |  |
|Bot Service | Ne | Ne | Ne |  |
|Cloud Services | Ano | Ano | Ne | Agent potřebný ke sledování hostovaného operačního systému a pracovních postupů.  |
|Cloud Shell | Ne | Ne | Ne |  |
|Cognitive Services | Ano | Ano | Ne |  |
|Container Instances | Ano | Ne | Ne |  |
|Container Registry | Ano | Ano | Ne |  |
|Síť pro doručování obsahu (CDN) | Ne | Ano | Ne |  |
|Databáze Cosmos | Ano | Ano | [Ano](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Ne | Ne | Ne |  |
|Data Box | Ne | Ne | Ne |  |
|Katalog dat Gen2 | Ne | Ne | Ne |  |
|Data Explorer | Ano | Ano | Ne |  |
|Data Factory | Ano | Ano | Ne |  |
|Datová továrna v2 | Ne | Ano | Ne |  |
|Data Share | Ne | Ne | Ne |  |
|Database for MariaDB | Ano | Ano | Ne |  |
|Database for MySQL | Ano | Ano | Ne |  |
|Database for PostgreSQL | Ano | Ano | Ne |  |
|Database Migration Service | Ne | Ne | Ne |  |
|Databricks | Ne | Ano | Ne |  |
|DDoS Protection | Ano | Ano | Ne |  |
|DevOps | Ne | Ne | Ne |  |
|DNS | Ano | Ne | Ne |  |
|Názvy domén | Ne | Ne | Ne |  |
|DPS | Ne | Ne | Ne |  |
|Angažovanost zákazníků Dynamics 365 | Ne | Ne | Ne |  |
|Dynamics 365 Finance a operace | Ne | Ne | Ne |  |
|Event Grid | Ano | Ne | Ne |  |
|Event Hubs | Ano | Ano | Ne |  |
|ExpressRoute | Ano | Ano | Ne |  |
|Brána firewall | Ano | Ano | Ne |  |
|Front Door | Ano | Ano | Ne |  |
|Functions | Ano | Ano | Ne |  |
|HDInsight | Ne | Ano | Ne |  |
|HPC Cache | Ne | Ne | Ne |  |
|Ochrana informací | Ne | Ano | Ne |  |
|Intune | Ne | Ano | Ne |  |
|IoT Central | Ne | Ne | Ne |  |
|IoT Hub | Ano | Ano | Ne |  |
|Key Vault | Ano | Ano | Ne |  |
|Kubernetes Service (AKS) | Ne | Ne | [Ano](insights/container-insights-overview.md)  |  |
|Load Balancer | Ano | Ano | Ne |  |
|Logic Apps | Ano | Ano | Ne |  |
|Služba Machine Learning | Ne | Ne | Ne |  |
|Managed Applications  | Ne | Ne | Ne |  |
|Maps  | Ne | Ne | Ne |  |
|Media Services | Ano | Ano | Ne |  |
|Microsoft Flow | Ne | Ne | Ne |  |
|Microsoft Managed Desktop | Ne | Ne | Ne |  |
|Microsoft PowerApps | Ne | Ne | Ne |  |
|Microsoft Social Engagement | Ne | Ne | Ne |  |
|Microsoft Stream | Ano | Ano | Ne |  |
|Migrace | Ne | Ne | Ne |  |
|Multi-Factor Authentication | Ne | Ano | Ne |  |
|Network Watcher | Ano | Ano | Ne |  |
|Notification Hubs | Ano | Ne | Ne |  |
|Otevřené datové sady | Ne | Ne | Ne |  |
|Zásada | Ne | Ne | Ne |  |
|Power BI | Ano | Ano | Ne |  |
|Power BI Embedded | Ne | Ne | Ne |  |
|Privátní propojení | Ne | Ne | Ne |  |
|Komunikační platforma pro zařazování projektů | Ne | Ne | Ne |  |
|Red Hat OpenShift | Ne | Ne | Ne |  |
|Redis Cache | Ano | Ano | Ne |  |
|Resource Graph | Ne | Ne | Ne |  |
|Resource Manager | Ne | Ne | Ne |  |
|Maloobchodní vyhledávání – podle bingu | Ne | Ne | Ne |  |
|Search | Ano | Ano | Ne |  |
|Service Bus | Ano | Ano | Ne |  |
|Service Fabric | Ne | Ano | Ne | Agent potřebný ke sledování hostovaného operačního systému a pracovních postupů.  |
|Registrační portál | Ne | Ne | Ne |  |
|Site Recovery | Ne | Ano | Ne |  |
|Jarní cloudová služba | Ne | Ne | Ne |  |
|SQL Data Warehouse | Ano | Ano | Ne |  |
|Databáze SQL | Ano | Ano | Ne |  |
|SQL Server Stretch Database | Ano | Ano | Ne |  |
|Zásobník | Ne | Ne | Ne |  |
|Storage | Ano | Ne | [Ano](insights/storage-insights-overview.md) |  |
|Mezipaměť úložiště | Ne | Ne | Ne |  |
|Služby synchronizace úložiště | Ne | Ne | Ne |  |
|Stream Analytics | Ano | Ano | Ne |  |
|Time Series Insights | Ano | Ano | Ne |  |
|Tina | Ne | Ne | Ne |  |
|Traffic Manager | Ano | Ano | Ne |  |
|Univerzální tisk | Ne | Ne | Ne |  |
|Virtual Machine Scale Sets | Ne | Ano | [Ano](insights/vminsights-overview.md) | Agent potřebný ke sledování hostovaného operačního systému a pracovních postupů. |
|Virtuální počítače | Ano | Ano | [Ano](insights/vminsights-overview.md) | Agent potřebný ke sledování hostovaného operačního systému a pracovních postupů. |
|Virtual Network | Ano | Ano | [Ano](insights/network-insights-overview.md) |  |
|Virtuální síť – protokoly toku nsg | Ne | Ano | Ne |  |
|VPN Gateway | Ano | Ano | Ne |  |
|Windows Virtual Desktop | Ne | Ne | Ne |  |


## <a name="product-integrations"></a>Integrace produktů
Služby a řešení v následující tabulce ukládají svá data do pracovního prostoru Analýzy protokolů, aby je bylo možné analyzovat pomocí jiných dat protokolu shromážděných službou Azure Monitor.

| Produkt/služba | Popis |
|:---|:---|
| [Azure Automation](/azure/automation/) | Spravujte aktualizace operačního systému a sledujte změny v počítačích se systémem Windows a Linux. Viz [Sledování změn](../automation/change-tracking.md) a správa [aktualizací](../automation/automation-update-management.md). |
| [Ochrana informací Azure](https://docs.microsoft.com/azure/information-protection/) | Klasifikují a volitelně chrání dokumenty a e-maily. Viz [Centrální vykazování pro Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](/azure/security-center/) | Shromažďujte a analyzujte události zabezpečení a provádějte analýzu hrozeb. Viz [Shromažďování dat v Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Připojuje se k různým zdrojům, včetně Office 365 a Amazon Web Services Cloud Trail. Viz [Připojení zdrojů dat](/azure/sentinel/connect-data-sources). |
| [Key Vault Analytics](insights/azure-key-vault.md) | Analyzujte protokoly auditu azure trezoru klíčů. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Vytvořte diagnostické nastavení pro odesílání protokolů do Azure Monitoru. Viz [Odesílání dat protokolu do úložiště, centra událostí nebo analýzy protokolů v Intune (preview).](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)  |
| Síť  | [Sledování výkonu sítě](insights/network-performance-monitor.md) – sledování připojení k síti a výkonu koncových bodů služby a aplikace.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) – analyzujte protokoly a metriky z Azure Application Gateway.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) – analyzuje protokoly toku skupiny zabezpečení sítě Network Watcher (NSG), aby poskytl přehled o toku provozu ve vašem cloudu Azure. |
| [Office 365](insights/solution-office-365.md) | Sledujte své prostředí Office 365. Aktualizovaná verze s vylepšeným nástupem, které je k dispozici prostřednictvím Azure Sentinelu. |
| [SQL Analytics](insights/azure-sql.md) | Sledujte výkon databází Azure SQL, elastických fondů a spravovaných instancí ve velkém měřítku a napříč několika předplatnými. |
| [Surface Hub](insights/surface-hubs.md) | Sledujte stav a využití zařízení Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Shromažďujte data od agentů Operations Manageru propojením jejich skupiny pro správu s Azure Monitorem. Viz [Připojení nástroje Operations Manager k Azure Monitoru](platform/om-agents.md)<br> Vyhodnoťte rizika a stav vaší skupiny pro správu operations center operations manageru pomocí řešení [Operations Manager Assessment.](insights/scom-assessment.md) |
| [Místnosti Microsoft Teams](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Integrovaná komplexní správa zařízení Microsoft Teams Rooms. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Vytvářejte, testujte a distribuujte aplikace a pak sledujte jejich stav a využití. Viz [Zahájení analýzy mobilní aplikace pomocí Centra aplikací a Přehledů aplikací](learn/mobile-center-quickstart.md). |
| Windows | [Dodržování předpisů služby Windows Update](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) – Vyhodnoťte upgrady plochy systému Windows.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) – Integruje se s nástrojem Configuration Manager, který poskytuje přehled a inteligenci pro informovanější rozhodování o připravenosti klientů systému Windows na aktualizace. |



## <a name="other-solutions"></a>Další řešení
Další řešení jsou k dispozici pro sledování různých aplikací a služeb, ale aktivní vývoj se zastavil a nemusí být k dispozici ve všech oblastech. Vztahuje se na ně smlouva o úrovni služeb služby Azure Log Analytics.

| Řešení | Popis |
|:---|:---|
| [Kontrola stavu služby Active Directory](insights/ad-assessment.md) | Vyhodnoťte rizika a stav prostředí služby Active Directory. |
| [Stav replikace služby Active Directory](insights/ad-replication-status.md) | Pravidelně sleduje prostředí služby Active Directory, pokud neselhaly replikace. |
| [Analýza protokolu aktivit](platform/activity-log-view.md#azure-portal) | Zobrazení položek protokolu aktivit. |
| [Služba DNS Analytics (náhled)](insights/dns-analytics.md) | Shromažďuje, analyzuje a koreluje analytické a auditní protokoly služby Windows DNS a další související data ze serverů DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Shromažďujte, zobrazujte a analyzujte metriky stavu a výkonu systému Cloud Foundry napříč několika nasazeními. |
| [Containers](insights/containers.md) | Zobrazení a správa hostitelů kontejnerů Dockeru a Windows. |
| [Hodnocení na vyžádání](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Vyhodnoťte a optimalizujte dostupnost, zabezpečení a výkon místních, hybridních a cloudových technologických prostředí Microsoftu. |
| [Kontrola stavu SQL](insights/sql-assessment.md) | Vyhodnoťte rizika a stav prostředí serveru SQL Server.  |
| [Linková data](insights/wire-data.md) | Konsolidovaná data o síti a výkonu shromážděná z počítačů připojených k Systému Windows a počítačů s Linuxem s agentem Log Analytics. |


## <a name="third-party-integration"></a>Integrace třetích stran

| Řešení | Popis |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | It Service Management Connector (ITSMC) umožňuje připojit Azure a podporovaný produkt/službu ITSM (IT Service).  |


## <a name="resources-outside-of-azure"></a>Prostředky mimo Azure
Azure Monitor může shromažďovat data z prostředků mimo Azure pomocí metod uvedených v následující tabulce.

| Prostředek | Metoda |
|:---|:---|
| Aplikace | Sledujte webové aplikace mimo Azure pomocí Application Insights. Podívejte [se, co jsou přehledy aplikací?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Virtuální počítače | Pomocí agenta Log Analytics můžete shromažďovat data z hostovaného operačního systému virtuálních počítačů v jiných cloudových prostředích nebo v místním prostředí. Viz [Shromažďování dat protokolu s agentem Log Analytics](platform/log-analytics-agent.md). |
| Klient rozhraní REST API | Samostatná rozhraní API jsou k dispozici pro zápis dat do protokolů monitorování Azure a metriky z libovolného klienta rozhraní REST API. Najdete [v tématu Odesílání dat protokolu do Azure Monitor s rozhraním API pro shromažďování dat HTTP](platform/data-collector-api.md) pro protokoly a [odeslat vlastní metriky pro prostředek Azure do úložiště metrik Azure Monitor pomocí rozhraní REST API](platform/metrics-store-custom-rest-api.md) pro metriky. |



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [datové platformě Azure Monitor, která ukládá protokoly a metriky shromážděné přehledy a řešeními](platform/data-platform.md).
- Dokončete [kurz monitorování prostředku Azure](learn/tutorial-resource-logs.md).
- Dokončete [kurz psaní dotazu protokolu k analýze dat v protokolech monitorování Azure](learn/tutorial-resource-logs.md).
- Dokončete [kurz na vytvoření grafu metrik pro analýzu dat v metrikách Azure Monitor](learn/tutorial-metrics-explorer.md).

 
