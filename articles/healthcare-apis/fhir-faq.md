---
title: Nejčastější dotazy týkající se služeb FHIR v Azure – Azure API pro FHIR
description: Získejte odpovědi na nejčastější dotazy týkající se Azure API pro FHIR, jako je umístění úložiště dat za FHIR rozhraní API a podpora verzí.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871775"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Nejčastější dotazy k rozhraní Azure API pro FHIR

## <a name="what-is-fhir"></a>Co je FHIR?
Prostředky pro interoperabilitu s rychlými zdravotními péče (FHIR – "oheň") jsou standardem interoperability, který slouží k povolení výměny dat zdravotnictví mezi různými systémy zdravotní péče. Tento standard vyvinula organizace změněného HL7 a je přijímána organizacemi zdravotnictví po celém světě. Nejaktuálnější verze FHIR je k dispozici jako R4 (verze 4). Rozhraní Azure API pro FHIR podporuje R4 a podporuje také předchozí verzi STU3 (standard pro zkušební použití 3). Další informace o FHIR najdete na [HL7.org](http://hl7.org/fhir/summary.html).

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Jsou data za rozhraními API FHIR uložená v Azure?

Ano, data se ukládají do spravovaných databází v Azure. Rozhraní API Azure pro FHIR neposkytuje přímý přístup k podkladovým datovým skladům.

## <a name="what-identity-provider-do-you-support"></a>Jaký poskytovatel identity podporujete?

V tuto chvíli podporujeme Microsoft Azure Active Directory jako zprostředkovatele identity.

## <a name="what-fhir-version-do-you-support"></a>Jakou verzi FHIR podporujete?

Podporujeme verze 4.0.0 a 3.0.1 na rozhraní API Azure pro FHIR (PaaS) i na FHIR serveru pro Azure (Open Source).

Podrobnosti najdete v tématu [podporované funkce](fhir-features-supported.md). Přečtěte si informace o tom, co se změnilo mezi verzemi v [historii verzí pro změněného HL7 FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Jaký je rozdíl mezi open source serverem Microsoft FHIR pro Azure a rozhraním Azure API pro FHIR?

Rozhraní Azure API pro FHIR je hostovaná a spravovaná verze serveru Microsoft FHIR pro Azure. Ve spravované službě poskytuje společnost Microsoft veškerou údržbu a aktualizace. 

Pokud používáte server FHIR pro Azure, máte přímý přístup k příslušným službám. I když ukládáte data FÍ, zodpovídáte i za údržbu a aktualizaci serveru a všech požadovaných dodržování předpisů.

Z hlediska vývoje je každá funkce nasazená na Open Source Microsoft FHIR Server pro Azure jako první. Po ověření v open source se uvolní do PaaS Azure API pro řešení FHIR. Doba mezi vydáním v open source a PaaS závisí na složitosti funkce a dalších prioritách plánu. 

## <a name="what-is-smart-on-fhir"></a>Co je inteligentní na FHIR?

Inteligentní (nahraditelné lékařské aplikace a opakovaně použitelná technologie) na FHIR je sada otevřených specifikací pro integraci partnerských aplikací se servery FHIR a dalšími systémy IT, jako jsou například elektronické záznamy o stavu a výměny informací o stavu. Když vytvoříte inteligentní aplikaci v FHIR, můžete zajistit, že k aplikaci bude možné přistupovat a využívat spoustu různých systémů.
Ověřování a rozhraní Azure API pro FHIR. Další informace o INTELIGENTNÍch možnostech najdete v přehledu [inteligentního stavu](https://smarthealthit.org/).

## <a name="next-steps"></a>Další kroky

V tomto článku jste si přečetli některé z nejčastějších dotazů k rozhraní Azure API pro FHIR. Přečtěte si o podporovaných funkcích serveru FHIR pro Azure:
 
>[!div class="nextstepaction"]
>[Podporované funkce FHIR](fhir-features-supported.md)