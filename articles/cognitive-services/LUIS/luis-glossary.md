---
title: Glosář služby Language Understanding (LUIS) API | Dokumentace Microsoftu
description: Glosář vysvětluje pojmy, které můžete narazit při práci se službou LUIS rozhraní API.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 3016d1318d031494057f4a8ce61af37576a7c4f2
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346803"
---
# <a name="glossary"></a>Glosář

## <a name="active-version"></a>Aktivní verze

Aktivní LUIS verze je verze, která bude přijímat všechny změny modelu. V [LUIS](luis-reference-regions.md) webu, pokud chcete změnit verzi, která není aktivní verze, musíte nejdřív nastavit tuto verzi jako aktivní. 

## <a name="authoring"></a>Pro tvorbu

Vytváření obsahu je schopnost vytvářet, spravovat a nasazovat [aplikace LUIS](#luis-app), buď pomocí [LUIS](luis-reference-regions.md) webu nebo [rozhraní API pro vytváření](https://aka.ms/luis-authoring-api). 

## <a name="authoring-key"></a>Klíč pro tvorbu

Dříve s názvem "Programátorské" klíč. Umožňuje vytvářet aplikace. Není možné použít u dotazů na provozní úrovni koncového bodu. Další informace najdete v tématu [klíče omezení](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Dávkový soubor JSON textu

Dávkový soubor je pole JSON. Každý prvek v poli má tři vlastnosti: `text`, `intent`, a `entities`. `entities` Vlastnost je pole. Pole může být prázdný. Pokud `entities` pole není prázdný, je potřeba přesně zjistit entity.

```JSON
[
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book a flight to orlando on the 25th",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "orlando",
                "type": "Location",
                "startIndex": 18,
                "endIndex": 25
            }
        ]
    }
]

```


## <a name="collaborator"></a>Spolupracovníka

Spolupracovníka se nenachází [vlastníka](#owner) aplikace, ale má stejná oprávnění pro přidání, úpravy a odstraňování tříd Intent entity, projevy.

## <a name="currently-editing"></a>Právě se upravuje

Stejné jako [aktivní verze](#active-version)

## <a name="domain"></a>Domény

V rámci služby LUIS **domény** je oblast znalostní báze. Vaše doména je specifické pro oblast znalostní báze vaší aplikace. To může být obecné oblasti, jako je například agenta cestovní aplikace. Agent cestovní aplikace mohou být také specifické pro právě oblasti informace vaší společnosti, jako je například konkrétní zeměpisné umístění, jazyků a služeb. 

## <a name="endpoint"></a>Koncový bod

[Koncový bod služby LUIS](https://aka.ms/luis-endpoint-apis) je adresa URL, kde odesílání dotazů LUIS po [aplikace LUIS](#luis-app) je vytvořili a publikovali. Adresa URL koncového bodu obsahuje oblast publikované aplikace i ID aplikace. Koncový bod můžete najít na **[publikovat](luis-how-to-publish-app.md)** stránky vaší aplikace v tabulce prostředky a klíčů nebo je můžete získat adresu URL koncového bodu z [získat informace o aplikaci](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) rozhraní API.

Příkladem koncového bodu vypadá takto:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Parametr řetězce dotazu|description|
|--|--|
|oblast| [publikované oblasti](luis-reference-regions.md#publishing-regions) |
|ID aplikace | ID aplikace LUIS |
|ID předplatného | Služba LUIS klíče koncového bodu (předplatné) vytvořené na webu Azure portal |
|q | Utterance |
|timezoneOffset| minutes|

## <a name="entity"></a>Entity

[Entity](luis-concept-entity-types.md) jsou důležité slova v [projevy](luis-concept-utterance.md) , které popisují informace relevantní pro [záměr](luis-concept-intent.md), a někdy jsou nezbytné k němu. Entita je v podstatě datový typ v LUIS. 

## <a name="f-measure"></a>F – – Měrná jednotka

V [batch testování][batch-testing], míru testu přesnosti.

## <a name="false-negative"></a>Záporné hodnoty false (TN)

V [batch testování][batch-testing], datových bodů představují projevy, ve kterých aplikace správně předpovědět neexistence cílové záměr na entitu.

## <a name="false-positive"></a>Falešně pozitivní (TP)

V [batch testování][batch-testing], datových bodů představují projevy, ve kterých vaše aplikace správně předpovědět existenci cílové záměr na entitu.

## <a name="features"></a>Funkce

Ve službě machine learning [funkce](luis-concept-feature.md) rozlišovací vlastností nebo atribut data, která dodržuje vašeho systému.

## <a name="intent"></a>Záměr

[Záměr](luis-concept-intent.md) představuje úloh nebo uživatel chce provést akci. Jedná o účely nebo cíl vyjádřené ve vstupu uživatele, jako je například rezervace letenky, placení faktury nebo hledání zpravodajskému článku. V LUIS záměru předpovědi vychází celý utterance. Entity, naproti tomu jsou části utterance.

## <a name="labeling"></a>Používání popisků

Používání popisků je proces přiřazení slova nebo fráze ve záměru [utterance](#utterance) s [entity](#entity) (datový typ). 

## <a name="luis-app"></a>Aplikace LUIS

Aplikace LUIS je trénovaného datový model pro zpracování přirozeného jazyka včetně [záměry](#intent), [entity](#entity)a s popiskem [projevy](#utterance).

## <a name="owner"></a>Vlastník

Každá aplikace má jednoho vlastníka, který je osoba, která vytvořili aplikaci. Můžete přidat vlastníka [spolupracovníci](#collaborator).

## <a name="pattern"></a>Vzory
Nahradí předchozí funkce vzor [vzory](luis-concept-patterns.md). Použití vzorů zvyšte přesnost předpovědi tím, že poskytuje příklady méně školení. 

## <a name="phrase-list"></a>Seznam frází

A [seznam frází](luis-concept-feature.md#what-is-a-phrase-list-feature) obsahuje skupinu hodnot (slova nebo fráze), která patří do stejné třídy a musí být zacházeno podobně (například názvy města nebo produkty). Zaměňovat seznam se považuje synonym. 

## <a name="prebuilt-domains"></a>Předem připravená doména

A [předem připravených domény](luis-how-to-use-prebuilt-domains.md) je aplikace LUIS nakonfigurované pro konkrétní doménu třeba domácí automatizace (HomeAutomation) nebo rezervace restaurace (RestaurantReservation). Záměrů, projevy a entity jsou nakonfigurované pro tuto doménu. 

## <a name="prebuilt-entity"></a>Předem připravených entit

A [předem připravených entit](luis-prebuilt-entities.md) je entita LUIS poskytuje pro běžné typy informace, jako je číslo, adresy URL a e-mailu. Můžete přidat do svojí aplikace předem připravených entit. 

## <a name="precision"></a>Přesnost
V [batch testování][batch-testing], přesnost (také nazývané kladného prediktivní) je část relevantní projevy mezi načtený projevy.

## <a name="programmatic-key"></a>Klíče prostřednictvím kódu programu

Přejmenováno na [vytváření klíč](#authoring-key). 

## <a name="publish"></a>Publikování

Publikování znamená přijetí LUIS [aktivní verze](#active-version) k dispozici na pracovní nebo produkční [koncový bod](#endpoint).  

## <a name="quota"></a>Kvóta

Služba LUIS kvóta je omezení [úroveň předplatného Azure](https://aka.ms/luis-price-tier). Služba LUIS kvóty může omezit pomocí obou požadavků za sekundu (HTTP 429 stav) a celkový počet požadavků za měsíc (HTTP 403 stav). 

## <a name="recall"></a>Odvolání
V [batch testování][batch-testing], si možná Vzpomínáte (označovaný také jako citlivosti), je schopnost LUIS k zobecnění. 

## <a name="semantic-dictionary"></a>Sémantické slovník
Sémantické slovník je k dispozici na stránce seznamu entity, jakož i stránka seznamu frázi. Sémantické slovníku nabízí návrhy slov na základě aktuálního oboru.

## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení
Analýza subjektivního hodnocení poskytuje kladné nebo záporné hodnoty projevy poskytované [rozhraní Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/). 

## <a name="speech-priming"></a>Dočištění řeči

Dočištění řeči umožňuje službě řeči se naplní LUIS model. Zobrazit [povolit řeči dočištění ](luis-how-to-publish-app.md#enable-speech-priming).

## <a name="spelling-correction"></a>Opravy pravopisu

Na stránce publikování povolit [kontrolu pravopisu Bingu](luis-how-to-publish-app.md#enable-bing-spell-checker) k opravovat slova s překlepem v projevy před předpovědi. 

## <a name="starter-key"></a>Klíč Starter

Stejné jako [programový klíč](#programmatic-key), přejmenováno na klíč pro vytváření obsahu.

## <a name="subscription-key"></a>Klíč předplatného

Klíč předplatného je **koncový bod** klíč související se službou LUIS [jste vytvořili v Azure](luis-how-to-azure-subscription.md). Tento klíč není [vytváření klíč](#programmatic-key). Pokud máte klíč koncového bodu, by měla použít pro libovolný koncový bod požadavky na místo vytváření klíče. Zobrazí aktuální klíč koncového bodu uvnitř adresu URL koncového bodu v dolní části [ **publikovat aplikace** stránky](luis-how-to-publish-app.md) v [LUIS](luis-reference-regions.md) webu. Je hodnota **klíč předplatného** dvojici název/hodnota. 

## <a name="test"></a>Test

[Testování](interactive-test.md#test-your-app) aplikace LUIS znamená, že výsledky předávání utterance LUIS a zobrazení ve formátu JSON.

## <a name="timezoneoffset"></a>Posun časového pásma

Koncový bod zahrnuje timezoneOffset. Toto je počet v minutách, které chcete přidat nebo odebrat z datetimeV2 předem připravených entit. Například pokud je utterance "jaké čas je nyní?".", datetimeV2 vrátil je aktuální čas požadavku klienta. Pokud vaše žádost klienta pochází z robota nebo jiné aplikace, který není stejný jako uživatel svého robota, je třeba předat ve posun mezi robota a uživatelem. 

Zobrazit [změnit časové pásmo datetimeV2 předem připravených entit](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Token je nejmenší jednotka, která mohou být označeny v entitě. Tokenizace vychází z vaší aplikace [jazykovou verzi](luis-supported-languages.md#tokenization).

## <a name="train"></a>Trénování

Školení je proces LUIS o všechny změny [aktivní verze](#active-version) od poslední školení.

## <a name="true-negative"></a>Záporné hodnoty true (TN)

V [batch testování][batch-testing], datových bodů představují projevy, ve kterých vaše aplikace správně předpovědět neexistence cílové záměr na entitu.

## <a name="true-positive"></a>Pravdivě pozitivní upozornění (TP)

V [batch testování][batch-testing], datových bodů představují projevy, ve kterých vaše aplikace správně předpovědět existenci cílové záměr na entitu.

## <a name="utterance"></a>Utterance

Utterance je přirozený jazyk frázi, jako je například "book 2 lístky pro Seattle další úterý". Příklad projevů budou přidány do záměr. 

## <a name="version"></a>Verze

LUIS [verze](luis-how-to-manage-versions.md) je konkrétní datový model přidružené k ID aplikace LUIS a publikované koncového bodu. Každá aplikace LUIS má alespoň jednu verzi.

[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/interactive-test#batch-testing
