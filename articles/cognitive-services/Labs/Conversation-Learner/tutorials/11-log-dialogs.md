---
title: Jak protokolovat dialogy v modelu Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se protokolovat dialogy v modelu Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 388942e8195048d7528a54e5a290f1724c8e876b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703970"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Postup při protokolování dialogových oken v modelu Conversation Learner

V tomto kurzu se dozvíte, jak se používají dialogy protokolu pro lepší výuku Conversation Learnerch modelů od zaznamenaných interakcí s uživateli reálného světa.

## <a name="video"></a>Video

[![Kurz dialogových oken protokolu Preview](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

a model počasí vytvořený v předchozích kurzech.

## <a name="details"></a>Podrobnosti
Dialogová okna protokolu zaznamenávají protokoly interakce robota s koncovými uživateli. Použitím těchto dialogových oken protokolu můžete opravit popisky entit a výběr akcí, aby se zlepšil výkon modelu a celkový výkon systému.

## <a name="steps"></a>Kroky

Ve webovém uživatelském rozhraní klikněte na "Import kurzů" a vyberte model s názvem "kurz-11-LogDialogs".

Tento model obsahuje jednu entitu s názvem City a akce navržené k reakci na dotazy týkající se počasí v tomto městě. Pro výuku modelu byly použity dva výukové dialogy, takže očekávání výkonu jsou poněkud nízká. Model by se vylepšil o další školení a expozici skutečným interakcím uživatelů v reálném světě.

### <a name="create-a-new-conversation"></a>Vytvořit novou konverzaci

1. Na levém panelu klikněte na možnost protokolovat dialogy a pak na tlačítko nový dialog protokolu.
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte "Austin počasí předpověď".
3. Klikněte na tlačítko dokončené testování.
4. V seznamu klikněte na dialog protokolu Austin počasí předpověď.
5. Na panelu chat klikněte na Austin předpověď počasí na utterance.
6. Klikněte na "Austin" a potom v seznamu entit klikněte na "City".
7. Klikněte na tlačítko odeslat změny.
    - Tato změna v hodnotě entity způsobí u konverzace navazující změny, protože v paměti máme nové hodnoty entit. Pozdější akce se pravděpodobně stanou neplatnými zvlášť, které zahrnují entitu "City".
8. Klikněte na "které město?". utterance na panelu chatu.
9. Vyberte odpověď "počasí v Austin je pravděpodobně Slunečné."
10. Klikněte na tlačítko Uložit jako výukový dialog.
    - Školení se okamžitě vypnulo.

![](../media/T11_logdialog.png)

Jedna poslední poznámka. V závislosti na obchodních potřebách můžete funkci protokolování konverzace vypnout tak, že v části nastavení a zrušíte zaškrtnutí možnosti "konverzace v protokolu".

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zpětné volání detekce entit](./12-entity-detection-callback.md)
