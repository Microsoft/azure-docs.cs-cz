---
title: Sestavení integrovaných řešení
description: Nástroje a partneři s řešeními, která se integrují s Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e159e0e254ae8b2515515dfaeb2c514e0f25e0b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685642"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrace dalších služeb s SQL Data Warehouse
Kromě základních funkcí SQL Data Warehouse umožňuje uživatelům integraci s mnoha ostatními službami v Azure. Mezi tyto služby patří:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse nadále integrovat s dalšími službami v Azure a dalšími [partnery pro integraci](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Power BI Integration umožňuje kombinovat výpočetní výkon SQL Data Warehouse s dynamickým vytvářením sestav a vizualizací Power BI. Power BI integrace v současné době zahrnuje:

* **Přímé připojení**: pokročilejší připojení s logickým směrem dolů proti SQL Data Warehouse. Přenos přenosů zajišťuje rychlejší analýzu ve větším měřítku.
* **Otevřít v Power BI**: tlačítko otevřít v Power BI předává informace o instanci Power BI pro zjednodušený způsob připojení.

Další informace najdete v tématu věnovaném [integraci s Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)nebo v [dokumentaci k Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory poskytuje uživatelům spravovanou platformu pro vytváření komplexních extrakcí a načítání kanálů. SQL Data Warehouse integrace s Azure Data Factory zahrnuje:

* **Uložené procedury**: orchestrujte provádění uložených procedur na SQL Data Warehouse.
* **Kopírovat**: pomocí ADF můžete přesunout data do SQL Data Warehouse. Tato operace může v rámci pokrývání použít standardní mechanismus pro přesun dat nebo základ. 

Další informace najdete v tématu věnovaném [integraci s Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning je plně spravovaná analytická služba, která umožňuje vytvářet komplikované modely pomocí velké sady prediktivních nástrojů. SQL Data Warehouse se podporuje jako zdroj i cíl pro tyto modely s následujícími funkcemi:

* **Číst data:** Škálujte modely jednotek pomocí T-SQL proti SQL Data Warehouse.
* **Zapisovat data:** Potvrďte změny ze všech modelů zpátky do SQL Data Warehouse.

Další informace najdete v tématu věnovaném [integraci s Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics je složitá plně spravovaná infrastruktura pro zpracování a využívání dat událostí generovaných z centra událostí Azure.  Integrace s SQL Data Warehouse umožňuje efektivně zpracovávat a ukládat streamovaná data společně s relačními daty, což umožňuje hlubší a pokročilejší analýzu.  

* **Výstup úlohy:** Odeslání výstupu z Stream Analytics úloh přímo do SQL Data Warehouse.

Další informace najdete v tématu věnovaném [integraci s Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


