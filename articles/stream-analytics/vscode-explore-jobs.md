---
title: Prozkoumejte úlohy Azure Stream Analytics pomocí Visual Studio Code (Preview)
description: Tento článek ukazuje, jak exportovat úlohy Azure Stream Analytics na místní projekt, seznam úloh a zobrazení úloh entity.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827797"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Prozkoumejte službu Azure Stream Analytics pomocí sady Visual Studio Code (Preview)

Azure Stream Analytics pro Visual Studio Code příponou vývojářům poskytuje zjednodušené prostředí pro správu svých úloh Stream Analytics. Je možné na Windows, Mac a Linux. Rozšíření Azure Stream Analytics vám umožní:

- [Vytvoření](quick-create-vs-code.md), spuštění a zastavení úlohy
- Exportovat stávající úlohy do místní projekt
- Zobrazení seznamu úloh a zobrazení úlohy entity

## <a name="export-a-job-to-a-local-project"></a>Exportovat úlohy do místní projekt

Exportovat úlohu do místní projekt, vyhledejte úlohu chcete exportovat **Průzkumníku Stream Analytics** ve Visual Studio Code. Potom vyberte složku pro váš projekt. Projekt se exportují do vámi vybrané složky. proto můžete nadále spravovat úlohy z Visual Studio Code. Další informace o použití ke správě úlohy Stream Analytics Visual Studio Code najdete v tématu Visual Studio Code [rychlý Start](quick-create-vs-code.md).

![Exportovat úlohy Azure Stream Analytics ve Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Zobrazení seznamu úloh a zobrazení úloh entity

Zobrazení úloh můžete použít k interakci s úlohy Azure Stream Analytics ze sady Visual Studio.


1. Klikněte na tlačítko **Azure** ikonu na řádku aktivita kódu Visual Studio a potom rozbalte **Stream Analytics uzel**. Úlohy by se zobrazit v rámci vašich předplatných.

   ![Průzkumníka otevřete Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Rozbalte uzel projektu, můžete otevřít a zobrazit dotaz na úlohu, konfiguraci, vstupy, výstupy a funkce. 

3. Klikněte pravým tlačítkem na uzel projektu a zvolte **otevřete zobrazení úloh portálu** uzlu otevřete zobrazení úloh na webu Azure Portal.

   ![Volná zobrazení portálu](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Další postup

* [Vytvořit úlohu Azure Stream Analytics cloudu ve Visual Studio Code (Preview)](quick-create-vs-code.md)
