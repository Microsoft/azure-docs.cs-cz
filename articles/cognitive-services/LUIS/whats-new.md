---
title: Co je nového – Language Understanding (LUIS)
description: Tento článek se pravidelně aktualizuje o novinky o Language Understanding API Azure Cognitive Services.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 06/15/2020
ms.openlocfilehash: 52f8aad0a429d3378d46265fab719b1cb9f07fd0
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813186"
---
# <a name="whats-new-in-language-understanding"></a>Co je nového v Language Understanding

Zjistěte, co je nového ve službě. Mezi tyto položky patří poznámky k verzi, videa, příspěvky na blogu a další typy informací. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu.

## <a name="release-notes"></a>Poznámky k verzi

### <a name="december-2020"></a>Prosinec 2020

* Všichni LUIS uživatelé se musí [migrovat do prostředku AUTHORINT Luis](luis-migration-authoring.md) .

### <a name="june-2020"></a>Červen 2020

* [Preview 3,0 vytváření](luis-migration-authoring-entities.md) Sadě
    * Verze 3.2.0-Preview. 3 – [.NET – NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Verze 4.0.0-Preview. 3- [js-npm](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* Použití postupů DevOps s LUIS
    * Koncepty
        * [DevOps postupy pro LUIS](luis-concept-devops-sourcecontrol.md)
        * [Pracovní postupy průběžné integrace a průběžného doručování pro LUIS DevOps](luis-concept-devops-automation.md)
        * [Testování pro LUIS DevOps](luis-concept-devops-testing.md)
    * Postupy
        * [Použití DevOps pro vývoj aplikací LUIS pomocí akcí GitHubu](luis-how-to-devops-with-github.md)
    * [Kompletní úložiště GitHubu kódu](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Květen 2020 – Build

* Vydáno **všeobecně k dispozici** (GA):
    * [Language Understanding kontejner](luis-container-howto.md)
    * Portál Preview povýšen na [aktuální portál](https://www.luis.ai), [předchozí](https://previous.luis.ai) portál je stále k dispozici
    * Nové prostředí pro vytváření a označování entit pro strojové učení
    * [Proces upgradu](migrate-from-composite-entity.md) ze složených a jednoduchých entit na entity strojového učení
    * [Nastavení](how-to-application-settings-portal.md) podpory normalizace variant Wordu
* Náhled změn rozhraní API pro vytváření verzí
    * Schéma aplikace 7. x pro vnořené entity strojového učení
    * [Migrace na požadovanou funkci](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Nové prostředky pro vývojáře
    * [Nástroje pro kontinuální integraci](developer-reference-resource.md#continuous-integration-tools)
    * Workshop – Naučte se osvědčené postupy pro [ _přirozené Language Understanding_ (NLU) pomocí Luis](developer-reference-resource.md#workshops)
* [Spravované klíče zákazníka](luis-encryption-of-data-at-rest.md) – Šifrujte všechna data, která používáte v Luis, pomocí vlastního klíče.
* [Zobrazit AI](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (video) – Podívejte se na nové funkce v Luis.



### <a name="march-2020"></a>Březen 2020

* Pro všechny požadavky HTTP na tuto službu se teď vynutilo TLS 1,2. Další informace najdete v tématu [zabezpečení Azure Cognitive Services](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4. listopadu 2019 – Ignite

* Video – [pokročilé modely přirozeného Language Understanding (NLU) využívající Luis a Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Zlepšení produktivity vývojářů
    * Obecná dostupnost našeho [koncového bodu předpovědi V3](luis-migration-api-v3.md).
    * Možnost importovat a exportovat aplikace ve `.lu` formátu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Tím se PAVES způsob efektivního procesu CI/CD.
* Jazykové rozšíření
    * [Arabština a Hindština](luis-language-support.md) ve verzi Public Preview.
* Předem připravené modely
    * [Předem připravené domény](luis-reference-prebuilt-domains.md) jsou teď všeobecně dostupné (GA).
    * Japonské předem [připravené entity](luis-reference-prebuilt-entities.md#japanese-entity-support) – stáří, měna, číslo a procento nejsou v v3 podporovány.
    * [Předem připravené entity](luis-reference-prebuilt-entities.md#italian-entity-support) v italštině – stáří, měna, dimenze, číslo a procento rozlišení se změnilo z v2.
* Vylepšené uživatelské prostředí [portálu Preview.Luis.AI](https://preview.luis.ai) – prostředí přepracované označování, které umožňuje sestavování a ladění složitých modelů. Vyzkoušejte si kurzy portálu Preview:
    * [Jenom záměry](tutorial-intents-only.md)
    * [Entita pro desložení strojového učení](tutorial-machine-learned-entity.md)
* Možnosti pro pokročilý jazyk – [vytváření sofistikovaných jazykových modelů](luis-concept-entity-types.md) s menší námahou
* Definujte funkce strojového učení na úrovni modelu a povolte použití modelů jako signálů jiným modelům, například pomocí entit jako funkcí k záměrům a jiným entitám.
* Nové, rozšířená [omezení](luis-limits.md) – vyšší maximum pro seznamy frází a celkový počet frází, nový model jako omezení funkcí
* Extrahujte informace z textu ve formátu hloubkové hierarchické struktury, aby aplikace v konverzaci byly výkonnější.

    ![Obrázek entity strojového učení](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3. září 2019

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
    * Byl vylepšen výkon.

### <a name="june-24-2019"></a>24. června 2019

* [OrdinalV2 předem vytvořenou entitu](luis-reference-prebuilt-ordinal-v2.md) pro podporu řazení, jako je například další, předchozí a poslední. Pouze Anglická jazyková verze.

### <a name="may-6-2019---build-conference"></a>6. května 2019 – konference Build

Následující funkce byly vydány na konferenci Build 2019:

* [Verze Preview Průvodce migrací rozhraní V3 API](luis-migration-api-v3.md)
* [Vylepšený řídicí panel analýzy](luis-how-to-use-dashboard.md)
* [Vylepšené předem připravené domény](luis-reference-prebuilt-domains.md)
* [Dynamické seznam entit](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externí entity](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogy

[Architektura robotů](https://blog.botframework.com/)

## <a name="videos"></a>Videa

### <a name="2019-ignite-videos"></a>Ignite videa 2019

[Pokročilé modely přirozeného Language Understanding (NLU) využívající LUIS a Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>videa k sestavám 2019

[Jak používat konverzační službu Azure ke škálování vašeho podnikání pro novou generaci](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aktualizace služeb

[Oznámení o aktualizacích Azure pro Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
