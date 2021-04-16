---
title: 'Kurz: Začínáme s integrací s kanály'
description: V tomto kurzu se dozvíte, jak integrovat kanály a aktivity pomocí synapse studia.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 19bff62883341947eb5290118494b8244c5476ac
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518248"
---
# <a name="integrate-with-pipelines"></a>Integrace s kanály

V tomto kurzu se dozvíte, jak integrovat kanály a aktivity pomocí synapse studia. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Vytvoření kanálu a přidání aktivity poznámkového bloku

1. V synapse studiu přejdete do centra **Integration** .
1. Vyberte **+**  >  **kanál** a vytvořte nový kanál. Kliknutím na nový objekt kanálu Otevřete návrháře kanálů.
1. V části **aktivity** rozbalte složku **synapse** a přetáhněte objekt **poznámkového bloku** do návrháře.
1. Vyberte kartu **Nastavení** vlastností aktivity poznámkového bloku. Pomocí rozevíracího seznamu vyberte Poznámkový blok z aktuálního pracovního prostoru synapse.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Naplánování spuštění kanálu každou hodinu

1. V kanálu vyberte **Přidat Trigger**  >  **Nový/upravit**.
1. V části **zvolit Trigger** vyberte **Nový** a nastavte **opakování** na "každou 1 hodinu".
1. Vyberte **OK**. 
1. Vyberte **Publikovat vše**. 

## <a name="forcing-a-pipeline-to-run-immediately"></a>Vynucení okamžitého spuštění kanálu

Po publikování kanálu ho můžete chtít spustit hned bez čekání na předání hodiny.

1. Otevřete kanál.
1. Nyní klikněte na tlačítko **Přidat** aktivační  >  **událost**.

## <a name="monitor-pipeline-execution"></a>Monitorování provádění kanálu

1. Přejít do centra **monitorování** .
1. Vyberte **spuštění kanálu** a sledujte průběh provádění kanálu.
1. V tomto zobrazení můžete přepínat mezi tabulkovým **seznamem** zobrazením grafického **Ganttova diagramu** . 
1. Kliknutím na název kanálu zobrazíte stav aktivit v tomto kanálu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vizualizace dat pomocí Power BI](get-started-visualize-power-bi.md)
