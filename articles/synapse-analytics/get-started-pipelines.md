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
ms.openlocfilehash: 05c33db130bfa3fcc1a4f5d75935294fcc0ba1d7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365463"
---
# <a name="integrate-with-pipelines"></a>Integrace s kanály

V tomto kurzu se dozvíte, jak integrovat kanály a aktivity pomocí synapse studia. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Vytvoření kanálu a přidání aktivity poznámkového bloku



1. V synapse studiu přejdete do centra **Integration** .
1. Vyberte **+**  >  **kanál** a vytvořte nový kanál. Kliknutím na nový objekt kanálu Otevřete návrháře kanálů.
1. V části **aktivity** rozbalte složku **synapse** a přetáhněte objekt **poznámkového bloku** do návrháře.
1. Vyberte kartu **Nastavení** vlastností aktivity poznámkového bloku. Pomocí rozevíracího seznamu vyberte libovolný Poznámkový blok z aktuálního pracovního prostoru synapse.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Naplánování spuštění kanálu každou hodinu

1. V kanálu vyberte **Přidat Trigger**  >  **Nový/upravit**.
1. V části **zvolit Trigger** vyberte **Nový** a nastavte **opakování** na "každou 1 hodinu".
1. Vyberte **OK**. 
1. Vyberte **Publikovat vše**. 


## <a name="monitor-pipeline-execution"></a>Monitorování provádění kanálu

1. Když je kanál publikovaný, aby se spustilo okamžitě, aniž byste čekali na další hodinu, vyberte **Přidat**  >  **aktivační proceduru** aktivační události nyní.
1. V synapse studiu přejdete do centra **monitorování** .
1. Vyberte **spuštění kanálu** a sledujte průběh provádění kanálu.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vizualizace dat pomocí Power BI](get-started-visualize-power-bi.md)
