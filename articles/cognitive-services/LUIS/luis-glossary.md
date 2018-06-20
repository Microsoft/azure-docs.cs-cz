---
title: Glosář pro službu jazyk Principy (LEOŠ) rozhraní API | Microsoft Docs
description: Glosáři vysvětluje pojmy, můžete narazit při práci se službou API LEOŠ.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 7cfcc5b907b28a877bea57ea869e17f01aae00cb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265383"
---
# <a name="glossary"></a>Glosář

## <a name="active-version"></a>Aktivní verze

Aktivní LEOŠ verze je verze, která bude přijímat všechny změny modelu. V [LEOŠ](luis-reference-regions.md) web, pokud chcete změnit na verzi, která není aktivní verze, musíte nejprve nastavit tuto verzi jako aktivní. 

## <a name="authoring"></a>Vytváření obsahu

Vytváření obsahu je možnost vytváření, správu a nasazení [LEOŠ aplikace](#luis-app), buď pomocí [LEOŠ](luis-reference-regions.md) webu nebo [vytváření rozhraní API](https://aka.ms/luis-authoring-api). 

## <a name="authoring-key"></a>vytváření klíče

Dříve s názvem "Programmatic" klíč. Použít k vytváření aplikace. Nepoužívá se pro dotazy na produkční úrovni koncového bodu. Další informace najdete v tématu [klíče omezení](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Dávkový soubor JSON textu

Dávkový soubor je pole JSON. Každý prvek v poli má tři vlastnosti: `text`, `intent`, a `entities`. `entities` Vlastnost je pole. Pole nesmí být prázdné. Pokud `entities` pole není prázdná, musí se přesně zjistit entity.

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

Spolupracovníka není [vlastníka](#owner) aplikace, ale má stejné oprávnění přidávat, upravovat a odstraňovat záměry, entity, utterances.

## <a name="currently-editing"></a>Aktuálně úpravy

Stejné jako [aktivní verze](#active-version)

## <a name="domain"></a>Domény

V kontextu LEOŠ **domény** je oblast znalostní báze. Vaše doména je specifické pro vaši aplikaci část znalostní báze. To může být obecné oblasti, jako je například aplikace cesta agenta. Cesta agenta aplikace může být také specifické pro právě oblasti informace o vaší společnosti, jako je například konkrétní zeměpisné polohy, jazyků a služby. 

## <a name="endpoint"></a>koncový bod

[Koncový bod LEOŠ](https://aka.ms/luis-endpoint-apis) je adresa URL, kde odeslat LEOŠ dotazy po [LEOŠ aplikace](#luis-app) je vytvořena a publikovaná. Adresa URL koncového bodu obsahuje oblasti publikované aplikace, stejně jako ID aplikace. Koncový bod můžete najít na **[publikovat](publishapp.md)** stránku aplikace, v tabulce prostředky a klíče nebo je můžete získat adresu URL koncového bodu z [získat informace o aplikaci](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) rozhraní API.

Koncový bod příkladu vypadá takto:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Parametr řetězce dotazu|description|
|--|--|
|oblast| [publikované oblast](luis-reference-regions.md#publishing-regions) |
|appID | ID aplikace LEOŠ |
|ID předplatného | Klíč předplatného LEOŠ vytvořit na portálu Azure |
|OTÁZKY | Utterance |
|timezoneOffset| minutes|

## <a name="entity"></a>Entity

[Entity](luis-concept-entity-types.md) jsou důležité slova v [utterances](luis-concept-utterance.md) popisují informace, které jsou relevantní pro [záměr](luis-concept-intent.md), a někdy jsou nezbytné k němu. Entita je v podstatě datový typ v LEOŠ. 

## <a name="f-measure"></a>F-měr

V [testování batch][batch-testing], míru přesnost test.

## <a name="false-negative"></a>Falešně negativní (TN)

V [testování batch][batch-testing], datových bodů představují utterances, ve kterých vaše aplikace nesprávně předpovědět absenci cílovou záměr nebo entitu.

## <a name="false-positive"></a>Falešně pozitivní (TP)

V [testování batch][batch-testing], datových bodů představují utterances, ve kterých vaše aplikace nesprávně předpovědět existenci cílovou záměr nebo entitu.

## <a name="features"></a>Funkce

Ve strojovém učení se [funkce](luis-concept-feature.md) je rozlišovací znak nebo atribut dat, které dodržuje systému.

## <a name="intent"></a>Záměr

[Záměr](luis-concept-intent.md) představuje úloh nebo akce, které chce uživatel provést. Je to účel nebo cílem vyjádřené v vstup od uživatele, například rezervace letu, platícího faktury nebo hledání článku zprávy. V LEOŠ záměrné předpovědi je založena na celý utterance. Entity, pro srovnání, jsou údaje utterance.

## <a name="labeling"></a>označování

Označování je přiřazování slovo nebo frázi v záměr [utterance](#utterance) s [entity](#entity) (datový typ). 

## <a name="luis-app"></a>LEOŠ aplikace

LEOŠ aplikace je vyškolení datový model pro zpracování přirozeného jazyka včetně [záměry](#intent), [entity](#entity)a s popiskem [utterances](#utterance).

## <a name="owner"></a>Vlastník

Každá aplikace má jednoho vlastníka, který je osoba, kterou vytvořili aplikaci. Vlastník může přidat [spolupracovníci](#collaborator).

## <a name="pattern"></a>Vzory
Nahradí předchozí funkce vzor [vzory](luis-concept-patterns.md). Využít vzory a zvyšte tak přesnost předpovědi tím, že poskytuje příklady méně školení. 

## <a name="phrase-list"></a>Seznam frází

A [frázi seznamu](luis-concept-feature.md#what-is-a-phrase-list-feature) zahrnuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí být považované podobně (například názvy města nebo produkty). Zaměňovat seznamu je považována za synonyma. 

## <a name="prebuilt-domains"></a>Předkompilované domény

A [předem domény](luis-how-to-use-prebuilt-domains.md) je LEOŠ aplikace, nakonfigurované pro konkrétní doménu například domácí automatizace (HomeAutomation) nebo restaurace rezervace (RestaurantReservation). Záměry, utterances a entity jsou nakonfigurované pro tuto doménu. 

## <a name="prebuilt-entity"></a>Předkompilované entity

A [předem entity](pre-builtentities.md) je entita LEOŠ poskytuje pro běžné typy informace, jako je číslo, adresa URL a e-mailu. Můžete přidat předem entity do vaší aplikace. 

## <a name="precision"></a>přesnost
V [testování batch][batch-testing], přesnost (také nazývané kladnou hodnotu prediktivní) je podíl relevantní utterances mezi načtené utterances.

## <a name="programmatic-key"></a>Programová klíč

Přejmenované na [vytváření klíč](#authoring-key). 

## <a name="publish"></a>Publikování

Publikování znamená přijetí LEOŠ [active verze](#active-version) k dispozici na pracovním nebo produkčním [koncový bod](#endpoint).  

## <a name="quota"></a>kvóta

Kvóta LEOŠ je omezení [předplatného Azure vrstvy](https://aka.ms/luis-price-tier). Kvóta LEOŠ můžete omezené obou požadavků za sekundu (429 stav protokolu HTTP) a celkový počet požadavků za měsíc (HTTP 403 stav). 

## <a name="recall"></a>Odvolat
V [testování batch][batch-testing], odvolat (označované také jako velkých a malých písmen), je schopnost LEOŠ ke generalizaci. 

## <a name="semantic-dictionary"></a>Sémantické slovník
Slovník sémantického je k dispozici na stránce seznamu entity, jakož i seznam stránce frázi. Sémantické slovník obsahuje návrhy slova na základě aktuálního oboru.

## <a name="sentiment-analysis"></a>Analýza postojích
Analýza postojích poskytuje kladné a záporné hodnoty utterances poskytované [Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/). 

## <a name="speech-priming"></a>Rozpoznávání řeči dočištění

Rozpoznávání řeči dočištění umožňuje vaší služby řeči se naplní LEOŠ modelu. V tématu [povolit řeči dočištění ](publishapp.md#enable-speech-priming).

## <a name="spelling-correction"></a>Oprava pravopisu

Na stránce publikovat povolit [kontrola pravopisu v Bingu](publishapp.md#enable-bing-spell-checker) opravit slovo obsahující chyby v utterances před předpovědi. 

## <a name="starter-key"></a>Úvodní klíč

Stejné jako [programový klíč](#programmatic-key), přejmenován na klíč pro vytváření obsahu.

## <a name="subscription-key"></a>Klíč předplatného

Klíč předplatného je klíč přidružený k službě LEOŠ [jste vytvořili v Azure](luis-how-to-azure-subscription.md). Tento klíč se [vytváření klíč](#programmatic-key). Pokud máte klíč předplatného, by měl použít pro všechny žádosti koncového bodu místo pro tvorbu klíče. Můžete zobrazit svůj aktuální klíč předplatného uvnitř adresu URL koncového bodu v dolní části [ **publikovat aplikace** stránky](publishapp.md) v [LEOŠ](luis-reference-regions.md) webu. Je hodnota **klíč předplatného** dvojice název hodnota. 

## <a name="test"></a>Test

[Testování](interactive-test.md#test-your-app) LEOŠ aplikace znamená předání utterance LEOŠ a zobrazení JSON výsledků.

## <a name="timezoneoffset"></a>Posun časového pásma

Koncový bod zahrnuje timezoneOffset. Toto je počet minut, které chcete přidat nebo odebrat z datetimeV2 předem entity. Například pokud je utterance "jaký čas je nyní?", datetimeV2 vrátil je aktuální čas na žádost klienta. Pokud váš požadavek klienta pochází z robotu nebo jiná aplikace, který není stejný jako vaše robota uživatele, by měl předáte v posun mezi robota a uživatelem. 

V tématu [změnit časové pásmo předem datetimeV2 entity](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Token je nejmenší jednotka, ve které můžete s názvem bez přípony v entitě. Tokenizace vychází z aplikace [jazykovou verzi](luis-supported-languages.md#tokenization).

## <a name="train"></a>Train

Školení je proces nastavení LEOŠ o všechny změny [active verze](#active-version) od poslední školení.

## <a name="true-negative"></a>Hodnota TRUE, záporné (TN)

V [testování batch][batch-testing], datových bodů představují utterances, ve kterých vaše aplikace správně předpovědět absenci cílovou záměr nebo entitu.

## <a name="true-positive"></a>Hodnota TRUE, kladnou (TP)

V [testování batch][batch-testing], datových bodů představují utterances, ve kterých vaše aplikace správně předpovědět existenci cílovou záměr nebo entitu.

## <a name="utterance"></a>Utterance

Utterance je přirozeného jazyka slovní spojení "kniha 2 lístky k webu Seattle další úterý". Příklad utterances se přidají do záměr. 

## <a name="version"></a>Verze

LEOŠ [verze](luis-how-to-manage-versions.md) je model konkrétní data související s ID aplikace LEOŠ a publikovaná koncový bod. Každé LEOŠ aplikace má alespoň jednu verzi.

[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/interactive-test#batch-testing
