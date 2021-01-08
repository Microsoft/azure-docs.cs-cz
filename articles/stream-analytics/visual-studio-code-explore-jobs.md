---
title: Prozkoumejte úlohy Azure Stream Analytics v Visual Studio Code
description: V tomto článku se dozvíte, jak exportovat úlohu Azure Stream Analytics do místního projektu, vypsat úlohy a zobrazit entity úloh.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 4f1c0650820a419275ade6095344033b6d81a568
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013868"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Prozkoumat Azure Stream Analytics s využitím Visual Studio Code (Preview)

Rozšíření Azure Stream Analytics for Visual Studio Code poskytuje vývojářům zjednodušené prostředí pro správu Stream Analytics úloh. Dá se použít v systémech Windows, Mac a Linux. S rozšířením Azure Stream Analytics můžete:

- [Vytváření](quick-create-visual-studio-code.md), spouštění a zastavování úloh
- Exportovat existující úlohy do místního projektu
- Vypsání úloh a zobrazení entit úloh

## <a name="export-a-job-to-a-local-project"></a>Export úlohy do místního projektu

Chcete-li exportovat úlohu do místního projektu, vyhledejte úlohu, kterou chcete exportovat, v **průzkumníkovi Stream Analytics** v Visual Studio Code. Pak vyberte složku pro svůj projekt. Projekt se exportuje do vybrané složky a můžete dál spravovat úlohu z Visual Studio Code. Další informace o použití Visual Studio Code ke správě úloh Stream Analytics najdete v tématu [rychlý Start](quick-create-visual-studio-code.md)pro Visual Studio Code.

![Exportovat úlohu ASA v Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Vypsat úlohy a zobrazit entity úloh

Můžete použít zobrazení úlohy k interakci s Azure Stream Analytics úlohami ze sady Visual Studio.


1. Klikněte na ikonu **Azure** na řádku Visual Studio Code aktivity a potom rozbalte **uzel Stream Analytics**. Vaše úlohy by se měly zobrazit v rámci vašich předplatných.

   ![Otevřít Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Rozbalte uzel úlohy, můžete otevřít a zobrazit dotaz úlohy, konfiguraci, vstupy, výstupy a funkce. 

3. Klikněte pravým tlačítkem na uzel úlohy a vyberte **Otevřít zobrazení úlohy v uzlu portál** a otevřete zobrazení úlohy v Azure Portal.

   ![Otevřít zobrazení úlohy na portálu](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Další kroky

* [Vytvoření cloudové úlohy Azure Stream Analytics v Visual Studio Code (Preview)](quick-create-visual-studio-code.md)
