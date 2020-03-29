---
title: Životní cyklus znalostní báze - QnA Maker
description: QnA Maker se nejlépe učí v iterativním cyklu změn modelu, příkladů utterance, publikování a shromažďování dat z dotazů koncového bodu.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914948"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Životní cyklus znalostní báze v QnA Makeru
QnA Maker se nejlépe učí v iterativním cyklu změn modelu, příkladů utterance, publikování a shromažďování dat z dotazů koncového bodu.

![Cyklus vytváření](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Vytvoření znalostní báze QnA Maker
Koncový bod znalostní báze QnA Maker (KB) poskytuje odpověď shody na uživatelský dotaz na základě obsahu kb. Vytvoření znalostní báze je jednorázová akce k nastavení úložiště obsahu otázek, odpovědí a přidružených metadat. Znalostní báze lze vytvořit procházením již existujícího obsahu, jako jsou stránky s nejčastějšími dotazy, příručky k produktům nebo strukturované dvojice Q-A. Přečtěte [si,](../quickstarts/create-publish-knowledge-base.md)jak vytvořit znalostní bázi .

## <a name="testing-and-updating-the-knowledge-base"></a>Testování a aktualizace znalostní báze

Znalostní báze je připravena k testování, jakmile je naplněna obsahem, a to buď redakční, nebo prostřednictvím automatické extrakce. Interaktivní testování lze provést na portálu QnA Maker prostřednictvím panelu **Test** zadáním běžných uživatelských dotazů a ověřením, že odpovědi vrácené se správnou odpovědí a dostatečným skóre spolehlivosti.

* **Chcete-li opravit nízké skóre spolehlivosti**: přidejte alternativní otázky.
* **Pokud dotaz nesprávně vrátí [výchozí odpověď](../How-to/change-default-answer.md)**: přidejte nové odpovědi na správnou otázku.

Tato těsná smyčka aktualizace testu pokračuje, dokud nebudete spokojeni s výsledky. Přečtěte si, jak [otestovat znalostní bázi](../How-To/test-knowledge-base.md).

Pro velké kb, použijte automatizované testování s `isTest` [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) a `test` body vlastnost, která dotazuje znalostní báze namísto publikované znalostní báze.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze
Po dokončení testování znalostní báze ji můžete publikovat. Publikování odešle nejnovější verzi testované znalostní báze do vyhrazeného indexu Azure Cognitive Search představujícího **publikovanou znalostní** bázi. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tímto způsobem žádné změny provedené v testovací verzi znalostní báze nemají vliv na publikovanou verzi, která může být aktivní v produkční aplikaci.

Každá z těchto znalostních bází může být určena pro samostatné testování. Pomocí api můžete cílit na testovací verzi znalostní báze s `isTest` vlastností body ve volání generateAnswer.

Přečtěte si, jak [publikovat znalostní bázi](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorování využití
Abyste mohli protokolovat protokoly chatu ve vaší službě, musíte při [vytváření služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md)povolit application insights .

Můžete získat různé analýzy využití služeb. Přečtěte si další informace o tom, jak pomocí přehledů aplikací získat [analýzy pro vaši službu QnA Maker](../How-To/get-analytics-knowledge-base.md).

Na základě toho, co se dozvíte z analýzy, proveďte příslušné [aktualizace znalostní báze](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Správa verzí dat ve znalostní bázi

Správa verzí dat je poskytována prostřednictvím funkcí importu a exportu na stránce **Nastavení** na portálu QnA Maker.

Znalostní bázi můžete zálohovat exportem znalostní `.tsv` báze v jednom formátu nebo `.xls` ve formátu. Po exportu zahrňte tento soubor jako součást běžné kontroly správy zdrojového kódu.

Pokud se potřebujete vrátit k určité verzi, je třeba importovat tento soubor z místního systému. Exportovaná znalostní báze **lze** použít pouze prostřednictvím importu na stránce **Nastavení.** Nelze jej použít jako zdroj dat dokumentu url nebo adresu URL. Tím se nahradí otázky a odpovědi, které jsou aktuálně ve znalostní bázi, obsahem importovaného souboru.

## <a name="test-and-production-knowledge-base"></a>Znalostní báze test a výroba
Znalostní báze je úložiště otázek a sad odpovědí vytvořených, udržovaných a používaných prostřednictvím QnA Makeru. Každý prostředek QnA Maker uchovává více znalostních bází.

Znalostní báze má dva stavy: *test* a *publikováno*.

### <a name="test-knowledge-base"></a>Zkušební znalostní báze

*Testovací znalostní báze* je verze, která je aktuálně upravována, uložena a testována na přesnost a úplnost odpovědí. Změny provedené v testovací znalostní bázi nemají vliv na koncového uživatele aplikace nebo chatovacího robota. Testovací znalostní báze je známá jako `test` v požadavku HTTP. Znalosti `test` jsou k dispozici v interaktivním **testovacím** podokně portálu QnA Maker.

### <a name="production-knowledge-base"></a>Znalostní báze pro výrobu

*Publikovaná znalostní báze* je verze, která se používá ve vašem chatovacím robotu nebo aplikaci. Akce publikování znalostní báze umístí obsah testovací znalostní báze do publikované verze znalostní báze. Vzhledem k tomu, že publikovaná znalostní báze je verze, kterou aplikace používá prostřednictvím koncového bodu, ujistěte se, že obsah je správný a dobře testovaný. Publikovaná znalostní báze je známá jako `prod` v požadavku HTTP.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Návrhy aktivních učení](./active-learning-suggestions.md)