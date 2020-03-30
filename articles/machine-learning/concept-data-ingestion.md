---
title: Možnosti přijímání dat
titleSuffix: Azure Machine Learning
description: Seznamte se s možnostmi sdílení dat pro školení modelů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086888"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Přijímat data v Azure Machine Learning

V tomto článku se dozvíte výhody a nevýhody následující možnosti ingestování dat, které jsou k dispozici v Azure Machine Learning. 

1. [Kanály Azure Data Factory](#use-azure-data-factory)
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

Přijímání dat je proces, ve kterém jsou nestrukturovaná data extrahována z jednoho nebo více zdrojů a poté připravena pro trénování modelů strojového učení. Je to také časově náročné, zejména pokud se provádí ručně, a pokud máte velké množství dat z více zdrojů. Automatizace tohoto úsilí uvolní prostředky a zajistí, že vaše modely budou používat nejnovější a použitelná data.

Azure Data Factory (ADF) je speciálně vytvořená pro extrahování, načítání a transformace dat, ale Python SDK umožňuje vyvinout vlastní kód řešení pro základní úlohy příjem dat. Pokud ani jeden z nich není to, co potřebujete, můžete také použít ADF a Python SDK společně vytvořit celkový pracovní postup ingestování dat, který vyhovuje vašim potřebám. 

## <a name="use-azure-data-factory"></a>Použití Azure Data Factory

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) nabízí nativní podporu pro monitorování zdrojů dat a aktivační chod pro kanály pro ingestování dat.  

Následující tabulka shrnuje výhody a nevýhody pro použití Azure Data Factory pro vaše pracovní postupy pro ingestování dat.

|Výhody|Nevýhody
---|---
Speciálně vytvořené pro extrahování, načítání a transformaci dat.|V současné době nabízí omezenou sadu úloh kanálu Azure Data Factory 
Umožňuje vytvářet pracovní postupy řízené daty pro orchestraci přesunu dat a transformací ve velkém měřítku.|Drahé postavit a udržovat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) Azure Data Factory.
Integrovaná s různými nástroji Azure, jako jsou [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) a [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Není nativně spustit skripty, místo toho spoléhá na samostatné výpočetní prostředky pro spuštění skriptu 
Nativně podporuje intesor dat aktivovaných zdrojem dat| 
Procesy přípravy dat a modelového školení jsou samostatné.|
Funkce vložené datové linie pro toky dat Azure Data Factory|
Poskytuje [uživatelské rozhraní](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) s nízkým prostředím kódu pro přístupy bez skriptování. |

Tyto kroky a následující diagram znázorňují pracovní postup pro ingestování dat Azure Data Factory.

1. Vyžádat data z jeho zdrojů
1. Transformace a uložení dat do výstupního kontejneru objektů blob, který slouží jako úložiště dat pro Azure Machine Learning
1. S uloženými připravenými daty kanál Azure Data Factory vyvolá kanál trénovacího machine learningu, který přijímá připravená data pro školení modelu.


    ![Požití dat adf](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Zjistěte, jak vytvořit kanál pro ingestování dat pro Machine Learning s [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="use-the-python-sdk"></a>Použití sady Python SDK 

Pomocí [sady Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml)můžete začlenit úlohy ingestování dat do kroku [kanálu Azure Machine Learning.](how-to-create-your-first-pipeline.md)

Následující tabulka shrnuje výhody a kon pro použití sady SDK a krok kanálu ML pro úlohy při ingestování dat.

Výhody| Nevýhody
---|---
Konfigurace vlastních skriptů Pythonu | Nativně nepodporuje aktivaci změny zdroje dat. Vyžaduje implementace aplikace Logika nebo Funkce Azure
Příprava dat jako součást každého spuštění tréninku modelu|Vyžaduje vývojové dovednosti k vytvoření skriptu pro požití dat.
Podporuje skripty pro přípravu dat na různých výpočetních cílech, včetně [výpočetních prostředků Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) |Neposkytuje uživatelské rozhraní pro vytvoření mechanismu ingestování

V následujícím diagramu kanálu Azure Machine Learning se skládá ze dvou kroků: ingestování dat a školení modelu. Krok ingestování dat zahrnuje úkoly, které lze provést pomocí knihoven Pythonu a sady Python SDK, jako je například extrahování dat z místních/webových zdrojů a základní transformace dat, jako je chybějící imputace hodnoty. Krok školení pak používá připravená data jako vstup do trénovacího skriptu k trénování modelu strojového učení. 

![Kanál Azure + připožití dat sady SDK](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak vytvořit kanál pro ingestování dat pro Machine Learning s [Azure Data Factory](how-to-data-ingest-adf.md)
* Zjistěte, jak automatizovat a spravovat životní cykly vývoje vašich kanálů pro ingestování dat pomocí [Azure Pipelines](how-to-cicd-data-ingestion.md).
