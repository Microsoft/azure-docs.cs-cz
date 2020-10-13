---
title: Azure Security benchmark v2 – zásady správného řízení a strategie
description: Strategie řízení a testování Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 420ab87591e230592ec8f728c6acb155266b5912
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403522"
---
# <a name="security-control-v2-governance-and-strategy"></a>Řízení zabezpečení v2: zásady správného řízení a strategie

Zásady správného řízení a strategie poskytují pokyny k zajištění soudržné strategie zabezpečení a dokumentovaného přístupu pro řízení a zajištění zabezpečení, včetně stanovení rolí a odpovědností pro různé funkce cloudového zabezpečení, jednotnou technickou strategii a podpůrné zásady a standardy.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definování správy prostředků a strategie ochrany dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| GS – 1 | 2, 13 | SC, AC |

Ujistěte se, že jste dokument a komunikovali s jasnými strategiemi pro nepřetržité monitorování a ochranu systémů a dat. Určení priorit zjišťování, hodnocení, ochrany a monitorování důležitých podnikových dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Standardní klasifikace dat v souladu s obchodními riziky

- Organizace zabezpečení – přehled o rizicích a inventáři prostředků 

- Schválení organizace služby Azure pomocí organizace zabezpečení 

- Zabezpečení prostředků prostřednictvím jejich životního cyklu

- Požadovaná strategie řízení přístupu v souladu s klasifikací dat organizace

- Používání funkcí Azure Native a ochrany dat třetích stran

- Požadavky na šifrování dat pro případy použití v přenosech a v REST

- Příslušné kryptografické standardy

Další informace najdete v následujících odkazech:
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../fundamentals/encryption-overview.md)

- [Rozhraní pro přijetí do cloudu – osvědčené postupy zabezpečení a šifrování dat Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test zabezpečení Azure – Správa prostředků](security-controls-v2-asset-management.md)

- [Azure Security test – ochrana dat](security-controls-v2-data-protection.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Všichni účastníci](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definování strategie podnikové segmentace

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| GS – 2 | 4, 9, 16 | AC, CA, SC |

Vytvořte strategii pro celé podniky za účelem segmentace přístupu k assetům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších ovládacích prvků.

Pečlivě vyvážit nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat, a přistupovat k datům.

Zajistěte, aby se strategie segmentace implementovala konzistentně napříč typy ovládacích prvků včetně zabezpečení sítě, modelů identit a přístupu a oprávnění aplikace/modelů přístupu a ovládacích prvků pro lidské procesy.

- [Doprovodné materiály k strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Doprovodné materiály k strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Sjednotit segmentaci sítě pomocí strategie segmentace v podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Všichni účastníci](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definování strategie správy stav zabezpečení

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| GS – 3 | 20, 3, 5 | RA, CM, SC |

Nepřetržitě změřte a zmírnit rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Určete prioritu prostředků s vysokou hodnotou a vysoce vydaných ploch útoků, jako jsou publikované aplikace, příchozí a výstupní body sítě, koncové body uživatele a správce atd.

- [Azure Security test – stav a Správa ohrožení zabezpečení](security-controls-v2-posture-vulnerability-management.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Všichni účastníci](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: zarovnání rolí organizace, odpovědností a accountabilities

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| GS – 4 | Není k dispozici | PL, PM |

Ujistěte se, že máte dokument a komunikujete s jasnými strategiemi pro role a zodpovědnost ve vaší organizaci zabezpečení. Stanovte prioritu poskytování jasných zodpovědností při rozhodování o zabezpečení, informování všech uživatelů na sdílené zodpovědnosti a vzdělávání technických týmů na technologii pro zabezpečení cloudu.

- [Osvědčené postupy zabezpečení Azure 1 – lidé: vzdělávání týmů na cestě cloudového zabezpečení](https://aka.ms/AzSec1)

- [Osvědčené postupy zabezpečení Azure 2 – lidé: vzdělávání týmů na technologii Cloud Security](https://aka.ms/AzSec2)

- [Osvědčené postupy zabezpečení Azure 3 – proces: přiřazení zodpovědnosti pro rozhodování o zabezpečení cloudu](https://aka.ms/AzSec3)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Všichni účastníci](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: definování strategie zabezpečení sítě

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| GS – 5 | 9 | CA, SC |

Navažte přístup k zabezpečení sítě Azure v rámci celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Centralizovaná správa sítě a zodpovědnost zabezpečení

- Model segmentace virtuální sítě zarovnané na strategii podnikového segmentace

- Strategie oprav v různých scénářích hrozeb a útoků

- Strategie pro Internet Edge a příchozí a odchozí přenosy

- Hybridní cloudová a místní vzájemné propojení strategie

- Aktuální artefakty zabezpečení sítě (například síťové diagramy, referenční Síťová architektura)

Další informace najdete v následujících odkazech:

- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](https://aka.ms/AzSec11)

- [Testování zabezpečení Azure – zabezpečení sítě](security-controls-v2-network-security.md)

- [Přehled zabezpečení sítě Azure](../fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Všichni účastníci](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definování strategie identity a privilegovaného přístupu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| GS – 6 | 16, 4 | AC, AU, SC |

V rámci celkové strategie řízení přístupu k zabezpečení vaší organizace vytvořte přístup k identitě Azure a privilegovanému přístupu.  

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Centralizovaný systém identit a ověřování a jeho vzájemné propojení s dalšími interními a externími systémy identit

- Metody silného ověřování v různých případech a podmínkách použití

- Ochrana vysoce privilegovaných uživatelů

- Anomálie monitorování a zpracování aktivit uživatelů  

- Uživatelská identita a kontrola přístupu a proces odsouhlasení

Další informace najdete v následujících odkazech:

- [Azure Security benchmark – Správa identit](security-controls-v2-identity-management.md)

- [Azure Security test – privilegovaný přístup](security-controls-v2-privileged-access.md)

- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](https://aka.ms/AzSec11)

- [Přehled zabezpečení služby Azure Identity Management](../fundamentals/identity-management-overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Všichni účastníci](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definování strategie protokolování a reakce na hrozby

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| GS – 7 | 19 | IR, AU, RA, SC |

Navažte strategii odezvy a reakce na hrozby, která umožňuje rychle detekovat a opravovat hrozby při splnění požadavků na dodržování předpisů. Určete prioritu poskytování analytiků s vysoce kvalitními výstrahami a bezproblémové prostředí, aby se mohly soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Role a odpovědnosti organizace v oblasti operací zabezpečení (SecOps) 

- Dobře definovaný proces reakce na incidenty, který je v souladu s NIST nebo jiným oborovým rozhraním 

- Shromažďování a uchovávání protokolů pro podporu detekce hrozeb, reakce na incidenty a požadavků na dodržování předpisů

- Centralizované zobrazení a korelační informace o hrozbách, používání SIEM, nativních možností Azure a dalších zdrojů 

- Komunikace a plán oznámení vašim zákazníkům, dodavatelům a veřejným stranám zájmu

- Použití nativních platforem Azure a platforem třetích stran pro zpracování incidentů, jako je například protokolování a detekce hrozeb, forenzní a náprava či eradikace útoků

- Procesy pro zpracování incidentů a aktivit po incidentech, jako jsou zjištěné lekce a uchovávání důkazů

Další informace najdete v následujících odkazech:
- [Azure Security test benchmark – protokolování a detekce hrozeb](security-controls-v2-logging-threat-detection.md)

- [Srovnávací test zabezpečení Azure – odpověď na incident](security-controls-v2-incident-response.md)

- [Doporučený postup zabezpečení Azure 4 – proces. Aktualizace procesů reakce na incidenty pro Cloud](https://aka.ms/AzSec4)

- [Průvodce rozhodnutím o přijetí, protokolování a vytváření sestav pro Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure Enterprise Scale, Management a monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Všichni účastníci](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: definování strategie zálohování a obnovení

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| GS – 8 | 10 | CP |

Vytvořte si strategii zálohování a obnovení Azure pro vaši organizaci. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Definice plánované doby obnovení (RTO) a cíl bodu obnovení (RPO) v souladu s vašimi cíli vaší obchodní odolnosti

- Návrh redundance ve vašich aplikacích a nastavení infrastruktury

- Ochrana zálohování pomocí řízení přístupu a šifrování dat

Další informace najdete v následujících odkazech:
- [Azure Security test – zálohování a obnovení](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework – zálohování a zotavení po havárii pro aplikace Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Rozhraní pro přijetí do Azure – provozní kontinuita a zotavení po havárii](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Všichni účastníci](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

