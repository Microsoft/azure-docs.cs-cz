---
title: Integrace s dalšími aplikacemi - QnA Maker
description: QnA Maker integruje do klientských aplikací, jako jsou chatovací roboty, stejně jako s jinými službami zpracování přirozeného jazyka, jako je například language understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804183"
---
# <a name="design-knowledge-base-for-client-applications"></a>Návrh znalostní báze pro klientské aplikace

QnA Maker integruje do klientských aplikací, jako jsou chatovací roboty, stejně jako s jinými službami zpracování přirozeného jazyka, jako je například language understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integrace s konverzačním klientem

QnA Maker integruje s konverzační klientské aplikace, jako je [Microsoft Bot Framework](https://dev.botframework.com/). Text odeslaný do QnA Makeru nemusí být vyčištěn ani transformován. QnA Maker přijímá přirozené jazyky a vrací nejlepší odpověď.

## <a name="create-a-bot-without-writing-any-code"></a>Vytvořte bot bez psaní kódu

Po publikování znalostní báze vytvořte robota ze stránky **Publikovat** výběrem tlačítka **Vytvořit robota.** Pomocí [kurzu pro roboty](../Quickstarts/create-publish-knowledge-base.md) se dozvíte, co se stane po výběru tlačítka.

## <a name="providing-multi-turn-conversations"></a>Poskytování vícenásobných konverzací

Klient robota poskytuje nejlepší vybranou odpověď z vaší znalostní báze a může poskytnout následné výzvy, pokud je odpověď součástí vícestranného páru QnA. Přečtěte [si, jak do](../how-to/multiturn-conversation.md) znalostní báze přidat vícesměrné konverzační otázky a sady odpovědí.

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

Zatímco QnA Maker zpracovává otázky, které používají zpracování přirozeného jazyka, lze jej také použít jako součást většího systému, který odpovídá na otázky z více znalostních bází. QnA Maker můžete kombinovat s jinou službou Cognitive Service, Language Understanding (LUIS), a to před tím, než se dostanete ke konkrétní znalostní bázi, můžete zajistit zpracování přirozeného jazyka. Přečtěte si další informace o tom, kdy a jak používat [LUIS a QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) společně.

## <a name="next-steps"></a>Další kroky

Seznamte se [s koncepty vývojového](development-lifecycle-knowledge-base.md) cyklu pro QnA Maker.