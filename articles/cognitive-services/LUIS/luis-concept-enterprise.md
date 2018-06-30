---
title: Koncepty podnikové aplikace LEOŠ – Azure | Microsoft Docs
description: Koncepty návrhu pro velké aplikace LEOŠ Pochopte.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/05/2018
ms.author: v-geberr
ms.openlocfilehash: f5d1cf61ca7b8d8eeaed52fc3f45f8d4847ddda9
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108684"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Strategie organizace pro LEOŠ aplikace
Přečtěte si tyto návrhu strategie pro firemní aplikace.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Pokud očekáváte LEOŠ požadavky nad těchto kvótu
Pokud vaše rychlost požadavků aplikace LEOŠ překračuje povolené maximum [míra kvót](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), šíří zatížení pro další LEOŠ aplikace s [stejné definice aplikace](#use-multiple-apps-with-same-app-definition) nebo vytvořte a [přiřadit více klíčů](#assign-multiple-luis-keys-to-same-app) k aplikace. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Použití více aplikací s stejné definice aplikace
Export původní LEOŠ aplikace a pak importovat aplikaci zpět do samostatných aplikací. Každá aplikace má vlastní ID aplikace. Když publikujete, namísto pomocí stejného klíče mezi všechny aplikace, vytvořte samostatné klíč pro každou aplikaci. Vyrovnávání zatížení napříč všechny aplikace, aby je přetížena žádné jediné aplikaci. Přidat [Application Insights](luis-tutorial-bot-csharp-appinsights.md) pro sledování využití. 

Chcete-li získat stejné nejvyšší záměr mezi všechny aplikace, zkontrolujte, zda že je dost široké, že LEOŠ není zaměňovat, udělíte odlišné výsledky mezi aplikacemi pro malé změny v utterances záměrné předpovědi mezi prvním a druhém záměr. 

Určete jenom jedna aplikace, jako je hlavní server. Všechny utterances, které jsou navrženy pro kontrolní musí přidat k hlavní aplikaci potom přesunuta zpět do dalších aplikací. Toto je buď úplná export aplikace nebo načítání s popiskem utterances z hlavní u podřízených objektů. Načítání se dá udělat z buď [LEOŠ] [ LUIS] web nebo rozhraní API pro vytváření obsahu pro [jednotné utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) nebo pro [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Plánování pravidelné [kontrolní koncový bod utterances](label-suggested-utterances.md) active informací, jako je například každé dva týdny, pak přeučování a znovu publikovat. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Přiřadit více LEOŠ klíče stejná aplikace
Pokud vaše aplikace LEOŠ přijme další koncový bod přístupů než povoluje kvóta jeden klíč, vytvořte a přiřaďte další klíče LEOŠ aplikace. Vytvoření přenosem manager nebo službu spravovat dotazy koncového bodu v rámci klíče koncový bod Vyrovnávání zatížení. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Když aplikace monolitický vrátí nesprávný záměr
Pokud vaše aplikace je určená k předvídání širokou škálu utterances uživatele, zvažte implementaci [odesílání modelu](#dispatch-tool-and-model). Rozdělení monolitický aplikace umožňuje LEOŠ detekce fokus mezi záměry úspěšně místo získávání nerozumíte mezi záměry napříč aplikace nadřazené a podřízené aplikace. 

Plánování pravidelné [kontrolní koncový bod utterances](label-suggested-utterances.md) active informací, jako je například každé dva týdny, pak přeučování a znovu publikovat. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Když je potřeba mít více než 500 záměry
Řekněme například, že provádíte vývoj asistent office, který má více než 500 záměry. Pokud 200 záměry se týkají plánování schůzek, 200 jsou o připomenutí, 200 jsou o získání informací o kolegy a 200 jsou pro odesílání e-mailu, záměry skupiny tak, aby každá skupina je v jediné aplikaci, pak vytvořte nejvyšší úrovně aplikaci obsahující každý záměr. Použití [dispatch nástroj a architektura](#dispatch-tool-and-model) k sestavení aplikace nejvyšší úrovně. Změňte vaše robota chcete použít jako zobrazení v kaskádové volání [odesílání kurzu][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Když je nutné sloučit několik LEOŠ a QnA aplikace maker
Pokud máte několik LEOŠ a QnA maker aplikace, které je potřeba reagovat na robota, použijte [odesílání nástroj](#dispatch-tool-and-model) k sestavení aplikace nejvyšší úrovně. Změňte vaše robota chcete použít jako zobrazení v kaskádové volání [odesílání kurzu][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Nástroj pro odesílání a modelu
Použití [odesílání] [ dispatch-tool] nástroj příkazového řádku, v nalezen [BotBuilder nástroje](https://github.com/Microsoft/botbuilder-tools) kombinovat více LEOŠ nebo QnA Maker aplikace do nadřazené LEOŠ aplikace. Tento přístup umožňuje mít nadřazenou doménu, včetně všech témata a jiné podřízené domény předmětu v samostatných aplikací. 

![Koncepčního obrázku architektury odesílání](./media/luis-concept-enterprise/dispatch-architecture.png)

Nadřazená doména je uvedeno v LEOŠ jako **V odesílání** aplikace. 

![Seznam aplikací – snímek obrazovky LEOŠ s LEOŠ aplikace vytvořená nástrojem pro odesílání](./media/luis-concept-enterprise/dispatch.png)

Chatbot obdrží utterance a pak odešle do nadřazené LEOŠ aplikace pro předpověď. Horní předpokládaných záměr z nadřazené aplikace určuje, které podřízené LEOŠ aplikace se označuje jako další. Chatbot odešle utterance podřízené aplikace pro konkrétnější předpovědi.

Pochopit, jak se tato hierarchie volání z v4 robota Tvůrce přišla [kurz aplikace dispečera][dispatcher-application-tutorial].  

### <a name="intent-limits-in-dispatch-model"></a>Záměrné omezení v odesílání modelu
Odesílání aplikace má 500 odesílání zdrojů, odpovídá 500 záměry jako maximální. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [testování dávky](luis-how-to-batch-test.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch