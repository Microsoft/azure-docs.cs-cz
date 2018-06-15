---
title: Sestavení integrované řešení s SQL Data Warehouse | Microsoft Docs
description: 'Nástroje a partnery, se řešení, které se integrují se službou SQL Data Warehouse. '
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f198a99fc03a079be77c7f8167580bb7b758579e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599828"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrovat další služby SQL Data Warehouse
Kromě jeho základní funkce SQL Data Warehouse umožňuje uživatelům integrovat mnoho dalších služeb v Azure. Mezi tyto služby patří:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse nadále integruje s další služby v Azure a další [integrace partnery](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Integrace Power BI umožňuje kombinovat výpočetní výkon služby SQL Data Warehouse s dynamické generování sestav a vizualizaci Power BI. Integrace Power BI aktuálně zahrnuje:

* **Přímé připojení**: více rozšířených připojení s logické přenos směrem dolů vůči SQL Data Warehouse. Přenos směrem dolů poskytuje rychlejší analysis ve větším měřítku.
* **Otevřít v Power BI**: tlačítko Otevřít v Power BI předává informace o instanci Power BI pro simplifed způsob, jak připojit.

Další informace najdete v tématu [integrací s Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), nebo [Power BI dokumentaci](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory poskytuje uživatelům spravovaná platforma pro vytvoření komplexní extrakce a načtení kanály. SQL Data Warehouse integrace s Azure Data Factory obsahuje:

* **Uložené procedury**: orchestraci provádění uložené procedury v SQL Data Warehouse.
* **Kopírování**: použití ADF pro přesun dat do SQL Data Warehouse. Tato operace můžete v pozadí využívají mechanismus přesun ADF standardní dat nebo PolyBase. 

Další informace najdete v tématu [integrací s Azure Data Factory](sql-data-warehouse-get-started-visualize-with-power-bi.md).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning je plně spravovaná analýza služby, která vám umožní vytvořit pomocí rozsáhlé sady nástrojů pro prediktivní komplikované modelů. SQL Data Warehouse je podporovaný jako zdrojovém i cílovém pro tyto modely následující funkce:

* **Čtení dat:** jednotky modely škálované pomocí T-SQL s SQL Data Warehouse.
* **Zapisovat Data:** potvrzení změny z kteréhokoli modelu zpět do SQL Data Warehouse.

Další informace najdete v tématu [integrací se službami Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics je komplexní, plně spravovaná infrastruktury pro zpracování a využívají data události vygenerovaná z centra událostí Azure.  Integrace s SQL Data Warehouse umožňuje dat účinně zpracovat a uložená spolu s relačních dat povolení hlubší, pokročilejší analýzy datových proudů.  

* **Výstup úlohy:** odeslání výstupu z úlohy Stream Analytics přímo do SQL Data Warehouse.

Další informace najdete v tématu [integrací se službami Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>Další postup
Při integraci s Azure SQL Database, najdete v části [elastické dotazu konfigurace databáze SQL](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)

