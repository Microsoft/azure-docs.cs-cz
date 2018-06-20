---
title: Vysvětlení tříd Intent v aplikacích LEOŠ v Azure | Microsoft Docs
description: Popisuje, co záměry jsou v aplikacích jazyka Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 5c2feb0240b676d4e106cbda65aaaed7604a35c5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265148"
---
# <a name="intents-in-luis"></a>Záměry v LEOŠ

Záměrem reprezentuje úlohu nebo chce provést akce uživatele. Je to účel nebo cílem vyjádřené v uživatele [utterance](luis-concept-utterance.md).

Definujte sadu tříd Intent, která odpovídá na akce, které uživatelé chtít využít ve vaší aplikaci. Například cesta aplikace definuje několik tříd Intent:

Cesta aplikace záměry   |   Příklad utterances   | 
------|------|
 BookFlight     |   "Sešit mi cestě a Rio příští týden" <br/> "Fyzicky dostavili mi Rio na 24th" <br/> "Potřebuji rovinu lístku další neděle do Rio de Janeiro"    |
 Pozdrav     |   "Hi" <br/>Text "hello" <br/>"Dobré ráno"  |
 CheckWeather | "Co je počasí jako v Boston?" <br/> "Ukázat předpovědi pro tento víkendu" |
 Žádný         | "Get z mi recepturách souboru cookie"<br>"Lakers win?" |

Všechny aplikace pocházet předdefinované záměrem "[žádné](#none-intent-is-fallback-for-app)" což je záložní záměr. 

## <a name="prebuilt-domains-provide-intents"></a>Předkompilované domény poskytují záměry
Kromě záměry, které definujete můžete předem záměry z jednoho předem domén. Další informace najdete v tématu [použít předem domén v aplikacích LEOŠ](luis-how-to-use-prebuilt-domains.md) Další informace o tom, jak přizpůsobit záměry z předem domény pro použití ve vaší aplikaci.

## <a name="return-all-intents-scores"></a>Vrátí všechny záměry skóre
Přiřadíte utterance jeden záměr. Když LEOŠ obdrží utterance v koncovém bodě, vrátí jeden hlavní záměr pro tento utterance. Pokud chcete použít pro všechny záměry pro utterance skóre, můžete zadat `verbose=true` příznak v řetězci dotazu API [koncový bod volání](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>Záměr ve srovnání s entity
Záměr představuje akci chatbot provést pro uživatele a je založena na celý utterance. Entita představuje slova nebo fráze obsažené uvnitř utterance. Utterance může mít pouze jeden horní vyhodnocování záměr ale může mít mnoho entit. 

<a name="how-do-intents-relate-to-entities"></a> Vytvoření záměrem při uživatele _záměr_ by aktivují akci v aplikaci klienta, například volání funkce checkweather(). Vytvořte entitu představují parametrů požadovaných pro spuštění akce. 

|Příklad záměru   | Entita | Entity v příkladu utterances   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"typ": "místo", "entita": "seattle"}<br>{"typ": "builtin.datetimeV2.date","entity": "zítra", "řešení": "2018-05-23"} | Co počasí stejně jako v nástroji `Seattle` `tomorrow`? |
| CheckWeather | {"typ": "date_range", "entita": "Tento víkendu"} | Zobrazit mi předpovědi pro `this weekend` | 

## <a name="custom-intents"></a>Vlastní záměry

Podobně míněný [utterances](luis-concept-utterance.md) odpovídají jedné záměr. Utterances ve vašich představ, můžete použít některou [entity](luis-concept-entity-types.md) v aplikaci, protože entity nejsou specifické pro záměr. 

## <a name="prebuilt-domain-intents"></a>Předkompilované domény záměry

[Předkompilované domén](luis-how-to-use-prebuilt-domains.md) mít záměry s utterances.  

## <a name="none-intent-is-fallback-for-app"></a>Žádná záměrné není záložního pro aplikaci
**Žádné** je záměr catch-all nebo záložní. Slouží k naučit LEOŠ utterances, které nejsou důležité v doméně aplikace (subjektu oblast). **Žádné** záměr by měl mít mezi 10 a 20 procent celkového utterances v aplikaci. Nenechávejte ho prázdný. 

### <a name="none-intent-helps-conversation-direction"></a>Žádné záměrné pomáhá konverzace směr
Pokud je jako parametr None předpovědět utterance záměrné a vrácený do chatbot s předpovědi, robota můžete klást otázky další nebo zadejte nabídky se má nasměrovat uživatele platné možnosti v chatbot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Žádné utterances v žádné záměrné zkosí předpovědi
Pokud je nemůžete přidat žádné utterances pro **žádné** záměrné, vynutí LEOŠ utterance, který je mimo doménu do jedné domény záměry. Ve výukových LEOŠ nesprávný záměr pro utterance to bude zkreslit skóre předpovědi. 

### <a name="add-utterances-to-the-none-intent"></a>Přidat utterances záměrné none
**Žádné** záměr vytvoří, ale s cílem prázdné. Vyplňte v něm s utterances, které jsou mimo doménu. Dobrý utterance pro **žádné** je něco úplně mimo aplikaci, jakož i odvětví aplikace slouží. Například cesta aplikace neměli používat pro všechny utterances **žádné** které se týkají projít, jako je například rezervace, fakturace, jídlo, pohoštění, nákladní, aktivních pořadových Zábava. 

Jaký typ utterances jsou ponechána pro žádný záměrné? Začněte s nějaké konkrétní vaší robota nesmí zodpovědět takové "jaký druh dinosaur má modré zuby?" To je velmi konkrétní dotaz daleko mimo cesta aplikace. 

### <a name="none-is-a-required-intent"></a>Žádná není požadovaný záměr
**Žádné** záměr je požadovaný záměr a nemůže být odstraněna nebo přejmenována.

## <a name="negative-intentions"></a>Záporné záměry 
Pokud chcete určit kladné a záporné záměry, jako například "I **má** automobilu" a "I **nemáte** má automobilu", můžete vytvořit dvě záměry (jeden kladnou a jeden záporné) a přidejte příslušné utterances pro Každý. Nebo můžete vytvořit jeden záměr a označit dva různé kladné a záporné podmínky jako entity.  

## <a name="intent-balance"></a>Záměrné zůstatek
Záměry domény aplikace by měl mít rovnováhu utterances mezi každou záměr. Nemají jeden záměr s 10 utterances a jiné záměr s 500 utterances. Toto není vyvážit. Pokud máte tuto situaci, zkontrolujte záměr s 500 utterances chcete zobrazit, pokud řadu záměry může být znovu uspořádat do [vzor](luis-concept-patterns.md). 

**Žádné** záměr není součástí rovnováhu mezi. Aby záměr musí obsahovat 10 % z celkové utterances v aplikaci.

## <a name="intent-limits"></a>Záměrné omezení
Zkontrolujte [omezení](luis-boundaries.md#model-boundaries) zjistit, kolik záměry můžete přidat k modelu. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Pokud potřebujete více než maximální počet záměry 
Nejprve zvažte, zda systém používá příliš mnoho tříd Intent. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Více tříd Intent zkombinovat do jednoho záměr s entity 
Záměry, které jsou příliš podobné může být obtížnější pro LEOŠ k rozlišení mezi nimi. Záměry by měla být nejrůznější dost pro zachycení hlavní úlohy, které uživatel požaduje, ale není nutné zaznamenat všechny cesty kódu trvá. Například samostatné záměry v aplikaci cesta může být BookFlight a FlightCustomerService, ale BookInternationalFlight a BookDomesticFlight jsou příliš podobné. Pokud systém je nutné je odlišili, použijte entity nebo jiných logiku, nikoli tříd Intent. 

### <a name="dispatcher-model"></a>Dispečer modelu
Další informace o kombinování LEOŠ a QnA maker aplikací pomocí [odesílání modelu](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Nápověda pro žádost o u aplikací s velký počet záměry
Pokud pro vás nebude fungovat snížit počet tříd Intent nebo dělení vaše záměry do více aplikací, obraťte se na podporu. Pokud vaše předplatné zahrnuje služby podpory, obraťte se na [Azure technickou podporu](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Další postup

* Další informace o [entity](luis-concept-entity-types.md), které jsou relevantní pro záměry důležité slova
* Zjistěte, jak [přidávat a spravovat záměry](luis-how-to-add-intents.md) ve vaší aplikaci LEOŠ.
* Zkontrolujte záměr [osvědčené postupy](luis-concept-best-practices.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website