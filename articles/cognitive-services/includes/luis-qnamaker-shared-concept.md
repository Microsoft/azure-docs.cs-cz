---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.author: diberry
ms.openlocfilehash: 02610e647e2138cbf52f86c22107feec2d61273b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604976"
---
Cognitive Services poskytuje dvě služby pro zpracování v přirozeném jazyce, [Language Understanding](../luis/what-is-luis.md) a [QnA maker](../qnamaker/overview/overview.md), každý s jiným účelem. Informace o tom, kdy používat jednotlivé služby a jak se vzájemně přidávají

Zpracování přirozeného jazyka (NLP) umožňuje vaše klientské aplikace, jako je třeba robota, pracovat s uživateli, a to v přirozeném jazyce. Uživatel zadá větu nebo frázi. Text uživatele může mít špatnou gramatiku, pravopis a interpunkční znaménko. Služba rozpoznávání může i přesto pracovat s uživatelskou větou a vracet informace, které musí uživatel poznamenat v programu chat bot.

## <a name="cognitive-services-with-nlp"></a>Cognitive Services s NLP

Language Understanding (LUIS) a QnA Maker poskytují NLP. Klientská aplikace odesílá text v přirozeném jazyce. Služba převezme text, zpracuje ho a vrátí výsledek.

## <a name="when-to-use-each-service"></a>Kdy použít jednotlivé služby

Language Understanding (LUIS) a QnA Maker řeší různé problémy. LUIS určuje záměr textu uživatele (označovaného jako utterance), zatímco QnA Maker určuje odpověď na text uživatele (známý jako dotaz).

Aby bylo možné vybrat správnou službu, je nutné pochopit text uživatele z klientské aplikace a informace o tom, které klientské aplikace potřebují získat ze služby pro rozpoznávání.

Pokud robot váš chat obdrží text `How do I get to the Human Resources building on the Seattle North campus?`, použijte následující graf, který vám pomůže pochopit, jak každá služba pracuje s textem.

|Služba|Klientská aplikace určuje|
|--|--|
|LUIS|**Určuje** , zda je uživatel členem textu – služba nevrátí odpověď na otázku. Například tento text je klasifikován jako shodný s `FindLocation` záměrem.<br>|
|QnA Maker|**Vrátí odpověď na dotaz** z vlastní znalostní báze. Například tento text je určen jako otázka se statickou textovou odpovědí `Get on the #9 bus and get off at Franklin street`.|
|||

## <a name="when-do-you-use-luis"></a>Kdy používáte LUIS?

LUIS použijte, pokud potřebujete znát záměr utterance jako součást procesu v robotovi chatu. Po pokračování s ukázkovým textem `How do I get to the Human Resources building on the Seattle North campus?`, když znáte záměr uživatele, zjistíte umístění, můžete podrobnosti o utterance (vydaná s entitami) předat jiné službě, jako je například Transport Server, a získat tak odpověď.

K určení záměru nemusíte kombinovat LUIS a QnA Maker.

Tyto dvě služby můžete v tomto utterance kombinovat, pokud robot robot potřebuje zpracovat text na základě záměrů a entit (pomocí LUIS) a najít konkrétní odpověď statického textu (pomocí QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Kdy použijete QnA Maker?

Použijte QnA Maker, když máte statickou znalostní bázi odpovědí. Tato znalostní báze je vlastní pro vaše potřeby, které jste sestavili s dokumenty, jako jsou soubory PDF a adresy URL.

V případě, že budete pokračovat `How do I get to the Human Resources building on the Seattle North campus?`s příkladem utterance, pošlete text jako dotaz do publikované služby QnA maker a dostanete nejlepší odpověď.

K určení odpovědi na otázku nemusíte kombinovat LUIS a QnA Maker.

Tyto dvě služby pro tento utterance můžete kombinovat, pokud robot robot potřebuje zpracovat text založený na záměrech a entitách (pomocí LUIS) a také najít odpověď (pomocí QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Pokud je vaše znalostní báze neúplná, použijte obě služby.

Pokud sestavíte QnA Maker znalostní bázi, ale víte, že se doména předmětu mění (například včasné informace), mohli byste kombinovat služby LUIS a QnA Maker. To vám umožňuje používat informace ve znalostní bázi Knowledge Base, ale také pomocí LUIS určit záměr uživatele. Jakmile má klientská aplikace záměr, může požádat o relevantní informace z jiného zdroje.

Vaše klientská aplikace by musela monitorovat LUIS i QnA Maker reakce na skóre. Pokud je skóre z QnA Maker menší než jakákoli prahová hodnota, použijte informace o záměru a entitě vrácené z LUIS a předejte informace službě třetí strany.

Pokud budete pokračovat s ukázkovým `How do I get to the Human Resources building on the Seattle North campus?`textem, Předpokládejme, že QnA maker vrací skóre s nízkou spolehlivostí. Použijte záměr vrácený z LUIS `FindLocation` a všechny extrahované entity, například `Human Resources building` a `Seattle North campus`, k odeslání těchto informací službě mapování nebo vyhledávání pro jinou odpověď.

Tuto odpověď třetí strany můžete zobrazit uživateli k ověření. Jakmile budete mít schválení uživatele, můžete přejít zpět na QnA Maker a přidat informace, abyste mohli rozšířit vaše znalosti.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>V případě, že robot potřebuje další informace, použijte obě služby.

Pokud vaše chatovací robot potřebuje více informací, než je kterákoli z poskytované služby, pokračujte v rozhodování pomocí obou služeb a zpracujte obě odezvy v klientské aplikaci.

Pomocí nástroje bot Framework **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** můžete vytvořit proces, který bude spolupracovat s oběma službami. Tento nástroj vytvoří nejvyšší LUIS aplikaci záměrů, které odesílají mezi LUIS a QnA Maker jako podřízené aplikace.

K implementaci tohoto typu robota chatu použijte vzorový tvůrce robota **NLP s odesláním**v [jazyce C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) nebo [Node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch).

## <a name="best-practices"></a>Osvědčené postupy

Implementujte osvědčené postupy pro každou službu:

* Osvědčené postupy pro [Luis](../luis/luis-concept-best-practices.md)
* [QnA maker](../qnamaker/concepts/best-practices.md) osvědčené postupy

## <a name="see-also"></a>Viz také

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Rozhraní příkazového řádku pro odesílání](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Ukázky pro robot Framework](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Emulátor Azure bot](https://github.com/Microsoft/BotFramework-Emulator)
* [Webový chat pro robot Framework](https://github.com/microsoft/BotFramework-WebChat)