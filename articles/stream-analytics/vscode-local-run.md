---
title: Testování Azure Stream Analytics dotazů pomocí Visual Studio Code
description: Tento článek popisuje, jak testovat dotazy místně pomocí Azure Stream Analyticsch nástrojů pro Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924975"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Test Stream Analytics dotazy místně pomocí Visual Studio Code

Pomocí Azure Stream Analytics nástrojů pro Visual Studio Code můžete testovat úlohy Stream Analytics lokálně pomocí ukázkových dat.

V tomto [rychlém](quick-create-vs-code.md) startu se dozvíte, jak pomocí Visual Studio Code vytvořit úlohu Stream Analytics.

## <a name="prerequisites"></a>Předpoklady
* Nainstalujte [sadu .NET Core SDK](https://dotnet.microsoft.com/download).
* Restartujte Visual Studio Code.
 
## <a name="run-queries-locally"></a>Spustit dotazy místně

Azure Stream Analytics rozšíření pro Visual Studio Code můžete použít k otestování Stream Analytics úloh místně s ukázkovými daty.

1. Po vytvoření úlohy Stream Analytics použijte **kombinaci kláves CTRL + SHIFT + P** a otevřete paletu příkazů. Pak zadejte a vyberte **ASA: přidat vstup**.

    ![Přidat vstup z ASA do Visual studia Code](./media/vscode-local-run/add-input.png)

2. Vyberte **místní vstup**.

    ![Přidání místního vstupu do programu ASA v nástroji Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Vyberte **+ nový místní vstup**.

    ![Přidání nového místního vstupu do programu ASA v nástroji Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Zadejte stejný alias vstupu, který jste použili v dotazu.

    ![Přidat nový alias místního vstupu pro ASA](./media/vscode-local-run/new-local-input-alias.png)

5. V souboru **LocalInput_DefaultLocalStream. JSON** zadejte cestu k souboru, kde se nachází váš místní datový soubor.

    ![Zadejte cestu k místnímu souboru v aplikaci Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Vraťte se do editoru dotazů a vyberte **spustit místně**.

    ![V editoru dotazů vyberte spustit místně.](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Další kroky

* [Vytvoření cloudové úlohy Azure Stream Analytics v Visual Studio Code (Preview)](quick-create-vs-code.md)

* [Prozkoumejte Azure Stream Analytics úlohy pomocí Visual Studio Code (Preview).](vscode-explore-jobs.md)
