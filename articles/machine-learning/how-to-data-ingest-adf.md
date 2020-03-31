---
title: Přijímaní dat pomocí Azure Data Factory
titleSuffix: Azure Machine Learning
description: Zjistěte, jak vytvořit kanál pro ingestování dat pomocí Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274785"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Přijímaní dat pomocí Azure Data Factory

V tomto článku se dozvíte, jak vytvořit kanál pro ingestování dat pomocí Azure Data Factory (ADF). Tento kanál se používá k ingestování dat pro použití s Azure Machine Learning. Azure Data Factory umožňuje snadno extrahovat, transformovat a načíst (ETL) data. Jakmile jsou data transformována a načtena do úložiště, lze je použít k trénování modelů strojového učení.

Jednoduchou transformaci dat lze zpracovat pomocí nativních aktivit ADF a nástrojů, jako je [tok dat](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity). Pokud jde o složitější scénáře, data mohou být zpracovány s nějakým vlastním kódem. Například kód Python nebo R.

Existuje několik běžných technik použití Azure Data Factory k transformaci dat během ingestování. Každá technika má klady a zápory, které určují, zda je vhodný pro konkrétní případ použití:

| Technika | Výhody | Nevýhody |
| ----- | ----- | ----- |
| Funkce ADF + Azure | S nízkou latencí, výpočetní výkon bez serveru</br>Stavové funkce</br>Opakovaně použitelné funkce | Dobré pouze pro krátkodobé zpracování |
| ADF + vlastní součást | Rozsáhlá paralelní výpočetní technika</br>Vhodné pro těžké algoritmy | Zabalení kódu do spustitelného souboru</br>Složitost zpracování závislostí a vi |
| Poznámkový blok ADF + Azure Databricks | Apache Spark</br>Nativní prostředí Pythonu | Může být drahé</br>Vytváření clusterů zpočátku vyžaduje čas a přidává latenci

## <a name="adf-with-azure-functions"></a>ADF s funkcemi Azure

![adf-funkce](media/how-to-data-ingest-adf/adf-function.png)

Funkce Azure umožňuje spouštět malé části kódu (funkce) bez obav o aplikační infrastruktury. V této možnosti jsou data zpracována s vlastním kódem Pythonu zabaleným do funkce Azure. 

Funkce je vyvolána aktivitou [funkce ADF Azure](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Tento přístup je dobrou volbou pro zjednodušené transformace dat. 

* Profesionály:
    * Data jsou zpracovávána na výpočetních počítačích bez serveru s relativně nízkou latencí
    * Kanál ADF můžete vyvolat [trvalé funkce Azure,](/azure/azure-functions/durable/durable-functions-overview) který může implementovat sofistikovaný tok transformace dat 
    * Podrobnosti transformace dat jsou abstrahovány pryč funkce Azure, které lze znovu použít a vyvolat z jiných míst
* Nevýhody:
    * Funkce Azure musí být vytvořeny před použitím s ADF
    * Funkce Azure jsou vhodné pouze pro krátkodobé zpracování dat

## <a name="adf-with-custom-component-activity"></a>ADF s vlastní aktivitou komponent

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

V této možnosti jsou data zpracována s vlastním kódem Pythonu zabaleným do spustitelného souboru. Je vyvolána s [aktivitou vlastní součásti adf](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Tento přístup je vhodnější pro velká data než předchozí technika.

* Profesionály:
    * Data se zpracovávají ve fondu [Azure Batch,](https://docs.microsoft.com/azure/batch/batch-technical-overview) který poskytuje rozsáhlé paralelní a vysoce výkonné výpočetní techniky
    * Lze použít ke spuštění těžkých algoritmů a zpracování významného množství dat
* Nevýhody:
    * Fond dávek Azure musí být vytvořen před použitím s ADF
    * Přes inženýrství související s zabalením kódu Pythonu do spustitelného souboru. Složitost zpracování závislostí a vstupních a výstupních parametrů

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF s poznámkovým blokem Pythonu Azure Databricks

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) je analytická platforma založená na Apache Spark v cloudu Microsoftu.

V této technice transformace dat se provádí [poznámkový blok Pythonu](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook), spuštěné v clusteru Azure Databricks. Toto je pravděpodobně nejběžnější přístup, který využívá plný výkon služby Azure Databricks. Je určen pro distribuované zpracování dat ve velkém měřítku.

* Profesionály:
    * Data se transformují na nejvýkonnější službu Azure pro zpracování dat, která je zálohována prostředím Apache Spark
    * Nativní podpora Pythonu spolu s datovými vědeckými rámci a knihovnami včetně TensorFlow, PyTorch a scikit-learn
    * Není třeba zalamovat kód Pythonu do funkcí nebo spustitelných modulů. Kód funguje tak, jak je.
* Nevýhody:
    * Infrastruktura Azure Databricks musí být vytvořena před použitím s ADF
    * Může být nákladné v závislosti na konfiguraci Azure Databricks
    * Střídání výpočetních clusterů z "studeného" režimu trvá nějakou dobu, která přináší vysokou latenci řešení 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Využití dat v kanálech Azure Machine Learning

![datová sada aml](media/how-to-data-ingest-adf/aml-dataset.png)

Transformovaná data z kanálu ADF se uloží do úložiště dat (například objekt blob Azure). Azure Machine Learning má přístup k těmto datům pomocí [datových úložišť](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) a [datových sad](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

Při každém spuštění kanálu ADF jsou data uložena do jiného umístění v úložišti. Chcete-li předat umístění Azure Machine Learning, kanál ADF volá kanál Azure Machine Learning. Při volání kanálu ML jsou jako parametry odeslány umístění dat a ID spuštění. Kanál ML pak můžete vytvořit úložiště dat nebo datové sady pomocí umístění dat. 

> [!TIP]
> Datové sady [podporují správu verzí](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets), takže kanál ML může zaregistrovat novou verzi datové sady, která odkazuje na nejnovější data z kanálu ADF.

Jakmile jsou data přístupná prostřednictvím úložiště dat nebo datové sady, můžete je použít k trénování modelu ML. Proces školení může být součástí stejného kanálu ML, který je volán z ADF. Nebo to může být samostatný proces, jako je experimentování v poznámkovém bloku Jupyter.

Vzhledem k tomu, že datové sady podporují správu verzí a každý spuštění z kanálu vytvoří novou verzi, je snadné pochopit, která verze dat byla použita k trénování modelu.

## <a name="next-steps"></a>Další kroky

* [Spuštění poznámkového bloku Databricks v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Přístup k datům ve službách úložiště Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Trénování modelů s datovými sadami v Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps pro kanál ingestování dat](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

