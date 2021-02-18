---
title: Řízení zabezpečení Azure – protokolování a sledování
description: Protokolování a sledování řízení zabezpečení Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cfa2548abb28fa6f84178a1fb5716d0c03b163f1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570728"
---
# <a name="security-control-logging-and-monitoring"></a>Řízení zabezpečení: protokolování a sledování

Protokolování zabezpečení a monitorování se zaměřuje na aktivity související s povolením, získáním a ukládáním protokolů auditu pro služby Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft udržuje časové zdroje pro prostředky Azure, ale máte možnost spravovat nastavení synchronizace času pro výpočetní prostředky.

- [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure Windows](../../virtual-machines/windows/time-sync.md)

- [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure Linux](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2,2 | 6,5, 6,6 | Zákazník |

Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé nebo archivní úložiště.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2.3 | 6,2, 6,3 | Zákazník |

Povolte nastavení diagnostiky prostředků Azure pro přístup k protokolům auditu, zabezpečení a diagnostickým protokolům. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2,4 | 6,2, 6,3 | Zákazník |

Pokud je výpočetní prostředek vlastněn společností Microsoft, zodpovídá za jeho monitorování společnost Microsoft. Pokud je výpočetní prostředek vlastněn vaší organizací, máte zodpovědnost za jeho monitorování. Pomocí Azure Security Center můžete monitorovat operační systém. Data shromažďovaná Security Center z operačního systému zahrnují typ a verzi operačního systému, operační systém (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel. Agent Log Analytics také shromažďuje soubory s výpisem stavu systému.

- [Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Pochopení Azure Security Center shromažďování dat](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2.5 | 6.4 | Zákazník |

V rámci Azure Monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Změnit dobu uchovávání dat v Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../../storage/common/storage-monitor-storage-account.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2,6 | 6.7 | Zákazník |

Analyzujte a monitorujte protokoly pro neobvyklé chování a pravidelně kontrolujte výsledky. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM. 

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Principy Log Analyticsho pracovního prostoru](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2.7 | 6.8 | Zákazník |

Pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech použijte Azure Security Center s Log Analytics pracovním prostorem.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](../../azure-monitor/alerts/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2,8 | 8.6 | Zákazník |

Povolte shromažďování antimalwarových událostí pro Azure Virtual Machines a Cloud Services.

- [Jak nakonfigurovat Microsoft Antimalware pro Virtual Machines](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [Jak nakonfigurovat Microsoft Antimalware pro Cloud Services](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Pochopení antimalwarového programu Microsoftu](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2.9 | 8.7 | Zákazník |

Implementujte řešení od jiného výrobce z Azure Marketplace pro řešení protokolování DNS podle potřeby vaší organizace.  

## <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 2.10 | 8.8 | Zákazník |

Pomocí Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure s Windows Zaprotokolujte událost vytvoření procesu a pole CommandLine.   U podporovaných virtuálních počítačů se systémem Azure Linux můžete ručně nakonfigurovat protokolování konzoly na bázi jednotlivých uzlů a pomocí protokolu syslog ukládat data.  K prohlížení protokolů a provádění dotazů na protokolovaných datech z virtuálních počítačů Azure taky použijte pracovní prostor Azure Monitor Log Analytics. 

- [Shromažďování dat v Azure Security Center](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

- [Jak provádět vlastní dotazy v Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

- [Zdroje dat Syslogu ve službě Azure Monitor](../../azure-monitor/agents/data-sources-syslog.md)


## <a name="next-steps"></a>Další kroky

- Podívejte se na další ovládací prvek zabezpečení: [Identita a Access Control](security-control-identity-access-control.md)