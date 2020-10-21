---
title: 'Kurz: Seznámení s kanály'
description: V tomto kurzu se dozvíte, jak orchestrovat kanály a aktivity pomocí synapse studia.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329879"
---
# <a name="orchestrate-with-pipelines"></a>Orchestrace pomocí kanálů

V tomto kurzu se dozvíte, jak orchestrovat kanály a aktivity pomocí synapse studia. 

## <a name="overview"></a>Přehled

V Azure synapse můžete orchestrovat širokou škálu úloh.

1. V synapse studiu přejdete do centra **Integration** .
1. Vyberte **+**  >  **kanál** a vytvořte nový kanál.
1. Přejít do centra pro **vývoj** a vyberte jeden z poznámkových bloků, které jste vytvořili dříve.
1. Přetáhněte tento poznámkový blok do kanálu (**Poznámka**: Přidání kroku importovat moduly do poznámkového bloku, který je zadaný v [dokumentu](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace) , který se vyžaduje při spuštění z kanálu).
1. V kanálu vyberte **Přidat Trigger**  >  **Nový/upravit**.
1. V části **zvolit Trigger**vyberte **Nový**a nastavte **opakování** na "každou 1 hodinu".
1. Vyberte **OK**. 
1. Vyberte **Publikovat vše**.
1. Pro okamžité spuštění kanálu bez čekání na další hodinu vyberte **Přidat**  >  **aktivační událost nyní**.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vizualizace dat pomocí Power BI](get-started-visualize-power-bi.md)
                                 
