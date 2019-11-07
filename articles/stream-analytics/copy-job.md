---
title: Kopírování nebo zálohování úloh Azure Stream Analytics
description: Tento článek popisuje, jak zkopírovat nebo zálohovat úlohu Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 8ae97a3ef6e354bb07e257b4997341297e8abe51
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588132"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopírování nebo zálohování úloh Azure Stream Analytics

Nasazené Azure Stream Analytics úlohy můžete kopírovat nebo zálohovat pomocí Visual Studio Code nebo sady Visual Studio. 

## <a name="before-you-begin"></a>Než začnete
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

* Nainstalujte [Azure Stream Analytics rozšíření pro Visual Studio Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code#install-the-azure-stream-analytics-extension) nebo [Azure Stream Analytics nástroje pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code#install-the-azure-stream-analytics-extension).  



## <a name="visual-studio-code"></a>Visual Studio Code 

1. Klikněte na ikonu **Azure** na řádku Visual Studio Code aktivity a potom rozbalte uzel **Stream Analytics** . Vaše úlohy by se měly zobrazit v rámci vašich předplatných.

   ![Otevřít Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Chcete-li exportovat úlohu do místního projektu, vyhledejte úlohu, kterou chcete exportovat, v **průzkumníkovi Stream Analytics** v Visual Studio Code. Pak vyberte složku pro svůj projekt. 

    ![Exportovat úlohu ASA v Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Projekt se vyexportuje do vybrané složky a přidá se do vašeho aktuálního pracovního prostoru.

    ![Exportovat úlohu ASA v Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Pokud chcete úlohu publikovat do jiné oblasti nebo zálohy s použitím jiného názvu, v editoru dotazů (\*. asaql) vyberte **vybrat z vašich předplatných** a postupujte podle pokynů. 

    ![Publikování do Azure v Visual Studio Code](./media/quick-create-vs-code/select-subscription.png)


## <a name="visual-studio"></a>Visual Studio 

1. Postupujte podle [pokynů k projektu export Azure Stream Analytics nasazené úlohy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project). 

2. V editoru dotazů otevřete soubor \*. asaql, v editoru skriptů vyberte **Odeslat do Azure** a postupujte podle pokynů pro publikování úlohy do jiné oblasti nebo zálohování pomocí nového názvu. 


## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Visual Studio Code](quick-create-vs-code.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Nasazení úlohy Azure Stream Analytics s CI/CD pomocí Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)