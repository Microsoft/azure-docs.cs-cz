---
title: Získat výchozí odpověď – QnA Maker
description: Pokud se k otázce neshoduje, vrátí se výchozí odpověď. Je možné, že budete chtít změnit výchozí odpověď z standardní výchozí odpovědi.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979962"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Změna výchozí odpovědi pro prostředek QnA Maker

Výchozí odpověď pro znalostní bázi je určena k vrácení, když odpověď nenalezne. Pokud používáte klientskou aplikaci, například [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), může mít také samostatnou výchozí odpověď, která indikuje, že žádná odpověď nesplňovala prahovou hodnotu skóre.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Nastavit výchozí odpověď při vytváření znalostní báze

Při vytváření nové znalostní báze je výchozím textem odpovědí jedno z nastavení. Pokud se rozhodnete, že ho nechcete během procesu vytváření nastavit, můžete ho později změnit pomocí následujícího postupu.

## <a name="change-default-answer-in-qna-maker-portal"></a>Změna výchozí odpovědi na portálu QnA Maker

Výchozí odpověď znalostní báze se vrátí, když služba QnA Maker nevrátí žádnou odpověď.

1. Přihlaste se k [portálu QnA maker](https://www.qnamaker.ai/) a ze seznamu vyberte znalostní bázi.
1. V navigačním panelu vyberte **Nastavení** .
1. Změňte hodnotu **výchozího textu odpovědi** v části **Správa znalostní báze** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Snímek obrazovky QnA Makerového portálu, stránka nastavení s zvýrazněným výchozím textovým polem odpovědi":::

1. Kliknutím na **Uložit a výuka** změny uložte.

## <a name="next-steps"></a>Další kroky

* [Vytvoření znalostní báze](../How-to/manage-knowledge-bases.md)