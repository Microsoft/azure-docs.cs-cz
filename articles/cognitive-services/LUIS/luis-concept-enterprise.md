---
title: Podnikové koncepce – LUIS
titleSuffix: Azure Cognitive Services
description: Koncepce návrhu pro velké aplikace LUIS nebo více aplikací, včetně služby LUIS a nástroje QnA Maker společně.
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
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619934"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Podnikové strategie pro aplikace LUIS
Projděte si tyto strategie návrhu pro podnikové aplikace.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Pokud očekáváte, že služba LUIS požadavky nad těchto kvóty

LUIS má měsíční kvótu a kvótu za sekundu, a to na základě cenové úrovně prostředku Azure. 

Pokud vaše rychlost požadavků aplikace LUIS překračuje povolenou [kvótu](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), můžete:

* Rozprostře zatížení do více aplikací LUIS se [stejnou definicí aplikace](#use-multiple-apps-with-same-app-definition). To zahrnuje volitelně i spuštění LUIS z [kontejneru](luis-container-howto.md). 
* Vytvořte a [přiřaďte aplikaci více klíčů](#assign-multiple-luis-keys-to-same-app) . 

### <a name="use-multiple-apps-with-same-app-definition"></a>Použití více aplikací s stejné definice aplikace
Původní aplikaci LUIS, exportovat a importovat aplikace do samostatných aplikací. Každá aplikace má vlastní ID aplikace. Když publikujete, namísto použití stejného klíče mezi všemi aplikacemi, vytvořte samostatné klíč pro každou aplikaci. Vyrovnávání zatížení mezi všemi aplikacemi tak, aby žádné aplikace s jedním se cítili zahlcení. Přidat [Application Insights](luis-tutorial-bot-csharp-appinsights.md) monitorovat využití. 

Chcete-li získat stejnou hlavní záměr mezi všemi aplikacemi, ujistěte se, že záměru předpovědi mezi prvním a druhém záměr je dostatečně, LUIS není zaměňovat, poskytující různé výsledky mezi aplikacemi pro malé změny v projevy. 

Při školení těchto aplikací na stejné úrovni se ujistěte, že jste provedli [výuku všech dat](luis-how-to-train.md#train-with-all-data).

Určete jednu aplikaci jako hlavní server. Jakékoli projevy, které jsou navržené ke kontrole by měl přidá do hlavní aplikace poté přesunut na všechny ostatní aplikace. To je úplné export aplikace, nebo načítání s popiskem projevy od hlavního na podřízené položky. Načítání lze provést buď z [LUIS](luis-reference-regions.md) webu nebo rozhraní API pro vytváření obsahu pro [jednotné utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) nebo pro [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Naplánujte pravidelnou kontrolu, třeba každé dva týdny, [koncového bodu projevy](luis-how-to-review-endpoint-utterances.md) pro aktivní učení a pak znovu prostudujte a znovu publikujte. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Přiřadit více LUIS klíče pro stejnou aplikaci
Pokud aplikace LUIS obdrží další koncový bod přístupů než povoluje kvóta jeden klíč, vytvořit a přiřadit aplikace LUIS více klíčů. Vytvoření traffic manager nebo Vyrovnávání zatížení do spravovat dotazy koncový bod v rámci klíče koncového bodu. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Když monolitické aplikace vrátí nesprávné záměr
Pokud se vaše aplikace k předvídání širokou škálu projevy uživatele, zvažte implementaci [odesílání modelu](#dispatch-tool-and-model). Rozdělení monolitické aplikace umožňuje zjišťování fokus mezi záměry úspěšně místo získávání zaměňovat mezi záměry napříč aplikace nadřazené a podřízené aplikace LUIS. 

Naplánovat pravidelnou [přehled koncových bodů projevy](luis-how-to-review-endpoint-utterances.md) pro službu active learning, jako je například každé dva týdny, pak přetrénujete a znovu publikovat. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Když je potřeba mít více než 500 záměrů
Předpokládejme, že vyvíjíte pomocníka Office, který má více než 500 záměrů. Pokud 200 záměry se týkají plánování schůzek, 200 se týkají připomenutí, 200 jsou o získání informací o kolegy a 200 jsou určené pro odesílání e-mailů, záměry skupiny tak, aby každá skupina je v jedné aplikaci, vytvořte aplikaci s nejvyšší úrovně obsahující každý záměr. Použijte [model odeslání](#dispatch-tool-and-model) k sestavení aplikace nejvyšší úrovně. Pak změňte robota na použití kaskádového volání, jak je znázorněno v [kurzu modelu odeslání](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Pokud je potřeba sloučit několik LUIS a QnA maker aplikace
Pokud máte několik aplikací LUIS a QnA maker, které potřebují reagovat na robota, použijte [model odeslání](#dispatch-tool-and-model) k sestavení aplikace nejvyšší úrovně.  Pak změňte robota na použití kaskádového volání, jak je znázorněno v [kurzu modelu odeslání](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Nástroj pro odesílání a model
Použijte nástroj [][dispatch-tool] příkazového řádku Dispatch, který se nachází v [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools) pro kombinování více LUIS a/nebo QnA maker aplikací do nadřazené aplikace Luis. Tento přístup umožňuje mít nadřazenou doménu, včetně všech tématech a jiné podřízené domény předmětu v samostatných aplikací. 

![Koncepční obrázek architektury odeslání](./media/luis-concept-enterprise/dispatch-architecture.png)

Nadřazené doméně, je uvedena v LUIS s verzí s názvem `Dispatch` v seznamu aplikací. 

Robot pro chat obdrží utterance a pak pošle nadřazené aplikaci v LUIS k předpovědi. Nejvyšší předpovězený záměr z nadřazené aplikace určuje, která podřízená aplikace LUIS se nazývá Next. Robot pro chat pošle utterance do podřízené aplikace a podrobnější předpověď.

Pochopte, jak se tato hierarchie volání provádí z dispečera Bot Builder v4 [– Application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>V modelu odeslání záměru omezení
Odeslání aplikace má 500 odeslání zdrojů, odpovídá 500 záměrů, jako maximální. 

## <a name="more-information"></a>Další informace

* [Sada robot Framework SDK](https://github.com/Microsoft/botframework)
* [Kurz pro model odeslání](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Rozhraní příkazového řádku pro odesílání](https://github.com/Microsoft/botbuilder-tools)
* Ukázka robota modelu odeslání – [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [testování dávky](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
