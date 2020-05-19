---
title: Streamování dat ve službě Azure SQL Edge (Preview)
description: Další informace o streamování dat ve službě Azure SQL Edge (Preview)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597307"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streamování dat ve službě Azure SQL Edge (Preview)

Azure SQL Edge (Preview) poskytuje dvě různé možnosti implementace streamování dat. 

1. Nasazuje se úlohy Edge Azure Stream Analytics vytvořené v Azure. Další informace o tom, jak nasadit úlohy Edge Azure Stream Analytics v Azure SQL Edge, najdete v tématu [nasazení Azure Stream Analytics úloh](deploy-dacpac.md).
2. Pomocí nové funkce pro **streamování T-SQL** můžete vytvářet úlohy streamování v SQL Edge, aniž byste museli konfigurovat úlohy streamování v Azure. 

I když je možné použít obě možnosti k implementaci streamování dat v SQL Edge, důrazně doporučujeme použít jenom jeden. Při použití obou možností může dojít ke konfliktům časování, které mají vliv na fungování operací streamování dat.

Zbytek tohoto dokumentu odkazuje na novou funkci **streamování T-SQL**, která poskytuje streamování dat, analýzu a zpracování událostí v reálném čase pro analýzu a zpracování vysokého objemu rychlých streamování dat z více zdrojů současně. Služba *T-SQL streaming* je sestavená pomocí stejného stroje streamování s vysokým výkonem, který je [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) v Microsoft Azure a podporuje podobnou sadu funkcí nabízených Azure Stream Analytics běžícími na hraničních zařízeních.

Stejně jako u Azure Stream Analytics umožňuje streamování T-SQL rozpoznávání vzorů a vztahů v informacích extrahovaných z řady vstupních zdrojů IoT včetně zařízení, senzorů a aplikací. Pomocí těchto vzorů můžete aktivovat akce a iniciovat pracovní postupy, jako je vytváření výstrah, zakládání informací do řešení pro generování sestav nebo vizualizace nebo ukládání dat pro pozdější použití. 

Následující scénáře jsou příklady, kdy můžete použít streamování T-SQL:

* Analyzujte streamy telemetrie v reálném čase ze zařízení IoT.
* Analýza dat v reálném čase generovaných z autonomních a neřidičních vozidel.
* Vzdálené monitorování a prediktivní údržba prostředků v průmyslu a výrobě s vysokými hodnotami.
* Detekce anomálií nebo rozpoznávání vzorků pro čtení snímačů IoT v rámci zemědělské nebo energetické farmy.

## <a name="how-does-t-sql-streaming-work"></a>Jak funguje streamování T-SQL?

Streamování T-SQL funguje stejným způsobem jako [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work), například používá koncept úloh streamování pro zpracování streamování dat v reálném čase. 

Úloha Stream Analytics se skládá z těchto:

- Vstup z datového proudu – vstupní datový proud definuje připojení ke zdroji dat, ze kterého se čte datový proud. Azure SQL Edge aktuálně podporuje následující typy vstupů datového proudu:
    - Hraniční centrum
    - Kafka – podpora pro vstupy Kafka je v tuto chvíli dostupná jenom pro verze Intel/AMD64 Azure SQL Edge.

- Výstup streamu – výstup datového proudu definuje připojení ke zdroji dat, do kterého se má datový proud zapsat. Azure SQL Edge aktuálně podporuje následující typy výstupu streamu.
    - Hraniční centrum
    - SQL – výstup SQL může být místní databáze v rámci instance SQL Edge nebo vzdálené SQL Server nebo Azure SQL Database. 
    - Azure Blob Storage

- Dotaz na datový proud – dotaz datového proudu definuje transformaci, agregace, filtr, řazení a spojení, které musí být aplikovány na vstupní datový proud před zapsáním do výstupu datového proudu. Dotaz na datový proud je založen na stejném dotazovacím jazyku, který používá Azure Stream Analytics. Další informace o Azure Stream Analytics dotazovacího jazyka najdete v tématu [Stream Analytics dotazovací jazyk](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> Streamování T-SQL, na rozdíl od Azure Stream Analytics, v současné době nepodporuje [použití referenčních dat pro vyhledávání](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) a [používání úlohy služby Uda v systému souborů UDF a v datovém proudu](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> Služba T-SQL streaming podporuje pouze podmnožinu oblasti jazykového povrchu, kterou podporuje Azure Stream Analytics. Další informace o Azure Stream Analytics dotazovacího jazyka najdete v tématu [Stream Analytics dotazovacího jazyka](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Omezení a omezení

Následující omezení a omezení platí pro streamování T-SQL. 

- V jednom okamžiku může být aktivní jenom jedna úloha streamování. Úlohy, které jsou již spuštěny, je nutné před spuštěním další úlohy zastavit.
- Každé spuštění úlohy streamování je jedno vlákno. Pokud úloha streamování obsahuje více dotazů, bude každý dotaz vyhodnocen v sériovém pořadí.

## <a name="next-steps"></a>Další kroky

- [Vytvoření úlohy Stream Analytics v Azure SQL Edge (Preview)](create-stream-analytics-job.md)
- [Zobrazení metadat přidružených ke úlohám streamu ve službě Azure SQL Edge (Preview)](streaming-catalog-views.md)
- [Vytvořit externí Stream](create-external-stream-transact-sql.md)