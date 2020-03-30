---
title: Zpracování událostí v reálném čase pomocí Azure Stream Analytics
description: Tento článek popisuje referenční architekturu pro dosažení zpracování událostí v reálném čase a analýzy pomocí Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: d219b3fcb27b23527c0a651bc8e842a9e036bfc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431483"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referenční architektura: Zpracování událostí v reálném čase pomocí Služby Microsoft Azure Stream Analytics
Referenční architektura pro zpracování událostí v reálném čase pomocí Azure Stream Analytics je určena k poskytování obecného podrobného plánu pro nasazení řešení zpracování datových proudů v reálném čase jako služby (PaaS) s Microsoft Azure.

## <a name="summary"></a>Souhrn
Analytická řešení jsou tradičně založena na možnostech, jako je ETL (extrahovat, transformovat, načíst) a datové sklady, kde jsou data uložena před analýzou. Měnící se požadavky, včetně rychlejšího přijínáho data, posouvají tento stávající model na hranici možností. Schopnost analyzovat data v rámci pohybujících se datových proudů před úložištěm je jedním z řešení, a i když to není nová funkce, přístup nebyl široce přijat ve všech oborových vertikálách. 

Microsoft Azure poskytuje rozsáhlý katalog analytických technologií, které jsou schopné podporovat řadu různých scénářů a požadavků řešení. Výběr služeb Azure, které chcete nasadit pro komplexní řešení, může být vzhledem k šíři nabídek výzvou. Tento článek je navržen tak, aby popisoval možnosti a spolupráci různých služeb Azure, které podporují řešení streamování událostí. Vysvětluje také některé scénáře, ve kterých mohou zákazníci těžit z tohoto typu přístupu.

## <a name="contents"></a>Obsah
* Shrnutí
* Úvod do analýzy v reálném čase
* Návrh hodnoty dat v reálném čase v Azure
* Běžné scénáře pro analýzy v reálném čase
* Architektura a komponenty
  * Zdroje dat
  * Vrstva integrace dat
  * Vrstva analýzy v reálném čase
  * Vrstva úložiště dat
  * Vrstva prezentace / spotřeby
* Závěr

**Autor:** Charles Feddersen, architekt řešení, Centrum excelence datových poznatků, Microsoft Corporation

**Zveřejněno:** Leden 2015

**Revize:** 1.0

**Ke stažení:** [Zpracování událostí v reálném čase pomocí služby Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Podpora
Další pomoc našlápte na [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

