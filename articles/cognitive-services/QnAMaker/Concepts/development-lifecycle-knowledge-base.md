---
title: Životní cyklus znalostní báze Knowledge Base – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker se nejlépe učí v iterativním koloběhu změn modelu, příkladů utterance, publikování a shromažďování dat z dotazů na koncové body.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1cb5af13bdd309c762337e64ecde8538afc756b0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794846"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Životní cyklus znalostní báze v QnA Maker
QnA Maker se nejlépe učí v iterativním koloběhu změn modelu, příkladů utterance, publikování a shromažďování dat z dotazů na koncové body. 

![Cyklus vytváření](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Vytváření QnA Maker znalostní báze
QnA Maker koncový bod znalostní báze (KB) poskytuje nejvhodnější odpověď na dotaz na uživatele na základě obsahu KB. Vytvoření znalostní báze je jednorázová akce pro nastavení úložiště obsahu otázek, odpovědí a přidružených metadat. Znalostní bázi se dá vytvořit procházením již existujícího obsahu, jako jsou nejčastější dotazy, příručky k produktům nebo strukturované páry Q-A. Přečtěte si, jak [vytvořit znalostní bázi](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testování a aktualizace znalostní báze

Znalostní báze je připravená na testování, jakmile se naplní obsahem, a to buď redakčním, nebo prostřednictvím automatické extrakce. Interaktivní testování se dá provést na QnA Makerovém portálu prostřednictvím **testovacího** panelu tak, že zadáte běžné uživatelské dotazy a ověříte, že se v odpovědích vrátila správná odpověď a že máte dostatečné skóre spolehlivosti. 

* **Řešení výsledků s nízkou mírou spolehlivosti**: Přidejte alternativní otázky. 
* **Když dotaz nesprávně vrátí [výchozí odpověď](confidence-score.md#change-default-answer)** : přidejte nové odpovědi na správnou otázku. 

Tato těsná smyčka test-Update pokračuje, dokud nebudete spokojeni s výsledky. Naučte se [testovat znalostní bázi](../How-To/test-knowledge-base.md).

Pro velké aktualizací KB použijte automatizované testování pomocí [rozhraní generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) a vlastnost těla `isTest`, která se dotazuje `test` znalostní báze místo publikované znalostní báze. 

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

Každý z těchto znalostní báze se dá cílit na samostatné testování. Pomocí rozhraní API můžete v volání generateAnswer cílit na testovací verzi znalostní báze s vlastností těla `isTest`.

Naučte se [publikovat znalostní bázi](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorování využití
Aby bylo možné protokolovat protokoly chatu vaší služby, je nutné povolit Application Insights při [vytváření služby QnA maker](../How-To/set-up-qnamaker-service-azure.md).

Můžete získat různé analýzy využití služeb. Přečtěte si další informace o tom, jak pomocí Application Insights získat [analýzy pro službu QnA maker](../How-To/get-analytics-knowledge-base.md).

Na základě toho, co se seznámíte s analýzou, proveďte příslušné [aktualizace znalostní báze](../How-To/edit-knowledge-base.md).

## <a name="version-control-of-a-knowledge-base"></a>Správa verzí ve znalostní bázi

Správa verzí není od QnA Maker k dispozici. Musíte vyexportovat znalostní bázi ze stránky **Nastavení** a použít vlastní metodologii a nástroje.

Export znalostní báze do formátu TSV nebo XLS se dokončí na stránce **Nastavení** . 

Pokud se potřebujete vrátit ke konkrétní verzi, budete muset tento soubor naimportovat z místního systému. Na stránce **Nastavení** IMPORTUJTE soubor TSV nebo XLS. Tato akce nahradí otázky a odpovědi aktuálně ve znalostní bázi obsahem importovaného souboru.   

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Hodnocení spolehlivosti](./confidence-score.md)