---
title: Začínáme s tokem dat v Azure Data Factory
description: Návod, jak připravit data v Azure Data Factory pomocí tahák dat toku
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8ae109045381dba77610dedc5bb95e233b213eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682269"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Příprava dat s tokem hašteření dat

## <a name="create-a-wrangling-data-flow"></a>Vytvoření toku dat hádání

Existují dva způsoby, jak vytvořit tahanice tok dat v Azure Data Factory. Jedním ze způsobů je kliknout na ikonu plus a vybrat **tok dat** v podokně výrobních prostředků.

![Hádky](media/wrangling-data-flow/tutorial7.png)

Druhá metoda je v podokně aktivit na plátně kanálu. Otevřete akordeon **Move and Transform** a přetáhněte aktivitu **toku dat** na plátno.

V obou metodách vyberte v bočním podokně, které se otevře, **vytvořit nový tok dat** a zvolte **Tok dat hádání**. Klikněte na tlačítko OK.

![Hádky](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Vytvoření hašteření datového toku

Přidejte **zdrojovou datovou sadu** pro tok dat hádání. Můžete buď zvolit existující datovou sadu, nebo vytvořit novou. Můžete také vybrat datovou sadu jímky. Můžete zvolit jednu nebo více zdrojových datových sad, ale v tuto chvíli je povolena pouze jedna jímka. Výběr datové sady jímky je volitelný, ale je vyžadována alespoň jedna zdrojová datová sada.

> [!NOTE]
> Pouze ADLS Gen 2 Oddělený text jsou podporovány pro omezený náhled. 

![Hádky](media/wrangling-data-flow/tutorial4.png)

Kliknutím na **Vytvořit** otevřete editor mashup Power Query Online.

![Hádky](media/wrangling-data-flow/tutorial5.png)

Vytvářejte tok dat hádání pomocí přípravy dat bez kódu. Seznam dostupných funkcí naleznete v [tématu transformační funkce](wrangling-data-flow-functions.md)/

![Hádky](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Běh a monitorování toku dat hašteření

Chcete-li spustit ladění kanálu tahanice toku dat, klepněte na **tlačítko Ladění** na plátně kanálu. Po publikování toku **dat, Trigger nyní** provede spuštění poslední publikovaný kanál na vyžádání. Hašteření toků dat může být naplánovat se všemi existujícími aktivačními událostmi Azure Data Factory.

![Hádky](media/wrangling-data-flow/tutorial3.png)

Přejděte na kartu **Monitor** a vizualizovat výstup spuštěného spuštění aktivity toku dat.

![Hádky](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit tok dat mapování](tutorial-data-flow.md).