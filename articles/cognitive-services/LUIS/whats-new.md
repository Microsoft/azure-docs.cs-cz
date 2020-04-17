---
title: Co je nového – jazykové porozumění (LUIS)
description: Tento článek je pravidelně aktualizován s novinkami o Azure Cognitive Services language understanding API.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 6334b157f68f09f2b165c6073ba63f45a0caf3c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538014"
---
# <a name="whats-new-in-language-understanding"></a>Co je nového v jazykovém porozumění

Přečtěte si, co je ve službě nového. Mezi tyto položky patří poznámky k verzi, videa, příspěvky na blogu a další typy informací. Záložka tuto stránku udržovat aktuální se službou.

## <a name="release-notes"></a>Poznámky k verzi

### <a name="march-2020"></a>Březen 2020

* TLS 1.2 je nyní vynucena pro všechny požadavky HTTP pro tuto službu. Další informace najdete v [tématu Azure Cognitive Services security](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4. listopadu 2019 - Zapalte

* Video – [pokročilé modely pro porozumění přirozenému jazyku (NLU) pomocí luisa a služby Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Vyšší produktivita vývojářů
    * Obecná dostupnost našeho [koncového bodu předpovědi V3](luis-migration-api-v3.md).
    * Možnost importu a exportu aplikací ve formátu .lu[(LUDown).](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) To dláždí cestu pro efektivní proces CI/CD.
* Rozšíření jazyka
    * [Arabština a hindština](luis-language-support.md) ve veřejné verzi preview.
* Předem připravené modely
    * [Předem sestavené domény](luis-reference-prebuilt-domains.md) jsou nyní obecně dostupné (GA)
    * Japonské [předem sestavené entity](luis-reference-prebuilt-entities.md#japanese-entity-support) – stáří, měna, číslo a procento nejsou ve V3 podporovány.
    * Italské [předem sestavené entity](luis-reference-prebuilt-entities.md#italian-entity-support) – stáří, měna, dimenze, počet a procentuální rozlišení změněno z V2.
* Vylepšené uživatelské prostředí v [preview.luis.ai portálu](https://preview.luis.ai) – vylepšené prostředí pro vytváření a ladění složitých modelů. Vyzkoušejte výukové programy pro náhled portálu:
    * [Pouze záměry](tutorial-intents-only.md)
    * [Rozkládatelná strojově naučená entita](tutorial-machine-learned-entity.md)
* Rozvíjet možnosti porozumění jazykům – [vytvářet sofistikované jazykové modely](luis-concept-entity-types.md) s menším úsilím.
* Definujte funkce strojového učení na úrovni modelu a povolte modely, které mají být použity jako signály pro jiné modely, například pomocí entit jako prvků záměrů a jiných entit.
* Nové, rozšířené [limity](luis-boundaries.md) - vyšší maximum pro seznamy frází a celkové fráze, nový model jako omezení funkcí
* Extrahujte informace z textu ve formátu struktury hluboké hierarchie, díky čemuž jsou aplikace konverzace výkonnější.

    ![image entity naučené strojem](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3. září 2019

* Zdroj pro vytváření Azure – [migrovat nyní](luis-migration-authoring.md).
    * 500 aplikací na prostředek Azure
    * 100 verzí na aplikaci
* Turecká podpora předbudovařů
* Italská podpora pro datetimeV2

### <a name="july-23-2019"></a>23. července 2019July 23, 2019

* Aktualizace [textu pro rozpoznávání na](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) 1.2.3
    * Nástroje pro rozpoznávání věku, teploty, dimenze a měny v italštině
    * Zlepšení v uznání svátků v angličtině správně vypočítat data založená na Díkůvzdání.
    * Vylepšení ve francouzštině DateTime snížit falešně pozitivních hodnot non-Date a non-Time entity.
    * Podpora pro kalendář/školní/fiskální rok a zkratky v angličtině DateRange.
    * Vylepšené rozpoznávání telefonních čísel v čínštině a japonštině.
    * Vylepšená podpora numberrange v angličtině.
    * Byl vylepšen výkon.

### <a name="june-24-2019"></a>24. června 2019

* [OrdinalV2 předem vytvořená entita](luis-reference-prebuilt-ordinal-v2.md) pro podporu řazení, jako je například další, předchozí a poslední. Pouze anglická kultura.

### <a name="may-6-2019---build-conference"></a>6. května 2019 - //Build Conference

Na konferenci Build 2019 byly vydány následující funkce:

* [Náhled průvodce migrací rozhraní API V3](luis-migration-api-v3.md)
* [Vylepšený řídicí panel analýzy](luis-how-to-use-dashboard.md)
* [Vylepšené předem vyestavěné domény](luis-reference-prebuilt-domains.md)
* [Entity dynamického seznamu](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externí subjekty](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogy

[Architektura robotů](https://blog.botframework.com/)

## <a name="videos"></a>Videa

### <a name="2019-ignite-videos"></a>2019 Zapalte videa

[Pokročilé modely znalostního jazyka (NLU) využívající služby LUIS a služby Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Vytváření videí

[Jak používat Azure Conversational AI k škálování vašeho podnikání pro příští generaci](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aktualizace služeb

[Oznámení o aktualizacích Azure pro služby Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
