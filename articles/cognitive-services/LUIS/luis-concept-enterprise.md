---
title: Koncepty podnikové aplikace LUIS – Azure | Dokumentace Microsoftu
description: Koncepce návrhu pro velké aplikace LUIS.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/05/2018
ms.author: v-geberr
ms.openlocfilehash: aca67db88255585355bc59a29e53639bc5eca717
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37886752"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Podnikové strategie pro aplikace LUIS
Projděte si tyto strategie návrhu pro podnikové aplikace.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Pokud očekáváte, že služba LUIS požadavky nad těchto kvóty
Pokud vaše frekvence požadavků aplikace LUIS překročí celkový počet povolených [míra kvót](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), rozložit zátěž pro další aplikace LUIS se [stejnou definici pojmů aplikace](#use-multiple-apps-with-same-app-definition) nebo vytvořte a [přiřadit více klíčů](#assign-multiple-luis-keys-to-same-app) k aplikace. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Použití více aplikací s stejné definice aplikace
Původní aplikaci LUIS, exportovat a importovat aplikace do samostatných aplikací. Každá aplikace má vlastní ID aplikace. Když publikujete, namísto použití stejného klíče mezi všemi aplikacemi, vytvořte samostatné klíč pro každou aplikaci. Vyrovnávání zatížení mezi všemi aplikacemi tak, aby žádné aplikace s jedním se cítili zahlcení. Přidat [Application Insights](luis-tutorial-bot-csharp-appinsights.md) monitorovat využití. 

Chcete-li získat stejnou hlavní záměr mezi všemi aplikacemi, ujistěte se, že záměru předpovědi mezi prvním a druhém záměr je dostatečně, LUIS není zaměňovat, poskytující různé výsledky mezi aplikacemi pro malé změny v projevy. 

Určete jednu aplikaci jako hlavní server. Jakékoli projevy, které jsou navržené ke kontrole by měl přidá do hlavní aplikace poté přesunut na všechny ostatní aplikace. To je úplné export aplikace, nebo načítání s popiskem projevy od hlavního na podřízené položky. Načítání lze provést buď z [LUIS](luis-reference-regions.md) webu nebo rozhraní API pro vytváření obsahu pro [jednotné utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) nebo pro [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Naplánovat pravidelnou [přehled koncových bodů projevy](label-suggested-utterances.md) pro službu active learning, jako je například každé dva týdny, pak přetrénujete a znovu publikovat. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Přiřadit více LUIS klíče pro stejnou aplikaci
Pokud aplikace LUIS obdrží další koncový bod přístupů než povoluje kvóta jeden klíč, vytvořit a přiřadit aplikace LUIS více klíčů. Vytvoření traffic manager nebo Vyrovnávání zatížení do spravovat dotazy koncový bod v rámci klíče koncového bodu. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Když monolitické aplikace vrátí nesprávné záměr
Pokud se vaše aplikace k předvídání širokou škálu projevy uživatele, zvažte implementaci [odesílání modelu](#dispatch-tool-and-model). Rozdělení monolitické aplikace umožňuje zjišťování fokus mezi záměry úspěšně místo získávání zaměňovat mezi záměry napříč aplikace nadřazené a podřízené aplikace LUIS. 

Naplánovat pravidelnou [přehled koncových bodů projevy](label-suggested-utterances.md) pro službu active learning, jako je například každé dva týdny, pak přetrénujete a znovu publikovat. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Když je potřeba mít více než 500 záměrů
Řekněme například, že vyvíjíte asistent office, který má více než 500 záměry. Pokud 200 záměry se týkají plánování schůzek, 200 se týkají připomenutí, 200 jsou o získání informací o kolegy a 200 jsou určené pro odesílání e-mailů, záměry skupiny tak, aby každá skupina je v jedné aplikaci, vytvořte aplikaci s nejvyšší úrovně obsahující každý záměr. Použití [odeslání nástroje a architektury](#dispatch-tool-and-model) k sestavení aplikace nejvyšší úrovně. Změňte robotům, aby použít jako zobrazení v kaskádové volání [kurz odeslání][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Pokud je potřeba sloučit několik LUIS a QnA maker aplikace
Pokud máte několik LUIS a QnA maker aplikace, které je potřeba reagovat na robota, použijte [odeslání nástroj](#dispatch-tool-and-model) sestavit aplikaci nejvyšší úrovně. Změňte robotům, aby použít jako zobrazení v kaskádové volání [kurz odeslání][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Nástroj pro odesílání a model
Použití [odeslání] [ dispatch-tool] nástroje příkazového řádku v [BotBuilder nástroje](https://github.com/Microsoft/botbuilder-tools) zkombinovat více LUIS nebo nástroje QnA Maker aplikace do nadřazené aplikace LUIS. Tento přístup umožňuje mít nadřazenou doménu, včetně všech tématech a jiné podřízené domény předmětu v samostatných aplikací. 

![Koncepční obrázek architektury odeslání](./media/luis-concept-enterprise/dispatch-architecture.png)

Nadřazené doméně, je uvedena v LUIS jako **V odesílání** aplikace. 

![Snímek obrazovky LUIS seznam aplikací s vytvořené nástrojem odeslání aplikace LUIS](./media/luis-concept-enterprise/dispatch.png)

Chatovací robot přijímá utterance pak odešle nadřazená aplikace LUIS pro předpovědi. Hlavní záměr předpokládané z nadřazené aplikace určuje, které podřízené aplikace LUIS se nazývá dále. Chatovací robot odešle do aplikace podřízené pro konkrétnější predikcí utterance.

Vysvětlení, jak se provádí tato hierarchie volání z v4 Bot Builder [kurz aplikace dispečer][dispatcher-application-tutorial].  

### <a name="intent-limits-in-dispatch-model"></a>V modelu odeslání záměru omezení
Odeslání aplikace má 500 odeslání zdrojů, odpovídá 500 záměrů, jako maximální. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [testování dávky](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch