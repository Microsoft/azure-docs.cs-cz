---
title: Glosář – LUIS
titleSuffix: Azure Cognitive Services
description: Glosář vysvětluje pojmy, které můžete narazit při práci se službou LUIS rozhraní API.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220949"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Jazyk porozumění běžným slovníkům a konceptům
Language Understanding (LUIS) Glosář vysvětluje podmínky, které můžete narazit při práci se službou LUIS rozhraní API.

## <a name="active-version"></a>Aktivní verze

Aktivní LUIS verze je verze, která bude přijímat všechny změny modelu. Pokud chcete provést změny verze, která není aktivní, musíte na portálu [Luis](luis-reference-regions.md) nejdřív nastavit tuto verzi jako aktivní.

## <a name="authoring"></a>Zdroj

Vytváření, Správa a nasazování [aplikace Luis](#luis-app), a to buď pomocí portálu [Luis](luis-reference-regions.md) nebo [rozhraní API pro vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087), je možné vytvářet.

## <a name="authoring-key"></a>Vytváření klíče

Dříve s názvem "Programátorské" klíč. Umožňuje vytvářet aplikace. Není možné použít u dotazů na provozní úrovni koncového bodu. Další informace najdete v tématu [omezení klíčů](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a>Soubor JSON dávkového testu

Dávkové testování je schopnost ověřit aktuální model aplikace v LUIS s konzistentní a známou sadou testů uživatele projevy. Dávkový test je definován v souboru ve [formátu JSON](luis-concept-batch-test.md#batch-file-format).

Viz také:
* [Koncepty](luis-concept-batch-test.md)
* [Postupy](luis-how-to-batch-test.md)
* [Kurz](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a>Spolupracovníka

Spolupracovníka/Přispěvatel není [vlastníkem](#owner) aplikace, ale má stejná oprávnění k přidávání, úpravám a odstraňování záměrů, entit a projevy.

## <a name="contributor"></a>Skupinou

Přispěvatel je stejný jako [spolupracovníka](#collaborator).

## <a name="descriptor"></a>Popisovač

Popisovač je [funkce](#features) použitá pro model v době školení, včetně [seznamů frází](#phrase-list) a [entit](#entity). 

## <a name="domain"></a>Domain

V kontextu LUIS je **doména** oblastí znalostí. Vaše doména je specifické pro oblast znalostní báze vaší aplikace. To může být obecné oblasti, jako je například agenta cestovní aplikace. Agent cestovní aplikace mohou být také specifické pro právě oblasti informace vaší společnosti, jako je například konkrétní zeměpisné umístění, jazyků a služeb.

## <a name="endpoint"></a>Služba

Adresa URL [koncového bodu Luis](https://go.microsoft.com/fwlink/?linkid=2092356) je místo, kam odesíláte dotazy Luis, až bude [aplikace Luis](#luis-app) vytvořená a publikovaná. Adresa URL koncového bodu obsahuje oblast publikované aplikace i ID aplikace. Koncový bod můžete najít na stránce **[klíčů a koncových bodů](luis-how-to-azure-subscription.md)** vaší aplikace, nebo můžete získat adresu URL koncového bodu z rozhraní API [získat informace o aplikaci](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

## <a name="entity"></a>Právnick

[Entity](luis-concept-entity-types.md) jsou důležitá slova v [projevy](luis-concept-utterance.md) , která popisují informace týkající se [záměru](luis-concept-intent.md)a někdy jsou pro ně zásadní. Entita je v podstatě datový typ v LUIS.

## <a name="f-measure"></a>Měření F

V [dávkovém testování](luis-interactive-test.md#batch-testing)je míra přesnosti testu.

## <a name="false-negative"></a>Falešně negativní (FN)

V [dávkovém testování](luis-interactive-test.md#batch-testing)představuje datový bod projevy, ve kterém vaše aplikace nesprávně předpovídá absenci cílového záměru nebo entity.

## <a name="false-positive"></a>Falešně pozitivní (FP)

V [dávkovém testování](luis-interactive-test.md#batch-testing)představuje datový bod projevy, ve kterém vaše aplikace nesprávně předpovězena existenci cílového záměru nebo entity.

## <a name="features"></a>Funkce

Ve službě Machine Learning je [funkce](luis-concept-feature.md) odlišující se vlastností a atributů dat, která systém sleduje.

## <a name="intent"></a>Úmysl

[Záměr](luis-concept-intent.md) představuje úkol nebo akci, kterou chce uživatel provést. Jedná o účely nebo cíl vyjádřené ve vstupu uživatele, jako je například rezervace letenky, placení faktury nebo hledání zpravodajskému článku. V LUIS záměru předpovědi vychází celý utterance. Entity, naproti tomu jsou části utterance.

## <a name="labeling"></a>Označování

Označení nebo označení je proces přidružení slova nebo fráze v [utterance](#utterance) záměru k [entitě](#entity) (DataType).

## <a name="luis-app"></a>Aplikace LUIS

Aplikace LUIS je kolekce jazykových modelů pro zpracování v přirozeném jazyce, včetně [záměrů](#intent), [entit](#entity)a popisků [projevy](#utterance).

## <a name="owner"></a>Owner

Každá aplikace má jednoho vlastníka, který je osoba, která vytvořili aplikaci. Vlastník může přidat [spolupracovníky](#collaborator).

## <a name="pattern"></a>Charakteristiky
Funkce předchozí vzor je nahrazena [vzorci](luis-concept-patterns.md). Použití vzorů zvyšte přesnost předpovědi tím, že poskytuje příklady méně školení.

## <a name="phrase-list"></a>Seznam frází

[Seznam frází](luis-concept-feature.md) obsahuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí se nacházet podobně (například názvy měst nebo produktů). Zaměňovat seznam se považuje synonym.

## <a name="prebuilt-domains"></a>Předem sestavená doména

[Předem vytvořená doména](luis-how-to-use-prebuilt-domains.md) je aplikace Luis konfigurovaná pro konkrétní doménu, jako je například domácí automatizace (HomeAutomation) nebo rezervace restaurace (RestaurantReservation). Záměrů, projevy a entity jsou nakonfigurované pro tuto doménu.

## <a name="prebuilt-entity"></a>Předem vytvořená entita

[Předem vytvořená entita](luis-prebuilt-entities.md) je Luis entit, která poskytuje běžné typy informací, jako je číslo, adresa URL a e-mail. Můžete přidat do svojí aplikace předem připravených entit.

## <a name="precision"></a>Číslic
V [dávkovém testování](luis-interactive-test.md#batch-testing)je přesnost (označovaná také jako kladná prediktivní hodnota) zlomkem relevantního projevy mezi načteným projevy.

## <a name="programmatic-key"></a>Programový klíč

Přejmenováno na [klíč pro vytváření](#authoring-key).

## <a name="publish"></a>Opětovn

Publikování znamená, že LUIS aktivní verze je dostupná na pracovním nebo produkčním [koncovém bodu](#endpoint).  

## <a name="quota"></a>Přidělení

LUIS kvóta je omezením [úrovně předplatného Azure](https://aka.ms/luis-price-tier). Služba LUIS kvóty může omezit pomocí obou požadavků za sekundu (HTTP 429 stav) a celkový počet požadavků za měsíc (HTTP 403 stav).

## <a name="recall"></a>Hled
Při [dávkovém testování](luis-interactive-test.md#batch-testing)je možné Luis zobecnit (označované také jako citlivost).

## <a name="semantic-dictionary"></a>Sémantický slovník
Sémantické slovník je k dispozici na stránce seznamu entity, jakož i stránka seznamu frázi. Sémantické slovníku nabízí návrhy slov na základě aktuálního oboru.

## <a name="sentiment-analysis"></a>Analýza mínění
Analýza mínění poskytuje kladné nebo záporné hodnoty projevy, které poskytuje [Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Neprojevení řeči

Dočištění řeči umožňuje službě řeči se naplní LUIS model.

## <a name="spelling-correction"></a>Oprava pravopisu

Povolte kontrolu pravopisu Bingu k opravovat slova s překlepem v projevy před předpovědi.

## <a name="starter-key"></a>Počáteční klíč

Bezplatný klíč, který se použije při prvním spuštění pomocí LUIS.

## <a name="structure"></a>Strukturované

Přidáním struktury k entitě získané na stroji můžete poskytnout dílčí komponenty s popisovači (funkcemi) a omezeními (regulární výraz nebo seznam entit).

## <a name="subscription-key"></a>Klíč předplatného

Klíč předplatného je klíč **koncového bodu předpovědi** přidružený ke službě Luis [, kterou jste vytvořili v Azure](luis-how-to-azure-subscription.md). Tento klíč není klíč pro [vytváření](#programmatic-key). Pokud máte klíč koncového bodu, by měla použít pro libovolný koncový bod požadavky na místo vytváření klíče. Aktuální klíč koncového bodu můžete zobrazit v adrese URL koncového bodu na konci [stránky **klíče a koncové body** ](luis-how-to-azure-subscription.md) na webu [Luis](luis-reference-regions.md) . Je to hodnota dvojice název/hodnota **klíče předplatného** .

## <a name="test"></a>Napaden

[Testování](luis-interactive-test.md#test-your-app) aplikace Luis znamená předání UTTERANCE do Luis a zobrazení výsledků JSON.

## <a name="timezoneoffset"></a>Posun časového pásma

Koncový bod zahrnuje timezoneOffset. Toto je počet v minutách, které chcete přidat nebo odebrat z datetimeV2 předem připravených entit. Například pokud je utterance "jaké čas je nyní?".", datetimeV2 vrátil je aktuální čas požadavku klienta. Pokud vaše žádost klienta pochází z robota nebo jiné aplikace, který není stejný jako uživatel svého robota, je třeba předat ve posun mezi robota a uživatelem.

Podívejte se [na téma Změna časového pásma předem sestavené entity datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Klíčové
Token je nejmenší jednotka, která mohou být označeny v entitě. Tokenizace je založena na [jazykové verzi](luis-language-support.md#tokenization)aplikace.

## <a name="train"></a>Průřez

Školení je proces výuky LUIS o jakýchkoli změnách v aktivní verzi od posledního školení.

## <a name="true-negative"></a>Pravý záporný (TN)

V [dávkovém testování](luis-interactive-test.md#batch-testing)představuje datový bod projevy, ve kterém vaše aplikace správně předpovídá absenci cílového záměru nebo entity.

## <a name="true-positive"></a>Pravdivý pozitivní (TP)

V [dávkovém testování](luis-interactive-test.md#batch-testing)představuje datové body projevy, ve kterém vaše aplikace správně předpovězena existenci cílového záměru nebo entity.

## <a name="utterance"></a>Utterance

Utterance je přirozený jazyk frázi, jako je například "book 2 lístky pro Seattle další úterý". Příklad projevů budou přidány do záměr.

## <a name="version"></a>Znění

[Verze](luis-how-to-manage-versions.md) Luis je konkrétní datový model PŘIDRUŽENÝ k ID aplikace Luis a publikovanému koncovému bodu. Každá aplikace LUIS má alespoň jednu verzi.
