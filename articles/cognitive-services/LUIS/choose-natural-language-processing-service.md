---
title: Obohacování konverzací pomocí služeb NLP Cognitive Services
titleSuffix: Azure Cognitive Services
description: Služby Cognitive Services poskytují dvě služby zpracování přirozeného jazyka, language understanding a QnA Maker, z nichž každá má jiný účel. Pochopit, kdy používat každou službu a jak se kompliment navzájem.
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: 32159b37d3d1a8609181d81dc1a73f27177adb85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73818195"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>Použití služeb Cognitive Services se zpracováním přirozeného jazyka (NLP) k obohacení konverzací botů

Služby Cognitive Services poskytují dvě služby zpracování přirozeného jazyka, [language understanding](what-is-luis.md) a [QnA Maker](../qnamaker/overview/overview.md), z nichž každá má jiný účel. Pochopit, kdy používat každou službu a jak se kompliment navzájem. 

Zpracování přirozeného jazyka (NLP) umožňuje vaší klientské aplikaci, jako je například chatovací robot, pracovat s uživateli pomocí přirozeného jazyka. Uživatel zadá větu nebo frázi. Text uživatele může mít špatnou gramatiku, pravopis a interpunkci. Služba Cognitive Service může pracovat prostřednictvím věty uživatele tak jako tak, vrácení informací chat bot potřebuje pomoci uživateli. 

## <a name="cognitive-services-with-nlp"></a>Kognitivní služby s NLP

Porozumění jazykům (LUIS) a QnA Maker poskytují NLP. Klientská aplikace odešle text v přirozeném jazyce. Služba převezme text, zpracuje jej a vrátí výsledek. 

## <a name="when-to-use-each-service"></a>Kdy použít každou službu

Porozumění jazykům (LUIS) a QnA Maker řeší různé problémy. Služba LUIS určuje záměr textu uživatele (označované jako utterance), zatímco QnA Maker určuje odpověď na text uživatele (označovaný jako dotaz). 

Chcete-li vybrat správnou službu, musíte pochopit text uživatele pocházející z klientské aplikace a jaké informace klientská aplikace potřebuje získat ze služby Cognitive Service.

Pokud váš chatovací robot `How do I get to the Human Resources building on the Seattle North campus?`obdrží text , použijte níže uvedenou tabulku, abyste pochopili, jak jednotlivé služby s textem fungují.

|Služba|Klientská aplikace určuje|
|--|--|
|LUIS|**Určuje záměr uživatele** textu - služba nevrátí odpověď na otázku. Tento text je například klasifikován `FindLocation` jako odpovídající záměru.<br>|
|QnA Maker|**Vrátí odpověď na otázku** z vlastní znalostní báze. Tento text je například určen jako otázka se `Get on the #9 bus and get off at Franklin street`statickou textovou odpovědí společnosti .|
|||

## <a name="when-do-you-use-luis"></a>Kdy používáte službu LUIS? 

Luis, když potřebujete znát záměr utterance jako součást procesu v chatu robota. Pokračování s ukázkovým `How do I get to the Human Resources building on the Seattle North campus?`textem , jakmile znáte, že záměrem uživatele je najít umístění, můžete předat podrobnosti o utterance (vytažené s entitami) jiné službě, například dopravnímu serveru, abyste získali odpověď. 

Není nutné kombinovat LUIS a QnA Maker k určení záměru. 

Můžete kombinovat dvě služby pro tuto utterance, pokud chatovací robot potřebuje zpracovat text na základě záměrů a entit (pomocí LUIS) stejně jako najít konkrétní statické textové odpovědi (pomocí QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Kdy používáte QnA Maker? 

QnA Maker použijte, pokud máte statickou znalostní bázi odpovědí. Tato znalostní báze je vlastní vašim potřebám, které jste vytvořili pomocí dokumentů, jako jsou soubory PDF a adresy URL. 

Pokračování s příklad utterance `How do I get to the Human Resources building on the Seattle North campus?`, odešlete text jako dotaz na publikovanou službu QnA Maker a obdržíte nejlepší odpověď. 

Není nutné kombinovat LUIS a QnA Maker k určení odpovědi na otázku.

Můžete kombinovat dvě služby pro tuto utterance, pokud chatovací robot potřebuje zpracovat text na základě záměrů a entit (pomocí LUIS) stejně jako najít odpověď (pomocí QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Využijte obě služby v případě neúplné znalostní báze

Pokud vytváříte znalostní bázi QnA Maker, ale víte, že se doména předmětu mění (například včasné informace), můžete kombinovat služby LUIS a QnA Maker. To umožňuje použít informace ve znalostní bázi, ale také použít LUIS k určení záměru uživatele. Jakmile má klientská aplikace záměr, může požadovat relevantní informace z jiného zdroje. 

Vaše klientská aplikace bude muset sledovat luis a QnA Maker odpovědi na skóre. Pokud skóre z QnA Maker je pod některé libovolné prahové hodnoty, použijte záměr a informace o entitě vrácené z LUIS předat informace o službě třetí strany.

Pokračujte v ukázkovém textu , předpokládejme, `How do I get to the Human Resources building on the Seattle North campus?`že QnA Maker vrátí nízké skóre spolehlivosti. Použijte záměr vrácený `FindLocation` z LUIS a všechny extrahované entity, jako je například `Human Resources building` a `Seattle North campus`, k odeslání těchto informací do mapování nebo vyhledávací služby pro jinou odpověď. 

Tuto odpověď jiného výrobce můžete uživateli představit k ověření. Jakmile budete mít souhlas uživatele, můžete se vrátit do QnA Maker přidat informace pro růst své znalosti. 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Obě služby používejte, když chatovací robot potřebuje více informací

Pokud váš chatovací robot potřebuje více informací, než poskytuje kterákoli služba, chcete-li pokračovat prostřednictvím rozhodovacího stromu, použijte obě služby a zpracujte obě odpovědi v klientské aplikaci. 

Pomocí nástroje **[rozhraní SE řízení montovny rozhraní PRO odesílání](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** architektury botů můžete vytvořit proces pro práci s oběma službami. Tento nástroj vytváří horní LUIS aplikace záměrů, které odesílá mezi LUIS a QnA Maker jako podřízené aplikace. 

Použijte bot tvůrce ukázky **NLP s odesláním**, v [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) nebo [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), k implementaci tohoto typu chatu bot. 

## <a name="best-practices"></a>Osvědčené postupy

Implementujte osvědčené postupy pro každou službu:

* Doporučené postupy [služby LUIS](luis-concept-best-practices.md)
* Doporučené postupy [pro qnA maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Viz také

* [Language Understanding (LUIS)](what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Odeslání CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Ukázky architektury botů](https://github.com/Microsoft/BotBuilder-Samples)
* [Služba Azure botů](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Emulátor robotů Azure](https://github.com/Microsoft/BotFramework-Emulator)
* [Webový chat s frameworkem botů](https://github.com/microsoft/BotFramework-WebChat)