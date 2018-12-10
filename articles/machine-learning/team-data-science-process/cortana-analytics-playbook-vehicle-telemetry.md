---
title: Předpověď stavu vozidel a jízdních návycích - vědecké zpracování týmových dat
description: Pomocí možností Cortana Intelligence získat v reálném čase a prediktivní přehledy o stavu vozidel a jízdních návycích.
services: machine-learning
author: marktab
ms.author: tdsp
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/14/2018
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: db223aa831b646339670cdbd332b61906bf05c28
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138768"
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Řešení analýzy Telemetrie vozidla

Superpočítače se přesunuly z testovacího prostředí a jsou teď zaparkované ve garáží. Ty jsou nyní umístěných v špičkové automobily, které obsahují řadu senzorů. Tyto senzory jim umožňuje sledovat a monitorovat miliony událostí za sekundu. Do roku 2020 většinu těchto vozidel připojí k Internetu. Proniknutí do dat v plné šíři poskytuje vyšší bezpečnost, spolehlivost, a tak lepší řízení prostředí. Společnost Microsoft neposkytuje to sní realita s využitím Cortana Intelligence.

Cortana Intelligence je plně spravovaná velké objemy dat a sada pro pokročilou analýzu, který vám pomůže získat z dat na inteligentní interpretovatelné přehledy. Řešení šablona Cortana Intelligence Telemetrie vozidel Analytics ukazuje, jak můžou autorizovaní prodejci, výrobci automobilů a pojišťovací společnosti budou moct získat v reálném čase a prediktivní přehledy o stavu vozidel a jízdních návycích.

Toto řešení je implementované jako [vzor architektury lambda](https://en.wikipedia.org/wiki/Lambda_architecture), který ukazuje celý potenciál platformy Cortana Intelligence pro v reálném čase a dávkové zpracování.

## <a name="architecture"></a>Architektura
Architektura řešení analýzy Telemetrie vozidla je znázorněno v tomto diagramu:

![Diagram architektury řešení](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Toto řešení zahrnuje následující součásti Cortana Intelligence a prezentuje jejich integrace:

* **Azure Event Hubs** ingestuje miliony událostí telemetrie vozidel do Azure.
* **Azure Stream Analytics** poskytuje v reálném čase přehledy o stavu vozidel a ukládá tato data do dlouhodobého úložiště pro bohatší dávkové analýzy.
* **Azure Machine Learning** detekuje anomálie v reálném čase a používá k zajištění prediktivních přehledů dávkové zpracování.
* **Azure HDInsight** transformuje data ve velkém měřítku.
* **Azure Data Factory** zajišťuje orchestraci, plánování, správu prostředků a monitorování kanálu dávkového zpracování.
* **Power BI** poskytuje tomuto řešení panel s bohatými funkcemi pro vizualizace prediktivních analýz a dat v reálném čase.

Toto řešení má přístup k dvou různých datových zdrojů: 

* **Simulované vozidla signály a Diagnostika**: simulátor telematiky vozidel vysílá diagnostické informace a signály, které odpovídají stavu vozidla a řízení vzor k danému bodu v čase. 
* **Katalog vozidel**: Tento referenční sady dat mapuje VIN čísla na modely.

## <a name="next-steps"></a>Další kroky

Prozkoumat další toto řešení, najdete v článku [playbook řešení analýzy Telemetrie vozidla: Podívejte se na řešení](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

Další informace o konfiguraci sestavy Power BI a řídicí panely pro toto řešení, najdete v článku [pokyny k instalaci vozidla telemetrická data Analytics řešení šablony Power BI řídicí panel](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
