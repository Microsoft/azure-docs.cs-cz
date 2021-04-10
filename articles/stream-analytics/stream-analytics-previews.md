---
title: Funkce ve verzi Preview pro Azure Stream Analytics
description: V tomto článku jsou uvedené Azure Stream Analytics funkce, které jsou momentálně ve verzi Preview.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 55745c022038fa85f5b114f2bc347ed7292665eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589646"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkce ve verzi Preview pro Azure Stream Analytics

Tento článek shrnuje všechny funkce, které jsou v současnosti ve verzi Preview, k disAzure Stream Analytics. Používání funkcí verze Preview v produkčním prostředí se nedoporučuje.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Ověřování pro SQL Database výstup se spravovanými identitami (Preview)

Azure Stream Analytics podporuje [spravované ověřování identity](../active-directory/managed-identities-azure-resources/overview.md) pro Azure SQL Database výstupní jímky. Spravované identity odstraňují omezení metod ověřování založených na uživatelích, například nutnost opětovného ověření v důsledku změn hesel. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Bodování vysokého výkonu v reálném čase s vlastními modely ML spravovanými nástrojem Azure Machine Learning

Azure Stream Analytics podporuje vysoce výkonné bodování v reálném čase, protože využívá předem připravené Machine Learning modely spravované Azure Machine Learning a hostované ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI), a to pomocí pracovního postupu, který nevyžaduje psaní kódu. [Zaregistrujte](https://aka.ms/asapreview1) se do verze Preview

## <a name="c-custom-de-serializers"></a>Vlastní de-serializátory v jazyce C#
Vývojáři mohou využít sílu Azure Stream Analytics ke zpracování dat v Protobuf, XML nebo jakémkoli vlastním formátu. Můžete implementovat [vlastní de-serializátory](custom-deserializer-examples.md) v jazyce C#, které lze poté použít k deserializaci událostí přijatých Azure Stream Analytics.

## <a name="extensibility-with-c-custom-code"></a>Rozšiřitelnost pomocí vlastního kódu jazyka C#

Vývojáři vytvářející Stream Analytics moduly v cloudu nebo na IoT Edge můžou zapisovat nebo znovu používat vlastní funkce jazyka C# a vyvolat je přímo v dotazu prostřednictvím [uživatelsky definovaných funkcí](stream-analytics-edge-csharp-udf-methods.md).

## <a name="debug-query-steps-in-visual-studio"></a>Postup ladění dotazů v aplikaci Visual Studio

Při místním testování v Azure Stream Analyticsch nástrojích pro sadu Visual Studio můžete snadno zobrazit náhled sady pokročilých řádků v datovém diagramu. 


## <a name="live-data-testing-in-visual-studio"></a>Živé testování dat v aplikaci Visual Studio

Nástroje Visual Studio Tools for Azure Stream Analytics zlepšují funkci místního testování, která vám umožní testovat dotazy na proudy událostí živého vysílání z cloudových zdrojů, jako je centrum událostí nebo centrum IoT. Přečtěte si, jak [místně testovat živá data pomocí Azure Stream Analyticsch nástrojů pro Visual Studio](stream-analytics-live-data-local-testing.md).

## <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code pro Azure Stream Analytics

Azure Stream Analytics úlohy lze vytvořit v Visual Studio Code. Podívejte se na náš úvodní [kurz vs Code](./quick-create-visual-studio-code.md).

## <a name="local-testing-with-live-data-in-visual-studio-code"></a>Místní testování s dynamickými daty v Visual Studio Code

Před odesláním úlohy do Azure můžete testovat své dotazy proti živým datům na místním počítači. Každá iterace testování trvá v průměru méně než dvě až tři sekundy, což vede k velmi efektivnímu procesu vývoje.

