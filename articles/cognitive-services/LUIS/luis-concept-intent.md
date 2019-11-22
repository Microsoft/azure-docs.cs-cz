---
title: Záměry a entity – LUIS
titleSuffix: Azure Cognitive Services
description: Jeden záměr představuje úkol nebo akci, kterou chce uživatel provést. Jedná o účely nebo cíl vyjádřeny utterance uživatele. Definujte sadu příkazů, který odpovídá na akce, které uživatelé chtějí využít ve vaší aplikaci.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280890"
---
# <a name="intents-in-your-luis-app"></a>Záměry v aplikaci LUIS

Záměru představuje úkol nebo akci uživatel chce provést. Jedná o účely nebo cíl vyjádřeny uživatele [utterance](luis-concept-utterance.md).

Definujte sadu příkazů, který odpovídá na akce, které uživatelé chtějí využít ve vaší aplikaci. Například definuje cestovní aplikaci několika způsoby:

Cestovní záměry aplikace   |   Ukázkové promluvy   | 
------|------|
 BookFlight (Rezervovat let)     |   "Rezervovat mě cestě a Rio příští týden" <br/> "Plout mě na Rio na 24th" <br/> "Potřebuji lístek roviny další pondělí až Rio de Janeiro"    |
 Pozdrav     |   "Hi" <br/>"Hello" <br/>"Dobrým ráno"  |
 CheckWeather | "Jak se o počasí v Bostonu jako"? <br/> "Zobrazit předpovědi pro tento víkendu" |
 Žádný         | "Si předpisu soubor cookie"<br>"Lakers win?" |

Všechny aplikace jsou dodávány s předdefinovaným záměrem "[none](#none-intent)", což je záložní záměr. 

## <a name="prebuilt-domains-provide-intents"></a>Předem připravených domén poskytují záměrů
Kromě záměrů, které definujete, můžete použít předem sestavené záměry z jedné z [předem připravených domén](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Vrátí všechny záměry skóre
Přiřadit utterance na jedné záměr. Když LUIS přijme utterance na koncovém bodu, ve výchozím nastavení vrátí nejvyšší záměr pro daný utterance. 

Pokud chcete skóre pro všechny záměry pro utterance, můžete zadat příznak pro řetězec dotazu prediktivního rozhraní API. 

|Prediktivní verze rozhraní API|Příznak|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Záměr ve srovnání s entity
Záměr představuje akci, kterou by měla aplikace přijmout pro uživatele a je založena na celé utterance. Utterance může mít pouze jeden nejvyšší hodnocení záměr, ale může mít mnoho entit. 

<a name="how-do-intents-relate-to-entities"></a>

Vytvořte záměr, když má _záměr_ uživatele spustit akci v klientské aplikaci, jako je například volání funkce checkweather (). Pak vytvořte entity, které reprezentují parametry požadované ke spuštění akce. 

|Záměr   | Entita | Ukázková promluva   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "umístění", "osoba": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": "den", "řešení": "2018-05-23"} | Co počasí, jako jsou v `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "osoba": "Tento víkendu"} | Zobrazit předpovědi pro `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Předem připravené domény záměrů

[Předem připravené domény](luis-how-to-use-prebuilt-domains.md) poskytují záměry pomocí projevy. 

## <a name="none-intent"></a>Žádný záměr

**Žádný** záměr je vytvořen, ale zůstane prázdná záměrně. **Žádný** záměr je požadovaná záměr a nemůže smazány nebo přejmenovány. Vyplňte projevy, které jsou mimo vaši doménu.

Záměrem **none** je nouzový záměr, který je důležitý v každé aplikaci a měl by mít 10% z celkového počtu projevy. Používá se naučit LUIS projevy, které nejsou důležité v doméně aplikace (oblastí). Pokud nepřidáte pro jakékoli projevy **žádný** záměru, LUIS vynutí utterance, která je mimo doménu do jedné ze tříd Intent domény. To bude zkosení skóre předpovědi ve výuce LUIS nesprávné záměr pro utterance. 

Když je utterance předpokládaná jako záměr None, může klientská aplikace požádat o další otázky nebo poskytnout nabídku, která uživatele nasměruje na platné volby. 

## <a name="negative-intentions"></a>Záporná záměry 
Pokud chcete určit kladnému i záměrů, jako například "můžu **má** automobilu" a "můžu **není** má automobilu", můžete vytvořit dvě záměry (jednu pozitivní a negativní jeden) a přidejte odpovídající projevy pro Každý. Nebo můžete vytvořit jeden záměr a označit dvě různé kladné a záporné podmínky jako entity.  

## <a name="intents-and-patterns"></a>Záměry a vzory

Pokud máte příklad projevy, který lze definovat jako součást nebo celý jako regulární výraz, zvažte použití [entity regulárního výrazu](luis-concept-entity-types.md#regular-expression-entity) spárovaného se [vzorem](luis-concept-patterns.md). 

Použití entity regulárního výrazu garantuje extrakci dat, aby byl vzor porovnán. Porovnávání vzorů zaručuje, že se vrátí přesný záměr. 

## <a name="intent-balance"></a>Záměru zůstatek
Domény záměry aplikace by měl mít rovnováhu projevy mezi každou záměr. Není nutné jeden záměr s 10 projevy a jiný účel s 500 projevy. To se rovnováha. Pokud máte tuto situaci, přečtěte si záměr s 500 projevy zobrazíte, pokud řadu příkazů může být znovu uspořádat do [vzor](luis-concept-patterns.md). 

**Žádný** záměr není součástí zůstatek na účtu. 10 % celkové projevy v aplikaci by měla obsahovat tohoto záměru.

## <a name="intent-limits"></a>Omezení záměru
Kontrola [omezení](luis-boundaries.md#model-boundaries) o tom, kolik záměry můžete přidat do modelu. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Pokud potřebujete více než maximální počet příkazů 
Nejprve zvažte, zda váš systém používá příliš mnoho příkazů. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Více záměry zkombinovat do jedné záměr s entitami 
Příkazy, které jsou příliš podobná může to ztížit pro LUIS k rozlišení mezi nimi. Příkazy by měl být dost informací k zachycení hlavních úloh, která uživatele žádá, ale není potřeba zachytit každá cesta kódu má měnit. Například samostatné příkazy v cestovní aplikaci můžou být BookFlight a FlightCustomerService ale BookInternationalFlight a BookDomesticFlight jsou příliš podobné. Pokud váš systém potřebuje k jejich rozlišení, používejte entity nebo jiných logic namísto záměry. 

### <a name="dispatcher-model"></a>Dispečer modelu
Další informace o kombinování aplikace LUIS a QnA maker pomocí [odesílání modelu](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Požádat o pomoc pro aplikace s velký počet záměrů
Pokud pro vás nebude fungovat snížení počtu záměry nebo rozdělení vaše záměry do více aplikací, obraťte se na podporu. Pokud vaše předplatné Azure zahrnuje odbornou pomoc, obraťte se na [technické podpoře Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Další kroky

* Další informace o [entity](luis-concept-entity-types.md), které jsou relevantní pro záměry důležité slova
* Zjistěte, jak [přidávat a spravovat záměry](luis-how-to-add-intents.md) ve vaší aplikaci LUIS.
* Zkontrolujte záměr [osvědčené postupy](luis-concept-best-practices.md)
