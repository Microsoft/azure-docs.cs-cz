---
title: Azure Security test benchmark v2 – odpověď na incident
description: Reakce na incidenty Azure Security test v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172607a7f8f036bbfb68e8d15e77b2a3e3fb5377
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326384"
---
# <a name="security-control-v2-incident-response"></a>Řízení zabezpečení v2: reakce na incidenty

Reakce na incidenty pokrývá ovládací prvky v životním cyklu reakce na incidenty – přípravu, zjišťování a analýzy, zahrnutí a následné incidenty. To zahrnuje použití služeb Azure, jako jsou Azure Security Center a Sentinel, k automatizaci procesu reakce na incidenty.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| IR – 1 | 19 | IR-4, IR-8 |

Ujistěte se, že vaše organizace má procesy, které reagují na incidenty zabezpečení, aktualizovala tyto procesy pro Azure a pravidelně ji vykonává za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](https://aka.ms/AzSec4)

- [Referenční příručka k reakci na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – oznámení o nastavení incidentu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| IR – 2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Nastavte kontaktní informace incidentu zabezpečení v Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že vaše data jsou přístupná ze strany, která není protiprávní nebo oprávněná. Máte také možnost přizpůsobit upozornění na incidenty a oznámení v různých službách Azure v závislosti na potřebách reakce na incidenty. 

- [Jak nastavit kontakt zabezpečení Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detekce a analýza – vytvoření incidentů na základě výstrah s vysokou kvalitou

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Ujistěte se, že máte proces pro vytváření vysoce kvalitních výstrah a měření kvality výstrah. To vám umožní naučit se lekce z minulých incidentů a upřednostnit výstrahy pro analytiky, takže nemusejí ztrácet čas u falešně pozitivních výsledků. 

Výstrahy s vysokou kvalitou se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění výstrah odmítání a korelace různých zdrojů signálu. 

Azure Security Center poskytuje výstrahy vysoké kvality napříč mnoha prostředky Azure. K streamování výstrah do Azure Sentinel můžete použít datový konektor ASC. Azure Sentinel umožňuje vytvářet Pokročilá pravidla výstrah pro automatické generování incidentů pro účely šetření. 

Vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce exportu, která vám usnadní identifikaci rizik pro prostředky Azure. Vyexportujte výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem.

- [Postup konfigurace exportu](../../security-center/continuous-export.md)

- [Jak streamovat výstrahy do Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detekce a analýza – prozkoumání incidentu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Zajistěte, aby Analytiki mohli zadávat dotazy a používat různé zdroje dat při zkoumání potenciálních incidentů, abyste mohli vytvořit úplný přehled o tom, co se stalo. K tomu, aby bylo možné sledovat aktivity potenciálního útočníka v rámci dezaktivačního řetězu, by měly být shromažďovány různé protokoly.  Měli byste taky zajistit, aby byly informace o studiu a učení pro jiné analytiky a budoucí historické odkazy.  

Zdroje dat pro účely šetření zahrnují centralizované zdroje protokolování, které jsou již shromažďovány ze služeb v oboru a spuštěných systémů, ale mohou také zahrnovat:

- Síťová data – pomocí protokolů toků služby skupiny zabezpečení sítě, Azure Network Watcher a Azure Monitor zaznamenává protokoly toku sítě a další informace o analýze. 

- Snímky spuštěných systémů: 

    - Použijte schopnost snímku virtuálního počítače Azure a vytvořte snímek disku běžícího systému. 

    - Pomocí funkce nativního výpisu paměti operačního systému vytvořte snímek paměti běžícího systému.

    - Pomocí funkce snímků služeb Azure nebo vlastní schopnosti svého softwaru můžete vytvářet snímky spuštěných systémů.

Azure Sentinel poskytuje rozsáhlou analýzu dat napříč všemi zdroji protokolů a portálem pro správu případů, který umožňuje spravovat úplný životní cyklus incidentů. Informace o dataintelligence během šetření můžou být přidruženy k incidentu pro účely sledování a vytváření sestav. 

- [Snímek disku s počítačem s Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Vytvoření snímku disku počítače se systémem Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure podporu pro diagnostické informace a shromažďování výpisu paměti](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Prozkoumat incidenty pomocí služby Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detekce a analýza – určení priorit incidentů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| IR-5 | 19,8 | CA – 2, IR-4 |

Poskytněte kontext analytikům, na kterých se incidenty zaměřuje na první, na základě závažnosti výstrahy a citlivosti prostředků. 

Azure Security Center přiřadí každému upozornění závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označovat prostředky pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data.  Máte zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.

- [Výstrahy zabezpečení ve službě Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zahrnutí, vyhubení a obnovení – automatizace zpracování incidentů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| IR – 6 | 19 | IR-4, IR-5, IR-6 |

Automatizace ručních opakujících se úloh, které urychlují dobu odezvy a snižují zatížení analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také zvyšují únavu analytiků, což zvyšuje riziko lidské chyby, která způsobuje zpoždění, a snižuje schopnost analytiků efektivně se zaměřit na složité úlohy. Pomocí funkcí automatizace pracovních postupů v Azure Security Center a Azure Sentinel můžete automaticky aktivovat akce nebo spustit PlayBook a reagovat na příchozí výstrahy zabezpečení. PlayBook provádí akce, jako je odesílání oznámení, zakázání účtů a izolace problematických sítí. 

- [Konfigurace automatizace pracovního postupu v Security Center](../../security-center/workflow-automation.md)

- [Nastavení automatických odpovědí na hrozby v Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatických odpovědí na hrozby v Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

