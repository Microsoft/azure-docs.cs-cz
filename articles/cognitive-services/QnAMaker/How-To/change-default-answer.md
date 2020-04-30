---
title: Získat výchozí odpověď – QnA Maker
description: Pokud se k otázce neshoduje, vrátí se výchozí odpověď. Je možné, že budete chtít změnit výchozí odpověď z standardní výchozí odpovědi.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097094"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Změna výchozí odpovědi pro prostředek QnA Maker

Pokud se k otázce neshoduje, vrátí se výchozí odpověď. Je možné, že budete chtít změnit výchozí odpověď z standardní výchozí odpovědi.

## <a name="change-default-answer-in-the-azure-portal"></a>Změnit výchozí odpověď v Azure Portal

1. Přejděte na [Azure Portal](https://portal.azure.com) a přejděte do skupiny prostředků, která představuje službu QnA maker, kterou jste vytvořili.

2. Kliknutím otevřete **App Service**.

    ![V Azure Portal přistupovat ke službě App Service pro QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klikněte na **nastavení aplikace** a upravte pole **DefaultAnswer** na požadovanou výchozí odpověď. Klikněte na **Uložit**.

    ![Vyberte nastavení aplikace a pak upravte DefaultAnswer pro QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Restartujte službu App Service.

    ![Po změně DefaultAnswer restartujte QnA Maker AppService](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Další kroky

* [Vytvoření znalostní báze](../How-to/manage-knowledge-bases.md)