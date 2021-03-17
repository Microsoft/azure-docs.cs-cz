---
title: Protokoly prostředků Azure podporují služby a schémata.
description: Pochopte podporované služby a schéma událostí pro protokoly prostředků Azure.
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 661cba2d78dbb176bb04e6831cf8e045a285535e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033091"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Společné schéma pro protokoly prostředků Azure a schématu pro konkrétní služby

> [!NOTE]
> Protokoly prostředků se dříve nazývaly diagnostické protokoly. Název se změnil v říjnu 2019, protože typy protokolů shromážděných Azure Monitor se posunuly tak, aby zahrnovaly víc než jenom prostředek Azure. Seznam kategorií protokolu prostředků, které jste mohli shromažďovat, jste si také využívali v tomto článku. Byly přesunuty do [kategorií protokolu prostředků](resource-logs-categories.md). 

[Protokoly prostředků Azure monitor](../essentials/platform-logs-overview.md) jsou protokoly emitované službami Azure, které popisují fungování těchto služeb nebo prostředků. Všechny protokoly prostředků dostupné prostřednictvím Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události.

Kombinace typu prostředku (k dispozici ve `resourceId` Vlastnosti) a `category` jednoznačné určení schématu. Tento článek popisuje schéma nejvyšší úrovně pro protokoly prostředků a odkazy na schémat pro každou službu.


## <a name="top-level-common-schema"></a>Společné schéma nejvyšší úrovně

| Název | Požadováno/volitelné | Popis |
|---|---|---|
| time | Vyžadováno | Časové razítko (UTC) události. |
| resourceId | Vyžadováno | ID prostředku prostředku, který vyvolal událost. Pro služby tenanta se jedná o formulář/tenants/tenant-ID/Providers/Provider-Name. |
| tenantId | Vyžadováno pro protokoly klientů | ID tenanta klienta služby Active Directory, se kterým je tato událost svázána. Tato vlastnost se používá jenom pro protokoly na úrovni tenanta, nezobrazuje se v protokolech na úrovni prostředků. |
| operationName | Vyžadováno | Název operace reprezentované touto událostí. Pokud událost představuje operaci Azure RBAC, jedná se o název operace Azure RBAC (například Microsoft. Storage/storageAccounts/blobServices/BLOBs/Read). Obvykle se modeluje ve formě operace Správce prostředků, i když nejsou skutečné dokumentované Správce prostředků operace ( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ). |
| operationVersion | Volitelné | Verze rozhraní API přidružená k operaci, pokud se operace provedla pomocí rozhraní API (například `http://myservice.windowsazure.net/object?api-version=2016-06-01` ). Pokud neexistuje žádné rozhraní API, které by odpovídalo této operaci, verze představuje verzi této operace pro případ, že se vlastnosti přidružené k operaci v budoucnu mění. |
| category | Vyžadováno | Kategorie protokolu události Kategorie je členitost, na které můžete povolit nebo zakázat protokoly na konkrétním prostředku. Vlastnosti, které se zobrazují v objektu BLOB vlastností události, jsou stejné v rámci konkrétní kategorie protokolu a typu prostředku. Typické kategorie protokolu jsou "Audit" "provozní" "provádění" a "Request". |
| resultType | Volitelné | Stav události Mezi obvyklé hodnoty patří počáteční, probíhající, úspěšná, neúspěšná, aktivní a vyřešená. |
| resultSignature | Volitelné | Stav sub události Pokud tato operace odpovídá volání REST API, toto pole je stavový kód HTTP odpovídajícího volání REST. |
| resultDescription | Volitelné | Popis statického textu této operace, například "získat soubor úložiště". |
| durationMs | Volitelné | Doba trvání operace v milisekundách. |
| callerIpAddress | Volitelné | IP adresa volajícího, pokud operace odpovídá volání rozhraní API, které pochází z entity s veřejně dostupnou IP adresou. |
| correlationId | Volitelné | Identifikátor GUID použitý k seskupení sady souvisejících událostí. Pokud má obvykle dvě události stejný OperationName, ale dva různé stavy (například spuštěno a úspěšné), sdílejí stejné ID korelace. To může také představovat jiné vztahy mezi událostmi. |
| identity | Volitelné | Objekt BLOB JSON, který popisuje identitu uživatele nebo aplikace, která provedla operaci. Obvykle toto pole zahrnuje autorizaci a tokeny deklarací identity/JWT ze služby Active Directory. |
| Level | Volitelné | Úroveň závažnosti události Musí se jednat o jednu z informativních, varovných, chybových nebo kritických. |
| location | Volitelné | Oblast zdroje, která vyvolala událost, například "Východní USA" nebo "Francie – jih" |
| properties | Volitelné | Všechny rozšířené vlastnosti související s touto konkrétní kategorií událostí. Všechny vlastní a jedinečné vlastnosti musí být vloženy do tohoto "části B" schématu. |

## <a name="service-specific-schemas"></a>Schémata specifická pro službu

Schéma pro protokoly prostředků se liší v závislosti na kategoriích prostředků a protokolů. Tento seznam obsahuje služby, které zpřístupňují dostupné protokoly prostředků a odkazy na schéma specifické pro službu a kategorii, kde je k dispozici. Tento seznam se mění ve chvíli, kdy se přidávají nové služby, takže Pokud nevidíte, co potřebujete, použijte vyhledávací web ke zjištění další dokumentace. Nebojte se otevřít problém GitHubu v tomto článku, abychom ho mohli aktualizovat.

| Služba | Schéma & docs |
| --- | --- |
| Azure Active Directory | [Přehled](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schéma protokolu auditu](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) a [schéma přihlášení](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Protokolování diagnostiky Azure Analysis Servicesho nastavení](../../analysis-services/analysis-services-logging.md) |
| API Management | [Protokoly prostředků API Management](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| App Service | [Protokoly App Service](../../app-service/troubleshoot-diagnostic-logs.md)
| Brány Application Gateway |[Protokolování pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pro Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Protokolování Azure Batch](../../batch/batch-diagnostics.md) |
| Cognitive Services | [Protokolování pro Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Protokolování pro Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Protokoly Azure pro CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Protokolování Azure Cosmos DB](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Monitorování datových továren pomocí Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Přístup k protokolům pro Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Přístup k protokolům pro Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Průzkumník dat Azure | [Protokoly Průzkumník dat Azure](/azure/data-explorer/using-diagnostic-logs) |
| Azure Database for MySQL | [Protokoly diagnostiky Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Protokoly Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Protokolování diagnostiky v Azure Databricks](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure Digital Twins | [Nastavení diagnostiky digitálních vláken Azure](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Event Hubs |[Protokoly Event Hubs Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schéma není k dispozici. |
| Azure Firewall | Schéma není k dispozici. |
| Front Door | [Protokolování pro přední dveře](../../frontdoor/front-door-diagnostics.md) |
| IoT Hub | [IoT Hub operace](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Key Vault |[Protokolování Azure Key Vault](../../key-vault/general/logging.md) |
| Kubernetes Service |[Protokolování Azure Kubernetes](../../aks/view-control-plane-logs.md#log-event-schema) |
| Load Balancer |[Log Analytics pro Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Vlastní schéma sledování Logic Apps B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Skupiny zabezpečení sítě) |[Analýza protokolu pro skupiny zabezpečení sítě (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Protokolování pro Azure DDoS Protection Standard](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Vyhrazená služba Power BI | [Protokolování Power BI Embedded v Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datový model pro Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Povolení a použití Analýza provozu vyhledávání](../../search/search-traffic-analytics.md) |
| Service Bus |[Protokoly Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Databáze SQL | [Protokolování Azure SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Protokoly úloh](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schéma Traffic Managerho protokolu](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuální sítě | Schéma není k dispozici. |
| Brány virtuálních sítí | Schéma není k dispozici. |



## <a name="next-steps"></a>Další kroky

* [Podívejte se na kategorie protokolu prostředků, které můžete shromažďovat.](resource-logs-categories.md)
* [Další informace o protokolech prostředků](../essentials/platform-logs-overview.md)
* [**Event Hubs** protokolů prostředku prostředku streamování](./resource-logs.md#send-to-azure-event-hubs)
* [Změna nastavení diagnostiky protokolu prostředků pomocí Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analýza protokolů z Azure Storage pomocí Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)