---
title: Principy záměry v aplikace LUIS
titleSuffix: Azure Cognitive Services
description: Záměru představuje úkol nebo akci uživatel chce provést. Jedná o účely nebo cíl vyjádřeny utterance uživatele. Definujte sadu příkazů, který odpovídá na akce, které uživatelé chtějí využít ve vaší aplikaci.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: cfad122b44944cfb637d8ef98931410d60c81ab8
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041158"
---
# <a name="intents-in-luis"></a>Příkazy v LUIS

Záměru představuje úkol nebo akci uživatel chce provést. Jedná o účely nebo cíl vyjádřeny uživatele [utterance](luis-concept-utterance.md).

Definujte sadu příkazů, který odpovídá na akce, které uživatelé chtějí využít ve vaší aplikaci. Například definuje cestovní aplikaci několika způsoby:

Cestovní záměry aplikace   |   Ukázkové promluvy   | 
------|------|
 BookFlight (Rezervovat let)     |   "Rezervovat mě cestě a Rio příští týden" <br/> "Plout mě na Rio na 24th" <br/> "Potřebuji lístek roviny další pondělí až Rio de Janeiro"    |
 Pozdrav.     |   "Hi" <br/>"Hello" <br/>"Dobrým ráno"  |
 CheckWeather | "Jak se o počasí v Bostonu jako"? <br/> "Zobrazit předpovědi pro tento víkendu" |
 Žádný         | "Si předpisu soubor cookie"<br>"Lakers win?" |

Všechny aplikace, které jsou součástí předdefinovaných záměr "[žádný](#none-intent-is-fallback-for-app)" tedy záložní záměr. 

## <a name="prebuilt-domains-provide-intents"></a>Předem připravených domén poskytují záměrů
Kromě záměrů, které definujete můžete použít předem připravených záměry z jednoho z předem připravených domén. Další informace najdete v tématu [použití předem připravených domén v aplikacích LUIS](luis-how-to-use-prebuilt-domains.md) Další informace o tom, jak přizpůsobit příkazy z předem připravených domény pro použití ve vaší aplikaci.

## <a name="return-all-intents-scores"></a>Vrátí všechny záměry skóre
Přiřadit utterance na jedné záměr. Přijetí LUIS utterance v koncovém bodě, vrátí jeden hlavní záměr pro tento utterance. Pokud chcete pro všechny příkazy pro utterance skóre, můžete zadat `verbose=true` příznak v řetězci dotazu API [volání koncového bodu](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>Záměr ve srovnání s entity
Záměr představuje akci chatovací robot by měla trvat pro uživatele a je založena na celý utterance. Entita představuje slova nebo fráze obsažené uvnitř utterance. Utterance může mít pouze jeden nejvyšší hodnocení záměr, ale může mít mnoho entit. 

<a name="how-do-intents-relate-to-entities"></a> Vytvoření záměru při uživatele _záměr_ aktivuje akci v klientské aplikaci, jako je volání funkce checkweather(). Vytvořte entitu k reprezentování parametrů potřebných k provedení akce. 

|Příklad záměr   | Entita | Entity v příkladu projevy   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "umístění", "osoba": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": "den", "řešení": "2018-05-23"} | Co počasí, jako jsou v `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "osoba": "Tento víkendu"} | Zobrazit předpovědi pro `this weekend` | 

## <a name="custom-intents"></a>Vlastní příkazy

Podobně míněný [projevy](luis-concept-utterance.md) odpovídají jedné záměr. Projevy v máte v úmyslu použít libovolnou [entity](luis-concept-entity-types.md) v aplikaci, protože se nejedná o entity specifické cílem. 

## <a name="prebuilt-domain-intents"></a>Předem připravené domény záměrů

[Předem připravených domén](luis-how-to-use-prebuilt-domains.md) mají záměry s projevy.  

## <a name="none-intent-is-fallback-for-app"></a>Žádná záměru není pro aplikaci pro použití náhradní lokality
**Žádný** pokrývající vše nebo záložní záměr je cílem. Používá se naučit LUIS projevy, které nejsou důležité v doméně aplikace (oblastí). **Žádný** záměr by měl mít 10 až 20 procent celkového projevy v aplikaci. Nenechávejte ho prázdný. 

### <a name="none-intent-helps-conversation-direction"></a>Žádný záměru pomáhá směr konverzace
Když utterance očekává se, že jako žádný záměru a vrácené chatovací robot s predikcí, robota můžete další otázky nebo nabídky nasměrovat uživatele na platné možnosti v chatovací robot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Žádné projevy v žádné záměru zkosí predikcí
Pokud nepřidáte pro jakékoli projevy **žádný** záměru, LUIS vynutí utterance, která je mimo doménu do jedné ze tříd Intent domény. To bude zkosení skóre předpovědi ve výuce LUIS nesprávné záměr pro utterance. 

### <a name="add-utterances-to-the-none-intent"></a>Přidání projevů na hodnotu None záměru
**Žádný** záměr je vytvořen, ale zůstane prázdná záměrně. Vyplňte projevy, které jsou mimo vaši doménu. Dobré utterance pro **žádný** je něco úplně mimo aplikace, stejně jako v oboru aplikace slouží. Například cestovní aplikaci neměli používat jakékoli projevy pro **žádný** , které se týkají projít, jako je například rezervace, fakturace, food, nákladní ubytování, pohostinství probíhající Zábava. 

Jaký typ projevy jsou ponechána pro žádný záměru? Začněte s nějaké konkrétní váš robot by neměl odpovědět takové "jaký druh dinosaurů má modré zuby?" To je velmi konkrétní dotaz úplně mimo cestovní aplikaci. 

### <a name="none-is-a-required-intent"></a>Žádná není požadované záměr
**Žádný** záměr je požadovaná záměr a nemůže smazány nebo přejmenovány.

## <a name="negative-intentions"></a>Záporná záměry 
Pokud chcete určit kladnému i záměrů, jako například "můžu **má** automobilu" a "můžu **není** má automobilu", můžete vytvořit dvě záměry (jednu pozitivní a negativní jeden) a přidejte odpovídající projevy pro Každý. Nebo můžete vytvořit jeden záměr a označit dvě různé kladné a záporné podmínky jako entity.  

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

## <a name="next-steps"></a>Další postup

* Další informace o [entity](luis-concept-entity-types.md), které jsou relevantní pro záměry důležité slova
* Zjistěte, jak [přidávat a spravovat záměry](luis-how-to-add-intents.md) ve vaší aplikaci LUIS.
* Zkontrolujte záměr [osvědčené postupy](luis-concept-best-practices.md)