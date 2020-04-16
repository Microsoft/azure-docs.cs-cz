---
title: Azure Security Control – reakce na incidenty
description: Reakce na incidenty ovládacího prvku zabezpečení Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408423"
---
# <a name="security-control-incident-response"></a>Řízení zabezpečení: Reakce na incidenty

Chraňte informace organizace i její pověst vývojem a implementací infrastruktury pro reakci na incidenty (např. plány, definované role, školení, komunikace, dohled nad řízením) pro rychlé zjištění útoku a následné účinné potlačení poškození, vymýcení přítomnosti útočníka a obnovení integrity sítě a systémů.

## <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Zákazník |

Sestavte průvodce odezvou na incidenty pro vaši organizaci. Ujistěte se, že existují písemné plány reakce na incidenty, které definují všechny role personálu, stejně jako fáze zpracování incidentů / správy od detekce až po kontrolu po incidentu.  

- [Pokyny pro vytváření vlastního procesu reakce na bezpečnostní incidenty](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie incidentu v centru Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Využijte nist je počítačové bezpečnosti Incident Handling Guide na pomoc při vytváření vlastního plánu reakce na incidenty](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.2 | 19.8 | Zákazník |

Centrum zabezpečení přiřazuje každé výstraze závažnost, která vám pomůže určit prioritu, které výstrahy by měly být nejprve prošetřeny. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění. 

Navíc jasně označit odběry (pro ex. produkční, bez výrobních prvků) pomocí značek a vytvoření systému pojmenování pro jasně identifikovat a kategorizovat prostředky Azure, zejména ty, které zpracovávají citlivá data.  Je vaší odpovědností upřednostnit nápravu výstrah na základě kritičnosti prostředků Azure a prostředí, kde k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Používání značek k uspořádání prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.3 | 19 | Zákazník |

Proveďte cvičení a otestujte schopnosti reakce na incidenty vašich systémů na základě pravidelné kadence, abyste ochránili prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

- [Publikace NIST - Průvodce testovacími, školicími a cvičebními programy pro IT plány a schopnosti](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.4 | 19.5 | Zákazník |

Kontaktní informace o bezpečnostních incidentech budou společností Microsoft použity k tomu, aby vás kontaktovala, pokud centrum Microsoft Security Response Center (MSRC) zjistí, že k vašim datům získala přístup nezákonná nebo neautorizovaná strana. Zkontrolujte incidenty po faktu, abyste zajistili, že budou problémy vyřešeny.

- [Jak nastavit kontakt zabezpečení Centra zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10,5 | 19.6 | Zákazník |

Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce Nepřetržitý export, která vám pomůže identifikovat rizika pro prostředky Azure. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah do Azure Sentinelu.

- [Jak nakonfigurovat nepřetržitý export](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Jak streamovat výstrahy do Azure Sentinelu](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.6 | 19 | Zákazník |

Pomocí funkce Automatizace pracovních postupů v Centru zabezpečení Azure můžete automaticky aktivovat odpovědi prostřednictvím "Logic Apps" na výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Konfigurace automatizace pracovních postupů a aplikací logiky](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>Další kroky

- Podívejte se na další bezpečnostní kontrolu: [Penetrační testy a cvičení červeného týmu](security-control-penetration-tests-red-team-exercises.md)