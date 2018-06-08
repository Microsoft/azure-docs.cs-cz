---
title: Předpověď vehicle stavu a řídí zvyklosti - Azure | Microsoft Docs
description: Pomocí možnosti Cortana Intelligence získat přehledy v reálném čase a prediktivní na vehicle stavu a řídí zvyklosti.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: fc8dfbbfc40db33f19d2b183546ed9c6df0159fa
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836398"
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Vehicle řešení analýzy Telemetrie playbook
Tato nabídka odkazy na kapitoly v této playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Přehled
Super počítače byl přesunut mimo testovací prostředí a jsou nyní parkujících v garáží. Tyto jsou právě teď umístěny v nejmodernější automobilů, které obsahují řadu senzorů. Podobných senzorů jim poskytnout možnost sledování a monitorování miliony událostí za sekundu. Do roku 2020 většinu těchto vozidel se připojí k Internetu. Klepnutím na do této zadávané dat poskytuje vyšší zabezpečení, spolehlivost, a proto lepší řízení prostředí. Společnost Microsoft neposkytuje to sní realitou s Cortana Intelligence.

Cortana Intelligence je plně spravovaná velkých objemů dat a suite pokročilou analýzu, který můžete použít pro transformaci dat do inteligentního akce. Šablona Cortana Intelligence Vehicle Telemetrie Analytics řešení ukazuje, jak dealerům car, automobilů výrobců a pojištění společnosti, se můžou získat v reálném čase a zvyklosti prediktivní Statistika na vehicle stavu a řídí.

Řešení je implementovaný jako [lambda architektura vzor](https://en.wikipedia.org/wiki/Lambda_architecture), který zobrazuje kompletní potenciální Cortana Intelligence platformy pro v reálném čase a dávkové zpracování.

## <a name="architecture"></a>Architektura
Architektura řešení analýzy Telemetrie Vehicle je zobrazená v tomto diagramu:

![Diagram architektury řešení](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Toto řešení zahrnuje následující součásti Cortana Intelligence a umožňující prezentovat jejich integrace:

* **Azure Event Hubs** ingestuje miliony událostí vehicle telemetrická data do Azure.
* **Azure Stream Analytics** poskytuje přehledy v reálném čase na vehicle stavu a přetrvává tato data do dlouhodobého úložiště pro širší batch analýzu.
* **Azure Machine Learning** zjistí anomálie v reálném čase a k poskytování prediktivní insights používá dávkové zpracování.
* **Azure HDInsight** transformuje data ve velkém měřítku.
* **Azure Data Factory** zpracovává orchestration, plánování, správy prostředků a monitorování dávkové zpracování kanálu.
* **Power BI** toto řešení poskytuje bohaté řídicí panel pro data v reálném čase a vizualizací prediktivní analýzy.

Toto řešení používá dvou různých zdrojů dat.: 

* **Simulated vehicle signály a Diagnostika**: simulátoru telematika vehicle vysílá diagnostické informace a signály, které odpovídají stavu nástroj a podporovat jeho vzor k danému bodu v čase. 
* **Vehicle katalogu**: Tato referenční datová sada mapuje VIN čísla modelů.

