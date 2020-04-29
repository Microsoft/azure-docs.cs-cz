---
title: Životní cyklus znalostní báze Knowledge Base – QnA Maker
description: QnA Maker se nejlépe učí v iterativním koloběhu změn modelu, příkladů utterance, publikování a shromažďování dat z dotazů na koncové body.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77914948"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Životní cyklus znalostní báze v QnA Maker
QnA Maker se nejlépe učí v iterativním koloběhu změn modelu, příkladů utterance, publikování a shromažďování dat z dotazů na koncové body.

![Cyklus vytváření](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Vytváření QnA Maker znalostní báze
QnA Maker koncový bod znalostní báze (KB) poskytuje nejvhodnější odpověď na dotaz na uživatele na základě obsahu KB. Vytvoření znalostní báze je jednorázová akce pro nastavení úložiště obsahu otázek, odpovědí a přidružených metadat. Znalostní bázi se dá vytvořit procházením již existujícího obsahu, jako jsou nejčastější dotazy, příručky k produktům nebo strukturované páry Q-A. Přečtěte si, jak [vytvořit znalostní bázi](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testování a aktualizace znalostní báze

Znalostní báze je připravená na testování, jakmile se naplní obsahem, a to buď redakčním, nebo prostřednictvím automatické extrakce. Interaktivní testování se dá provést na QnA Makerovém portálu prostřednictvím **testovacího** panelu tak, že zadáte běžné uživatelské dotazy a ověříte, že se v odpovědích vrátila správná odpověď a že máte dostatečné skóre spolehlivosti.

* **Řešení výsledků s nízkou mírou spolehlivosti**: Přidejte alternativní otázky.
* **Když dotaz nesprávně vrátí [výchozí odpověď](../How-to/change-default-answer.md)**: přidejte nové odpovědi na správnou otázku.

Tato těsná smyčka test-Update pokračuje, dokud nebudete spokojeni s výsledky. Naučte se [testovat znalostní bázi](../How-To/test-knowledge-base.md).

Pro velké aktualizací KB použijte automatizované testování pomocí [rozhraní generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) a vlastnost `isTest` text, která se dotazuje `test` znalostní báze místo publikované znalostní báze.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze
Po dokončení testování znalostní báze ho můžete publikovat. Publikování nabídne nejnovější verzi testované znalostní báze do vyhrazeného indexu služby Azure Kognitivní hledání reprezentujícího **publikovanou** znalostní bázi. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Všechny změny v testovací verzi znalostní báze tak nebudou mít vliv na publikovanou verzi, která může být v produkční aplikaci živá.

Každý z těchto znalostní báze se dá cílit na samostatné testování. Pomocí rozhraní API můžete cílit na testovací verzi znalostní báze s `isTest` vlastností body ve volání generateAnswer.

Naučte se [publikovat znalostní bázi](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorování využití
Aby bylo možné protokolovat protokoly chatu vaší služby, je nutné povolit Application Insights při [vytváření služby QnA maker](../How-To/set-up-qnamaker-service-azure.md).

Můžete získat různé analýzy využití služeb. Přečtěte si další informace o tom, jak pomocí Application Insights získat [analýzy pro službu QnA maker](../How-To/get-analytics-knowledge-base.md).

Na základě toho, co se seznámíte s analýzou, proveďte příslušné [aktualizace znalostní báze](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Správa verzí pro data ve znalostní bázi

Správa verzí pro data je poskytována prostřednictvím funkcí importu/exportu na stránce **Nastavení** na portálu QnA maker.

Znalostní bázi můžete zálohovat tím, že vyexportujete znalostní bázi, a to `.tsv` ve `.xls` formátu. Po exportu zahrňte tento soubor jako součást vaší běžné kontroly zdrojového kódu.

Pokud se potřebujete vrátit ke konkrétní verzi, budete muset tento soubor naimportovat z místního systému. Exportovaná znalostní báze se **musí** použít jenom přes import na stránce **Nastavení** . Nedá se použít jako zdroj dat dokumentu nebo dokumentu URL. Tato akce nahradí otázky a odpovědi aktuálně ve znalostní bázi obsahem importovaného souboru.

## <a name="test-and-production-knowledge-base"></a>Testovací a provozní znalostní báze
Znalostní báze je úložiště otázek a sad odpovědí vytvořených, udržovaných a používaných prostřednictvím QnA Maker. Každý prostředek QnA Maker může obsahovat více znalostní báze.

Znalostní báze má dva stavy: *test* a *Publikováno*.

### <a name="test-knowledge-base"></a>Test znalostní báze Knowledge Base

*Test Knowledge Base* je verze, která je aktuálně upravována, ukládána a testována pro přesnost a úplnost reakcí. Změny provedené ve znalostní bázi test se netýkají koncového uživatele vaší aplikace nebo robota chatu. Testovací znalostní báze se označuje jako `test` požadavek HTTP. `test` Znalostní báze je k dispozici v podokně interaktivní **test** portálu QnA maker.

### <a name="production-knowledge-base"></a>Znalostní báze pro produkci

*Publikovaná znalostní báze* je verze, která se používá v robotu nebo v aplikaci chatu. Akce publikování znalostní báze znamená obsah testovací znalostní báze v publikované verzi znalostní báze. Vzhledem k tomu, že publikovaná znalostní báze je verze, kterou aplikace používá prostřednictvím koncového bodu, ujistěte se, že je obsah správný a dobře testovaný. Publikovaná znalostní báze je známá jako `prod` v požadavku HTTP.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aktivní návrhy výukových kurzů](./active-learning-suggestions.md)