---
title: Prozkoumejte úlohy Azure Stream Analytics v kódu Visual Studia
description: Tento článek ukazuje, jak exportovat úlohu Azure Stream Analytics do místního projektu, zobrazit seznam úloh a zobrazit entity úloh.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479238"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Prozkoumejte Azure Stream Analytics pomocí kódu Visual Studia (preview)

Rozšíření Azure Stream Analytics for Visual Studio Code poskytuje vývojářům zjednodušené prostředí pro správu úloh Stream Analytics. Může být použit na Windows, Mac a Linux. S rozšířením Azure Stream Analytics můžete:

- [Vytvoření](quick-create-vs-code.md), spuštění a zastavení úloh
- Export existujících úloh do místního projektu
- Zobrazit seznam úloh a zobrazit entity úlohy

## <a name="export-a-job-to-a-local-project"></a>Export úlohy do místního projektu

Chcete-li exportovat úlohu do místního projektu, vyhledejte úlohu, kterou chcete exportovat, v **průzkumníku analýzy datových proudů** v kódu sady Visual Studio. Pak vyberte složku pro váš projekt. Projekt se exportuje do vybrané složky a můžete pokračovat ve správě úlohy z kódu sady Visual Studio. Další informace o použití kódu sady Visual Studio ke správě úloh Služby Stream Analytics najdete v [tématu Rychlý start](quick-create-vs-code.md)kódu sady Visual Studio .

![Export úlohy ASA v kódu sady Visual Studio](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Zobrazit seznam úloh a zobrazit entity úlohy

Zobrazení úloh můžete použít k interakci s úlohami Azure Stream Analytics z Visual Studia.


1. Klikněte na ikonu **Azure** na panelu aktivit kódu Visual Studia a potom rozbalte **uzel Stream Analytics**. Vaše úlohy by se měly zobrazit v rámci vašich předplatných.

   ![Otevřít Průzkumník analýzy datového proudu](./media/vscode-explore-jobs/open-explorer.png)

2. Rozbalte svůj pracovní uzel, můžete otevřít a zobrazit pracovní dotaz, konfiguraci, vstupy, výstupy a funkce. 

3. Klikněte pravým tlačítkem myši na uzel úlohy a zvolte **zobrazení otevřené úlohy v** uzlu Portál, abyste otevřeli zobrazení úloh na webu Azure Portal.

   ![Otevřít zobrazení úlohy na portálu](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Další kroky

* [Vytvoření cloudové úlohy Azure Stream Analytics v kódu Visual Studia (preview)](quick-create-vs-code.md)
