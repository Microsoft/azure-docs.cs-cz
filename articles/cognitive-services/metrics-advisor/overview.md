---
title: Co je služba Advisor metriky?
titleSuffix: Azure Cognitive Services
description: Co je Metrics Advisor?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: dfdd7286013bbb6462fb8e5b1bdf52e6ed738029
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384676"
---
# <a name="what-is-metrics-advisor-preview"></a>Co je poradce pro metriky (Preview)? 

Poradce pro metriky je součástí Azure Cognitive Services, který používá AI k provádění monitorování dat a detekce anomálií v datech časových řad. Služba automatizuje proces aplikování modelů na vaše data a poskytuje sadu rozhraní API a webový pracovní prostor pro přijímání dat, detekci anomálií a diagnostiku – bez nutnosti znát Machine Learning. Vývojáři můžou na službě sestavovat aplikace AIOps, predicative Maintenance a Business monitor. Pomocí Poradce pro metriky můžete:

* Analyzujte multidimenzionální data z více zdrojů dat.
* Identifikace a korelace anomálií
* Konfigurace a vyladění modelu detekce anomálií, který se používá pro vaše data
* Diagnostika anomálií a pomáhat s analýzou hlavní příčiny

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Přehled Poradce pro metriky":::

Tato dokumentace obsahuje následující typy článků:
* [Rychlé starty](./Quickstarts/web-portal.md) jsou podrobné pokyny, které vám umožní volat službu a získat výsledky v krátké době. 
* [Příručky](./how-tos/onboard-your-data.md) návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném způsobu.
* [Koncepční články](glossary.md) poskytují podrobné vysvětlení funkcí a funkcí služby.

## <a name="connect-to-a-variety-of-data-sources"></a>Připojení k nejrůznějším zdrojům dat

Poradce metriky se může připojit [k multidimenzionálním datům z](how-tos/onboard-your-data.md) mnoha úložišť dat a ingestovat je, a to včetně: SQL Server, Azure Blob Storage, MongoDB a dalších.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Snadné použití a přizpůsobitelná detekce anomálií

* Poradce pro metriky automaticky vybere nejlepší model pro vaše data, aniž by musel znát žádné Machine Learning.
* Automaticky monitorujte všechny časové řady v rámci multidimenzionální [metriky](glossary.md#multi-dimensional-metric).
* Pomocí [ladění parametrů](how-tos/configure-metrics.md) a [interaktivní zpětné vazby](how-tos/anomaly-feedback.md) můžete přizpůsobit model použitý pro vaše data a budoucí výsledky detekce anomálií.

## <a name="real-time-alerts-through-multiple-channels"></a>Výstrahy v reálném čase prostřednictvím několika kanálů

Při zjištění anomálií dokáže Poradce pro metriky [odesílat výstrahy v reálném čase](how-tos/alerts.md) prostřednictvím několika kanálů pomocí háčků, jako jsou například zavěšení e-mailů, webhooků a DevOps zavěšení Azure. Flexibilní pravidla výstrah umožňují přizpůsobit, které výstrahy se odesílají a jejich cíl.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Inteligentní diagnostické přehledy analýzou anomálií

Analyzujte anomálie zjištěné u multidimenzionálních metrik a vygenerujte [inteligentní diagnostické přehledy](how-tos/diagnose-incident.md) , včetně většiny nejpravděpodobnější hlavní příčiny, diagnostických stromů, vrtných metrik a dalších. Když nakonfigurujete [graf metriky](how-tos/metrics-graph.md), můžete povolit analýzu různých metrik, která vám pomůžou vizualizovat incidenty.


## <a name="typical-workflow"></a>Typický pracovní postup

Pracovní postup je jednoduchý: po připojení dat můžete vyladit detekci anomálií a vytvořit konfigurace, které odpovídají vašemu scénáři.

1. [Vytvořte prostředek Azure](https://go.microsoft.com/fwlink/?linkid=2142156) pro poradce metrik. 
2. Sestavte své první monitorování pomocí webového portálu.
    1. Onboarding dat
    2. Vyladit detekci anomálií
    3. Přihlášení k odběru upozornění
    4. Zobrazit diagnostické přehledy
3. K přizpůsobení instance použijte REST API.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte rychlý Start: [monitorování první metriky na webu](quickstarts/web-portal.md).
* Prozkoumejte rychlý Start: [k přizpůsobení řešení použijte rozhraní REST API](./quickstarts/rest-api-and-client-library.md).
