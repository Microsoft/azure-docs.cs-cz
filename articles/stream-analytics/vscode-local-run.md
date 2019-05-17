---
title: Testování dotazů Azure Stream Analytics místně pomocí Visual Studio Code (Preview)
description: Tento článek popisuje, jak testovat dotazy místně pomocí nástroje Azure Stream Analytics pro Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827947"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testování dotazů Stream Analytics místně pomocí Visual Studio Code

Nástroje Azure Stream Analytics pro Visual Studio Code můžete použít k otestování vaší úlohy Stream Analytics místně s ukázkovými daty.

Použijte tento [rychlý Start](quick-create-vs-code.md) informace o vytvoření úlohy Stream Analytics pomocí sady Visual Studio Code.

## <a name="run-queries-locally"></a>Spouštění dotazů místně

Rozšíření Azure Stream Analytics pro Visual Studio Code můžete použít k otestování vaší úlohy Stream Analytics místně s ukázkovými daty.

1. Po vytvoření vaší úlohy Stream Analytics, použijte **Ctrl + Shift + P** otevřete paletu příkazů. Zadejte a vyberte **Azure Stream Analytics: Přidat vstup**.

    ![Přidat vstup Azure Stream Analytics ve Visual Studio code](./media/vscode-local-run/add-input.png)

2. Vyberte **místní vstup**.

    ![Přidat místní vstup Azure Stream Analytics ve Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Vyberte **+ nový místní vstup**.

    ![Přidat nové místní ASA vstup v aplikaci Visual Studio code](./media/vscode-local-run/add-new-local-input.png)

4. Zadejte stejný vstupní alias, který jste použili v dotazu.

    ![Přidejte nový místní vstupní alias Azure Stream Analytics](./media/vscode-local-run/new-local-input-alias.png)

5. V **LocalInput_DefaultLocalStream.json** soubor, zadejte cestu k souboru, kde se nachází vaše data z místního souboru.

    ![Zadejte cestu k místnímu souboru v sadě Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Vraťte se do editoru dotazů a vyberte **místní spuštění**.

    ![Vyberte možnost spustit místně v editoru dotazů](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Další postup

* [Vytvořit úlohu Azure Stream Analytics cloudu ve Visual Studio Code (Preview)](quick-create-vs-code.md)

* [Prozkoumejte úlohy Azure Stream Analytics pomocí Visual Studio Code (Preview)](vscode-explore-jobs.md)
