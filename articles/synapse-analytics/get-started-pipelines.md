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
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744931"
---
# <a name="integrate-with-pipelines"></a>Integrace s kanály

V tomto kurzu se dozvíte, jak integrovat kanály a aktivity pomocí synapse studia. 

## <a name="overview"></a>Přehled

V Azure synapse můžete integrovat širokou škálu úloh.

1. V synapse studiu přejdete do centra **Integration** .
1. Vyberte **+**  >  **kanál** a vytvořte nový kanál.
1. Přejít do centra pro **vývoj** a vyberte jeden z poznámkových bloků, které jste vytvořili dříve.
1. Přetáhněte tento poznámkový blok do kanálu ( **Poznámka** : Přidání kroku importovat moduly do poznámkového bloku, který je zadaný v [dokumentu](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace), který se vyžaduje při spuštění z kanálu).
1. V kanálu vyberte **Přidat Trigger**  >  **Nový/upravit** .
1. V části **zvolit Trigger** vyberte **Nový** a nastavte **opakování** na "každou 1 hodinu".
1. Vyberte **OK** . 
1. Vyberte **Publikovat vše** .
1. Pro okamžité spuštění kanálu bez čekání na další hodinu vyberte **Přidat**  >  **aktivační událost nyní** .



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vizualizace dat pomocí Power BI](get-started-visualize-power-bi.md)
                                 
