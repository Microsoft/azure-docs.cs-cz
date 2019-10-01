---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: 93a2554b5d3cc24e1b5fc1e3d0f18ed1bfe0579c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71692027"
---
Díky výraznému růstu virtuálních počítačů hostovaných v Azure je důležité identifikovat problémy s výkonem a stavem, které mají vliv na aplikace a služby infrastruktury, které podporují. Základní monitorování se ve výchozím nastavení doručuje s Azure podle typu metriky využití procesoru, využití disku, využití paměti a síťového provozu shromážděného hypervisorem hostitele. Další data metrik a protokolů je možné shromažďovat pomocí [rozšíření](../articles/virtual-machines/windows/extensions-features.md) pro konfiguraci diagnostiky virtuálních počítačů z hostovaného operačního systému.

Aby bylo možné detekovat a pomáhat diagnostikovat problémy s výkonem a stavem v hostovaném operačním systému, jsou komponenty webové aplikace založené na technologii .NET nebo Java spuštěné ve virtuálním počítači, Azure Monitor poskytují centralizované monitorování s využitím komplexních funkcí, jako je Azure Monitor pro virtuální počítače a Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnostika a metriky 

Můžete nastavit a monitorovat shromažďování [diagnostických dat](https://docs.microsoft.com/cli/azure/vm/diagnostics) pomocí [metrik](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) v Azure Portal, rozhraní příkazového řádku Azure CLI, Azure PowerShell a programovacích rozhraní API (Application Programming Interface). Můžete například provést následující věci:

- **Sledujte základní metriky pro virtuální počítač.** Na obrazovce s přehledem Azure Portal základní metriky zahrnují využití CPU, využití sítě, celkový počet bajtů disku a diskových operací za sekundu.

- **Povolte shromažďování diagnostiky spouštění a zobrazte ji pomocí Azure Portal.** Když narazíte na vlastní image do Azure nebo dokonce spustíte jednu z imagí platformy, může to mít spoustu důvodů, proč se virtuální počítač dostane do nespouštěcího stavu. Diagnostiku spouštění můžete snadno povolit při vytváření virtuálního počítače kliknutím na možnost **povoleno** pro diagnostiku spouštění v části monitorování obrazovky nastavení.

    Při spuštění virtuálních počítačů zaznamená agent diagnostiky spouštění výstup spouštění a uloží ho do služby Azure Storage. Tato data můžete použít k odstraňování problémů při spouštění virtuálních počítačů. Diagnostika spouštění není automaticky povolena při vytváření virtuálního počítače z nástrojů příkazového řádku. Před povolením diagnostiky spouštění je třeba vytvořit účet úložiště pro ukládání protokolů spouštění. Pokud povolíte diagnostiku spouštění v Azure Portal, automaticky se vytvoří účet úložiště.

    Pokud jste nepovolili diagnostiku spouštění při vytváření virtuálního počítače, můžete ho kdykoli povolit později pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)nebo [šablony Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Povolit shromažďování diagnostických dat operačního systému hosta** Když vytváříte virtuální počítač, budete mít příležitost na obrazovce nastavení a povolíte diagnostiku hostovaného operačního systému. Když povolíte shromažďování diagnostických dat, do virtuálního počítače se přidá [rozšíření IaaSDiagnostics pro Linux](../articles/virtual-machines/linux/diagnostic-extension.md) nebo [rozšíření IaaSDiagnostics pro Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) , které umožňuje shromažďovat další data o disku, procesoru a paměti.

    Pomocí shromážděných diagnostických dat můžete pro své virtuální počítače nakonfigurovat automatické škálování. Můžete také nakonfigurovat [protokoly Azure monitor](../articles/azure-monitor/platform/data-platform-logs.md) pro uložení dat a nastavit výstrahy, abyste věděli, že výkon není správný.

## <a name="alerts"></a>Výstrahy

Můžete vytvářet [výstrahy](../articles/azure-monitor/platform/alerts-overview.md) na základě konkrétních metrik výkonu. Příklady problémů, které se dají upozornit, když průměrné využití procesoru překročí určitou prahovou hodnotu nebo je dostupné volné místo na disku pod určitou velikostí. Výstrahy můžete nakonfigurovat v [Azure Portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), pomocí [šablon Azure Resource Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md)nebo pomocí [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) poskytuje individuální pokyny a podporu v případě, že se vás týkají problémy se službami Azure, a pomůže vám připravit se na nadcházející plánovanou údržbu. Azure Service Health vás upozorní a vaše týmy s využitím cíleného a flexibilního oznámení.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md) pomáhá diagnostikovat a získat podporu v případě, že problém Azure ovlivňuje vaše prostředky. Informuje o aktuálním a minulém stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc s problémy se službami Azure.

## <a name="azure-activity-log"></a>Protokol aktivit Azure

[Protokol aktivit Azure](../articles/azure-monitor/platform/activity-logs-overview.md) je protokol předplatného, který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Protokol zahrnuje rozsah dat, od Azure Resource Manager provozních dat až po aktualizace Service Healthch událostí. Kliknutím na protokol aktivit v Azure Portal zobrazíte protokol pro svůj virtuální počítač.

Mezi akce, které můžete s protokolem aktivit dělat, patří:

- Vytvoří [výstrahu pro událost protokolu aktivit](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Streamujte ho do centra událostí](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) pro ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu, jako je Power BI.
- Analyzujte ho v Power BI pomocí [balíčku obsahu Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Uložte ho do účtu úložiště](../articles/azure-monitor/platform/archive-activity-log.md) pro archivaci nebo ruční kontrolu. Dobu uchování (ve dnech) můžete určit pomocí profilu protokolu.

Data protokolu aktivit můžete také přistupovat pomocí [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/monitor)nebo [monitorovat rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/).

[Protokoly prostředků Azure](../articles/azure-monitor/platform/resource-logs-overview.md) jsou protokoly EMITOVANÉ vaším virtuálním počítačem, které poskytují bohatou a častou data o jeho provozu. Protokoly prostředků se liší od protokolu aktivit tím, že poskytují přehled o operacích, které byly provedeny v rámci virtuálního počítače.

Mezi postupy, které můžete s diagnostickými protokoly provádět, patří:

- [Uložte je do účtu úložiště](../articles/azure-monitor/platform/archive-diagnostic-logs.md) pro auditování nebo ruční kontrolu. Dobu uchování (ve dnech) můžete určit pomocí nastavení diagnostiky prostředků.
- [Streamujte je Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) k ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu, jako je například Power BI.
- Analyzujte je pomocí [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Pokročilé sledování

Pro přehlednost aplikace nebo služby podporované virtuálním počítačem Azure a sadou škálování virtuálních počítačů, identifikace problémů s hostovaným operačním systémem nebo úlohami spuštěnými ve virtuálním počítači, abyste zjistili, jestli má dopad na dostupnost nebo výkon aplikace, nebo je při potížích s aplikací povolte [Azure monitor pro virtuální počítače](../articles/azure-monitor/insights/vminsights-overview.md) i [Application Insights](../articles/azure-monitor/app/app-insights-overview.md).

Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure ve velkém měřítku tím, že analyzuje výkon a stav virtuálních počítačů s Windows a Linux, včetně různých procesů a propojených závislostí s dalšími prostředky a externími procesy IT. zjistí. Zahrnuje několik grafů s výkonem trendů, které vám pomůžou při zkoumání problémů a vyhodnocování kapacity vašich virtuálních počítačů. Mapa závislostí zobrazuje monitorované a nemonitorované počítače, neúspěšná a aktivní síťová připojení mezi procesy a těmito počítači a zobrazuje vývojové grafy se standardními metrikami síťového připojení. V kombinaci s Application Insights můžete monitorovat aplikaci a zachytit telemetrii, jako jsou požadavky HTTP, výjimky atd., takže můžete sladit problémy mezi virtuálními počítači a vaší aplikací. Nakonfigurujte [výstrahy Azure monitor](../articles/azure-monitor/platform/alerts-overview.md) , aby vás upozornily na důležité podmínky zjištěné z dat monitorování shromažďovaných pomocí Azure monitor pro virtuální počítače.

## <a name="next-steps"></a>Další kroky

- Projděte si postup v části [monitorování virtuálního počítače s Windows pomocí Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) nebo [monitorování virtuálního počítače se systémem Linux pomocí Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Přečtěte si další informace o osvědčených postupech pro [monitorování a diagnostiku](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
