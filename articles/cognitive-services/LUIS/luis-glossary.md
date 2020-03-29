---
title: Glosář - LUIS
titleSuffix: Azure Cognitive Services
description: Glosář vysvětluje termíny, které se mohou setkat při práci se službou LUIS API.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220949"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Slovníček porozumění jazykům společné slovní zásoby a pojmů
Glosář pro porozumění jazykům (LUIS) vysvětluje termíny, se kterými se můžete setkat při práci se službou LUIS API.

## <a name="active-version"></a><a name="active-version"></a>Aktivní verze

Aktivní verze LUIS je verze, která obdrží všechny změny modelu. Na portálu [LUIS,](luis-reference-regions.md) pokud chcete provést změny ve verzi, která není aktivní verze, je třeba nejprve nastavit tuto verzi jako aktivní.

## <a name="authoring"></a><a name="authoring"></a>Vytváření obsahu

Vytváření je možnost vytvářet, spravovat a nasazovat [aplikaci LUIS](#luis-app), a to buď pomocí portálu [LUIS](luis-reference-regions.md) nebo [vytváření api](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a><a name="authoring-key"></a>Klíč pro vytváření

Dříve pojmenovaný "Programový" klíč. Používá se k vytváření aplikace. Nepoužívá se pro dotazy koncového bodu na úrovni výroby. Další informace naleznete [v tématu Omezení klíčů](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Dávkový test Soubor JSON

Dávkové testování je možnost ověřit aktuální model aplikace LUIS s konzistentní a známé testovací sady projevy uživatelů. Dávkový test je definován v [souboru ve formátu JSON](luis-concept-batch-test.md#batch-file-format).

Viz také:
* [Koncepty](luis-concept-batch-test.md)
* [Postupy](luis-how-to-batch-test.md)
* [Tutorial](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Spolupracovník

Spolupracovník/přispěvatel, není [vlastníkem](#owner) aplikace, ale má stejná oprávnění k přidávání, úpravám a odstraňování záměrů, entit, promluv.

## <a name="contributor"></a><a name="contributor"></a>Přispěvatel

Přispěvatel je totéž jako [spolupracovník](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Popisovač

Deskriptor je [funkce](#features) aplikovaná na model v době školení, včetně [seznamů frází](#phrase-list) a [entit](#entity). 

## <a name="domain"></a><a name="domain"></a>Domény

V kontextu LUIS je **doména** oblastí znalostí. Vaše doména je specifická pro oblast znalostí aplikace. Může se jedná o obecnou oblast, například aplikaci cestovní kanceláře. Aplikace cestovní kanceláře může být také specifická pouze pro oblasti informací pro vaši společnost, jako jsou konkrétní zeměpisné polohy, jazyky a služby.

## <a name="endpoint"></a><a name="endpoint"></a>Koncový bod

Adresa URL [koncového bodu LUIS](https://go.microsoft.com/fwlink/?linkid=2092356) je místo, kde odesíláte dotazy LUIS po vytvoření a publikování [aplikace LUIS.](#luis-app) Adresa URL koncového bodu obsahuje oblast publikované aplikace a id aplikace. Koncový bod najdete na stránce **[Klíče a koncové body](luis-how-to-azure-subscription.md)** aplikace nebo můžete získat adresu URL koncového bodu z rozhraní API pro získání informací o [aplikaci.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)

## <a name="entity"></a><a name="entity"></a>Entita

[Entity](luis-concept-entity-types.md) jsou důležitá slova v [projevech,](luis-concept-utterance.md) které popisují informace důležité pro [záměr](luis-concept-intent.md)a někdy jsou pro něj nezbytné. Entita je v podstatě datový typ v LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>F-opatření

Při [testování šarží](luis-interactive-test.md#batch-testing)se měří přesnost zkoušky.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Falešně negativní (FN)

V [dávkovétestování](luis-interactive-test.md#batch-testing), datové body představují projevy, ve kterém vaše aplikace nesprávně předpověděl absence cílovézáměr/entity.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Falešně pozitivní (FP)

V [dávkovétestování](luis-interactive-test.md#batch-testing), datové body představují projevy, ve kterém vaše aplikace nesprávně předpověděl existenci cílovézáměr/entity.

## <a name="features"></a><a name="features"></a>Funkce

Ve strojovém učení je [funkce](luis-concept-feature.md) rozlišovací vlastností nebo atributem dat, které váš systém sleduje.

## <a name="intent"></a><a name="intent"></a>Záměr

[Záměr](luis-concept-intent.md) představuje úkol nebo akci, kterou chce uživatel provést. Jedná se o účel nebo cíl vyjádřený ve vstupu uživatele, jako je rezervace letu, zaplacení faktury nebo nalezení novinového článku. V LUIS záměr předpověď je založena na celý utterance. Entity, pro srovnání, jsou kusy utterance.

## <a name="labeling"></a><a name="labeling"></a>Označování

Označování nebo označování je proces přidružování slova nebo fráze v [utterance](#utterance) záměru s [entitou](#entity) (datový typ).

## <a name="luis-app"></a><a name="luis-app"></a>Aplikace LUIS

Aplikace LUIS je kolekce jazykových modelů pro zpracování přirozeného jazyka včetně [záměrů](#intent), [entit](#entity)a popisek [projevy](#utterance).

## <a name="owner"></a><a name="owner"></a>Vlastník

Každá aplikace má jednoho vlastníka, který je osobou, která aplikaci vytvořila. Vlastník může přidat [spolupracovníky](#collaborator).

## <a name="patterns"></a><a name="pattern"></a>Vzory
Předchozí funkce Pole je nahrazena [polem](luis-concept-patterns.md). Pomocí vzorů ke zlepšení přesnosti předpovědi tím, že poskytuje méně příklady školení.

## <a name="phrase-list"></a><a name="phrase-list"></a>Seznam frází

[Seznam frází](luis-concept-feature.md) obsahuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí se s nimi zacházet podobně (například názvy měst nebo produktů). Zaměnitelný seznam je považován za synonyma.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Předem vyestavěná doména

[Předem připravená doména](luis-how-to-use-prebuilt-domains.md) je aplikace LUIS nakonfigurovaná pro konkrétní doménu, jako je domácí automatizace (HomeAutomation) nebo rezervace restaurací (RestaurantReservation). Záměry, projevy a entity jsou konfigurovány pro tuto doménu.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Předem sestavená entita

[Předem připravená entita](luis-prebuilt-entities.md) je entita, která luis poskytuje běžné typy informací, jako je číslo, adresa URL a e-mail. Můžete přidat předem sestavenou entitu do aplikace.

## <a name="precision"></a><a name="precision"></a>Přesnost
V [dávkové testování](luis-interactive-test.md#batch-testing), přesnost (také volal pozitivní prediktivní hodnota) je zlomek relevantní projevy mezi načtené projevy.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Programový klíč

Přejmenována na [klíč pro vytváření](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publikovat

Publikování znamená zpřístupnění aktivní verze luis v pracovním nebo [produkčním koncovém bodě](#endpoint).  

## <a name="quota"></a><a name="quota"></a>Kvóta

Kvóta SLUŽBY LUIS je omezení [úrovně předplatného Azure](https://aka.ms/luis-price-tier). Kvóta SLUŽBY LUIS může být omezena požadavky za sekundu (stav HTTP 429) a celkovým počtem požadavků za měsíc (stav HTTP 403).

## <a name="recall"></a><a name="recall"></a>Připomenout
V [dávkové testování](luis-interactive-test.md#batch-testing), odvolání (také známý jako citlivost), je schopnost LUIS generalizovat.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Sémantický slovník
Sémantický slovník je k dispozici na stránce entity Seznam a také na stránce se seznamem frází. Sémantický slovník poskytuje návrhy slov na základě aktuálního oboru.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Analýza mínění
Analýza mínění poskytuje kladné nebo záporné hodnoty promluv poskytovaných [analýzou textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Řečové priming

Příprava řeči umožňuje, aby se vaše služba řeči napravovací zařízení s modelem LUIS.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Oprava pravopisu

Povolte kontrolu pravopisu Bing opravit chybně napsaná slova v projevy před předpověď.

## <a name="starter-key"></a><a name="starter-key"></a>Startovací klávesa

Volný klíč použít při prvním spuštění pomocí LUIS.

## <a name="structure"></a><a name="structure"></a>Struktura

Přidejte strukturu do entity naučené počítačem, která poskytuje dílčím součástem popisovače (prvky) a omezení (regulární výraz nebo entity seznamu).

## <a name="subscription-key"></a><a name="subscription-key"></a>Klíč předplatného

Klíč předplatného je **klíč koncového bodu předpovědi** přidružený ke službě [LUIS, kterou jste vytvořili v Azure](luis-how-to-azure-subscription.md). Tento klíč není [provázkovací mač](#programmatic-key). Pokud máte klíč koncového bodu, měl by být použit pro všechny požadavky koncového bodu namísto vývojového klíče. Aktuální klíč koncového bodu můžete zobrazit uvnitř adresy URL koncového bodu v dolní části stránky [ **Klíče a koncové body** ](luis-how-to-azure-subscription.md) na webu [LUIS.](luis-reference-regions.md) Jedná se o hodnotu páru název/hodnota **klíče předplatného.**

## <a name="test"></a><a name="test"></a>Test

[Testování](luis-interactive-test.md#test-your-app) aplikace LUIS znamená předání utterance luis a zobrazení výsledků JSON.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Posun časového pásma

Koncový bod zahrnuje timezoneOffset. Toto číslo v minutách, které chcete přidat nebo odebrat z entity datetimeV2 předestavěné. Například pokud utterance je "jaký čas je to teď?", datetimeV2 vrátil je aktuální čas pro požadavek klienta. Pokud váš požadavek klienta pochází z robota nebo jiné aplikace, která není stejná jako uživatel vašeho robota, měli byste předat posun mezi robotem a uživatelem.

Viz [Změna časového pásma předem sestavené entity datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Token
Token je nejmenší jednotka, která může být označena v entitě. Tokenizace je založena na [jazykové verzi](luis-language-support.md#tokenization)aplikace .

## <a name="train"></a><a name="train"></a>Trénování

Školení je proces výuky LUIS o všech změnách aktivní verze od posledního školení.

## <a name="true-negative-tn"></a><a name="true-negative"></a>True negativní (TN)

V [dávkovétestování](luis-interactive-test.md#batch-testing), datové body představují projevy, ve kterém vaše aplikace správně předpověděl nepřítomnost cílovézáměr/entity.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Pravda pozitivní (TP)

V [dávkovétestování](luis-interactive-test.md#batch-testing), datové body představují projevy, ve kterém vaše aplikace správně předpověděl existenci cílovézáměr/entity.

## <a name="utterance"></a><a name="utterance"></a>Promluva

Utterance je přirozený jazyk fráze, jako je "rezervovat 2 vstupenky do Seattlu příští úterý". Příklad projevy jsou přidány do záměru.

## <a name="version"></a><a name="version"></a>Verze

[Verze](luis-how-to-manage-versions.md) LUIS je specifický datový model přidružený k ID aplikace LUIS a publikovaného koncového bodu. Každá aplikace LUIS má alespoň jednu verzi.
