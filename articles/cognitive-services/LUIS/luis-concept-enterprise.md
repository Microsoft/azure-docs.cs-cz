---
title: Podnikové koncepty - LUIS
titleSuffix: Azure Cognitive Services
description: Seznamte se s koncepty návrhu pro velké aplikace LUIS nebo více aplikací, včetně LUIS a QnA Maker společně.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221059"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Podnikové strategie pro aplikaci LUIS
Projděte si tyto strategie návrhu pro podnikovou aplikaci.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Očekáváte-li požadavky služby LUIS nad rámec kvóty

Služba LUIS má měsíční kvótu a kvótu za sekundu na základě cenové úrovně prostředku Azure. 

Pokud míra požadavků na aplikace LUIS překročí povolenou [kvótu](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), můžete:

* Rozprostřete zatížení do více aplikací LUIS se [stejnou definicí aplikace](#use-multiple-apps-with-same-app-definition). To zahrnuje, volitelně spuštění LUIS z [kontejneru](luis-container-howto.md). 
* Vytvořte a [přiřaďte](#assign-multiple-luis-keys-to-same-app) aplikaci více klíčů. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Použití více aplikací se stejnou definicí aplikace
Exportujte původní aplikaci LUIS a pak ji importujte zpět do samostatných aplikací. Každá aplikace má své vlastní ID aplikace. Když publikujete, místo použití stejného klíče ve všech aplikacích vytvořte pro každou aplikaci samostatný klíč. Vyvažte zatížení všech aplikací tak, aby žádná aplikace nebyla zahlcena. Přidejte [přehledy aplikací](luis-tutorial-bot-csharp-appinsights.md) pro sledování využití. 

Chcete-li získat stejný hlavní záměr mezi všemi aplikacemi, ujistěte se, že záměr předpověď mezi první a druhý záměr je dostatečně široká, že LUIS není zaměňována, poskytuje různé výsledky mezi aplikacemi pro drobné změny v projevy. 

Při trénování těchto sourozeneckých aplikací nezapomeňte trénovat se [všemi daty](luis-how-to-train.md#train-with-all-data).

Určete jednu aplikaci jako hlavní. Všechny projevy, které jsou navrženy pro kontrolu by měly být přidány do hlavní aplikace a pak přesunuty zpět do všech ostatních aplikací. Toto je buď úplný export aplikace nebo načítání popisné projevy z hlavního serveru na podřízené. Načítání lze provést z webu [LUIS](luis-reference-regions.md) nebo vytváření rozhraní API pro [jeden utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) nebo pro [dávku](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Naplánujte pravidelné kontroly, například každé dva týdny, [projevy koncového bodu](luis-how-to-review-endpoint-utterances.md) pro aktivní učení, pak přeškolit a znovu publikovat. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Přiřazení více klíčů LUIS ke stejné aplikaci
Pokud vaše aplikace LUIS obdrží více přístupů koncovýbod, než umožňuje kvóta vašeho jediného klíče, vytvořte a přiřaďte další klíče k aplikaci LUIS. Vytvořte správce provozu nebo nástroje pro vyrovnávání zatížení pro správu dotazů koncového bodu napříč klíči koncového bodu. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Když vaše monolitická aplikace vrátí špatný záměr
Pokud vaše aplikace je určen k předpovědět širokou škálu projevy uživatelů, zvažte implementaci [modelu odeslání](#dispatch-tool-and-model). Rozdělení monolitické aplikace umožňuje LUIS zaměřit detekci mezi záměry úspěšně namísto získání zmatený mezi záměry v rámci nadřazené aplikace a podřízené aplikace. 

Naplánujte pravidelné [kontroly projevy koncového bodu](luis-how-to-review-endpoint-utterances.md) pro aktivní učení, například každé dva týdny, pak přeškolit a znovu publikovat. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Když potřebujete mít více než 500 záměrů
Předpokládejme, že vyvíjíte kancelářského asistenta, který má více než 500 záměrů. Pokud 200 záměrů souvisí s plánováním schůzek, 200 se týká připomenutí, 200 je o získání informací o kolezích a 200 pro odesílání e-mailů, záměry skupin tak, aby každá skupina byla v jedné aplikaci, a pak vytvořte aplikaci nejvyšší úrovně obsahující každý záměr. Použijte [model odeslání](#dispatch-tool-and-model) k vytvoření aplikace nejvyšší úrovně. Pak změňte bot a použijte kaskádové volání, jak je znázorněno v [kurzu modelu odeslání](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Když potřebujete zkombinovat několik aplikací LUIS a QnA maker
Pokud máte několik luis a QnA maker aplikace, které potřebují reagovat na robota, použijte [model odeslání](#dispatch-tool-and-model) k vytvoření aplikace nejvyšší úrovně.  Pak změňte bot a použijte kaskádové volání, jak je znázorněno v [kurzu modelu odeslání](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Expediční nástroj a model
Pomocí nástroje příkazového řádku [Odeslání,][dispatch-tool] který se nachází v [nástrojích BotBuilder,](https://github.com/Microsoft/botbuilder-tools) můžete kombinovat více aplikací LUIS nebo QnA Maker do nadřazené aplikace LUIS. Tento přístup umožňuje mít nadřazenou doménu včetně všech předmětů a různých podřízených oborových domén v samostatných aplikacích. 

![Koncepční obraz dispečerské architektury](./media/luis-concept-enterprise/dispatch-architecture.png)

Nadřazená doména je `Dispatch` zaznamenána v LUIS s verzí pojmenovanou v seznamu aplikací. 

Chatovací robot obdrží utterance, pak odešle do nadřazené aplikace LUIS pro předpověď. Nejvyšší předpovídaný záměr z nadřazené aplikace určuje, která podřízená aplikace LUIS se nazývá další. Chat bot odešle utterance do podřízené aplikace pro konkrétnější předpověď.

Pochopit, jak se tato hierarchie volání provádí z Bot Builder v4 [dispečer-aplikace-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Omezení záměru v modelu odeslání
Odeslání aplikace má 500 zdrojů odeslání, což odpovídá 500 záměry, jako maximum. 

## <a name="more-information"></a>Další informace

* [Sada SDK framework botů](https://github.com/Microsoft/botframework)
* [Kurz expedičního modelu](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Odeslání CLI](https://github.com/Microsoft/botbuilder-tools)
* Ukázka modelu odeslání - [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [otestovat dávku](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
