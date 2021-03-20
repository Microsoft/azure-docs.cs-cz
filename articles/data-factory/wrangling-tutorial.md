---
title: Začínáme se službou tahání data flow v Azure Data Factory
description: Kurz o přípravě dat v Azure Data Factory pomocí toku dat tahání
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684018"
---
# <a name="prepare-data-with-data-wrangling"></a>Příprava dat pomocí tahání dat

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Data tahání ve vaší datové továrně umožňují vytvářet interaktivní Power Query zpracování nativně v ADF a pak je provádět ve velkém měřítku v kanálu ADF.

> [!NOTE]
> Power Query aktivitu v ADF je aktuálně dostupných ve verzi Public Preview.

## <a name="create-a-power-query-activity"></a>Vytvoření aktivity Power Query

Existují dva způsoby, jak vytvořit Power Query v Azure Data Factory. Jedním ze způsobů, jak kliknout na ikonu Plus a vybrat **tok dat** v podokně prostředky továrny.

> [!NOTE]
> Dříve byla funkce data tahání umístěná v pracovním postupu toku dat. Teď budete sestavovat data tahání zpracování – od ```New > Power query```

![Snímek obrazovky, který ukazuje Power Query v podokně prostředky továrny.](media/data-flow/power-query-wrangling.png)

Druhá metoda je v podokně aktivity plátna kanálu. Otevřete **Power Query** přiznávání a přetáhněte aktivitu **Power Query** na plátno.

![Snímek obrazovky, který zvýrazní možnost tahání dat](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>Vytvoření aktivity Power Query tahání dat

Přidejte **zdrojovou datovou sadu** pro Power Query zpracování. Můžete buď zvolit existující datovou sadu, nebo vytvořit novou. Můžete také vybrat datovou sadu jímky. Můžete zvolit jednu nebo více zdrojových datových sad, ale v tuto chvíli je povolena pouze jedna jímka. Výběr datové sady jímky je nepovinný, ale vyžaduje se aspoň jedna zdrojová datová sada.

![Tahání](media/wrangling-data-flow/tutorial4.png)

Kliknutím na **vytvořit** otevřete Power Query Editor hybridních webových aplikací v online režimu.

![Snímek obrazovky, který zobrazuje tlačítko vytvořit, které otevře Power Query Editor hybridních webových aplikací v online režimu.](media/wrangling-data-flow/tutorial5.png)

Vytvořte tahání Power Query pomocí přípravy dat bez kódu. Seznam dostupných funkcí naleznete v tématu [transformační funkce](wrangling-functions.md). ADF překládá skript M do skriptu toku dat, aby bylo možné spouštět Power Query ve velkém měřítku pomocí prostředí Azure Data Factory Flow toku dat.

![Snímek obrazovky, který ukazuje proces vytváření Power Query dat tahání.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Spuštění a monitorování aktivity Power Query tahání dat

Pokud chcete spustit ladění kanálu Power Query aktivity, klikněte na **ladit** na plátně kanálu. Po publikování kanálu **teď aktivační událost** spustí spuštění posledního publikovaného kanálu na vyžádání. U Power Querych kanálů se dají naplánovat všechny existující triggery Azure Data Factory.

![Snímek obrazovky, který ukazuje, jak přidat aktivitu Power Query tahání data.](media/wrangling-data-flow/tutorial3.png)

Otevřete kartu **monitorování** a vizualizujte výstup spuštění spouštěného Power Query aktivity.

![Snímek obrazovky, který zobrazuje výstup spouštěné tahání Power Query aktivity.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Další kroky

Naučte se [vytvořit tok dat mapování](tutorial-data-flow.md).
