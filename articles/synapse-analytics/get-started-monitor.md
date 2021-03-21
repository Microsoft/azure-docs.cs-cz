---
title: 'Kurz: Začínáme s Azure synapse Analytics – monitorování pracovního prostoru synapse'
description: V tomto kurzu se naučíte monitorovat aktivity v pracovním prostoru synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 7e8525dbebb42e1f387ee8f0c192efd5e64c9453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426036"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorování pracovního prostoru synapse

V tomto kurzu se naučíte monitorovat aktivity v pracovním prostoru synapse. Můžete monitorovat aktuální a historické aktivity pro SQL, Apache Spark a kanály. 

## <a name="introduction-to-the-monitor-hub"></a>Úvod do centra monitorování

Otevřete synapse Studio a přejděte do centra **monitorování** . Tady vidíte historii všech aktivit prováděných v pracovním prostoru a ty, které jsou teď aktivní. 

* V části **integrace** můžete monitorovat kanály, triggery a prostředí Integration runtime.
* V části **aktivity** můžete monitorovat aktivity Spark a SQL. 

## <a name="integration"></a>Integrace

1. Přejděte ke **spuštění kanálu integrace >**. V tomto zobrazení můžete zobrazit pokaždé, když se v pracovním prostoru spustí kanál. 
1. Vyhledejte kanál, který jste spustili v předchozím kroku, a kliknutím na jeho **název** zobrazte podrobnosti.
1. Klikněte na **panel s popisem cesty** v horní části synapse studia, klikněte na **všechna spuštění kanálu** a vraťte se k předchozímu zobrazení.

## <a name="apache-spark-activities"></a>Apache Spark aktivity

1. Přejděte na **aktivity > Apache Spark aplikace**. Teď uvidíte všechny aplikace Spark, které běží nebo které běží ve vašem pracovním prostoru.
1. Najděte aplikaci, která už není spuštěná, a klikněte na její **název aplikace**. Nyní můžete zobrazit podrobnosti o aplikaci Spark.
1. Pokud jste obeznámeni s Apache Spark, můžete najít standardní uživatelské rozhraní serveru Apache Spark historie kliknutím na **Server historie Spark**.

## <a name="sql-activities"></a>Aktivity SQL

1. Přejděte na **aktivity > požadavky SQL**.
1. V tomto zobrazení si můžete prohlédnout požadavky SQL.
1. Vyberte **fond** , který chcete monitorovat z filtru **fondu** . Nyní můžete zobrazit všechny požadavky SQL spuštěné nebo spuštěné ve vašem pracovním prostoru ve fondu.
1. Najděte konkrétní požadavek SQL a kliknutím na odkaz **Další** zobrazte úplný text žádosti SQL.

    > [!NOTE] 
    > Žádosti SQL odeslané prostřednictvím synapse studia v pracovním prostoru vyhrazeného fondu SQL (dříve SQL DW) se dají zobrazit v centru monitorování. Pro všechny ostatní monitorovací aktivity můžete přejít na Azure Portal vyhrazeného fondu SQL (dříve SQL DW).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte centrum znalostí](get-started-knowledge-center.md)
