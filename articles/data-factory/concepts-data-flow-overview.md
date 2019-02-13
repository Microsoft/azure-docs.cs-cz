---
title: Azure Data Factory mapování přehled toku dat
description: Vysvětlení přehled mapování toků dat ve službě Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ac07c0207f9463107b0c354bcd690cd9cc3981f4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213301"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Co jsou mapování toků dat ve službě Azure Data Factory?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Data proudí mapování povolit datoví architekti k vývoji logiku transformace grafické dat bez psaní kódu. Výsledné datové toky jsou spouštěny jako aktivity v rámci kanály datové továrny Azure pomocí Azure Databricks clusterů horizontálním navýšením kapacity.

Záměr toku dat objekt pro vytváření dat Azure je poskytnout plně vzhled nevyžadují žádné kódování. Data proudí se spustí na spuštění clusteru pro zpracování dat horizontálním navýšením kapacity. Azure Data Factory zajišťuje všechny překladu kódu, cesta k optimalizaci a spuštění úlohy toku dat.

Začněte vytvořením datové toky v režimu ladění tak, aby mohli ověřit svoji logiku transformace interaktivně. V dalším kroku přidáte aktivitu toku dat do vašeho kanálu ke spuštění a testování data toku v potrubí ladění, nebo pomocí "Aktivovat" v kanálu testování toku dat z kanálu aktivitu.

Se pak plánování a monitorování aktivit toku dat s využitím kanálů Azure Data Factory, které jsou spouštěny tok dat aktivit.

Režim ladění posuvný přepínač na návrhové ploše toku dat umožňuje interaktivní vytváření transformace dat. Režim ladění poskytuje přípravu prostředí pro vytváření toku dat a data.
