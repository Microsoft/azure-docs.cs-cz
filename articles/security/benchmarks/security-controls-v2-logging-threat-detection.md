---
title: Azure Security test Testing v2 – protokolování a detekce hrozeb
description: Protokolování a detekce hrozeb v Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a274ec1aab3f530700f89ef810fa667fdf4d08e6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042976"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>Řízení zabezpečení v2: protokolování a detekce hrozeb

Protokolování a detekce hrozeb pokrývá ovládací prvky pro detekci hrozeb v Azure a povolení, shromažďování a ukládání protokolů auditu pro služby Azure. To zahrnuje povolení zjišťování, vyšetřování a nápravy procesů s ovládacími prvky pro generování výstrah s vysokou kvalitou s nativní detekcí hrozeb ve službách Azure. zahrnuje také shromažďování protokolů s Azure Monitor, centralizaci analýzy zabezpečení s použitím Sentinel Azure, synchronizaci času a uchovávání protokolů.

Pokud se chcete podívat na příslušný integrovaný Azure Policy, přečtěte si [Podrobnosti o integrované iniciativě Azure Security test dodržování předpisů: protokolování a detekce hrozeb.](../../governance/policy/samples/azure-security-benchmark.md#logging-and-threat-detection)

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Ujistěte se, že sledujete různé typy prostředků Azure pro potenciální hrozby a anomálie. Zaměřte se na získávání vysoce kvalitních výstrah, které umožní analytikům, kteří se mají seřadit, omezit falešně pozitivní. Výstrahy se dají nacházet z dat protokolů, agentů nebo jiných dat.

Využijte Azure Security Center integrovanou funkci detekce hrozeb, která vychází z monitorování telemetrie služeb Azure a analýzy protokolů služby. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením ze systému a kopíruje data do vašeho pracovního prostoru pro účely analýzy. 

Kromě toho použijte Azure Sentinel k vytváření pravidel analýzy, které usnadňují hrozby, které odpovídají konkrétním kritériím v rámci vašeho prostředí. Pravidla generují incidenty, když jsou kritéria shodná, takže můžete prozkoumat jednotlivé incidenty. Funkce Sentinel Azure může taky importovat analýzy hrozeb jiných výrobců, aby se zlepšila její schopnost detekce hrozeb. 

- [Ochrana před hrozbami v Azure Security Center](../../security-center/azure-defender.md)

- [Referenční příručka pro Azure Security Center výstrahy zabezpečení](../../security-center/alerts-reference.md)

- [Vytváření vlastních pravidel analýzy pro detekci hrozeb](../../sentinel/tutorial-detect-threats-custom.md)

- [Funkce Azure Sentinel pro internetovou analýzu hrozeb](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| LT – 2 | 6.8 | AU-3, AU-6, AU-12, SI-4 |

Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD nebo v nich integrovat s Azure Monitor, Azure Sentinelem nebo jinými nástroji SIEM/Monitoring pro složitější účely monitorování a analýzy: 
-   Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení může modul ochrany před internetovými útoky Azure Security Center taky shromažďovat podrobnější výstrahy zabezpečení z jednotlivých výpočetních prostředků Azure (jako jsou virtuální počítače, kontejnery, App Service), datové prostředky (například SQL DB a Storage) a vrstvy služeb Azure. Tato funkce umožňuje zobrazit anomálie účtů v jednotlivých prostředcích.

- [Sestavy aktivit auditu v Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Povolení ochrany identit Azure](../../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrana před hrozbami v Azure Security Center](../../security-center/azure-defender.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Povolení protokolování pro síťové aktivity Azure

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| LT-3 | 9,3, 12,2, 12,5, 12,8 | AU-3, AU-6, AU-12, SI-4 |

Povolte a Shromážděte protokoly o prostředcích skupiny zabezpečení sítě (NSG), protokoly toku NSG, protokoly Azure Firewall a protokoly brány firewall webových aplikací (WAF) pro analýzu zabezpečení pro podporu vyšetřování incidentů, lov hrozeb a generování výstrah zabezpečení. Protokoly toku můžete odeslat do pracovního prostoru Azure Monitor Log Analytics a pak pomocí Analýza provozu získat přehledy.

Ujistěte se, že shromažďujete protokoly dotazů DNS, které pomáhají při korelaci dalších síťových dat.

- [Jak povolit protokoly toku skupin zabezpečení sítě](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Metriky a protokoly Azure Firewallu](../../firewall/logs-and-metrics.md)

- [Postup povolení a použití Analýza provozu](../../network-watcher/traffic-analytics.md)

- [Monitorování pomocí Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

- [Řešení monitorování sítě Azure v Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md)

- [Získejte přehled o vaší infrastruktuře DNS pomocí DNS Analytics řešení](../../azure-monitor/insights/dns-analytics.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| LT-4 | 6,2, 6,3, 8,8 | AU-3, AU-12 |

Povolte protokolování prostředků Azure tak, aby splňovalo požadavky na dodržování předpisů, detekci hrozeb, lov a vyšetřování incidentů. 

Pomocí Azure Security Center a Azure Policy můžete povolit protokoly prostředků a data protokolů, která se shromažďují v prostředcích Azure, a získat tak přístup k protokolům auditu, zabezpečení a prostředků. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

- [Principy protokolování a různých typů protokolů v Azure](../../azure-monitor/essentials/platform-logs-overview.md)

- [Pochopení Azure Security Center shromažďování dat](../../security-center/security-center-enable-data-collection.md)

**Odpovědnost:** Sdílené

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Zabezpečení infrastruktury a koncových bodů 

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| LT-5 | 6,5, 6,6 | AU-3, SI-4 |

Centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu zajistěte, aby byl přiřazen vlastník dat, pokyny pro přístup, umístění úložiště, jaké nástroje jsou používány pro zpracování a přístup k datům a požadavky na uchovávání dat.

Ujistěte se, že do svého centrálního protokolování integrujete protokoly aktivit Azure. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| LT – 6 | 6.4 | AU-3, AU-11 |

Nakonfigurujte uchovávání protokolů podle vašich požadavků na dodržování předpisů, nařízení a podnikových požadavků. 

V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Změnit dobu uchovávání dat v Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center upozornění a export doporučení](../../security-center/continuous-export.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: použití schválených zdrojů synchronizace času

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| LT-7 | 6.1 | AU – 8 |

Microsoft udržuje časové zdroje pro většinu služeb Azure PaaS a SaaS. U virtuálních počítačů použijte výchozí server NTP společnosti Microsoft pro dobu synchronizace času, pokud nemáte konkrétní požadavek. Pokud potřebujete vytvořit vlastní server NTP (Network Time Protocol), ujistěte se, že jste zabezpečili port UDP 123.

Všechny protokoly generované prostředky v Azure poskytují časová razítka s časovým pásmem zadaným ve výchozím nastavení.

- [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure Windows](../../virtual-machines/windows/time-sync.md)

- [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure Linux](../../virtual-machines/linux/time-sync.md)

- [Jak zakázat příchozí UDP pro služby Azure](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Odpovědnost:** Sdílené

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zásady a standardy](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)