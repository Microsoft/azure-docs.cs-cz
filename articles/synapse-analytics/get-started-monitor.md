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
ms.date: 07/27/2020
ms.openlocfilehash: 752021e3838809cace8b5d1c71c2516645031f9d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089770"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorování pracovního prostoru synapse

V tomto kurzu se naučíte monitorovat aktivity v pracovním prostoru synapse. Můžete monitorovat aktuální a historické aktivity pro SQL Apache Spark. a kanály. 

## <a name="introduction-to-the-monitor-hub"></a>Úvod do centra monitorování

Otevřete synapse Studio a přejděte do centra **monitorování** . Tady vidíte historii všech aktivit prováděných v pracovním prostoru a ty, které jsou teď aktivní. 

* V části **orchestrace**můžete monitorovat kanály, triggery a prostředí Integration runtime.
* V části **aktivity**můžete monitorovat aktivity Spark a SQL. 

## <a name="orchestration"></a>Orchestrace

1. Přejděte k **>mu kanálu orchestrace**. V tomto zobrazení můžete zobrazit pokaždé, když se v pracovním prostoru spustí kanál. 
1. Vyhledejte kanál, který jste spustili v předchozím kroku, a klikněte na **název jeho kanálu**.
1. Nyní vidíte, jak jednotlivé aktivity v rámci tohoto kanálu spouští.
1. Klikněte na **panel s popisem cesty** v horní části synapse studia, klikněte na **všechna spuštění kanálu** a vraťte se k předchozímu zobrazení.

## <a name="apache-spark-activities"></a>Apache Spark aktivity

1. Přejděte do **orchestrace > aktivity > Apache Spark aplikací**. Teď uvidíte všechny aplikace Spark, které běží nebo které běží ve vašem pracovním prostoru.
1. Najděte aplikaci, která už není spuštěná, a klikněte na její **název aplikace**. Nyní můžete zobrazit podrobnosti o aplikaci Spark.
1. Pokud jste obeznámeni s Apache Spark, můžete najít standardní uživatelské rozhraní serveru Apache Spark historie kliknutím na **Server historie Spark**.

## <a name="sql-activities"></a>Aktivity SQL

1. Přejděte do **orchestrace > aktivity > požadavky SQL**.
1. V tomto zobrazení si můžete prohlédnout požadavky SQL.
1. Vyberte **fond** , který chcete monitorovat. Nyní můžete zobrazit všechny požadavky SQL spuštěné nebo spuštěné ve vašem pracovním prostoru ve fondu.
1. Najděte konkrétní požadavek SQL a najeďte myší na tuto položku. Po najetí myší se zobrazí ikona skriptu SQL.
1. Pokud chcete zobrazit úplný text požadavku SQL, klikněte na ikonu skriptu SQL.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte centrum znalostí](get-started-knowledge-center.md)
