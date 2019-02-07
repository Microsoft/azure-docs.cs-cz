---
title: Co je FHIR serveru pro Azure – FHIR serveru pro Azure
description: Tento článek popisuje FHIR serveru pro Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 20b29ba4c23da90b4941b37d02a1d3f42310dd40
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823483"
---
# <a name="what-is-fhir-server-for-azure"></a>Co je FHIR serveru pro Azure

FHIR&reg; serveru pro Azure je open source implementace nově vznikající [HL7 rychlé Healthcare Interoperability prostředky (FHIR) specifikace](https://www.hl7.org/fhir/) navržené pro cloud od Microsoftu. Specifikace FHIR definuje jak clinical health dat můžete provést interoperabilní napříč systémy. FHIR serveru pro Azure usnadňuje tento vzájemná funkční spolupráce v cloudu. Cílem tohoto projektu Microsoft Healthcare je vývojářům umožňuje rychle nasadit službu FHIR.
 
S daty ve formátu FHIR, FHIR serveru v Azure umožňuje vývojářům rychle ingestování a správu FHIR datových sad v cloudu, sledovat a správa přístupu k datům a normalizovat data pro machine learning úlohy. Server FHIR for Azure je optimalizovaná pro ekosystému Azure: 
* Skripty a šablony ARM jsou k dispozici pro okamžité zřizování v cloudu Microsoftu 
* Skripty jsou k dispozici pro mapování na Azure AAD a povolte řízení přístupu na základě role (RBAC) 

FHIR serveru pro Azure je sestavován logického oddělení, umožňuje vývojářům flexibilně změnit, jak je implementován a rozšířit její schopnosti podle potřeby. Logika vrstvy serveru FHIR jsou: 

* Hostování vrstvy – podporuje hostování v různých prostředích, s vlastní konfigurací – kontejnery ovládacích IOC (Inversion). 
* Vrstva rozhraní rESTful API – implementace rozhraní API definované ve specifikaci HL7 FHIR. 
* Základní logiku vrstva – implementace základní logiku FHIR. 
* Vrstvy trvalosti – poskytovatele trvalého chování modulární povolení FHIR serveru pro připojení k prakticky libovolný nástroj trvalost data. Server FHIR pro Azure obsahuje poskytovatele trvalého chování data připravená k použití pro službu Azure Cosmos DB (globálně replikovaného databázová služba, která nabízí bohaté dotazování na data). 

FHIR serveru pro Azure umožňuje vývojářům – šetří čas, kdy je potřebují můžete rychle integrovat FHIR Server do jejich vlastních aplikací nebo poskytovat jim jako základ, na kterém můžou přizpůsobit svoje vlastní FHIR služby. Jako opensourcový projekt příspěvky a zpětnou vazbu od komunity vývojářů FHIR bude dál vylepšit tento projekt. 

Ochrana osobních údajů a zabezpečení jsou nejvyšší priority a vyvinula Server FHIR pro Azure efektivnějších požadavky pro chráněné zdravotní informace (chráněných zdravotních informací). Všechny služby Azure používá v FHIR serveru pro Azure [splňují požadavky na dodržování předpisů pro chráněné zdravotní informace](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings).

Tento projekt open source plně tímto modulem stojí tým Microsoft Healthcare, ale víme, že tento projekt pouze získá vám dá víc názorů a příspěvků. Můžeme se vedoucí vývoje tento základ kódu a testování sestavování a nasazování každý den. 