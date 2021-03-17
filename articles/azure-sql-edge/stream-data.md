---
title: Streamování dat ve službě Azure SQL Edge
description: Přečtěte si o streamování dat ve službě Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392108"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Streamování dat ve službě Azure SQL Edge

Azure SQL Edge poskytuje nativní implementaci funkcí streamování dat, které se nazývají streamování T-SQL. Poskytuje streamování dat, analýzu a zpracování událostí v reálném čase k analýze a zpracování vysokého objemu rychlých streamování dat z více zdrojů současně. Streamování T-SQL se sestavuje pomocí stejného vysoce výkonného streamu, který způsobil [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) v Microsoft Azure. Tato funkce podporuje podobnou sadu funkcí, které nabízí Azure Stream Analytics běžící na hraničních zařízeních.

Stejně jako u Stream Analytics se streamování T-SQL rozpoznává vzory a vztahy v informacích extrahovaných z řady vstupních zdrojů IoT, včetně zařízení, senzorů a aplikací. Pomocí těchto vzorů můžete aktivovat akce a iniciovat pracovní postupy. Můžete například vytvořit upozornění, informace o informačním kanálu do řešení pro vytváření sestav nebo vizualizace nebo ukládat data pro pozdější použití. 

Streamování T-SQL vám může pomáhat:

* Analyzujte streamy telemetrie v reálném čase ze zařízení IoT.
* Využijte analýzy dat vytvářených z autonomního a neřidičních vozidel v reálném čase.
* Využijte vzdálené monitorování a prediktivní údržbu prostředků průmyslových nebo výrobních prostředků s vysokými hodnotami.
* Využijte detekci anomálií a rozpoznávání vzorků pro čtení ze senzorů IoT v zemědělské nebo energetické farmě.

## <a name="how-does-t-sql-streaming-work"></a>Jak funguje streamování T-SQL?

Streamování T-SQL funguje stejným způsobem jako [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work). Například používá koncept *úloh* streamování pro zpracování streamování dat v reálném čase. 

Úloha Stream Analytics se skládá z těchto:

- **Vstup z datového proudu** : Tato definice definuje připojení ke zdroji dat, ze kterého se má datový proud číst. Azure SQL Edge aktuálně podporuje následující typy vstupů datového proudu:
    - Hraniční centrum
    - Kafka (podpora pro vstupy Kafka je v tuto chvíli dostupná jenom pro verze Intel/AMD64 Azure SQL Edge.)

- **Výstup datového proudu** : definuje připojení ke zdroji dat, do kterého se má datový proud zapsat. Azure SQL Edge aktuálně podporuje následující typy výstupu streamu.
    - Hraniční centrum
    - SQL (výstup SQL může být místní databáze v rámci instance Azure SQL Edge nebo vzdálené SQL Server nebo Azure SQL Database.) 

- **Dotaz na datový proud** : definuje transformaci, agregace, filtrování, řazení a spojení, které mají být aplikovány na vstupní datový proud, před zápisem do výstupu datového proudu. Dotaz na datový proud vychází ze stejného dotazovacího jazyka, který používá Stream Analytics. Další informace najdete v tématu [Stream Analytics dotazovacího jazyka](/stream-analytics-query/stream-analytics-query-language-reference).

> [!IMPORTANT]
> Streamování T-SQL, na rozdíl od Stream Analytics, v současné době nepodporuje [použití referenčních dat pro vyhledávání](../stream-analytics/stream-analytics-use-reference-data.md) nebo [použití systému souborů UDF a Uda v úloze streamu](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> Služba T-SQL streaming podporuje pouze podmnožinu oblasti jazykového povrchu, kterou podporuje Stream Analytics. Další informace najdete v tématu [Stream Analytics dotazovacího jazyka](/stream-analytics-query/stream-analytics-query-language-reference).

## <a name="limitations-and-restrictions"></a>Omezení a omezení

Následující omezení a omezení platí pro streamování T-SQL. 

- V určitém čase může být aktivní pouze jedna úloha streamování. Úlohy, které jsou již spuštěny, je nutné před spuštěním další úlohy zastavit.
- Každé spuštění úlohy streamování je jediné vlákno. Pokud úloha streamování obsahuje víc dotazů, vyhodnotí se každý dotaz v sériovém pořadí.
- Když zastavíte úlohu streamování ve službě Azure SQL Edge, může dojít ke zpoždění před spuštěním další úlohy streamování. Toto zpoždění je zavedeno, protože podkladové procesy streamování musí být zastaveny v reakci na požadavek na úlohu zastavení a následně restartován v reakci na požadavek na spuštění úlohy. 
- 32 až oddíly pro streamování T-SQL pro datový proud Kafka. Pokusy o konfiguraci většího počtu oddílů budou mít za následek chybu. 

## <a name="next-steps"></a>Další kroky

- [Vytvoření úlohy Stream Analytics v Azure SQL Edge ](create-stream-analytics-job.md)
- [Zobrazení metadat přidružených ke úlohám streamu ve službě Azure SQL Edge ](streaming-catalog-views.md)
- [Vytvořit externí Stream](create-external-stream-transact-sql.md)