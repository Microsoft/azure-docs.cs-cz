---
title: Záměry a entity – LUIS
titleSuffix: Azure Cognitive Services
description: Jeden záměr představuje úkol nebo akci, kterou chce uživatel provést. Je to účel nebo cíl vyjádřený výrokem uživatele. Definujte sadu záměrů, které odpovídají akcím, které uživatelé chtějí provést ve své aplikaci.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: cf138248e878b21531df2035dfeda1b90162ea99
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536099"
---
# <a name="intents-in-your-luis-app"></a>Záměry v aplikaci LUIS

Záměr představuje úkol nebo akci, kterou chce uživatel provést. Jedná se o účel nebo cíl vyjádřený v [utterance](luis-concept-utterance.md)uživatele.

Definujte sadu záměrů, které odpovídají akcím, které uživatelé chtějí provést ve své aplikaci. Například cestovní aplikace definuje několik záměrů:

Cestovní záměrové aplikace   |   Ukázkové promluvy   |
------|------|
 BookFlight (Rezervovat let)     |   "Book mi do Rio Next Week" <br/> "Postupování do Rio na 24 července" <br/> "Potřebuji lístek roviny další neděli do Rio de Janeiro"    |
 Pozdrav     |   Vysoké <br/>"Hello" <br/>"Dobré ráno"  |
 CheckWeather | "Jaké je počasí, jako je například Boston?" <br/> "Zobrazit prognózu pro tento víkend" |
 Žádné         | "Získat recept na soubory cookie"<br>"Lakers Win?" |

Všechny aplikace jsou dodávány s předdefinovaným záměrem "[none](#none-intent)", což je záložní záměr.

## <a name="prebuilt-domains-provide-intents"></a>Předem připravené domény poskytují záměry
Kromě záměrů, které definujete, můžete použít předem sestavené záměry z jedné z [předem připravených domén](luis-how-to-use-prebuilt-domains.md).

## <a name="return-all-intents-scores"></a>Vrátit skóre všech záměrů
Přiřadíte utterance k jednomu záměru. Když LUIS přijme utterance na koncovém bodu, ve výchozím nastavení vrátí nejvyšší záměr pro daný utterance.

Pokud chcete skóre pro všechny záměry pro utterance, můžete zadat příznak pro řetězec dotazu prediktivního rozhraní API.

|Prediktivní verze rozhraní API|Příznak|
|--|--|
|V2|`verbose=true`|
|Technologie|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Záměr v porovnání s entitou
Záměr představuje akci, kterou by měla aplikace přijmout pro uživatele a je založena na celé utterance. Utterance může mít jenom jeden nejvyšší záměr vyhodnocování, ale může mít spoustu entit.

<a name="how-do-intents-relate-to-entities"></a>

Vytvořte záměr, když má _záměr_ uživatele spustit akci v klientské aplikaci, jako je například volání funkce checkweather (). Pak vytvořte entity, které reprezentují parametry požadované ke spuštění akce.

|Záměr   | Entita | Ukázková promluva   |
|------------------|------------------------------|------------------------------|
| CheckWeather | {"Type": "Location"; "entita": "Praha"}<br>{"Type": "Builtin. datetimeV2. Date", "entita": "zítra", "Solution": "2018-05-23"} | Jaký je počasí jako v `Seattle` `tomorrow` ? |
| CheckWeather | {"Type": "date_range", "entita": "Tento víkend"} | Zobrazit prognózu pro `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Předem sestavené záměry domény

[Předem připravené domény](luis-how-to-use-prebuilt-domains.md) poskytují záměry pomocí projevy.

## <a name="none-intent"></a>Žádný záměr

Záměr **none** není vytvořen, ale je ponechán prázdný. Záměr **none** je povinný záměr a nelze ho odstranit ani přejmenovat. Naplňte ji do projevy, které jsou mimo vaši doménu.

Záměrem **none** je nouzový záměr, který je důležitý v každé aplikaci a měl by mít 10% z celkového počtu projevy. Slouží k učení LUIS projevy, které nejsou důležité v doméně aplikace (předmět oblasti). Pokud nepřidáte žádné projevyy pro záměr **none** , Luis vynutí utterance, který se nachází mimo doménu, do jedné z doménových záměrů. Tato akce zkosí skóre předpovědi podle výuky LUIS špatného záměru pro utterance.

Když je utterance předpokládaná jako záměr None, může klientská aplikace požádat o další otázky nebo poskytnout nabídku, která uživatele nasměruje na platné volby.

## <a name="negative-intentions"></a>Záporné záměry
Chcete-li určit negativní a pozitivní záměry, například **"Chci auto** " a " **nechci auto** ", můžete vytvořit dva záměry (jedna pozitivní a jedna záporná) a přidat odpovídající projevy pro každou z nich. Nebo můžete vytvořit jediný záměr a označit dvě různé kladné a záporné výrazy jako entitu.

## <a name="intents-and-patterns"></a>Záměry a vzory

Pokud máte příklad projevy, který lze definovat jako součást nebo celý jako regulární výraz, zvažte použití [entity regulárního výrazu](luis-concept-entity-types.md#regular-expression-entity) spárovaného se [vzorem](luis-concept-patterns.md).

Použití entity regulárního výrazu garantuje extrakci dat, aby byl vzor porovnán. Porovnávání vzorů zaručuje, že se vrátí přesný záměr.

## <a name="intent-balance"></a>Zůstatek záměru
Záměry domény aplikace by měly mít rovnováhu projevy napříč jednotlivými záměry. Nemusíte mít jeden záměr s 10 projevy a dalším záměrem s 500 projevy. Toto není vyvážené. Pokud máte tuto situaci, Projděte si záměr s 500 projevy, abyste viděli, jestli se mnoho záměrů dá znovu uspořádat do [vzoru](luis-concept-patterns.md).

V zůstatku není zahrnutý záměr **none** . Tento záměr by měl obsahovat 10% celkového projevy v aplikaci.

## <a name="intent-limits"></a>Omezení záměru
Přečtěte si [omezení](luis-limits.md#model-boundaries) , abyste porozuměli počtu záměrů, které můžete do modelu přidat.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Pokud potřebujete více než maximální počet záměrů
Nejdřív zvažte, jestli váš systém používá příliš mnoho záměrů.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Může zkombinovat více záměrů do jednoho záměru s entitami.
Záměry, které jsou příliš podobné, mohou být obtížnější pro LUIS rozlišení mezi nimi. Záměry by měly být dostatečně rozlišitelné pro zachycení hlavních úloh, pro které uživatel žádá, ale nemusí zachytit všechny cesty, které váš kód potřebuje. Například BookFlight a FlightCustomerService můžou být samostatné záměry v rámci cestovní aplikace, ale BookInternationalFlight a BookDomesticFlight jsou moc podobné. Pokud je systém potřebuje odlišit, používejte entity nebo jiné logiky spíše než záměry.

### <a name="dispatcher-model"></a>Model dispečera
Přečtěte si další informace o kombinování aplikací LUIS a QnA maker s [modelem odeslání](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Požádat o nápovědu pro aplikace s významným počtem záměrů
Pokud se omezuje počet záměrů nebo rozdělení vašich záměrů do více aplikací, obraťte se na podporu. Pokud vaše předplatné Azure zahrnuje služby podpory, obraťte se na [technickou podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další kroky

* Další informace o [entitách](luis-concept-entity-types.md), což jsou důležitá slova relevantní pro záměry
* Naučte se [přidávat a spravovat záměry](luis-how-to-add-intents.md) v aplikaci Luis.
* [Osvědčené postupy](luis-concept-best-practices.md) pro kontrolu záměrů
