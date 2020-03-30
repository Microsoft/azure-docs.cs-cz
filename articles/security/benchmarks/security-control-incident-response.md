---
title: Azure Security Control – reakce na incidenty
description: Reakce na incidenty bezpečnostního řízení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934421"
---
# <a name="security-control-incident-response"></a>Řízení zabezpečení: Reakce na incidenty

Chraňte informace organizace i její pověst vývojem a implementací infrastruktury pro reakci na incidenty (např. plány, definované role, školení, komunikace, dohled nad řízením) pro rychlé zjištění útoku a poté účinně potlačovat škody, vymýtit přítomnost útočníka a obnovit integritu sítě a systémů.

## <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Zákazník |

Sestavte průvodce odezvou na incidenty pro vaši organizaci. Ujistěte se, že existují písemné plány reakce na incidenty, které definují všechny role personálu, stejně jako fáze zpracování incidentů / správy od detekce až po kontrolu po incidentu.

Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Pokyny pro vytváření vlastního procesu reakce na bezpečnostní incidenty:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie incidentu v centru Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Zákazník může také využít nist počítačové bezpečnosti Incident Handling Guide na pomoc při vytváření vlastního plánu reakce na incidenty:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.2 | 19.8 | Zákazník |

Centrum zabezpečení přiřazuje každé výstraze závažnost, která vám pomůže určit prioritu, které výstrahy by měly být nejprve prošetřeny. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

Navíc jasně označit odběry (pro ex. výroby, non-prod) a vytvořit systém pojmenování pro jasně identifikovat a kategorizovat prostředky Azure.

## <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.3 | 19 | Zákazník |

Proveďte cvičení a otestujte schopnosti reakce na incidenty vašich systémů na pravidelné kadenci. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

Podívejte se na publikaci NIST: Průvodce testovacími, školicími a cvičebními programy pro plány a schopnosti IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.4 | 19.5 | Zákazník |

Kontaktní informace o bezpečnostních incidentech budou společností Microsoft použity k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k datům zákazníka byla přístupná nezákonná nebo neoprávněná strana.  Zkontrolujte incidenty po faktu, abyste zajistili, že budou problémy vyřešeny.

Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10,5 | 19.6 | Zákazník |

Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce Nepřetržitý export. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah Sentinel.

Jak nakonfigurovat nepřetržitý export:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinelu:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 10.6 | 19 | Zákazník |

Pomocí funkce Automatizace pracovních postupů v Centru &quot;zabezpečení&quot; Azure můžete automaticky aktivovat odpovědi prostřednictvím logic apps na výstrahy zabezpečení a doporučení.

Konfigurace automatizace pracovních postupů a aplikací logiky:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Další kroky

Podívejte se na další bezpečnostní kontrolu: [Penetrační testy a cvičení červeného týmu](security-control-penetration-tests-red-team-exercises.md)