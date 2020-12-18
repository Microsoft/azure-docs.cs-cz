---
title: Kopírování nebo zálohování úloh Azure Stream Analytics
description: Tento článek popisuje, jak zkopírovat nebo zálohovat úlohu Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: 67e28e8c5092f2b52a3a34053f81d8a00afb24ed
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683219"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopírování nebo zálohování úloh Azure Stream Analytics

Nasazené Azure Stream Analytics úlohy můžete kopírovat nebo zálohovat pomocí Visual Studio Code nebo sady Visual Studio. Zkopírování úlohy do jiné oblasti nekopíruje poslední výstupní čas. Proto nemůžete použít, pokud se při spuštění zkopírované úlohy používá možnost [**naposledy zastaveno**](./start-job.md#start-options) .

## <a name="before-you-begin"></a>Než začnete
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se na [Azure Portal](https://portal.azure.com/).

* Nainstalujte [Azure Stream Analytics rozšíření pro Visual Studio Code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) nebo [Azure Stream Analytics nástroje pro Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Klikněte na ikonu **Azure** na řádku Visual Studio Code aktivity a potom rozbalte uzel **Stream Analytics** . Vaše úlohy by se měly zobrazit v rámci vašich předplatných.

   ![Otevřít Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Chcete-li exportovat úlohu do místního projektu, vyhledejte úlohu, kterou chcete exportovat, v **průzkumníkovi Stream Analytics** v Visual Studio Code. Pak vyberte složku pro svůj projekt.

    ![Najít úlohu ASA v Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Projekt se vyexportuje do vybrané složky a přidá se do vašeho aktuálního pracovního prostoru.

3. Pokud chcete úlohu publikovat do jiné oblasti nebo zálohy s použitím jiného názvu, vyberte v editoru dotazů (. asaql) **možnost vybrat ze svých předplatných** \* a postupujte podle pokynů.

    ![Publikování do Azure v Visual Studio Code](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Postupujte podle [pokynů k projektu export Azure Stream Analytics nasazené úlohy](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Otevřete \* soubor. asaql v editoru dotazů, v editoru skriptů vyberte **Odeslat do Azure** a postupujte podle pokynů pro publikování úlohy do jiné oblasti nebo zálohy pomocí nového názvu.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Visual Studio Code](quick-create-visual-studio-code.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)