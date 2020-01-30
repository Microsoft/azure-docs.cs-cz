---
title: Životní cyklus znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker se učí nejlepší v iterativní cyklus změny modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b1978e45a7554358ddd948879143411f89e4c1b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843401"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Životní cyklus znalostní báze Knowledge base v nástroje QnA Maker
Nástroj QnA Maker se učí nejlepší v iterativní cyklus změny modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů.

![Cyklus vytváření](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Vytvoření znalostní báze QnA Maker
Koncový bod znalostní báze knowledge base (KB) nástroje QnA Maker poskytuje nejlepší shody odpověď na dotaz uživatele na základě obsahu KB. Vytvoření znalostní báze se o jednorázovou akci pro nastavení úložiště obsahu otázky, odpovědi a přidružená metadata. Procházení existující obsah, například stránky – nejčastější dotazy, produktových příruček nebo strukturovaných Q A páry je možné vytvořit znalostní báze. Zjistěte, jak [vytvoření znalostní báze](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testování a aktualizují znalostní báze

Znalostní báze připravený k testování, jakmile se vyplní s obsahem, buď pomocí redakčních úprav nebo Automatická extrakce. Interaktivní testování se dá provést na QnA Makerovém portálu prostřednictvím **testovacího** panelu tak, že zadáte běžné uživatelské dotazy a ověříte, že se v odpovědích vrátila správná odpověď a že máte dostatečné skóre spolehlivosti.

* **Řešení výsledků s nízkou mírou spolehlivosti**: Přidejte alternativní otázky.
* **Když dotaz nesprávně vrátí [výchozí odpověď](../How-to/change-default-answer.md)** : přidejte nové odpovědi na správnou otázku.

Tato těsné smyčce test aktualizace pokračuje, dokud budete spokojeni s výsledky. Zjistěte, jak [testování znalostní báze](../How-To/test-knowledge-base.md).

Pro velké aktualizací KB použijte automatizované testování pomocí [rozhraní generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) a vlastnost text `isTest`, která se dotazuje `test` znalostní báze místo publikované znalostní báze.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze
Po dokončení testování ve znalostní bázi, můžete ji publikovat. Publikování nabídne nejnovější verzi testované znalostní báze do vyhrazeného indexu služby Azure Kognitivní hledání reprezentujícího **publikovanou** znalostní bázi. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tímto způsobem veškerých změnách prováděných na testovací verzi znalostní báze nemají vliv publikovanou verzi, které můžou být naživo v produkčním prostředí aplikace.

Každá z těchto znalostních bází může služba je určená pro testování samostatně. Pomocí rozhraní API můžete v volání generateAnswer cílit na testovací verzi znalostní báze s vlastností těla `isTest`.

Zjistěte, jak [publikovat znalostní báze](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorování využití
Aby bylo možné protokol protokoly chatu vaší služby, je třeba, povolte Application Insights při vám [vytvoření služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Můžete získat různé analýzy využití služeb. Další informace o tom, jak pomocí služby application insights získáte [analytics pro vaši službu QnA Maker](../How-To/get-analytics-knowledge-base.md).

Podle další analýzy, provést příslušné [aktualizace do znalostní báze](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Správa verzí pro data ve znalostní bázi

Správa verzí pro data je poskytována prostřednictvím funkcí importu/exportu na stránce **Nastavení** na portálu QnA maker.

Znalostní bázi můžete zálohovat vyexportem znalostní báze, a to buď ve formátu `.tsv` nebo `.xls`. Po exportu zahrňte tento soubor jako součást vaší běžné kontroly zdrojového kódu.

Pokud se potřebujete vrátit ke konkrétní verzi, budete muset tento soubor naimportovat z místního systému. Exportovaná znalostní báze se **musí** použít jenom přes import na stránce **Nastavení** . Nedá se použít jako zdroj dat dokumentu nebo dokumentu URL. Tato akce nahradí otázky a odpovědi aktuálně ve znalostní bázi obsahem importovaného souboru.

## <a name="test-and-production-knowledge-base"></a>Testovací a provozní znalostní báze
Znalostní báze je úložiště otázek a sad odpovědí vytvořených, udržovaných a používaných prostřednictvím QnA Maker. Každý prostředek QnA Maker může obsahovat více znalostní báze.

Znalostní báze má dva stavy: *test* a *Publikováno*.

### <a name="test-knowledge-base"></a>Test znalostní báze Knowledge Base

*Test Knowledge Base* je verze, která je aktuálně upravována, ukládána a testována pro přesnost a úplnost reakcí. Změny provedené ve znalostní bázi test se netýkají koncového uživatele vaší aplikace nebo robota chatu. Testovací znalostní báze se v požadavku HTTP označuje jako `test`. `test` znalostní báze je k dispozici v podokně interaktivní **test** portálu QnA maker.

### <a name="production-knowledge-base"></a>Znalostní báze pro produkci

*Publikovaná znalostní báze* je verze, která se používá v robotu nebo v aplikaci chatu. Akce publikování znalostní báze znamená obsah testovací znalostní báze v publikované verzi znalostní báze. Vzhledem k tomu, že publikovaná znalostní báze je verze, kterou aplikace používá prostřednictvím koncového bodu, ujistěte se, že je obsah správný a dobře testovaný. Publikovaná znalostní báze se označuje jako `prod` v požadavku HTTP.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Skóre spolehlivosti](./confidence-score.md)