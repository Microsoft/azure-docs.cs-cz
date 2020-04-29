---
title: Začínáme se službou tahání data flow v Azure Data Factory
description: Kurz o přípravě dat v Azure Data Factory pomocí toku dat tahání
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81409010"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Příprava dat pomocí toku dat tahání

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Vytvoření toku dat tahání

Existují dva způsoby, jak vytvořit tok dat tahání v Azure Data Factory. Jedním ze způsobů, jak kliknout na ikonu Plus a vybrat **tok dat** v podokně prostředky továrny.

![Tahání](media/wrangling-data-flow/tutorial7.png)

Druhá metoda je v podokně aktivity plátna kanálu. Otevřete možnost **přesunutí a transformace** a přetáhněte aktivitu **toku dat** na plátno.

V obou metodách se v zobrazeném podokně vyberte **vytvořit nový tok dat** a zvolte **tahání tok dat**. Klikněte na tlačítko OK.

![Tahání](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Vytváření tahání toku dat

Přidejte **zdrojovou datovou sadu** pro tok dat tahání. Můžete buď zvolit existující datovou sadu, nebo vytvořit novou. Můžete také vybrat datovou sadu jímky. Můžete zvolit jednu nebo více zdrojových datových sad, ale v tuto chvíli je povolena pouze jedna jímka. Výběr datové sady jímky je nepovinný, ale vyžaduje se aspoň jedna zdrojová datová sada.

> [!NOTE]
> U omezené verze Preview se podporuje jenom text s oddělovači ADLS Gen 2. 

![Tahání](media/wrangling-data-flow/tutorial4.png)

Kliknutím na **vytvořit** otevřete Power Query Editor hybridních webových aplikací v online režimu.

![Tahání](media/wrangling-data-flow/tutorial5.png)

Vytvářejte tahání data Flow pomocí přípravy dat bez kódu. Seznam dostupných funkcí najdete v tématu [transformační funkce](wrangling-data-flow-functions.md) ./

![Tahání](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Spuštění a monitorování toku dat tahání

Pokud chcete spustit ladění kanálu pro tahání tok dat, klikněte na **ladit** na plátně kanálu. Po publikování toku dat **teď aktivační událost** spustí spuštění posledního publikovaného kanálu na vyžádání. Toky dat tahání se dají naplánovat se všemi existujícími triggery Azure Data Factory.

![Tahání](media/wrangling-data-flow/tutorial3.png)

Přechod na kartu **monitorování** a vizualizujte výstup spuštění aktivity toku dat aktivovaného tahání.

![Tahání](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Další kroky

Naučte se [vytvořit tok dat mapování](tutorial-data-flow.md).