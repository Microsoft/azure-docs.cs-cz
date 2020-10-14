---
title: Zabezpečení dat ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje způsob správy a ochrany dat ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: d829ffb9d3a264052e3f688018acd7afa854578e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018266"
---
# <a name="azure-security-center-data-security"></a>Zabezpečení dat Azure Security Center

Aby zákazníci mohli zabránit, detekovat a reagovat na hrozby, Azure Security Center shromažďuje a zpracovává data související se zabezpečením, včetně informací o konfiguraci, metadat, protokolů událostí a dalších. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb.

Tento článek vysvětluje, jak se spravují a chrání data v Security Center.

## <a name="data-sources"></a>Zdroje dat
Security Center analyzuje data z následujících zdrojů, aby poskytovala přehled o stavu zabezpečení, identifikovala ohrožení zabezpečení a doporučuje zmírnění rizik a zjišťoval aktivní hrozby:

- **Služby Azure**: používá informace o konfiguraci služeb Azure, které jste nasadili, prostřednictvím komunikace s poskytovatelem prostředků této služby.
- **Síťový provoz**: používá ukázková metadata síťového provozu z infrastruktury Microsoftu, jako je například zdrojová nebo cílová IP adresa/port, velikost paketu a síťový protokol.
- **Partnerská řešení**: používá výstrahy zabezpečení z integrovaných partnerských řešení, jako jsou brány firewall a antimalwarová řešení.
- **Vaše počítače**: používá konfigurační údaje a informace o událostech zabezpečení, jako jsou protokoly událostí a auditu systému Windows a zprávy syslog z vašich počítačů.


## <a name="data-protection"></a>Ochrana dat

### <a name="data-segregation"></a>Oddělení dat
Data se v každé komponentě v rámci služby udržují logicky odděleně. Všechna data jsou označená podle organizace. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby.

### <a name="data-access"></a>Přístup k datům
Abychom mohli poskytnout doporučení pro zabezpečení a prozkoumat potenciální bezpečnostní hrozby, mohou pracovníci Microsoftu přistupovat k informacím shromážděným nebo analyzovaným službami Azure, včetně událostí vytváření procesů a dalších artefaktů, které můžou neúmyslně zahrnovat zákaznická data nebo osobní údaje z vašich počítačů. 

Dodržujeme [dodatek k ochraně dat služeb Microsoft Online Services](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880), který uvádí, že společnost Microsoft nepoužije zákaznická data ani z nich odvozuje informace pro reklamní nebo podobné obchodní účely. Informace o zákaznících podle potřeby používáme pouze k poskytování služeb Azure a k účelům slučitelným s poskytováním těchto služeb. Všechna práva na informace o zákaznících zůstávají ve vašem vlastnictví.

### <a name="data-use"></a>Použití dat
Microsoft používá ke zvýšení možností prevence a detekce vzory a analýzy hrozeb, které se zobrazují napříč více klienty. v souladu se závazky ochrany osobních údajů popsané v našem [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/privacystatement).

## <a name="manage-data-collection-from-machines"></a>Správa shromažďování dat z počítačů
Když povolíte službu Security Center v Azure, u každého vašeho předplatného Azure se zapne funkce shromažďování dat. Můžete také povolit shromažďování dat pro vaše předplatná v Security Center. Když je povolené shromažďování dat, Security Center zřídí agenta Log Analytics na všech stávajících podporovaných virtuálních počítačích Azure a všech nově vytvořených.

Agent Log Analytics vyhledává různé konfigurace a události související se zabezpečením v [trasování událostí pro Windows](https://docs.microsoft.com/windows/win32/etw/event-tracing-portal) (ETW). Operační systém bude kromě toho během chodu počítače shromažďovat události protokolu událostí. Mezi příklady těchto údajů patří: typ a verze operačního systému, protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy, přihlášený uživatel a ID klienta. Agent Log Analytics načte položky protokolu událostí a trasování ETW a zkopíruje je do vašich pracovních prostorů pro účely analýzy. Agent Log Analytics taky umožňuje události vytváření procesů a auditování na příkazovém řádku.

Pokud nepoužíváte Azure Defender, můžete taky zakázat shromažďování dat z virtuálních počítačů v zásadách zabezpečení. Pro předplatná, která jsou chráněná pomocí Azure Defenderu, se vyžaduje shromažďování dat. Shromažďování artefaktů a snímků disku virtuálního počítače bude nadále povolené i v případě, že shromažďování dat je zakázané.

Můžete zadat pracovní prostor a oblast, kde se ukládají data shromážděná z vašich počítačů. Ve výchozím nastavení se data shromážděná z vašich počítačů ukládají do nejbližšího pracovního prostoru, jak je znázorněno v následující tabulce:

| Geografie virtuálního počítače                                      | Geografie pracovního prostoru  |
|---------------------------------------------|----------------|
| USA, Brazílie, Jižní Afrika         | Česká republika  |
| Canada                                      | Kanada         |
| Evropa (s výjimkou Spojeného království)           | Evropa         |
| Spojené království                              | Spojené království |
| Asie (s výjimkou Indie, Japonska, Koreje, ČLR) | Asie a Tichomoří   |
| Jižní Korea                                       | Asie a Tichomoří   |
| Indie                                       | Indie          |
| Japonsko                                       | Japonsko          |
| Čína                                       | Čína          |
| Austrálie                                   | Austrálie      |
|                                             |                |

> [!NOTE]
> **Azure Defender pro úložiště v** místním úložišti ukládá artefakty podle umístění souvisejícího prostředku Azure. Další informace najdete v [úvodu k Azure Defenderu pro úložiště](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Využití dat

Zákazníci mohou získat přístup k Security Center související data z následujících datových proudů:


| Datový proud                                                                                | Typy dat                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Protokol aktivit Azure](../azure-monitor/platform/activity-log.md)                       | Všechny výstrahy zabezpečení, schválené Security Center požadavky na přístup [za běhu](security-center-just-in-time.md) a všechny výstrahy vygenerované [adaptivními ovládacími prvky aplikace](security-center-adaptive-application.md).|
| [Protokoly Azure Monitor](../azure-monitor/platform/data-platform.md)                      | Všechny výstrahy zabezpečení                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | Výstrahy zabezpečení, doporučení zabezpečení, výsledky posouzení ohrožení zabezpečení, informace o bezpečném hodnocení, stav kontrol dodržování předpisů a další.                                                                       |
| [Rozhraní REST API služby Azure Security Center](https://docs.microsoft.com/rest/api/securitycenter/) | Výstrahy zabezpečení, doporučení zabezpečení a další.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli informace o způsobu správy a ochrany ve službě Azure Security Center. 

Další informace o Azure Security Center najdete v tématu [co je Azure Security Center?](security-center-introduction.md)
