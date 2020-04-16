---
title: Co je Delta Lake
description: Přehled delta jezera a jeho fungování v rámci Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429197"
---
# <a name="what-is-delta-lake"></a>Co je Delta Lake?

Azure Synapse Analytics je kompatibilní s Linux Foundation Delta Lake. Delta Lake je vrstva úložiště s otevřeným zdrojovým kódem, která přináší transakce ACID (atomicity, consistency, isolation a durability) do Apache Spark a úloh velkých objemů dat.

## <a name="key-features"></a>Klíčové funkce

| Funkce | Popis |
| --- | --- |
| **Transakce ACID** | Datová jezera jsou obvykle naplněna prostřednictvím více procesů a kanálů, z nichž některé zapisují data souběžně s čtením. Před delta lakem a přidáním transakcí museli datoví inženýři projít ručním procesem náchylným k chybám, aby zajistili integritu dat. Delta Lake přináší známé transakce ACID do datových jezer. Poskytuje serializovatelnost, nejsilnější úroveň izolace úrovně. Další informace najdete v části [Potápění do jezera Delta: Vybalování transakčního protokolu](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Škálovatelné zpracování metadat** | U velkých objemů dat mohou být i samotná metadata "velká data". Delta Lake zachází s metadaty stejně jako s daty a využívá distribuovaný výpočetní výkon Sparku ke zpracování všech svých metadat. V důsledku toho delta lake dokáže snadno zpracovat tabulky v měřítku petabajtů s miliardami oddílů a souborů. |
| **Cestování v čase (správa verzí dat)** | Možnost "vrátit zpět" změnu nebo se vrátit k předchozí verzi je jednou z klíčových vlastností transakcí. Delta Lake poskytuje snímky dat, které vám umožní vrátit se k dřívějším verzím dat pro audity, vrácení zpět nebo reprodukovat experimenty. Další informace najdete v [části Představujeme Cestování časem u jezer Delta Lake pro velká datová jezera](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Otevřít formát** | Apache Parquet je základní formát pro Delta Lake, což vám umožní využít efektivní kompresní a kódování schémata, které jsou nativní pro formát. |
| **Sjednocený zdroj a jímka pro přenos dat** | Tabulka v Delta Lake je dávková tabulka, stejně jako zdroj streamování a jímka. Streamování dat ingest, dávkové historické backfill a interaktivní dotazy všechny fungují pouze po vybalení z krabice. |
| **Vynucení schématu** | Vynucení schématu pomáhá zajistit, že datové typy jsou správné a požadované sloupce jsou k dispozici, brání chybné data způsobuje nekonzistenci dat. Další informace naleznete [v tématu Potápění do jezera Delta: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Vývoj schématu** | Delta Lake umožňuje provádět změny schématu tabulky, které lze použít automaticky, aniž by bylo třeba zapisovat migrace DDL. Další informace naleznete [v tématu Potápění do jezera Delta: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Historie auditu** | Protokol transakcí společnosti Delta Lake zaznamenává podrobnosti o každé změně dat, která poskytuje úplnou auditní stopu změn. |
| **Aktualizace a odstranění** | Delta Lake podporuje Scala / Java / Python a SQL API pro různé funkce. Podpora operací sloučení, aktualizace a odstranění vám pomůže splnit požadavky na dodržování předpisů. Další informace naleznete [v tématu Oznámení vydání Delta Lake 0.4.0](https://delta.io/news/delta-lake-0-4-0-released/) a [jednoduché, spolehlivé upserts a odstraní na tabulky Delta Lake pomocí Pythonu API](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), který zahrnuje fragmenty kódu pro sloučení, aktualizaci a odstranění příkazů DML. |
| **100% kompatibilní s Apache Spark API** | Vývojáři mohou používat Delta Lake se svými stávajícími datovými kanály s minimálními změnami, protože je plně kompatibilní s existujícími implementacemi Spark. |

Úplnou dokumentaci naleznete na [stránce dokumentace delta jezera](https://docs.delta.io/latest/delta-intro.html)

Další informace naleznete v tématu [Delta Lake Project](https://lfprojects.org).

## <a name="next-steps"></a>Další kroky

- [.NET pro dokumentaci Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
