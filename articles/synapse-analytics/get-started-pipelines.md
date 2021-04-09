---
title: 'Kurz: Začínáme s kanály pro integraci'
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
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219398"
---
# <a name="integrate-with-pipelines"></a>Integrace s kanály

V tomto kurzu se dozvíte, jak integrovat kanály a aktivity pomocí synapse studia. 

## <a name="overview"></a>Přehled

V Azure synapse můžete integrovat širokou škálu úloh.

1. V synapse studiu přejdete do centra **Integration** .
1. Vyberte **+**  >  **kanál** a vytvořte nový kanál. Kliknutím na nový objekt kanálu Otevřete návrháře kanálů.
1. V části **aktivity** rozbalte složku **synapse** a přetáhněte objekt **poznámkového bloku** do návrháře.
1. Vyberte kartu **Nastavení** vlastností aktivity poznámkového bloku. Pomocí rozevíracího seznamu vyberte libovolný Poznámkový blok z aktuálního pracovního prostoru synapse. 
1. V kanálu vyberte **Přidat Trigger**  >  **Nový/upravit**.
1. V části **zvolit Trigger** vyberte **Nový** a nastavte **opakování** na "každou 1 hodinu".
1. Vyberte **OK**. 
1. Vyberte **Publikovat vše**. 


## <a name="monitor-pipeline"></a>Monitorování kanálu

1. Když je kanál publikovaný, aby se spustilo okamžitě, aniž byste čekali na další hodinu, vyberte **Přidat**  >  **aktivační proceduru** aktivační události nyní.
1. V synapse studiu přejdete do centra **monitorování** a výběrem **spuštění kanálu** monitorujte průběh provádění kanálu.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vizualizace dat pomocí Power BI](get-started-visualize-power-bi.md)
