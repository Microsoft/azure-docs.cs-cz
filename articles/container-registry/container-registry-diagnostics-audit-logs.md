---
title: Shromažďování & analýzu protokolů zdrojů
description: Zaznamenejte a analyzujte události protokolu prostředků pro registr kontejnerů Azure, jako je ověřování, nabízená hlášení bitové kopie a vyžádat image.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409639"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Protokoly registru kontejnerů Azure pro diagnostické vyhodnocení a auditování

Tento článek vysvětluje, jak shromažďovat data protokolu pro registr kontejnerů Azure pomocí funkcí [Azure Monitor](../azure-monitor/overview.md). Azure Monitor shromažďuje [protokoly prostředků](../azure-monitor/platform/platform-logs-overview.md) (dříve nazývané *diagnostické protokoly)* pro události řízené uživateli ve vašem registru. Shromažďujte a konzumujte tato data tak, aby vyhovovala potřebám, jako jsou:

* Auditovat události ověřování registru pro zajištění zabezpečení a dodržování předpisů 

* Poskytněte úplnou stopu aktivity na artefaktech registru, jako jsou události vyžádat a vyžádat, abyste mohli diagnostikovat provozní problémy s registrem 

Shromažďování dat protokolu prostředků pomocí Azure Monitoru může vzniknout další náklady. Viz [Ceny Azure Monitoru](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Události úložiště

V současné době jsou zaznamenány následující události na úrovni úložiště pro obrázky a další artefakty:

* **Nabízené události**
* **Události vyžádat**
* **Zrušení označení událostí**
* **Odstranit události** (včetně událostí odstranění úložiště)

Události na úrovni úložiště, které nejsou aktuálně protokolovány: Vyčistit události.

## <a name="registry-resource-logs"></a>Protokoly prostředků registru

Protokoly prostředků obsahují informace vyzařované prostředky Azure, které popisují jejich interní operace. Pro registr kontejneru Azure protokoly obsahují ověřování a události na úrovni úložiště uložené v následujících tabulkách. 

* **ContainerRegistryLoginEvents** - události a stav ověřování registru, včetně příchozí identity a IP adresy
* **ContainerRegistryRepositoryEvents** - operace, jako je nabízená a vyžádat pro bitové kopie a další artefakty v úložištích registru
* **Metriky** - registru kontejneru AzureMetrics, jako jsou agregované počty nabízených[vyžádat](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) a vyžádat.

Pro operace data protokolu zahrnuje:
  * Stav úspěchu nebo neúspěchu
  * Časová razítka začátku a konce

Kromě protokolů prostředků azure poskytuje [protokol aktivit](../azure-monitor/platform/platform-logs-overview.md), jeden záznam na úrovni předplatného událostí správy Azure, jako je vytvoření nebo odstranění registru kontejneru.

## <a name="enable-collection-of-resource-logs"></a>Povolit shromažďování protokolů prostředků

Kolekce protokolů prostředků pro registr kontejneru není ve výchozím nastavení povolena. Explicitně povolte diagnostická nastavení pro každý registr, který chcete sledovat. Možnosti povolení nastavení diagnostiky najdete [v tématu Vytvoření diagnostického nastavení pro shromažďování protokolů platformy a metrik v Azure](../azure-monitor/platform/diagnostic-settings.md).

Chcete-li například zobrazit protokoly a metriky pro registr kontejnerů v téměř reálném čase v Azure Monitoru, shromažďovat protokoly prostředků v pracovním prostoru Log Analytics. Povolení tohoto diagnostického nastavení pomocí portálu Azure:

1. Pokud ještě nemáte pracovní prostor, vytvořte pracovní prostor pomocí [portálu Azure](../azure-monitor/learn/quick-create-workspace.md). Chcete-li minimalizovat latenci při shromažďování dat, ujistěte se, že pracovní prostor je **ve stejné oblasti** jako registr kontejneru.
1. Na portálu vyberte registr a vyberte **možnost Sledování > nastavení diagnostiky > Přidat diagnostické nastavení**.
1. Zadejte název nastavení a vyberte **Odeslat do analýzy protokolů**.
1. Vyberte pracovní prostor pro diagnostické protokoly registru.
1. Vyberte data protokolu, která chcete shromáždit, a klepněte na tlačítko **Uložit**.

Následující obrázek znázorňuje vytvoření diagnostického nastavení pro registr pomocí portálu.

![Povolení nastavení diagnostiky](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Shromažďujte pouze data, která potřebujete, vyvažujte náklady a potřeby monitorování. Například pokud potřebujete pouze auditovat události ověřování, vyberte pouze **containerRegistryLoginEvents** protokolu. 

## <a name="view-data-in-azure-monitor"></a>Zobrazení dat ve službě Azure Monitor

Po povolení shromažďování diagnostických protokolů v Log Analytics může trvat několik minut, než se data zobrazí v Azure Monitoru. Chcete-li zobrazit data na portálu, vyberte registr a vyberte **možnost Sledování > protokolů**. Vyberte jednu z tabulek, která obsahuje data pro registr. 

Spusťte dotazy pro zobrazení dat. Několik ukázkové dotazy jsou k dispozici nebo spustit vlastní. Například následující dotaz načte posledních 24 hodin dat z tabulky **ContainerRegistryRepositoryEvents:**

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Následující obrázek znázorňuje ukázkový výstup:

![Dotazy na data protokolů](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Kurz o používání Log Analytics na webu Azure portal najdete v [tématu Začínáme s Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)nebo zkuste [ukázkové prostředí](https://portal.loganalytics.io/demo)Analýzy protokolů . 

Další informace o dotazech protokolu najdete [v tématu Přehled dotazů protokolu v Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Další příklady dotazů

#### <a name="100-most-recent-registry-events"></a>100 nejnovějších událostí registru

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Další cíle protokolu

Kromě odesílání protokolů do Log Analytics nebo jako alternativu je běžným scénářem výběr účtu Úložiště Azure jako cíle protokolu. Chcete-li archivovat protokoly ve službě Azure Storage, vytvořte účet úložiště před povolením archivace prostřednictvím nastavení diagnostiky.

Události diagnostického protokolu můžete také streamovat do [centra událostí Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Centra událostí mohou ingestovat miliony událostí za sekundu, které pak můžete transformovat a ukládat pomocí libovolného poskytovatele analýzy v reálném čase. 

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o používání [log analytics](../azure-monitor/log-query/get-started-portal.md) a vytváření [dotazů protokolu](../azure-monitor/log-query/get-started-queries.md).
* Přehled [protokolů platformy Azure,](../azure-monitor/platform/platform-logs-overview.md) kde najdete informace o protokolech platformy, které jsou dostupné v různých vrstvách Azure.

