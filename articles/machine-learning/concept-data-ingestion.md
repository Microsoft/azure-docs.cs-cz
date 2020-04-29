---
title: Možnosti přijímání dat
titleSuffix: Azure Machine Learning
description: Přečtěte si o možnostech příjmu dat pro školení vašich modelů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79086888"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Přijímání dat v Azure Machine Learning

V tomto článku se seznámíte s odborníky a nevýhody následujících možností ingestování dat, které jsou dostupné v Azure Machine Learning. 

1. [Azure Data Factory](#use-azure-data-factory) kanály
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

Přijímání dat je proces, při kterém se nestrukturovaná data extrahují z jednoho nebo několika zdrojů a pak se připravují pro školení modelů strojového učení. Také je časově náročná, zejména pokud je to možné, a pokud máte velké množství dat z více zdrojů. Automatizace tohoto úsilí uvolní prostředky a zajišťuje, aby vaše modely používaly nejnovější a použitelné údaje.

Azure Data Factory (ADF) je speciálně sestavená tak, aby mohl extrahovat, načítat a transformovat data, ale sada Python SDK umožňuje vyvíjet vlastní řešení pro zpracování základních úloh ingestování dat. Pokud ani tak není, co potřebujete, můžete také pomocí ADF a sady Python SDK vytvořit celkový pracovní postup příjmu dat, který vyhovuje vašim potřebám. 

## <a name="use-azure-data-factory"></a>Použití Azure Data Factory

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) nabízí nativní podporu pro monitorování zdrojů dat a triggery pro kanály přijímání dat.  

Následující tabulka shrnuje specialisty a nevýhody použití Azure Data Factory pro pracovní postupy pro přijímání dat.

|Výhody|Nevýhody
---|---
Konkrétně sestavená k extrakci, načtení a transformaci dat.|V současné době nabízí omezená sada Azure Data Factorych úloh kanálu. 
Umožňuje vytvářet pracovní postupy řízené daty pro orchestraci přesunu a transformace dat ve velkém měřítku.|Nákladné pro sestavování a údržbu. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) Azure Data Factory.
Integrace s různými nástroji Azure, jako je [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) a [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Nespouští skripty nativně, místo toho se spoléhá na samostatné výpočetní prostředky pro spuštění skriptu. 
Nativně podporuje příjem dat aktivované zdrojem dat| 
Procesy přípravy dat a školicích procesů jsou oddělené.|
Možnost vloženého datového řádku pro Azure Data Factory datové toky|
Poskytuje [uživatelské rozhraní](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) s nízkou úrovní kódu pro neskriptovací přístupy. |

Tyto kroky a následující diagram znázorňují pracovní postup pro přijímání dat Azure Data Factory.

1. Vyžádat data ze svých zdrojů
1. Transformujte a uložte data do výstupního kontejneru objektů blob, který slouží jako úložiště dat pro Azure Machine Learning
1. Po uložení připravených dat Azure Data Factory kanál vyvolá školicí Machine Learning kanál, který obdrží připravená data pro školení modelů.


    ![Ingestování dat ADF](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Naučte se, jak vytvořit kanál pro příjem dat pro Machine Learning s využitím [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="use-the-python-sdk"></a>Použití sady Python SDK 

Pomocí [sady Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml)můžete do kroku [Azure Machine Learningého kanálu](how-to-create-your-first-pipeline.md) začlenit úkoly přijímání dat.

Následující tabulka shrnuje profesionály a con pro použití sady SDK a postupu kanálu ML pro úlohy přijímání dat.

Výhody| Nevýhody
---|---
Konfigurace vlastních skriptů Pythonu | Nepodporují nativně aktivaci změn zdroje dat. Vyžaduje aplikaci logiky nebo implementace Azure Functions.
Příprava dat v rámci každého spuštění školení modelu|Pro vytvoření skriptu pro příjem dat se vyžaduje dovednost pro vývoj.
Podporuje skripty pro přípravu dat na různých cílech výpočtů, včetně [Azure Machine Learning COMPUTE](concept-compute-target.md#azure-machine-learning-compute-managed) . |Neposkytuje uživatelské rozhraní pro vytvoření mechanismu přijímání.

V následujícím diagramu se Azure Machine Learning kanál skládá ze dvou kroků: přijímání dat a školení modelu. Krok příjmu dat zahrnuje úlohy, které lze provést pomocí knihoven Python a sady Python SDK, jako je extrakce dat z místních nebo webových zdrojů a základní transformace dat, jako například chybějící hodnota imputace. Krok školení pak použije připravená data jako vstup do vašeho školicího skriptu a naučí váš model strojového učení. 

![Kanál Azure + ingestování dat sady SDK](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak vytvořit kanál pro příjem dat pro Machine Learning s využitím [Azure Data Factory](how-to-data-ingest-adf.md)
* Naučte se automatizovat a spravovat vývojové cykly při přijímání kanálů příjmu dat pomocí [Azure Pipelines](how-to-cicd-data-ingestion.md).
