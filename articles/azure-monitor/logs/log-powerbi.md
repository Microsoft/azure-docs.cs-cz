---
title: Log Analytics integrace s Power BI a Excelem
description: Odeslání výsledků z Log Analytics do Power BI
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: d1db5f5e4164d971dff3ec6b042fa4b0a969e508
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704238"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics integrace s Power BI

Tento článek se zaměřuje na způsoby zakládání dat z Log Analytics do Microsoft Power BI pro vytváření vizuálně vyžádaného řízení sestav a řídicích panelů. 

## <a name="background"></a>Pozadí 

Protokoly Azure Monitor jsou platforma, která poskytuje ucelené řešení pro ingestování protokolů. [Azure Monitor Log Analytics](../platform/data-platform.md#) je rozhraní pro dotazování těchto protokolů. Další informace o celé Azure Monitor datové platformě, včetně Log Analytics, najdete v tématu [Azure monitor datovou platformu](../data-platform.md). 

Microsoft Power BI je platforma pro vizualizaci dat od Microsoftu. Další informace o tom, jak začít, najdete na [domovské stránce Power BI](https://powerbi.microsoft.com/). 


Obecně platí, že můžete používat bezplatné Power BI funkce k integraci a vytváření vizuálně vyžádané sestavy a řídicích panelů.

K pokročilejším funkcím může být potřeba koupit účet Power BI Pro nebo Premium. Patří k nim: 
 - sdílení práce 
 - naplánované aktualizace
 - Aplikace Power BI 
 - tok dat a přírůstková aktualizace 

Další informace najdete v tématu Další informace [o cenách a funkcích Power BI](https://powerbi.microsoft.com/pricing/) . 

## <a name="integrating-queries"></a>Integrace dotazů  

Power BI používá [dotazovací jazyk M](/powerquery-m/power-query-m-language-specification/) jako svůj jazyk hlavního dotazování. 

Dotazy Log Analytics mohou být exportovány do M a použity přímo v Power BI. Po spuštění úspěšného dotazu vyberte možnost **exportovat do Power BI (dotaz M)** z tlačítka **exportovat** v horním panelu akcí Log Analytics uživatelského rozhraní.


:::image type="content" source="media/log-powerbi/export-query2.png" alt-text="Log Analytics dotaz zobrazení nabídky možností exportu rozevírací" border="true":::

Log Analytics vytvoří soubor. txt obsahující kód M, který lze použít přímo v Power BI.

## <a name="connecting-your-logs-to-a-dataset"></a>Připojení protokolů k datové sadě 

Datová sada Power BI je zdrojem dat připravených pro vytváření sestav a vizualizace. Pokud chcete připojit Log Analytics dotaz k datové sadě, zkopírujte kód M exportovaný z Log Analytics do prázdného dotazu v Power BI. 

Další informace najdete v tématu [principy Power BI datových sad](/power-bi/service-datasets-understand/). 

## <a name="collect-data-with-power-bi-dataflows"></a>Shromažďování dat pomocí Power BIch datových toků 

Power BI tok dat také umožňuje shromažďovat a ukládat data. Další informace najdete v tématu [Power BI toku](/power-bi/service-dataflows-overview)dat.

Tok dat je typem "Cloud ETL" navržený tak, aby vám usnadnil shromažďování a přípravu dat. Datová sada je "model" navržený tak, aby vám usnadnil propojení různých entit a jejich modelování podle vašich potřeb.

## <a name="incremental-refresh"></a>Přírůstková aktualizace 

Obě Power BI datové sady i Power BI Dataflow mají možnost přírůstkové aktualizace. Tato funkce podporuje Power BI toku dat a datové sady Power BI, ale k jejímu používání potřebujete Power BI Premium.  


Přírůstková aktualizace spouští malé dotazy a aktualizuje menší objemy dat na spuštění namísto ingestování všech dat znovu a znovu při spuštění dotazu. Máte možnost ukládat velké objemy dat, ale při každém spuštění dotazu přidat nový přírůstek dat. Toto chování je ideální pro delší spuštěné sestavy.

Přírůstková aktualizace Power BI spoléhá na existenci hodnoty *DateTime* v sadě výsledků. Před konfigurací přírůstkové aktualizace se ujistěte, že vaše sada výsledků dotazu Log Analytics obsahuje alespoň jednu *hodnotu DateTime* . 

Další informace a postup konfigurace přírůstkové aktualizace najdete v tématech [Power BI datových sad a přírůstkové aktualizace](/power-bi/service-premium-incremental-refresh) a [Power BI toku dat a přírůstkové aktualizace](/power-bi/service-dataflows-incremental-refresh).

## <a name="reports-and-dashboards"></a>Sestavy a řídicí panely

Po odeslání dat do Power BI můžete dál používat Power BI k vytváření sestav a řídicích panelů.

Další informace najdete [v tomto průvodci, jak vytvořit první Power BI model a sestavu](/learn/modules/build-your-first-power-bi-report/).  

## <a name="excel-integration"></a>Integrace Excelu

Pro integraci s excelovou tabulkou můžete použít stejnou integraci M použitou v Power BI. Další informace najdete v tomto [průvodci, jak integrovat s aplikací Excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) a pak vložit dotaz M exportovaný z Log Analytics.

Další informace najdete v [integraci Log Analytics a Excelu](log-excel.md) .

## <a name="next-steps"></a>Další kroky

Začínáme s [Log Analytics dotazy](./log-query-overview.md)