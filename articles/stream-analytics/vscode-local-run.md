---
title: Test Azure Stream Analytics dotazy místně pomocí Visual Studio Code (Preview)
description: Tento článek popisuje, jak testovat dotazy místně pomocí Azure Stream Analyticsch nástrojů pro Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 751cdf50fccc654dfab06b4d18428531312d08e6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673025"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Test Stream Analytics dotazy místně pomocí Visual Studio Code

Pomocí Azure Stream Analytics nástrojů pro Visual Studio Code můžete testovat úlohy Stream Analytics lokálně pomocí ukázkových dat.

V tomto [rychlém](quick-create-vs-code.md) startu se dozvíte, jak pomocí Visual Studio Code vytvořit úlohu Stream Analytics.

## <a name="prerequisites"></a>Požadavky
* Nainstalujte [sadu .NET Core SDK](https://dotnet.microsoft.com/download).
* Restartujte Visual Studio Code.
 
## <a name="run-queries-locally"></a>Spustit dotazy místně

Azure Stream Analytics rozšíření pro Visual Studio Code můžete použít k otestování Stream Analytics úloh místně s ukázkovými daty.

1. Po vytvoření úlohy Stream Analytics použijte **kombinaci kláves CTRL + SHIFT + P** a otevřete paletu příkazů. Pak zadejte a vyberte **ASA: Přidejte vstup @ no__t-0.

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
