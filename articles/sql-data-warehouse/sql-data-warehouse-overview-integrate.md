---
title: Vytváření integrovaných řešení s využitím SQL Data Warehouse | Dokumentace Microsoftu
description: 'Nástroje a partnery s řešeními, které se integrují s využitím SQL Data Warehouse. '
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 43a714ae175e0d60f20b5e7ad79e1fa90125b0f8
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873344"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrace ostatních služeb s využitím SQL Data Warehouse
Kromě jejích klíčových funkcí SQL Data Warehouse umožňuje integrovat s mnoha dalšími službami v Azure. Mezi tyto služby patří:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse dál integrovat další služby napříč Azure a další [partneři pro integraci](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Integrace Power BI umožňuje kombinovat výkon služby SQL Data Warehouse s dynamické generování sestav a vizualizací Power BI. Integrace Power BI aktuálně zahrnuje:

* **Přímém připojení**: Pokročilejší připojení logické přenosu směrem dolů na SQL Data Warehouse. Přenosu směrem dolů poskytuje rychlejší analýzy ve větším měřítku.
* **Otevřít v Power BI**: Tlačítko Otevřít v Power BI předává informace o instanci Power BI pro jednodušší způsob, jak připojit.

Další informace najdete v tématu [integrace s Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), nebo [dokumentaci k Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory poskytuje uživatelům spravovaná platforma pro vytváření komplexních extrakce a načítání kanály. SQL Data Warehouse integrace s Azure Data Factory obsahuje:

* **Uložené procedury**: Orchestrace spuštění uložených procedur na SQL Data Warehouse.
* **Kopírování**: Přesun dat do SQL Data Warehouse pomocí ADF. Tato operace můžete použít mechanismus přesun ADF standardní dat nebo PolyBase pod pokličkou. 

Další informace najdete v tématu [integrace s Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning je plně spravovaná analytická služba, která umožňuje vytvářet složité modely pomocí rozsáhlé sady nástrojů pro prediktivní. SQL Data Warehouse je podporovaný jako zdroj i cíl pro tyto modely s následujícími funkcemi:

* **Čtení dat:** Jednotka modely ve velkém měřítku pomocí jazyka T-SQL s SQL Data Warehouse.
* **Zápis dat:** Potvrzení změn z jakéhokoli modelu zpět do služby SQL Data Warehouse.

Další informace najdete v tématu [integrací se službami Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics je komplexní, plně spravovaná infrastruktura pro zpracování a spotřebovává data události vygenerovaná ze služby Azure Event Hub.  Integrace s SQL Data Warehouse umožňuje pro streamování dat do efektivní zpracování a uložení společně s relačními daty povolení hlubší, rozšířené analýzy.  

* **Výstup úlohy:** Odeslání výstupu z úlohy Stream Analytics přímo do služby SQL Data Warehouse.

Další informace najdete v tématu [integrací se službami Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


