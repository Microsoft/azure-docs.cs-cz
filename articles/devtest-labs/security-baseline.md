---
title: Základní hodnoty zabezpečení Azure pro Azure DevTest Labs
description: Základní hodnoty zabezpečení Azure pro Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097094"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Základní hodnoty zabezpečení Azure pro Azure DevTest Labs

Základní plán zabezpečení Azure pro Azure DevTest Labs obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času
**Doprovodné materiály:** Microsoft udržuje časové zdroje pro prostředky Azure. Můžete ale spravovat nastavení synchronizace času pro výpočetní prostředky.

V následujícím článku se dozvíte, jak nakonfigurovat synchronizaci času pro výpočetní prostředky Azure: [čas synchronizace virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync). 

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení
**Doprovodné materiály:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Protokoly aktivit poskytují přehled o operacích, které byly provedeny na vašich Azure DevTest Labs instancích na úrovni roviny správy. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené na úrovni roviny správy pro instance DevTest Labs.

Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro odesílání protokolů platforem a metrik do různých umístění](../azure-monitor/platform/diagnostic-settings.md).

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure
**Doprovodné materiály:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Protokoly aktivit poskytují přehled o operacích, které byly provedeny na vašich Azure DevTest Labs instancích na úrovni roviny správy. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené na úrovni roviny správy pro instance DevTest Labs.

Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro odesílání protokolů platforem a metrik do různých umístění](../azure-monitor/platform/diagnostic-settings.md).

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů
**Doprovodné materiály:** Vytvářené virtuální počítače Azure DevTest Labs a vlastněné zákazníkem. Je to tedy zodpovědnost vaší organizace za jeho monitorování. Pomocí Azure Security Center můžete monitorovat výpočetní operační systém. Data shromažďovaná Security Center z operačního systému zahrnují typ a verzi operačního systému, operační systém (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel. Agent Log Analytics také shromažďuje soubory s výpisem stavu systému.

Další informace najdete v následujících článcích: 

- [Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Pochopení Azure Security Center shromažďování dat](../security-center/security-center-enable-data-collection.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení
***Doprovodné materiály:** V Azure Monitor nastavte dobu uchování protokolu pro pracovní prostory Log Analytics přidružené k vašim Azure DevTest Labs instancí podle předpisů pro dodržování předpisů vaší organizace.

Další informace najdete v následujícím článku: [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) .

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly
**Doprovodné materiály:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do pracovního prostoru Log Analytics. Spouštějte dotazy v Log Analytics pro hledání podmínek, identifikujte trendy, analyzujte vzorce a poskytněte spoustu dalších přehledů na základě dat protokolů aktivit, které se můžou shromažďovat pro Azure DevTest Labs.

Další informace najdete v následujících článcích:

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/platform/diagnostic-settings.md)
- [Jak shromažďovat a analyzovat protokoly aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor](../azure-monitor/platform/activity-log.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé
**Doprovodné materiály:** Pracovní prostor Azure Log Analytics slouží k monitorování a upozorňování na aktivity neobvyklé v protokolech zabezpečení a událostech souvisejících s vaší Azure DevTest Labs.

Další informace najdete v následujícím článku: [jak upozornit na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru
**Doprovodné materiály:** Nelze použít. Azure DevTest Labs nezpracovává ani nevytváří protokoly související s malwarem.

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS
**Doprovodné materiály:** Nelze použít. Azure DevTest Labs nezpracovává nebo nevytváří protokoly související se službou DNS.

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku
**Doprovodné materiály:** Azure DevTest Labs vytvoří výpočetní počítače Azure, které jsou vlastněné a spravované zákazníkem. Pomocí Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure s Windows Zaprotokolujte událost vytvoření procesu a pole CommandLine. U podporovaných virtuálních počítačů se systémem Azure Linux můžete ručně nakonfigurovat protokolování konzoly na bázi jednotlivých uzlů a pomocí protokolu syslog ukládat data. K prohlížení protokolů a spouštění dotazů na protokolované data z virtuálních počítačů Azure taky použijte pracovní prostor Log Analytics Azure Monitor.

- [Shromažďování dat v Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Spuštění vlastních dotazů v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Zdroje dat Syslogu ve službě Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek:

- [Výstrahy zabezpečení pro prostředí v Azure DevTest Labs](environment-security-alerts.md)