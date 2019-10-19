---
title: Přehled protokolů prostředků Azure | Microsoft Docs
description: Pochopte podporované služby a schéma událostí pro protokoly prostředků Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2019
ms.openlocfilehash: a418e3d1a59379284422d1d24c1457ab61d84a4c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551752"
---
# <a name="azure-resource-logs-overview"></a>Přehled protokolů prostředků Azure
Protokoly prostředků Azure jsou [protokoly platforem](platform-logs-overview.md) vydávané prostředky Azure, které popisují jejich vnitřní provoz. Všechny protokoly prostředků sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události.

> [!NOTE]
> Protokoly prostředků se dříve nazývaly diagnostické protokoly.

## <a name="collecting-resource-logs"></a>Shromažďování protokolů prostředků
Protokoly prostředků se automaticky generují pomocí podporovaných prostředků Azure, ale neshromažďují se, pokud je nenakonfigurujete pomocí [nastavení diagnostiky](diagnostic-settings.md). Vytvořte nastavení diagnostiky pro každý prostředek Azure a předejte protokoly do následujících umístění:

| Cíl | Scénář |
|:---|:---|:---|
| [Pracovní prostor služby Log Analytics](resource-logs-collect-storage.md) | Analyzujte protokoly s dalšími daty monitorování a využijte Azure Monitorch funkcí, jako jsou dotazy protokolů a výstrahy protokolu. |
| [Úložiště Azure](archive-diagnostic-logs.md) | Archivujte protokoly pro auditování nebo zálohování. |
| [Centrum událostí](resource-logs-stream-event-hubs.md) | Streamujte protokoly do systémů protokolování a telemetrie třetích stran.  |

## <a name="compute-resources"></a>Výpočetní prostředky
Protokoly prostředků se liší od protokolů na úrovni operačního systému hosta ve výpočetních prostředcích Azure. Výpočetní prostředky vyžadují, aby agent mohl shromažďovat protokoly a metriky ze svého hostovaného operačního systému, včetně takových dat, protokolů událostí, syslogu a čítačů výkonu. Pomocí [diagnostického rozšíření](agents-overview.md#azure-diagnostic-extension) můžete směrovat data protokolu z virtuálních počítačů Azure a [agenta Log Analytics](agents-overview.md#log-analytics-agent) ke shromáždění protokolů a metrik z virtuálních počítačů v Azure, v jiných cloudech a místních do pracovního prostoru Log Analytics. Podrobnosti najdete v tématu [zdroje dat monitorování pro Azure monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Schéma protokolů prostředků
Každá služba Azure má vlastní schéma, když se do jednoho z cílů zapisují protokoly prostředků, ale všichni sdílejí schéma nejvyšší úrovně v následující tabulce. Odkazy na schéma pro každou službu najdete níže v části [schémata pro konkrétní služby](#service-specific-schemas) níže. 

| Name (Název) | Požadováno/volitelné | Popis |
|---|---|---|
| time | Požaduje se | Časové razítko (UTC) události. |
| resourceId | Požaduje se | ID prostředku prostředku, který vyvolal událost. Pro služby tenanta se jedná o formulář/tenants/tenant-ID/Providers/Provider-Name. |
| TenantId | Vyžadováno pro protokoly klientů | ID tenanta klienta služby Active Directory, se kterým je tato událost svázána. Tato vlastnost se používá jenom pro protokoly na úrovni tenanta, nezobrazuje se v protokolech na úrovni prostředků. |
| operationName | Požaduje se | Název operace reprezentované touto událostí. Pokud událost představuje operaci RBAC, jedná se o název operace RBAC (např. Microsoft. Storage/storageAccounts/blobServices/BLOBs/Read). Obvykle se modeluje ve formě operace Správce prostředků, i když nejsou skutečné dokumentované Správce prostředků operace (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | Volitelné | Verze rozhraní API přidružená k operaci, pokud se operace provedla pomocí rozhraní API (např. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Pokud neexistuje žádné rozhraní API, které by odpovídalo této operaci, verze představuje verzi této operace pro případ, že se vlastnosti přidružené k operaci v budoucnu mění. |
| category | Požaduje se | Kategorie protokolu události Kategorie je členitost, na které můžete povolit nebo zakázat protokoly na konkrétním prostředku. Vlastnosti, které se zobrazují v objektu BLOB vlastností události, jsou stejné v rámci konkrétní kategorie protokolu a typu prostředku. Typické kategorie protokolu jsou "Audit" "provozní" "provádění" a "Request". |
| resultType | Volitelné | Stav události Mezi obvyklé hodnoty patří počáteční, probíhající, úspěšná, neúspěšná, aktivní a vyřešená. |
| resultSignature | Volitelné | Stav sub události Pokud tato operace odpovídá REST API volání, jedná se o stavový kód HTTP odpovídajícího volání REST. |
| resultDescription | Volitelné | Statický textový popis této operace, např. "Získat soubor úložiště" |
| durationMs | Volitelné | Doba trvání operace v milisekundách. |
| callerIpAddress | Volitelné | IP adresa volajícího, pokud operace odpovídá volání rozhraní API, které pochází z entity s veřejně dostupnou IP adresou. |
| correlationId | Volitelné | Identifikátor GUID použitý k seskupení sady souvisejících událostí. Obvykle Pokud mají dvě události stejný OperationName, ale dva různé stavy (např. "Spuštěno" a "úspěch") sdílí stejné ID korelace. To může také představovat jiné vztahy mezi událostmi. |
| identita | Volitelné | Objekt BLOB JSON, který popisuje identitu uživatele nebo aplikace, která provedla operaci. Obvykle se jedná o tokeny autorizace a deklarace identity/JWT ze služby Active Directory. |
| Úroveň | Volitelné | Úroveň závažnosti události Musí se jednat o jednu z informativních, varovných, chybových nebo kritických. |
| location | Volitelné | Oblast zdroje, která vyvolala událost, např. "Východní USA" nebo "Francie – jih" |
| properties | Volitelné | Všechny rozšířené vlastnosti související s touto konkrétní kategorií událostí. Všechny vlastní a jedinečné vlastnosti musí být vloženy do tohoto "části B" schématu. |

## <a name="service-specific-schemas"></a>Schémata specifická pro službu
Schéma pro diagnostické protokoly prostředků se liší v závislosti na typu prostředku, který je definován vlastností `resourceId`) a `category` vlastností. V tomto seznamu jsou uvedeny všechny služby Azure, které podporují protokoly prostředků, s odkazy na schéma specifické pro službu a kategorii, kde je k dispozici.

| Služba | Schéma & docs |
| --- | --- |
| Azure Active Directory | [Přehled](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schéma protokolu auditu](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) a [schéma přihlášení](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Protokoly diagnostiky API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Brány Application Gateway |[Protokolování diagnostiky pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pro Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Protokolování diagnostiky Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Protokoly diagnostiky Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Protokoly diagnostiky Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Protokolování diagnostiky pro Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [Diagnostické protokoly Azure pro CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Protokolování Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorování datových továren pomocí Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Analýza Data Lake |[Přístup k protokolům diagnostiky pro Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Přístup k diagnostickým protokolům pro Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Diagnostické protokoly Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma není k dispozici. |
| Azure Firewall | Schéma není k dispozici. |
| Centrum IoT | [IoT Hub operace](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Protokolování v Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics pro Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Vlastní schéma sledování B2B Logic Apps](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Skupiny zabezpečení sítě |[Analýza protokolu pro skupiny zabezpečení sítě (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Spravovat Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Vyhrazená služba Power BI | [Protokolování diagnostiky pro Power BI Embedded v Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datový model pro Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Hledat |[Povolení a použití Analýza provozu vyhledávání](../../search/search-traffic-analytics.md) |
| Service Bus |[Protokoly diagnostiky Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Databáze SQL | [Protokolování diagnostiky Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Protokoly diagnostiky úlohy](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schéma Traffic Managerho protokolu](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Služby Virtual Networks | Schéma není k dispozici. |
| Brány virtuálních sítí | Schéma není k dispozici. |

## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o protokolech platformy Azure](platform-logs-overview.md) , které můžete použít k monitorování Azure.
