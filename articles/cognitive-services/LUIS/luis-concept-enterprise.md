---
title: Podnikové koncepce – LUIS
titleSuffix: Azure Cognitive Services
description: Seznamte se s koncepty návrhu pro rozsáhlé aplikace LUIS nebo více aplikacemi, včetně LUIS a QnA Maker dohromady.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: d8c88883b839ff47ef57a17378f43918e9ecf7e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536116"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Podnikové strategie pro aplikaci pro LUIS
Projděte si tyto strategie návrhu pro podnikovou aplikaci.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Pokud očekáváte, že žádosti LUIS přesahují kvótu

LUIS má měsíční kvótu a kvótu za sekundu, a to na základě cenové úrovně prostředku Azure. 

Pokud vaše rychlost požadavků aplikace LUIS překračuje povolenou [kvótu](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), můžete:

* Rozprostře zatížení do více aplikací LUIS se [stejnou definicí aplikace](#use-multiple-apps-with-same-app-definition). To zahrnuje volitelně i spuštění LUIS z [kontejneru](luis-container-howto.md). 
* Vytvořte a [přiřaďte aplikaci více klíčů](#assign-multiple-luis-keys-to-same-app) . 

### <a name="use-multiple-apps-with-same-app-definition"></a>Použití více aplikací se stejnou definicí aplikace
Exportujte původní aplikaci LUIS a pak aplikaci importujte zpátky do samostatných aplikací. Každá aplikace má své vlastní ID aplikace. Když publikujete místo použití stejného klíče napříč všemi aplikacemi, vytvořte pro každou aplikaci samostatný klíč. Vyvážení zátěže napříč všemi aplikacemi, aby nedošlo k zahlcení žádné jedné aplikace. Přidejte [Application Insights](luis-tutorial-bot-csharp-appinsights.md) k monitorování využití. 

Aby bylo možné získat stejný nejvyšší záměr mezi všemi aplikacemi, ujistěte se, že předpověď záměrů mezi prvním a druhým záměrem je dostatečně rozsáhlá, že LUIS není zmatený a poskytuje různé výsledky mezi aplikacemi pro drobné variace v projevy. 

Při školení těchto aplikací na stejné úrovni se ujistěte, že jste provedli [výuku všech dat](luis-how-to-train.md#train-with-all-data).

Určete jednu aplikaci jako hlavní. Všechny projevy navržené pro kontrolu by se měly přidat do hlavní aplikace a pak se přesunout zpátky do všech ostatních aplikací. Toto je buď úplný export aplikace, nebo načtení popisku projevy z hlavní větve do podřízených objektů. Načítání se dá provést buď z webu [Luis](luis-reference-regions.md) , nebo z rozhraní API pro vytváření obsahu pro [jeden utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) nebo pro [dávku](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Naplánujte pravidelnou kontrolu, třeba každé dva týdny, [koncového bodu projevy](luis-how-to-review-endpoint-utterances.md) pro aktivní učení a pak znovu prostudujte a znovu publikujte. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Přiřazení více klíčů LUIS ke stejné aplikaci
Pokud vaše aplikace LUIS přijímá více přístupů do koncového bodu, než povoluje kvóta s jedním klíčem, vytvoří a přiřadí k aplikaci LUIS další klíče. Vytvořte Traffic Manager nebo nástroj pro vyrovnávání zatížení, abyste mohli spravovat dotazy koncových bodů napříč klíči koncových bodů. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Když aplikace monolitické vrátí nesprávný záměr
Pokud má vaše aplikace předpověď široké škály uživatelských projevy, zvažte implementaci [modelu odeslání](#dispatch-tool-and-model). Rozdělení aplikace monolitické umožňuje LUIS soustředit detekci mezi záměry a nemuseli se zaměňovat mezi záměry napříč nadřazenou aplikací a podřízenými aplikacemi. 

Naplánujte pravidelnou [kontrolu koncového bodu projevy](luis-how-to-review-endpoint-utterances.md) pro aktivní učení, třeba každé dva týdny, a pak znovu Projděte a znovu publikujte. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Pokud potřebujete mít více než 500 záměrů
Předpokládejme, že vyvíjíte pomocníka Office, který má více než 500 záměrů. Pokud se 200 záměrů vztahujících se na schůzky plánování, 200 se o připomenutích, 200 se dozvíte o získání informací o kolegech a 200 jsou pro posílání e-mailů, záměrů skupin tak, aby každá skupina byla v jedné aplikaci, a pak vytvořila aplikaci nejvyšší úrovně obsahující jednotlivé záměry. Použijte [model odeslání](#dispatch-tool-and-model) k sestavení aplikace nejvyšší úrovně. Pak změňte robota na použití kaskádového volání, jak je znázorněno v [kurzu modelu odeslání](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Pokud potřebujete zkombinovat několik aplikací LUIS a QnA maker
Pokud máte několik aplikací LUIS a QnA maker, které potřebují reagovat na robota, použijte [model odeslání](#dispatch-tool-and-model) k sestavení aplikace nejvyšší úrovně.  Pak změňte robota na použití kaskádového volání, jak je znázorněno v [kurzu modelu odeslání](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Nástroj pro expedici a model
Použijte nástroj příkazového řádku [Dispatch][dispatch-tool] , který se nachází v [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools) pro kombinování více LUIS a/nebo QnA maker aplikací do nadřazené aplikace Luis. Tento přístup umožňuje mít nadřazenou doménu včetně všech předmětů a různých podřízených domén subjektu v samostatných aplikacích. 

![Koncepční obrázek architektury odesílání](./media/luis-concept-enterprise/dispatch-architecture.png)

Nadřazená doména je zaznamenána v LUIS s verzí pojmenovanou `Dispatch` v seznamu aplikace. 

Robot pro chat obdrží utterance a pak pošle nadřazené aplikaci v LUIS k předpovědi. Nejvyšší předpovězený záměr z nadřazené aplikace určuje, která podřízená aplikace LUIS se nazývá Next. Robot pro chat pošle utterance do podřízené aplikace a podrobnější předpověď.

Pochopte, jak se tato hierarchie volání provádí z [dispečera bot Builder v4 – Application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Omezení záměrů v modelu odeslání
Expediční aplikace má 500 zdrojů odeslání, které odpovídají 500 záměrů. 

## <a name="more-information"></a>Další informace

* [Sada robot Framework SDK](https://github.com/Microsoft/botframework)
* [Kurz pro model odeslání](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Rozhraní příkazového řádku pro odesílání](https://github.com/Microsoft/botbuilder-tools)
* Ukázka robota modelu odeslání – [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Další kroky

* Postup [testování dávky](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
