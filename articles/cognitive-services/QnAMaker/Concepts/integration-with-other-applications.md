---
title: Integrace s jinými aplikacemi – QnA Maker
description: QnA Maker se integruje do klientských aplikací, jako jsou roboty chatu, stejně jako jiné služby pro zpracování v přirozeném jazyce, jako je například Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ff5e64dd31542b1e31df6d9ca709567b322995ce
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776523"
---
# <a name="design-knowledge-base-for-client-applications"></a>Návrh znalostní báze pro klientské aplikace

QnA Maker se integruje do klientských aplikací, jako jsou roboty chatu, stejně jako jiné služby pro zpracování v přirozeném jazyce, jako je například Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integrace s konverzací klienta

QnA Maker se integruje s klientskými aplikacemi v konverzaci, jako je [Microsoft bot Framework](https://dev.botframework.com/). Text odeslaný do QnA Maker není nutné vyčistit ani transformovat. QnA Maker přijímá přirozené jazyky a vrací nejlepší odpověď.

## <a name="create-a-bot-without-writing-any-code"></a>Vytvoření robota bez psaní kódu

Po publikování znalostní báze vytvořte robota ze stránky **publikování** tak, že vyberete tlačítko **vytvořit robot** . Pomocí [kurzu robota](../Quickstarts/create-publish-knowledge-base.md) se dozvíte, co se stane po výběru tlačítka.

## <a name="providing-multi-turn-conversations"></a>Zajištění vícenásobného zapínání konverzací

Klient bot nabízí nejlepší vybranou odpověď ze znalostní báze a může poskytnout následné výzvy, pokud je odpověď součástí QnA páru. Naučte se, [jak](../how-to/multiturn-conversation.md) do znalostní báze přidat otázky a sady odpovědí s vícenásobným zavoláním.

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

I když QnA Maker zpracovává dotazy, které používají zpracování přirozeného jazyka, lze také použít část většího systému, který odpovídá na otázky z více znalostní báze. Můžete zkombinovat QnA Maker s jinou službou pro rozpoznávání Language Understanding (LUIS), která zajistí zpracování v přirozeném jazyce před tím, než se dostanete ke konkrétní znalostní bázi. Přečtěte si další informace o tom, kdy a jak používat [Luis a QnA maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) dohromady.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [konceptech](development-lifecycle-knowledge-base.md) vývojové cykly pro QnA maker.