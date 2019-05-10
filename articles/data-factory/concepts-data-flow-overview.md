---
title: Azure Data Factory mapování přehled toku dat
description: Vysvětlení přehled mapování toků dat ve službě Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 051886f98d6d35594336291bbb2defb2a4acdfc5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233061"
---
# <a name="what-are-mapping-data-flows"></a>Co jsou mapování toků dat?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Data proudí mapování jsou transformace vizuálně navrhovat dat ve službě Azure Data Factory. Datové toky umožňují datoví architekti k vývoji logiku transformace grafické dat bez psaní kódu. Výsledné datové toky jsou spouštěny jako aktivity v rámci kanály datové továrny Azure pomocí Azure Databricks clusterů horizontálním navýšením kapacity.

Záměr toku dat objekt pro vytváření dat Azure je poskytnout plně vzhled nevyžadují žádné kódování. Data proudí se spustí na spuštění clusteru pro zpracování dat horizontálním navýšením kapacity. Azure Data Factory zajišťuje všechny překladu kódu, cesta k optimalizaci a spuštění úlohy toku dat.

Začněte vytvořením datové toky v režimu ladění tak, aby mohli ověřit svoji logiku transformace interaktivně. V dalším kroku přidáte aktivitu toku dat do vašeho kanálu ke spuštění a testování data toku v potrubí ladění, nebo pomocí "Aktivovat" v kanálu testování toku dat z kanálu aktivitu.

Se pak plánování a monitorování aktivit toku dat s využitím kanálů Azure Data Factory, které jsou spouštěny tok dat aktivit.

Režim ladění posuvný přepínač na návrhové ploše toku dat umožňuje interaktivní vytváření transformace dat. Režim ladění poskytuje přípravu prostředí pro vytváření toku dat a data.
