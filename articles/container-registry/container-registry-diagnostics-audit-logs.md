---
title: Shromažďovat & analyzovat protokoly prostředků
description: Zaznamenává a analyzuje události protokolu prostředků pro Azure Container Registry, jako je například ověřování, vkládání obrázků a vyžádanou image.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 952f239e94df6b3b21317985f56d3d7a999813fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571995"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky

Tento článek vysvětluje, jak shromažďovat data protokolu pro službu Azure Container Registry pomocí funkcí [Azure monitor](../azure-monitor/overview.md). Azure Monitor shromažďuje [protokoly prostředků](../azure-monitor/essentials/platform-logs-overview.md) (dříve nazývané *diagnostické protokoly*) pro události řízené uživateli v registru. Shromažďování a využívání těchto dat pro splnění potřeb, jako jsou:

* Auditování událostí ověřování registru za účelem zajištění zabezpečení a dodržování předpisů 

* Zadejte úplný záznam aktivity pro artefakty registru, jako jsou události Pull a pull, abyste mohli diagnostikovat provozní problémy s registrem. 

Shromažďování dat protokolu prostředků pomocí Azure Monitor může nabývat dalších nákladů. Viz [ceny Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Události úložiště

V současné době jsou protokolovány následující události na úrovni úložiště pro bitové kopie a jiné artefakty:

* **Replik**
* **Spotřeby**
* **Zrušit označení**
* **Odstranit** (včetně událostí odstranění úložiště)
* Vyprázdnit **značku** a **vyprázdnit manifest**

> [!NOTE]
> Události vyprázdnění se zaznamenávají jenom v případě, že jsou nakonfigurované [zásady uchovávání](container-registry-retention-policy.md) registru.

## <a name="registry-resource-logs"></a>Protokoly prostředků registru

Protokoly prostředků obsahují informace vydávané prostředky Azure, které popisují jejich interní operace. V případě služby Azure Container registry obsahují protokoly události na úrovni ověřování a úložiště uložené v následujících tabulkách. 

* **ContainerRegistryLoginEvents**  – události a stav ověřování registru, včetně příchozí identity a IP adresy
* **ContainerRegistryRepositoryEvents** – operace jako push a pull pro image a další artefakty v úložištích registru
* **AzureMetrics**  -  [Metriky registru kontejnerů](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries) , jako je agregovaná nabízená oznámení a počty vyžádaného čtení.

V případě operací zahrnuje data protokolu:
  * Stav úspěch nebo neúspěch
  * Počáteční a koncové časové razítko

Kromě protokolů prostředků poskytuje Azure [Protokol aktivit](../azure-monitor/essentials/platform-logs-overview.md), což je jeden záznam událostí správy Azure na úrovni předplatného, jako je například vytvoření nebo odstranění registru kontejneru.

## <a name="enable-collection-of-resource-logs"></a>Povolit shromažďování protokolů prostředků

Kolekce protokolů prostředků pro registr kontejneru není ve výchozím nastavení povolená. Explicitně povolte nastavení diagnostiky pro každý registr, který chcete monitorovat. Možnosti pro povolení nastavení diagnostiky najdete [v tématu Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../azure-monitor/essentials/diagnostic-settings.md).

Pokud například chcete zobrazit protokoly a metriky pro registr kontejnerů téměř v reálném čase v Azure Monitor, Shromážděte protokoly prostředků v pracovním prostoru Log Analytics. Povolení tohoto nastavení diagnostiky pomocí Azure Portal:

1. Pokud ještě nemáte pracovní prostor, vytvořte pracovní prostor pomocí [Azure Portal](../azure-monitor/logs/quick-create-workspace.md). Chcete-li minimalizovat latenci v shromažďování dat, ujistěte se, že je pracovní prostor ve **stejné oblasti** jako registr kontejneru.
1. Na portálu vyberte registr a vyberte **monitorování > nastavení diagnostiky > přidat nastavení diagnostiky**.
1. Zadejte název nastavení a vyberte **Odeslat do Log Analytics**.
1. Vyberte pracovní prostor pro diagnostické protokoly registru.
1. Vyberte data protokolu, která chcete shromáždit, a klikněte na **Uložit**.

Následující obrázek ukazuje vytvoření nastavení diagnostiky pro registr pomocí portálu.

![Povolení nastavení diagnostiky](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Shromážděte jenom data, která potřebujete, vyvážením nákladů a vašich potřeb pro monitorování. Pokud například potřebujete auditovat pouze události ověřování, vyberte pouze protokol **ContainerRegistryLoginEvents** . 

## <a name="view-data-in-azure-monitor"></a>Zobrazit data v Azure Monitor

Po povolení shromažďování diagnostických protokolů v Log Analytics může trvat několik minut, než se data zobrazí v Azure Monitor. Chcete-li zobrazit data na portálu, vyberte registr a vyberte **sledování > protokoly**. Vyberte jednu z tabulek, která obsahuje data pro registr. 

Spusťte dotazy pro zobrazení dat. Je k dispozici několik ukázkových dotazů nebo vlastní spuštění. Například následující dotaz načte nejaktuálnější 24 hodin dat z tabulky **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Následující obrázek ukazuje vzorový výstup:

![Dotazy na data protokolů](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Kurz týkající se použití Log Analytics v Azure Portal najdete v tématu [Začínáme s Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)nebo vyzkoušejte [ukázkové prostředí](https://portal.loganalytics.io/demo)Log Analytics. 

Další informace o dotazech protokolu najdete v tématu [Přehled dotazů protokolu v Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="query-examples"></a>Příklady dotazů

### <a name="error-events-from-the-last-hour"></a>Události chyb za poslední hodinu

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 nejnovějších událostí registru

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Identita uživatele nebo objektu, který odstranil úložiště

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Identita uživatele nebo objektu, který odstranil značku

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Selhání operací na úrovni úložiště

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Selhání ověřování registru

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>Další cíle protokolu

Kromě odesílání protokolů pro Log Analytics, nebo jako alternativu můžete běžným scénářem vybrat Azure Storage účet jako cíl protokolu. Pokud chcete archivovat protokoly v Azure Storage, vytvořte si účet úložiště ještě předtím, než povolíte archivaci prostřednictvím nastavení diagnostiky.

Události diagnostického protokolu můžete také streamovat do [centra událostí Azure](../event-hubs/event-hubs-about.md). Event Hubs může ingestovat miliony událostí za sekundu, které pak můžete transformovat a ukládat pomocí libovolného zprostředkovatele analýz v reálném čase. 

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o použití [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) a vytváření [dotazů protokolu](../azure-monitor/logs/get-started-queries.md).
* Další informace o protokolech platforem, které jsou k dispozici v různých vrstvách Azure, najdete v tématu [Přehled protokolů platformy Azure](../azure-monitor/essentials/platform-logs-overview.md) .