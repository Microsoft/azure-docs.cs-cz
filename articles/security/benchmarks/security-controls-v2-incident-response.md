---
title: Azure Security test benchmark v2 – odpověď na incident
description: Reakce na incidenty Azure Security test v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b9295482c2464eb80bc49fa707744f49a2fbebfd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035373"
---
# <a name="security-control-v2-incident-response"></a>Řízení zabezpečení v2: reakce na incidenty

Reakce na incidenty pokrývá ovládací prvky v životním cyklu reakce na incidenty – přípravu, zjišťování a analýzy, zahrnutí a následné incidenty. To zahrnuje použití služeb Azure, jako jsou Azure Security Center a Sentinel, k automatizaci procesu reakce na incidenty.

Pokud chcete zobrazit příslušné integrované Azure Policy, přečtěte si [Podrobnosti o integrované iniciativě Azure Security test dodržování předpisů: reakce na incidenty](../../governance/policy/samples/azure-security-benchmark.md#incident-response)

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IR – 1 | 19 | IR-4, IR-8 |

Ujistěte se, že vaše organizace má procesy, které reagují na incidenty zabezpečení, aktualizovala tyto procesy pro Azure a pravidelně ji vykonává za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Referenční příručka k reakcím na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – nastavení oznámení o incidentu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IR – 2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Nastavte kontaktní informace incidentu zabezpečení v Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že s vašimi daty někdo nezákonně nebo neoprávněně pracoval. Máte také možnost přizpůsobit upozornění na incidenty a oznámení o incidentech v různých službách Azure v závislosti na tom, jak potřebujete na incidenty reagovat. 

- [Jak nastavit kontakt zabezpečení pro Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detekce a analýza – vytvoření incidentů na základě upozornění s vysokou kvalitou

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Ujistěte se, že máte proces pro vytváření vysoce kvalitních výstrah a měření kvality výstrah. To vám umožní naučit se lekce z minulých incidentů a upřednostnit výstrahy pro analytiky, takže nemusejí ztrácet čas u falešně pozitivních výsledků. 

Vysoce kvalitní upozornění je možné vytvářet na základě zkušeností z minulých incidentů, ověřených komunitních zdrojů a nástrojů, které spojují a korelují signály z různorodých zdrojů a generují a mažou upozornění. 

Azure Security Center nabízí vysoce kvalitní upozornění pro celou řadu prostředků Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

Pomocí funkce exportu můžete upozornění a doporučení služby Azure Security Center exportovat, abyste mohli lépe identifikovat rizika pro prostředky Azure. Upozornění a doporučení můžete exportovat ručně nebo průběžně a nepřetržitě.

- [Konfigurace exportu](../../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detekce a analýza – prozkoumání incidentu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Zajistěte, aby Analytiki mohli zadávat dotazy a používat různé zdroje dat při zkoumání potenciálních incidentů, abyste mohli vytvořit úplný přehled o tom, co se stalo. Aby se zabránilo slepým místům, měly by se shromažďovat různorodé protokoly, které umožní sledovat aktivity potenciálního útočníka v celém průběhu útoku. Měli byste také zajistit zaznamenávání přehledů a poznatků pro další analytiky a budoucí použití jako historické referenční informace.

Mezi zdroje dat pro vyšetřování patří centrální zdroje protokolování, ve kterých se již shromažďují protokoly ze služeb v příslušném oboru a spuštěných systémů, ale můžou mezi ně patřit také:

- Síťová data – S využitím protokolů toků skupin zabezpečení sítě a služeb Azure Network Watcher a Azure Monitor můžete zachytávat protokoly toků sítě a další analytické informace. 

- Snímky spuštěných systémů: 

    - S využitím funkce vytváření snímků virtuálního počítače Azure můžete vytvořit snímek disku spuštěného systému. 

    - S využitím nativní funkce výpisu paměti operačního systému můžete vytvořit snímek paměti spuštěného systému.

    - S využitím funkce vytváření snímků služeb Azure nebo vlastních funkcí vašeho softwaru můžete vytvářet snímky spuštěných systémů.

Azure Sentinel nabízí rozsáhlé analýzy dat z prakticky jakéhokoli zdroje protokolů a portál pro správu případů, na kterém můžete spravovat celý životní cyklus incidentů. Analytické informace zjištěné během vyšetřování je možné přidružovat k incidentům pro účely sledování a generování sestav. 

- [Snímek disku počítače s Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snímek disku počítače s Linuxem](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Shromažďování výpisu paměti a diagnostických informací ze strany podpory Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Vyšetřování incidentů s využitím služby Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – stanovení priorit incidentů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IR-5 | 19.8 | CA – 2, IR-4 |

Poskytněte kontext analytikům, na kterých se incidenty zaměřuje na první, na základě závažnosti výstrahy a citlivosti prostředků. 

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost vychází z míry důvěry služby Security Center v závěr nebo analýzu, na základě kterých se upozornění vygenerovalo, a také z úrovně spolehlivosti, že za aktivitou, která vedla k upozornění, byl škodlivý záměr.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data. Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zadržení, zničení a obnovení – automatizace zpracování incidentů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IR – 6 | 19 | IR-4, IR-5, IR-6 |

Automatizace ručních opakujících se úloh, které urychlují dobu odezvy a snižují zatížení analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také analytiky více unavují, což zvyšuje riziko zpoždění v důsledku lidských chyb a snižuje schopnost analytiků efektivně se soustředit na složité úlohy. S využitím funkcí automatizace pracovních postupů ve službách Azure Security Center a Azure Sentinel můžete v reakci na příchozí upozornění zabezpečení automaticky aktivovat akce nebo spouštět playbooky. Playbook provádí akce, jako jsou odesílání oznámení, zakazování účtů nebo izolace problematických sítí. 

- [Konfigurace automatizace pracovních postupů ve službě Security Center](../../security-center/workflow-automation.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)