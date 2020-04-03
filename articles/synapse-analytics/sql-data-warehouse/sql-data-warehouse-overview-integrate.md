---
title: Vytváření integrovaných řešení
description: Nástroje řešení a partneři, kteří se integrují s fondem Synapse SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c8e3598e55d3f90ab2b7401380406677f56c0ce3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586487"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool"></a>Integrace dalších služeb s fondem Synapse SQL

Funkce fondu SYNApse SQL v rámci Azure Synapse Analytics umožňuje uživatelům integrovat se s mnoha dalšími službami v Azure. Pomocí Synapse SQL můžete vytvořit datový sklad prostřednictvím prostředku fondu SQL, který pak může využít několik dalších služeb, z nichž některé zahrnují:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Další informace týkající se integračních služeb v rámci Azure najděte článek [Partneři integrace.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI
Integrace Power BI umožňuje kombinovat výpočetní výkon datového skladu s dynamickým vytvářením sestav a vizualizací Power BI. Integrace Power BI v současné době zahrnuje:

* **Přímé připojení**: Pokročilejší připojení s logickým pushdown proti datovému skladu zřízeného pomocí fondu SQL. Pushdown poskytuje rychlejší analýzu ve větším měřítku.
* **Otevřít v Power BI:** Tlačítko Otevřít v Power BI předá informace o instanci Power BI pro zjednodušený způsob připojení.

Další informace najdete [v tématu Integrace s Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)nebo dokumentace k Power [BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory poskytuje uživatelům spravovanou platformu pro vytváření složitých kanálů extrakce a načítání. Integrace fondu SQL s Azure Data Factory zahrnuje:

* **Uložené procedury**: Orchestrate provádění uložené procedury.
* **Kopie**: Přesunutí dat do fondu SQL pomocí adf. Tato operace můžete použít ADF standardní mechanismus pohybu dat nebo PolyBase pod kryty. 

Další informace najdete [v tématu Integrace s Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning je plně spravovaná analytická služba, která umožňuje vytvářet složité modely pomocí velké sady prediktivních nástrojů. Fond SQL je podporován jako zdroj i cíl pro tyto modely a má následující funkce:

* **Číst data:** Jednotky modely ve velkém měřítku pomocí T-SQL proti fondu SQL.
* **Zapsat data:** Potvrzení změn z libovolného modelu zpět do fondu SQL.

Další informace najdete [v tématu Integrace s Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics je komplexní, plně spravovaná infrastruktura pro zpracování a využití dat událostí generovaných z Centra událostí Azure.  Integrace s fondem SQL umožňuje efektivní zpracování a ukládání streamovaných dat spolu s relačními daty, což umožňuje hlubší a pokročilejší analýzu.  

* **Výstup úlohy:** Odesílejte výstup z úloh Stream Analytics přímo do fondu SQL.

Další informace najdete [v tématu Integrace s Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


