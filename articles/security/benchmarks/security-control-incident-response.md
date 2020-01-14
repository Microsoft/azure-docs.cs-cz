---
title: Řízení zabezpečení Azure – reakce na incidenty
description: Odpověď na incident ovládacího prvku zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934421"
---
# <a name="security-control-incident-response"></a>Řízení zabezpečení: reakce na incidenty

Chraňte informace o organizaci a také její pověst, a to tím, že vyvíjíte a implementujete infrastrukturu reakce na incidenty (například plány, definované role, školení, komunikace, dohled nad správou) pro rychlé zjištění útoku a pak efektivně obsahující škodu, eradikaci přítomnosti útočníka a obnovování integrity sítě a systémů.

## <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Zákazník |

Vytvořte si Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

Postup konfigurace automatizace pracovních postupů v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie centra Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 10.2 | 19,8 | Zákazník |

Security Center přiřadí každému upozornění závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

## <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 10,3 | 19 | Zákazník |

Provádějte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Přečtěte si téma publikace NIST: Průvodce pro testování, školení a cvičení programů pro plány a možnosti IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 10,4 | 19,5 | Zákazník |

Kontaktní údaje incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

Jak nastavit Azure Security Center kontakt zabezpečení:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 10.5 | 19,6 | Zákazník |

Vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

Postup konfigurace průběžného exportu:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 10,6 | 19 | Zákazník |

Použijte funkci automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím &quot;Logic Apps&quot; výstrah zabezpečení a doporučení.

Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Další kroky

Podívejte se na další řízení zabezpečení: [testy průniku a cvičení červeného týmu](security-control-penetration-tests-red-team-exercises.md) .