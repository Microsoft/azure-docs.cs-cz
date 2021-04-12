---
title: Přijetí oboru standardní ontologie
titleSuffix: Azure Digital Twins
description: Přečtěte si o stávajících oborech ontologie, které je možné přijmout pro digitální vlákna Azure.
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102124223"
---
# <a name="adopting-an-industry-ontology"></a>Přijetí odvětví Ontology

Vzhledem k tomu, že může být snazší začít s open source DTDL Ontology, než začnete od prázdné stránky, společnost Microsoft spolupracuje s odborníky na doménu, aby publikovala ontologie, která představuje široce uznávané konvence v oboru a podporují různé případy použití zákazníků. 

Výsledkem je sada open source ontologie založených na DTDL, která se naučí, sestavovat, učit se od nich nebo přímo používat oborové standardy. Ontologie jsou navržené tak, aby splňovaly potřeby vývojářů pro příjem dat, a to s možností široce přijmout a/nebo rozšířit v rámci odvětví.

V tuto chvíli společnost Microsoft spolupracovala s partnery pro vývoj Ontology pro [inteligentní budovy](#realestatecore-smart-building-ontology) a Ontology pro [inteligentní města](#smart-cities-ontology), která poskytuje společné země pro modelování na základě standardů v těchto odvětvích, aby nedocházelo k vynálezu. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore inteligentní budova Ontology

*Tady najdete Ontology: [**digitální vlákna RealEstateCore Ontology založené na jazyce definition pro inteligentní budovy**](https://github.com/Azure/opendigitaltwins-building)*.

Společnost Microsoft spolupracuje s [RealEstateCore](https://www.realestatecore.io/), švédským konsorciem vlastníků, výrobcům softwaru a výzkumnými institucemi, aby dodávala tento Open-Source DTDL Ontology pro odvětví Real nemovitosti.

Tato inteligentní budova Ontology poskytuje běžnou zemi pro modelování inteligentních budov pomocí průmyslových standardů (například [schématu cihly](https://brickschema.org/ontology/) nebo [ontologyu stavební topologie W3C](https://w3c-lbd-cg.github.io/bot/index.html)), aby se zabránilo vynálezu. Ontology také obsahuje osvědčené postupy, jak ho využívat a správně rozšiřuje. 

Další informace o struktuře a konvencích modelování Ontology, způsobu jejich použití, způsobu jejich rozšiřování a způsobu, jak přispívat, najdete v úložišti Ontology na GitHubu: [Azure/opendigitaltwins – sestavování](https://github.com/Azure/opendigitaltwins-building). 

Další informace o partnerství s RealEstateCore a cíli pro tuto iniciativu najdete v tomto blogovém příspěvku a doprovodném videu: [RealEstateCore, inteligentní budova Ontology pro digitální vlákna je teď k dispozici](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Inteligentní města Ontology

*Tady najdete Ontology: [**Digital DTDLs Definition Language () Ontology pro inteligentní města**](https://github.com/Azure/opendigitaltwins-smartcities)*.

Microsoft spolupracuje s [otevřenými agilními inteligentními městy (OASC)](https://oascities.org/) a [SIRUS](https://sirus.be/) a poskytuje DTDL Ontology pro inteligentní města, počínaje [ETSI CIM NGSI-ld](https://www.etsi.org/committee/cim). Kromě ETSI NGSI-LD jsme také vyhodnotili Saref4City, CityGML, ISO a další.

Aktuální verze Ontology se zaměřuje na počáteční sadu modelů. Autoři Ontology vás vítá s cílem přispět k rozšíření počáteční sady případů použití a také zlepšit stávající modely. 

Další informace o Ontology, jak ho používat a jak přispívat, najdete v úložišti Ontology na GitHubu: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Můžete si také přečíst další informace o partnerství a přístupu pro inteligentní města v tomto blogovém příspěvku a doprovodném videu: [inteligentní města Ontology pro digitální vlákna](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o rozšíření oboru standardní ontologie, abyste splnili Vaše specifikace: [*Koncepty: rozšíření oboru ontologie*](concepts-ontologies-extend.md).

* Nebo pokračujte v cestě pro vývoj modelů na základě ontologie: [*použití strategií Ontology v cestě pro vývoj modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).