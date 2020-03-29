---
title: Získat výchozí odpověď - QnA Maker
description: Výchozí odpověď je vrácena, pokud neexistuje žádná shoda s otázkou. Můžete chtít změnit výchozí odpověď ze standardní výchozí odpovědi.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843272"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Nastavení výchozí odpovědi pro znalostní bázi

Výchozí odpověď je vrácena, pokud neexistuje žádná shoda s otázkou. Můžete chtít změnit výchozí odpověď ze standardní výchozí odpovědi.

## <a name="change-default-answer"></a>Změnit výchozí odpověď

1. Přejděte na [portál Azure](https://portal.azure.com) a přejděte na skupinu prostředků, která představuje službu QnA Maker, kterou jste vytvořili.

2. Klepnutím sem otevřete **službu App Service**.

    ![Na webu Azure Portal, přístup k app služby pro QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klikněte na **Nastavení aplikace** a upravte pole **DefaultAnswer** na požadovanou výchozí odpověď. Klikněte na **Uložit**.

    ![Vyberte Nastavení aplikace a pak upravte výchozí odpověď pro QnA Maker.](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Restartování služby App Service

    ![Po změně výchozí odpovědi restartujte službu aplikace QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Další kroky

* [Vytvoření robota s QnA Makerem a LUIS](../tutorials/integrate-qnamaker-luis.md)