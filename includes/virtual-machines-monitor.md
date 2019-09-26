---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: 11c9b2ea3ea054415f25f864651df28288aa0025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266836"
---
Můžete využít spoustu příležitostí k monitorování virtuálních počítačů, a to shromažďováním, zobrazením a analýzou dat diagnostiky a protokolů. K jednoduchému [monitorování](../articles/azure-monitor/overview.md) virtuálního počítače můžete použít obrazovku Přehled pro virtuální počítač v Azure Portal. Pomocí [rozšíření](../articles/virtual-machines/windows/extensions-features.md) můžete nakonfigurovat diagnostiku virtuálních počítačů, abyste mohli shromažďovat další data metrik. Můžete také použít pokročilejší možnosti monitorování, například [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) a [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostika a metriky 

Můžete nastavit a monitorovat shromažďování [diagnostických dat](https://docs.microsoft.com/cli/azure/vm/diagnostics) pomocí [metrik](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) v Azure Portal, rozhraní příkazového řádku Azure CLI, Azure PowerShell a programovacích rozhraní API (Application Programming Interface). Můžete například provést následující věci:

- **Sledujte základní metriky pro virtuální počítač.** Na obrazovce s přehledem Azure Portal základní metriky zahrnují využití CPU, využití sítě, celkový počet bajtů disku a diskových operací za sekundu.

- **Povolte shromažďování diagnostiky spouštění a zobrazte ji pomocí Azure Portal.** Když narazíte na vlastní image do Azure nebo dokonce spustíte jednu z imagí platformy, může to mít spoustu důvodů, proč se virtuální počítač dostane do nespouštěcího stavu. Diagnostiku spouštění můžete snadno povolit při vytváření virtuálního počítače kliknutím na možnost **povoleno** pro diagnostiku spouštění v části monitorování obrazovky nastavení.

    Při spuštění virtuálních počítačů zaznamená agent diagnostiky spouštění výstup spouštění a uloží ho do služby Azure Storage. Tato data můžete použít k odstraňování problémů při spouštění virtuálních počítačů. Diagnostika spouštění není automaticky povolena při vytváření virtuálního počítače z nástrojů příkazového řádku. Před povolením diagnostiky spouštění je třeba vytvořit účet úložiště pro ukládání protokolů spouštění. Pokud povolíte diagnostiku spouštění v Azure Portal, automaticky se vytvoří účet úložiště.

    Pokud jste nepovolili diagnostiku spouštění při vytváření virtuálního počítače, můžete ho kdykoli povolit později pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)nebo [šablony Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Povolit shromažďování diagnostických dat operačního systému hosta** Když vytváříte virtuální počítač, budete mít příležitost na obrazovce nastavení a povolíte diagnostiku hostovaného operačního systému. Když povolíte shromažďování diagnostických dat, do virtuálního počítače se přidá [rozšíření IaaSDiagnostics pro Linux](../articles/virtual-machines/linux/diagnostic-extension.md) nebo [rozšíření IaaSDiagnostics pro Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) , které umožňuje shromažďovat další data o disku, procesoru a paměti.

    Pomocí shromážděných diagnostických dat můžete pro své virtuální počítače nakonfigurovat automatické škálování. Můžete taky nakonfigurovat protokoly pro ukládání dat a nastavit výstrahy, abyste věděli, že výkon není úplně pravý.

## <a name="alerts"></a>Upozornění

Můžete vytvářet [výstrahy](../articles/azure-monitor/platform/alerts-overview.md) na základě konkrétních metrik výkonu. Příklady problémů, které se dají upozornit, když průměrné využití procesoru překročí určitou prahovou hodnotu nebo je dostupné volné místo na disku pod určitou velikostí. Výstrahy se dají nakonfigurovat v [Azure Portal](../articles/azure-monitor/platform/alerts-classic-portal.md), pomocí [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell)nebo v [Azure CLI](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) poskytuje individuální pokyny a podporu v případě, že se vás týkají problémy se službami Azure, a pomůže vám připravit se na nadcházející plánovanou údržbu. Azure Service Health vás upozorní a vaše týmy s využitím cíleného a flexibilního oznámení.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md) pomáhá diagnostikovat a získat podporu v případě, že problém Azure ovlivňuje vaše prostředky. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.

## <a name="azure-activity-log"></a>Protokol aktivit Azure

[Protokol aktivit Azure](../articles/azure-monitor/platform/activity-logs-overview.md) je protokol předplatného, který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Protokol zahrnuje rozsah dat, od Azure Resource Manager provozních dat až po aktualizace Service Healthch událostí. Kliknutím na protokol aktivit v Azure Portal zobrazíte protokol pro svůj virtuální počítač.

Mezi akce, které můžete s protokolem aktivit dělat, patří:

- Vytvoří [výstrahu pro událost protokolu aktivit](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Streamujte ho do centra událostí](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) pro ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu, jako je PowerBI.
- Analyzujte ho v PowerBI pomocí [balíčku obsahu PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Uložte ho do účtu úložiště](../articles/azure-monitor/platform/archive-activity-log.md) pro archivaci nebo ruční kontrolu. Dobu uchování (ve dnech) můžete určit pomocí profilu protokolu.

Data protokolu aktivit můžete také přistupovat pomocí [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/monitor)nebo [monitorovat rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/).

[Protokoly prostředků Azure](../articles/azure-monitor/platform/resource-logs-overview.md) jsou protokoly EMITOVANÉ vaším virtuálním počítačem, které poskytují bohatou a častou data o jeho provozu. Protokoly prostředků se liší od protokolu aktivit tím, že poskytují přehled o operacích, které byly provedeny v rámci virtuálního počítače.

Mezi postupy, které můžete s diagnostickými protokoly provádět, patří:

- [Uložte je do účtu úložiště](../articles/azure-monitor/platform/archive-diagnostic-logs.md) pro auditování nebo ruční kontrolu. Dobu uchování (ve dnech) můžete určit pomocí nastavení diagnostiky prostředků.
- [Streamujte je Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) k ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu, jako je PowerBI.
- Analyzujte je pomocí [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Rozšířené monitorování

- [Azure monitor](../articles/azure-monitor/overview.md) je služba, která monitoruje cloudové a místní prostředí a udržuje jejich dostupnost a výkon. Poskytuje komplexní řešení pro shromažďování, analýzu a provoz telemetrie z vašeho cloudového a místního prostředí. Pomůže vám při zjišťování stavu vašich aplikací a proaktivně identifikuje problémy, které je ovlivňují, a prostředky, na kterých jsou závislé. Rozšíření můžete nainstalovat na [virtuální počítač](../articles/virtual-machines/linux/extensions-oms.md) se systémem Linux nebo na [virtuální počítač s Windows](../articles/virtual-machines/windows/extensions-oms.md) , který nainstaluje agenta Log Analytics pro shromáždění dat protokolu a uložení v pracovním prostoru Log Analytics.

    Pro virtuální počítače se systémem Windows a Linux je doporučeným způsobem pro shromažďování protokolů instalace agenta Log Analytics. Nejjednodušší způsob, jak nainstalovat agenta Log Analytics na virtuální počítač, je prostřednictvím [rozšíření Log Analytics VM](../articles/log-analytics/log-analytics-azure-vm-extension.md). Použití rozšíření zjednodušuje proces instalace a automaticky agenta nakonfiguruje pro odesílání dat do pracovního prostoru Log Analytics, který zadáte. Agent se také automaticky upgraduje a tím zajišťuje, abyste měli nejnovější funkce a opravy.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) vám umožní monitorovat virtuální počítač a jeho přidružené prostředky, protože se vztahují k síti, ve které se nacházejí. Rozšíření agenta Network Watcher můžete nainstalovat na [virtuální počítač](../articles/virtual-machines/linux/extensions-nwa.md) se systémem Linux nebo na [virtuální počítač s Windows](../articles/virtual-machines/windows/extensions-nwa.md).

- [Azure monitor pro virtuální počítače](../articles/azure-monitor/insights/vminsights-overview.md) monitoruje vaše virtuální počítače Azure ve velkém měřítku analýzou výkonu a stavu virtuálních počítačů s Windows a Linux, včetně jejich různých procesů a propojených závislostí na jiných prostředcích a externích. procesem. 

## <a name="next-steps"></a>Další kroky
- Projděte si postup v části [monitorování virtuálního počítače s Windows pomocí Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) nebo [monitorování virtuálního počítače se systémem Linux pomocí Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Přečtěte si další informace o osvědčených postupech pro [monitorování a diagnostiku](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
