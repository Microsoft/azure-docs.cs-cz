---
title: Co je nového – Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje novinky o Language Understanding.
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 0788df1c05d70c01d4de7f7a6ba62a476252f75a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498921"
---
# <a name="whats-new-in-language-understanding"></a>Co je nového v Language Understanding

Zjistěte, co je nového ve službě. Mezi tyto položky patří poznámky k verzi, videa, příspěvky na blogu a další typy informací. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu.  

## <a name="release-notes"></a>Poznámky k verzi 

### <a name="november-4-2019---ignite"></a>4\. listopadu 2019 – Ignite

* Zlepšení produktivity vývojářů
    * Obecná dostupnost našeho [koncového bodu předpovědi V3](luis-migration-api-v3.md). 
    * Možnost importovat a exportovat aplikace pomocí formátu. lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Tím se PAVES způsob efektivního procesu CI/CD. 
* Jazykové rozšíření
    * [Arabština a Hindština](luis-language-support.md) ve verzi Public Preview.
* Modely předsestavení
    * [Předem připravené domény](luis-reference-prebuilt-domains.md) jsou teď všeobecně dostupné (GA).
    * Japonské předem [připravené entity](luis-reference-prebuilt-entities.md#japanese-entity-support) – stáří, měna, číslo, procento nejsou v v3 podporovány.
    * [Předem připravené entity](luis-reference-prebuilt-entities.md#italian-entity-support) v italštině – stáří, měna, dimenze, číslo, procento rozlišení změněno z v2.
* Vylepšení uživatelského prostředí [portálu Preview.Luis.AI](https://preview.luis.ai) – prostředí přepracované označování, které umožňuje sestavování a ladění složitých modelů.
* Možnosti pro pokročilý jazyk – [vytváření sofistikovaných jazykových modelů](luis-concept-entity-types.md) s menší námahou 
* Definování funkcí strojového učení na úrovni modelu a povolení použití modelů jako signálů jiným modelům, jako je například použití entit jako funkcí k záměrům a jiným entitám.
* Nové, rozšířená [omezení](luis-boundaries.md) – vyšší Max pro seznamy frází a celkový počet frází, nový model jako omezení funkcí
* Extrahujte informace z textu ve formátu hloubkové hierarchické struktury, aby aplikace v konverzaci byly výkonnější.

    ![bitová kopie entity zjištěná počítačem](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3\. září 2019

* Prostředek pro vytváření Azure – [migrace teď](luis-migration-authoring.md)
    * 500 aplikací na prostředek Azure
    * verze 100 na aplikaci
* Turecká podpora pro předem připravené entity
* Italská podpora pro datetimeV2

### <a name="july-23-2019"></a>23. července 2019

* Aktualizace [rozpoznávání – text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) na 1.2.3
    * Věk, teplota, dimenze a rozpoznávání měn v italštině.
    * Zlepšení rozpoznávání svátků v angličtině, aby se správně počítala data založená na Thanksgiving.
    * Vylepšení ve francouzštině DateTime k omezení falešně pozitivních entit nezaložených na datu a nepracovních entit.
    * Podpora pro Calendar, School, fiskální rok a akronymy v angličtině DateRange
    * Vylepšené rozpoznávání PhoneNumber v čínštině a japonštině.
    * Vylepšená podpora pro NumberRange v angličtině.
    * Vylepšení výkonu.

### <a name="june-24-2019"></a>24. června 2019

* [OrdinalV2 předem vytvořenou entitu](luis-reference-prebuilt-ordinal-v2.md) pro podporu řazení, jako je například další, předchozí a poslední. Pouze Anglická jazyková verze.

### <a name="may-6-2019---build-conference"></a>6\. května 2019 – konference Build

Následující funkce byly vydány na konferenci Build 2019:

* [Verze Preview Průvodce migrací rozhraní V3 API](luis-migration-api-v3.md)
* [Vylepšený řídicí panel analýzy](luis-how-to-use-dashboard.md)
* [Vylepšené předem připravené domény](luis-reference-prebuilt-domains.md) 
* [Dynamické seznam entit](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Externí entity](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogy

[Rozhraní bot](https://blog.botframework.com/)

## <a name="videos"></a>Videa

### <a name="2019-build-videos"></a>videa k sestavám 2019

[Jak používat konverzační službu Azure ke škálování vašeho podnikání pro novou generaci](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aktualizace služby

[Oznámení o aktualizacích Azure pro Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
