---
title: Glosář – LUIS
titleSuffix: Azure Cognitive Services
description: Glosář vysvětluje výrazy, se kterými se můžete setkat při práci se službou LUIS API.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: f764b0d42e08e68b45d49a5aae8542f05707ccde
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099355"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Jazyk porozumění běžným slovníkům a konceptům
Glosář Language Understanding (LUIS) vysvětluje výrazy, se kterými se můžete setkat při práci se službou LUIS API.

## <a name="active-version"></a><a name="active-version"></a>Aktivní verze

Aktivní verze LUIS je verze, která přijímá změny modelu. Pokud chcete provést změny verze, která není aktivní, musíte na portálu [Luis](luis-reference-regions.md) nejdřív nastavit tuto verzi jako aktivní.

## <a name="authoring"></a><a name="authoring"></a>Vytváření obsahu

Vytváření, Správa a nasazování [aplikace Luis](#luis-app), a to buď pomocí portálu [Luis](luis-reference-regions.md) nebo [rozhraní API pro vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087), je možné vytvářet.

## <a name="authoring-key"></a><a name="authoring-key"></a>Vytváření klíče

Dříve pojmenovaný klíč "program". Slouží k vytváření aplikací. Nepoužívá se pro dotazy na koncové body na úrovni výroby. Další informace najdete v tématu [omezení klíčů](luis-limits.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Soubor JSON dávkového testu

Dávkové testování je schopnost ověřit aktuální model aplikace v LUIS s konzistentní a známou sadou testů uživatele projevy. Dávkový test je definován v souboru ve [formátu JSON](luis-concept-batch-test.md#batch-file-format).

Viz také:
* [Koncepty](luis-concept-batch-test.md)
* [Postupy](luis-how-to-batch-test.md)
* [Kurz](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Spolupracovník

Spolupracovníka/Přispěvatel není [vlastníkem](#owner) aplikace, ale má stejná oprávnění k přidávání, úpravám a odstraňování záměrů, entit a projevy.

## <a name="contributor"></a><a name="contributor"></a>Přispěvatel

Přispěvatel je stejný jako [spolupracovníka](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Popisovač

Popisovač je [funkce](#features) použitá pro model v době školení, včetně [seznamů frází](#phrase-list) a [entit](#entity).

## <a name="domain"></a><a name="domain"></a>Domain

V kontextu LUIS je **doména** oblastí znalostí. Vaše doména je specifická pro vaši oblast znalostí vaší aplikace. Může to být obecná oblast, jako je například aplikace cestovního agenta. Aplikace cestovního agenta může být také specifická jenom pro oblasti informací vaší společnosti, jako jsou určitá geografická umístění, jazyky a služby.

## <a name="endpoint"></a><a name="endpoint"></a>Služba

Adresa URL [koncového bodu Luis](https://go.microsoft.com/fwlink/?linkid=2092356) je místo, kam odesíláte dotazy Luis, až bude [aplikace Luis](#luis-app) vytvořená a publikovaná. Adresa URL koncového bodu obsahuje oblast publikované aplikace i ID aplikace. Koncový bod můžete najít na stránce **[klíčů a koncových bodů](luis-how-to-azure-subscription.md)** vaší aplikace, nebo můžete získat adresu URL koncového bodu z rozhraní API [získat informace o aplikaci](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

## <a name="entity"></a><a name="entity"></a>Entita

[Entity](luis-concept-entity-types.md) jsou důležitá slova v [projevy](luis-concept-utterance.md) , která popisují informace týkající se [záměru](luis-concept-intent.md)a někdy jsou pro ně zásadní. Entita je v podstatě datovým typem v LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>Měření F

V [dávkovém testování](luis-interactive-test.md#batch-testing)je míra přesnosti testu.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Falešně negativní (FN)

V [dávkovém testování](luis-interactive-test.md#batch-testing)představuje datový bod projevy, ve kterém vaše aplikace nesprávně předpovídá absenci cílového záměru nebo entity.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Falešně pozitivní (FP)

V [dávkovém testování](luis-interactive-test.md#batch-testing)představuje datový bod projevy, ve kterém vaše aplikace nesprávně předpovězena existenci cílového záměru nebo entity.

## <a name="features"></a><a name="features"></a>Funkce

Ve službě Machine Learning je [funkce](luis-concept-feature.md) odlišující se vlastností a atributů dat, která systém sleduje.

## <a name="intent"></a><a name="intent"></a>Úmysl

[Záměr](luis-concept-intent.md) představuje úkol nebo akci, kterou chce uživatel provést. Jedná se o účel nebo cíl vyjádřený ve vstupu uživatele, jako je například rezervace letu, placení faktury nebo hledání článku s novinkami. V LUIS je předpověď záměrů založena na celé utterance. Entity, podle porovnání, jsou části utterance.

## <a name="labeling"></a><a name="labeling"></a>Označování

Označení nebo označení je proces přidružení slova nebo fráze v [utterance](#utterance) záměru k [entitě](#entity) (DataType).

## <a name="luis-app"></a><a name="luis-app"></a>Aplikace LUIS

Aplikace LUIS je kolekce jazykových modelů pro zpracování v přirozeném jazyce, včetně [záměrů](#intent), [entit](#entity)a popisků [projevy](#utterance).

## <a name="owner"></a><a name="owner"></a>Vlastník

Každá aplikace má jednoho vlastníka, který je osoba, která aplikaci vytvořila. Vlastník může přidat [spolupracovníky](#collaborator).

## <a name="patterns"></a><a name="pattern"></a>Vzory
Funkce předchozí vzor je nahrazena [vzorci](luis-concept-patterns.md). Pomocí vzorů můžete zlepšit přesnost předpovědi tím, že poskytnete méně příkladů školení.

## <a name="phrase-list"></a><a name="phrase-list"></a>Seznam frází

[Seznam frází](luis-concept-feature.md) obsahuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí se nacházet podobně (například názvy měst nebo produktů). Seznam proměnitelné se považuje za synonyma.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Předem sestavená doména

[Předem vytvořená doména](luis-how-to-use-prebuilt-domains.md) je aplikace Luis konfigurovaná pro konkrétní doménu, jako je například domácí automatizace (HomeAutomation) nebo rezervace restaurace (RestaurantReservation). Záměry, projevy a entity jsou nakonfigurovány pro tuto doménu.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Předem vytvořená entita

[Předem vytvořená entita](luis-prebuilt-entities.md) je Luis entit, která poskytuje běžné typy informací, jako je číslo, adresa URL a e-mail. Rozhodnete se do své aplikace přidat předem vytvořenou entitu.

## <a name="precision"></a><a name="precision"></a>Přesnost
V [dávkovém testování](luis-interactive-test.md#batch-testing)je přesnost (označovaná také jako kladná prediktivní hodnota) zlomkem relevantního projevy mezi načteným projevy.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Programový klíč

Přejmenováno na [klíč pro vytváření](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publikování

Publikování znamená, že LUIS aktivní verze je dostupná na pracovním nebo produkčním [koncovém bodu](#endpoint).

## <a name="quota"></a><a name="quota"></a>Kvóta

LUIS kvóta je omezením [úrovně předplatného Azure](https://aka.ms/luis-price-tier). Kvóta LUIS může být omezená oběma požadavky za sekundu (stav HTTP 429) a celkovými požadavky v měsíci (stav HTTP 403).

## <a name="recall"></a><a name="recall"></a>Hled
Při [dávkovém testování](luis-interactive-test.md#batch-testing)je možné Luis zobecnit (označované také jako citlivost).

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Sémantický slovník
Sémantický slovník je k dispozici na stránce seznam entit a také na stránce seznam frází. Sémantický slovník poskytuje návrhy slov na základě aktuálního rozsahu.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Analýza mínění
Analýza mínění poskytuje kladné nebo záporné hodnoty projevy, které poskytuje [Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Neprojevení řeči

Prokládání řeči umožňuje, aby vaše služba pro rozpoznávání řeči byla v LUIS modelu.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Oprava pravopisu

Povolí kontrolu pravopisu Bingu, která před předpověď opraví nesprávně napsaná slova v projevy.

## <a name="starter-key"></a><a name="starter-key"></a>Počáteční klíč

Bezplatný klíč, který se použije při prvním spuštění pomocí LUIS.

## <a name="structure"></a><a name="structure"></a>Struktura

Přidáním struktury k entitě získané na stroji můžete poskytnout dílčí komponenty s popisovači (funkcemi) a omezeními (regulární výraz nebo seznam entit).

## <a name="subscription-key"></a><a name="subscription-key"></a>Klíč předplatného

Klíč předplatného je klíč **koncového bodu předpovědi** přidružený ke službě Luis [, kterou jste vytvořili v Azure](luis-how-to-azure-subscription.md). Tento klíč není klíč pro [vytváření](#programmatic-key). Pokud máte klíč koncového bodu, měl by se místo vytváření klíče použít pro všechny požadavky na koncový bod. Aktuální klíč koncového bodu můžete zobrazit v adrese URL koncového bodu na konci [stránky **klíče a koncové body** ](luis-how-to-azure-subscription.md) na webu [Luis](luis-reference-regions.md) . Je to hodnota dvojice název/hodnota **klíče předplatného** .

## <a name="test"></a><a name="test"></a>Test

[Testování](luis-interactive-test.md#test-your-app) aplikace Luis znamená předání UTTERANCE do Luis a zobrazení výsledků JSON.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Posun časového pásma

Koncový bod zahrnuje timezoneOffset. Toto je číslo v minutách, po které chcete přidat nebo odebrat z předdefinované entity datetimeV2. Například pokud je utterance "jaký čas je nyní?", vrácený datetimeV2 je aktuální čas pro požadavek klienta. Pokud žádost klienta pochází z robota nebo jiné aplikace, která není stejná jako uživatel vaší robota, měli byste předávat posun od robota a uživatele.

Podívejte se [na téma Změna časového pásma předem sestavené entity datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Klíčové
Token je nejmenší jednotka, která může být označena v entitě. Tokenizace je založena na [jazykové verzi](luis-language-support.md#tokenization)aplikace.

## <a name="train"></a><a name="train"></a>Trénování

Školení je proces výuky LUIS o jakýchkoli změnách v aktivní verzi od posledního školení.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Pravý záporný (TN)

V [dávkovém testování](luis-interactive-test.md#batch-testing)představuje datový bod projevy, ve kterém vaše aplikace správně předpovídá absenci cílového záměru nebo entity.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Pravdivý pozitivní (TP)

V [dávkovém testování](luis-interactive-test.md#batch-testing)představuje datové body projevy, ve kterém vaše aplikace správně předpovězena existenci cílového záměru nebo entity.

## <a name="utterance"></a><a name="utterance"></a>Promluva

Utterance je fráze přirozeného jazyka, například lístky Book 2 do Seattlu Next úterý. Do záměru se přidají například projevy.

## <a name="version"></a><a name="version"></a>Znění

[Verze](luis-how-to-manage-versions.md) Luis je konkrétní datový model PŘIDRUŽENÝ k ID aplikace Luis a publikovanému koncovému bodu. Každá aplikace LUIS má alespoň jednu verzi.
