---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75751576"
---
S výrazným růstem virtuálních počítačů hostovaných v Azure je důležité identifikovat problémy s výkonem a stavem, které ovlivňují aplikace a služby infrastruktury, které podporují. Základní monitorování se ve výchozím nastavení provádí s Azure podle typů metrik využití procesoru, využití disku, využití paměti a síťového provozu shromážděných hypervisorem hostitele. Další data metrik a protokolu lze shromažďovat pomocí [rozšíření](../articles/virtual-machines/windows/extensions-features.md) pro konfiguraci diagnostiky na virtuálních počítačích z hostovaného operačního systému.

Azure Monitor poskytuje centralizované monitorování s komplexními funkcemi, jako je Azure Monitor pro virtuální počítače, a pomáhá vám diagnostikovat problémy s výkonem a stavem s komponentami webové aplikace založenými na rozhraní .NET nebo Java, které běží uvnitř virtuálního počítače. a Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnostika a metriky 

Shromažďování [diagnostických dat](https://docs.microsoft.com/cli/azure/vm/diagnostics) můžete nastavit a monitorovat pomocí [metrik](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) na webu Azure Portal, Azure CLI, Azure PowerShell a programovacích rozhraních (API). Můžete například provést následující věci:

- **Dodržujte základní metriky pro virtuální hod.** Na obrazovce Přehled na webu Azure Portal jsou zobrazeny základní metriky, včetně využití procesoru, využití sítě, celkového počtu bajtů na disku a operací na disku za sekundu.

- **Povolte kolekci diagnostiky spouštění a zobrazte ji pomocí portálu Azure.** Když přinášíte vlastní image do Azure nebo dokonce zavedete jednu z bitových kopií platformy, může existovat mnoho důvodů, proč se virtuální počítač dostane do stavu bez spuštění. Diagnostiku spouštění můžete snadno povolit při vytváření virtuálního počítače kliknutím na **Povoleno** pro diagnostiku spouštění v části Monitorování na obrazovce Nastavení.

    Při zavádění virtuálních počítačů zachytí spouštěcí agent spouštěcí výstup a uloží ho do úložiště Azure. Tato data můžete použít k odstraňování problémů při spouštění virtuálních počítačů. Diagnostika spouštění není automaticky povolena při vytváření virtuálního virtuálního serveru z nástrojů příkazového řádku. Před povolením diagnostiky spouštění je třeba vytvořit účet úložiště pro ukládání protokolů spouštění. Pokud na webu Azure Portal povolíte diagnostiku spouštění, automaticky se za vás vytvoří účet úložiště.

    Pokud jste při vytvoření virtuálního počítače nepovolili diagnostiku spouštění, můžete ji povolit později pomocí [azure cli](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)nebo [šablony Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Povolte shromažďování diagnostických dat hostovaného operačního systému.** Když vytvoříte virtuální počítač, máte možnost na obrazovce nastavení povolit diagnostiku hostovaného operačního systému. Pokud povolíte shromažďování diagnostických dat, [rozšíření IaaSDiagnostics pro Linux](../articles/virtual-machines/linux/diagnostic-extension.md) nebo rozšíření [IaaSDiagnostics pro Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) se přidá do virtuálního počítače, což umožňuje shromažďovat další data disku, procesoru a paměti.

    Pomocí shromážděných diagnostických dat můžete nakonfigurovat automatické škálování pro virtuální počítače. Můžete také nakonfigurovat [protokoly monitorování Azure](../articles/azure-monitor/platform/data-platform-logs.md) pro ukládání dat a nastavit výstrahy, abyste věděli, kdy výkon není správný.

## <a name="alerts"></a>Výstrahy

Můžete vytvářet [výstrahy](../articles/azure-monitor/platform/alerts-overview.md) na základě konkrétních metrik výkonu. Příklady problémů, na které můžete být upozorněni, zahrnují, když průměrné využití procesoru překročí určitou prahovou hodnotu nebo dostupné volné místo na disku klesne pod určitou hodnotu. Výstrahy se dá nakonfigurovat na [webu Azure Portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), pomocí šablon Azure Resource [Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md)nebo [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) poskytuje přizpůsobené pokyny a podporu, když se vás týkají problémy ve službách Azure, a pomáhá vám připravit se na nadcházející plánovanou údržbu. Azure Service Health upozorní vás a vaše týmy pomocí cílených a flexibilních oznámení.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource health](../articles/service-health/resource-health-overview.md) vám pomůže diagnostikovat a získat podporu, když problém Azure ovlivní vaše prostředky. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.

## <a name="azure-activity-log"></a>Protokol aktivit Azure

[Protokol aktivit Azure](../articles/azure-monitor/platform/platform-logs-overview.md) je protokol předplatného, který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Protokol obsahuje řadu dat, od provozních dat Azure Resource Manageru až po aktualizace událostí stavu služby. Kliknutím na protokol aktivit na webu Azure Portal zobrazíte protokol pro váš virtuální počítač.

Některé z věcí, které můžete dělat s protokolem aktivit patří:

- Vytvořte [výstrahu na událost protokolu aktivit](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Streamujte do Centra událostí](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) pro příjem službou třetí strany nebo vlastním analytickým řešením, jako je Power BI.
- Analyzujte ji v Power BI pomocí [balíčku obsahu Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Uložte jej do účtu úložiště](../articles/azure-monitor/platform/archive-activity-log.md) pro archivní nebo ruční kontrolu. Můžete určit čas uchování (ve dnech) pomocí profilu protokolu.

K datům protokolu aktivit můžete přistupovat také pomocí [Azure PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)nebo [Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).

[Azure Resource Logs](../articles/azure-monitor/platform/platform-logs-overview.md) jsou protokoly vyzařované virtuálním počítačem, které poskytují bohatá, častá data o jeho provozu. Protokoly prostředků se liší od protokolu aktivit tím, že poskytuje přehled o operacích, které byly provedeny v rámci virtuálního trhu.

Některé z věcí, které můžete dělat s protokoly diagnostiky patří:

- [Uložte je do účtu úložiště](../articles/azure-monitor/platform/archive-diagnostic-logs.md) pro auditování nebo ruční kontrolu. Čas uchování (ve dnech) můžete určit pomocí nastavení diagnostiky prostředků.
- [Streamujte je do Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) pro příjem pomocí služby třetí strany nebo vlastního analytického řešení, jako je Power BI.
- Analyzujte je pomocí [analýzy protokolů](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Pokročilé sledování

Pro viditelnost aplikace nebo služby podporované sady škálování virtuálních zařízení Azure a virtuálních strojů, identifikaci problémů s hostovaným os nebo zatížení spuštěné ve virtuálním počítači, abyste pochopili, zda má vliv na dostupnost nebo výkon aplikace, nebo je problém s aplikací, povolte [Azure Monitor pro virtuální počítače](../articles/azure-monitor/insights/vminsights-overview.md) a Application [Insights](../articles/azure-monitor/app/app-insights-overview.md).

Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure (VM) ve velkém měřítku analýzou výkonu a stavu vašich virtuálních počítačů s Windows a Linuxem, včetně různých procesů a vzájemně propojených závislostí na jiných prostředcích a externích procesech, které Zjistí. Obsahuje několik grafů výkonu trendů, které vám pomohou při vyšetřování problémů a posouzení kapacity vašich virtuálních počítačích. Mapa závislostí zobrazuje monitorované a nemonitorované počítače, selhání a aktivní síťová připojení mezi procesy a těmito počítači a zobrazuje grafy trendů se standardními metrikami síťového připojení. V kombinaci s Application Insights můžete sledovat aplikaci a zaznamenávat telemetrii, jako jsou požadavky HTTP, výjimky atd., takže můžete korelovat problémy mezi virtuálními počítači a vaší aplikací. Nakonfigurujte [výstrahy Azure Monitoru](../articles/azure-monitor/platform/alerts-overview.md) tak, aby vás upozorňovaly na důležité podmínky zjištěné z dat monitorování shromážděných službou Azure Monitor pro virtuální počítače.

## <a name="next-steps"></a>Další kroky

- Projděte si kroky v [části Monitorování virtuálního počítače s Windows pomocí Azure PowerShellu](../articles/virtual-machines/windows/tutorial-monitoring.md) nebo [monitorování virtuálního počítače s Linuxem pomocí azure cli](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Přečtěte si další informace o doporučených postupech [týkajících se monitorování a diagnostiky](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
