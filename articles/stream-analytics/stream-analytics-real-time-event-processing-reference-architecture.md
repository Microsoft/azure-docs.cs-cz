---
title: Zpracování událostí v reálném čase pomocí služby Azure Stream Analytics zpracování událostí
description: Tento článek popisuje referenční architektura k dosažení zpracování událostí v reálném čase a analytics pomocí Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 8a5d426d67916e010c7fff048eebdc77b93c5c38
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30902578"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referenční architektura: událostí v reálném čase zpracování pomocí služby Microsoft Azure Stream Analytics
Referenční architektura pro zpracování pomocí služby Azure Stream Analytics událostí v reálném čase slouží jako obecný plán, podle kterého pro nasazení v reálném čase platforma jako služba (PaaS) řešení zpracování datového proudu s Microsoft Azure.

## <a name="summary"></a>Souhrn
Obvyklým řešení pro analýzu měla vycházet z funkcí, jako je ETL (extrakce, transformace, zatížení) a datových skladů, kde jsou data uložena před analýzou. Změna požadavky, včetně další data rychle které nabízíte tento existující model limitu. Možnost analyzovat data v rámci přesunutí datové proudy před úložiště je jedno řešení a i když není novou funkci, přístupu nebyl přijat široce mezi všechny pocházejícími odvětví. 

Microsoft Azure poskytuje katalog rozsáhlé analytics technologií, které podporují řadu řešení pro různé scénáře a požadavky na podporu. Výběr služeb Azure k nasazení pro-komplexní řešení může být složité zadána šířka nabídky. Tento dokument je určen k popisují funkce a vzájemná spolupráce různých služeb Azure, které podporují řešení vysílání datového proudu událostí. Také vysvětluje některého z následujících scénářů, ve kterých zákazníci využívat tento typ přístupu.

## <a name="contents"></a>Obsah
* Shrnutí
* Úvod do analýzu v reálném čase
* Nabízená hodnota dat v reálném čase v Azure
* Časté scénáře pro analýzu v reálném čase
* Architektura a komponenty
  * Zdroje dat
  * Vrstva integraci dat.
  * Vrstva analýzu v reálném čase
  * Vrstvy úložiště dat
  * Prezentace / spotřeba vrstvy
* Závěr

**Autor:** Charlese Feddersen, architekt řešení datového centra Statistika vynikající výsledky, společnosti Microsoft Corporation.

**Publikovat:** leden 2015

**Revize:** 1.0

**Stáhnout:** [událostí v reálném čase zpracování pomocí služby Microsoft Azure Stream Analytics](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Podpora
O další pomoc, zkuste [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

