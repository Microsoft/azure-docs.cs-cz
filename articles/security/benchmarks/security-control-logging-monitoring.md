---
title: Řízení zabezpečení Azure – protokolování a sledování
description: Protokolování a sledování řízení zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 1354bd2895a8fe6e8aa1f12cd92295c77ca2d4b2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564163"
---
# <a name="security-control-logging-and-monitoring"></a>Řízení zabezpečení: protokolování a sledování

Protokolování zabezpečení a monitorování se zaměřuje na aktivity související s povolením, získáním a ukládáním protokolů auditu pro služby Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft udržuje časové zdroje pro prostředky Azure, ale máte možnost spravovat nastavení synchronizace času pro výpočetní prostředky.

Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.2 | 6,5, 6,6 | Zákazník |

Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé nebo archivní úložiště.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. Jak připojit Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure s Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Jak začít s Azure Monitor a integrací SIEM třetích stran:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.3 | 6,2, 6,3 | Zákazník |

Povolte nastavení diagnostiky prostředků Azure pro přístup k protokolům auditu, zabezpečení a diagnostickým protokolům. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Principy protokolování a různých typů protokolů v Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.4 | 6,2, 6,3 | Zákazník |

Pokud je výpočetní prostředek vlastněn společností Microsoft, zodpovídá za jeho monitorování společnost Microsoft. Pokud je výpočetní prostředek vlastněn vaší organizací, máte zodpovědnost za jeho monitorování. Pomocí Azure Security Center můžete monitorovat operační systém. Data shromažďovaná Security Center z operačního systému zahrnují typ a verzi operačního systému, operační systém (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel. Agent Log Analytics také shromažďuje soubory s výpisem stavu systému.

Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Princip Azure Security Center shromažďování dat: https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.5 | 6.4 | Zákazník |

V rámci Azure Monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2,6 | 6.7 | Zákazník |

Analyzujte a monitorujte protokoly pro neobvyklé chování a pravidelně kontrolujte výsledky. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM. 

Jak připojit Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Principy Log Analyticsho pracovního prostoru:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Postup provádění vlastních dotazů v Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.7 | 6.8 | Zákazník |

Pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech použijte Azure Security Center s Log Analytics pracovním prostorem.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

Jak připojit Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Správa výstrah v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Jak upozornit na data protokolu Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.8 | 8,6 | Zákazník |

Povolte shromažďování antimalwarových událostí pro Azure Virtual Machines a Cloud Services.

Jak nakonfigurovat Microsoft Antimalware pro Virtual Machines:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Jak nakonfigurovat Microsoft Antimalware pro Cloud Services:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Porozumění programu Microsoft Antimalware:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.9 | 8.7 | Zákazník |

Implementujte řešení třetí strany pro protokolování DNS.

## <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 2.1 | 8.8 | Zákazník |

Ruční konfigurace protokolování konzoly a přepisu prostředí PowerShell v jednotlivých uzlech.

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Identita a Access Control](security-control-identity-access-control.md)
