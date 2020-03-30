---
title: Kopírování nebo zálohování úloh Azure Stream Analytics
description: Tento článek popisuje, jak zkopírovat nebo zálohovat úlohu Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771491"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopírování nebo zálohování úloh Azure Stream Analytics

Nasazené úlohy Azure Stream Analytics můžete zkopírovat nebo zálohovat pomocí kódu Visual Studia nebo Visual Studia. 

## <a name="before-you-begin"></a>Než začnete
* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k [portálu Azure](https://portal.azure.com/).

* Nainstalujte [rozšíření Azure Stream Analytics pro kód Visual Studia](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) nebo nástroje Azure Stream [Analytics pro Visual Studio](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Klikněte na ikonu **Azure** na panelu aktivit kódu Visual Studia a potom rozbalte uzel **Stream Analytics.** Vaše úlohy by se měly zobrazit v rámci vašich předplatných.

   ![Otevřít Průzkumník analýzy datového proudu](./media/vscode-explore-jobs/open-explorer.png)

2. Chcete-li exportovat úlohu do místního projektu, vyhledejte úlohu, kterou chcete exportovat, v **průzkumníku analýzy datových proudů** v kódu sady Visual Studio. Pak vyberte složku pro váš projekt.

    ![Export úlohy ASA v kódu sady Visual Studio](./media/vscode-explore-jobs/export-job.png)

    Projekt se exportuje do vybrané složky a přidá se do aktuálního pracovního prostoru.

    ![Export úlohy ASA v kódu sady Visual Studio](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Pokud chcete úlohu publikovat do jiné oblasti nebo zálohu pod jiným názvem, vyberte **Vybrat z předplatných, které chcete publikovat** v editoru dotazů (\*asaql) a postupujte podle pokynů.

    ![Publikování do Azure v kódu Visual Studia](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Postupujte podle [exportu nasazené úlohy Azure Stream Analytics do pokynů k projektu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project).

2. Otevřete \*soubor ASAQL v Editoru dotazů, v yberte **Odeslat do Azure** v editoru skriptů a podle pokynů publikujte úlohu do jiné oblasti nebo zálohy pod novým názvem.

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vytvoření úlohy Stream Analytics pomocí kódu Sady Visual Studio](quick-create-vs-code.md)
* [Úvodní příručka: Vytvoření úlohy Stream Analytics pomocí Visual Studia](stream-analytics-quick-create-vs.md)
* [Nasazení úlohy Azure Stream Analytics s CI/CD pomocí Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
