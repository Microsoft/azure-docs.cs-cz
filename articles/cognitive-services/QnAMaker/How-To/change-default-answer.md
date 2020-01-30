---
title: Získat výchozí odpověď – QnA Maker
description: Pokud se k otázce neshoduje, vrátí se výchozí odpověď. Je možné, že budete chtít změnit výchozí odpověď z standardní výchozí odpovědi.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843272"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Nastavit výchozí odpověď pro znalostní bázi

Pokud se k otázce neshoduje, vrátí se výchozí odpověď. Je možné, že budete chtít změnit výchozí odpověď z standardní výchozí odpovědi.

## <a name="change-default-answer"></a>Změnit výchozí odpověď

1. Přejděte [webu Azure portal](https://portal.azure.com) a přejděte do skupiny prostředků, který představuje službu QnA Maker, kterou jste vytvořili.

2. Kliknutím otevřete **služby App Service**.

    ![Na webu Azure Portal přístup k App service pro nástroj QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klikněte na **nastavení aplikace** a upravit **DefaultAnswer** do požadovaného výchozí odpověď. Klikněte na možnost **Uložit**.

    ![Vyberte nastavení aplikace a pak upravte DefaultAnswer pro nástroj QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Restartujte službu App service

    ![Po změně DefaultAnswer restartujte App Service QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Další kroky

* [Vytvoření robota s QnA Maker a LUIS](../tutorials/integrate-qnamaker-luis.md)