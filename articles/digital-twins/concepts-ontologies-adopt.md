---
title: Přijetí oboru standardní ontologie
titleSuffix: Azure Digital Twins
description: Přečtěte si o stávajících oborech ontologie, které je možné přijmout pro digitální vlákna Azure.
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561462"
---
# <a name="adopting-an-industry-ontology"></a>Přijetí odvětví Ontology

Vzhledem k tomu, že může být snazší začít s open source DTDL Ontology, než začnete od prázdné stránky, společnost Microsoft spolupracuje s odborníky na doménu, aby publikovala ontologie, která představuje široce uznávané konvence v oboru a podporují různé případy použití zákazníků. 

Výsledkem je sada open source ontologie založených na DTDL, která se naučí, sestavovat, učit se od nich nebo přímo používat oborové standardy. Ontologie jsou navržené tak, aby splňovaly potřeby vývojářů pro příjem dat, a to s možností široce přijmout a/nebo rozšířit v rámci odvětví.

V tuto chvíli společnost Microsoft spolupracovala s partnery v reálném prostoru, aby vyvinula Ontology pro inteligentní budovy, která poskytuje běžný základ pro modelování inteligentních budov na základě oborových standardů, aby se zabránilo vynálezu. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore inteligentní budova Ontology

Společnost Microsoft spolupracuje s [RealEstateCoreem](https://www.realestatecore.io/), švédským konsorciem vlastníků, výrobcům softwaru a výzkumnými institucemi, aby poskytovala Open-Source DTDL Ontology pro odvětví realit: [**DTDL RealEstateCore Ontology pro inteligentní budovy**](https://github.com/Azure/opendigitaltwins-building).

Tato inteligentní budova Ontology poskytuje běžnou zemi pro modelování inteligentních budov pomocí průmyslových standardů (například [schématu cihly](https://brickschema.org/ontology/) nebo [ontologyu stavební topologie W3C](https://w3c-lbd-cg.github.io/bot/index.html)), aby se zabránilo vynálezu. Ontology také obsahuje osvědčené postupy, jak ho využívat a správně rozšiřuje. 

Další informace o struktuře a konvencích modelování Ontology, způsobu jejich použití, způsobu jejich rozšiřování a způsobu, jak přispívat, najdete v [úložišti Ontology na GitHubu](https://github.com/Azure/opendigitaltwins-building). 

Další informace o partnerství s RealEstateCore a cíli pro tuto iniciativu najdete v tomto blogovém příspěvku a doprovodném videu: [*RealEstateCore, inteligentní budova Ontology pro digitální vlákna je teď k dispozici*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o rozšíření oboru standardní ontologie, abyste splnili Vaše specifikace: [*Koncepty: rozšíření oboru ontologie*](concepts-ontologies-extend.md).

* Nebo pokračujte v cestě pro vývoj modelů na základě ontologie: [*použití strategií Ontology v cestě pro vývoj modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).