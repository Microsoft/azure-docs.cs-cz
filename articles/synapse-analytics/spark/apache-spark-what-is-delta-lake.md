---
title: Co je Delta Lake
description: Přehled rozdílových Lake a jak funguje jako součást analýzy Azure synapse
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 244cdf5329e26fc7d928998b734a539f086051ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85193375"
---
# <a name="what-is-delta-lake"></a>Co je rozdílový Lake?

Azure synapse Analytics je kompatibilní s nástrojem Linux Foundation Delta Lake. Rozdílový Lake je open source vrstva úložiště, která přináší transakce kyseliny (atomická, konzistence, izolace a odolnost) na Apache Spark a úlohy s velkým objemem dat.

## <a name="key-features"></a>Klíčové funkce

| Funkce | Description |
| --- | --- |
| **KYSELé transakce** | Data laků jsou obvykle naplněna prostřednictvím více procesů a kanálů, přičemž některé z nich zapisují data souběžně pomocí čtení. Před rozdílovým Lakeem a přidáním transakcí museli technici dat projít procesem ruční náchylnosti k chybě, aby se zajistila integrita dat. Rozdílový Lake přináší do datových laků známé transakce s KYSELou. Poskytuje serializovatelný a nejsilnější úroveň izolace. Další informace najdete na adrese [začnete do rozdílových Lake: rozbalení transakčního protokolu](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Škálovatelné zpracování metadat** | U velkých objemů dat i samotná metadata můžou být "Velká data". Rozdílový Lake zachází s metadaty, jako jsou data, a využívá k obsluze všech svých metadat napájení distribuovaného zpracování Sparku. V důsledku toho může rozdílový Lake zpracovávat řádu petabajtů tabulky s miliardami oddílů a souborů. |
| **Doba cesty (Správa verzí dat)** | Možnost vrátit zpět změny nebo přejít zpět na předchozí verzi je jednou z klíčových funkcí transakcí. Rozdílový Lake poskytuje snímky dat, které vám umožní vrátit se k dřívější verzi dat pro audity, vrátit zpět nebo reprodukce experimentů. Další informace najdete v článku [Představujeme službu data Lake pro velké objemy dat ve velkém měřítku](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Otevřít formát** | Apache Parquet je standardní formát pro rozdílový Lake, který umožňuje využívat efektivní schémata komprese a kódování, která jsou nativní pro formát. |
| **Unified Batch a zdroj a jímka streamování** | Tabulka v rozdílových Lake je jak tabulka Batch, tak i zdroj a jímka streamování. Zpracování datových proudů ingestuje, Batch historická zpětná výplň a interaktivní dotazy fungují pouze mimo pole. |
| **Vynucování schématu** | Vynucení schématu pomáhá zajistit, že jsou datové typy správné a jsou k dispozici požadované sloupce, což zabraňuje chybovým datům v případě nekonzistence dat. Další informace najdete v tématu [začneteing on Delta Lake: vymáhání schématu & vývoj.](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Vývoj schématu** | Rozdílový Lake umožňuje provádět změny schématu tabulky, které se dají použít automaticky, aniž byste museli psát DDL migrace. Další informace najdete v tématu [začneteing on Delta Lake: vymáhání schématu & vývoj.](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Historie auditu** | Protokol Delta Lake Transaction log zaznamenává údaje o každé změně provedené v datech, která poskytují úplný záznam auditu změn. |
| **Aktualizace a odstranění** | Rozdílových Lake podporuje rozhraní API Scala/Java/Python a SQL pro celou řadu funkcí. Podpora operací sloučení, aktualizace a odstranění vám pomůže splnit požadavky na dodržování předpisů. Další informace najdete v tématu [oznámení vydání Delta Lake 0.4.0](https://delta.io/news/delta-lake-0-4-0-released/) a [jednoduchých, spolehlivých upsertuje a odstraňování na rozdílných tabulkách Lake pomocí rozhraní API Pythonu](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), které obsahují fragmenty kódu pro příkazy sloučit, aktualizovat a odstranit DML. |
| **100% kompatibilní s rozhraním API Apache Spark** | Vývojáři můžou použít rozdílové Lake s jejich stávajícími datovými kanály s minimální změnou, protože je plně kompatibilní se stávajícími implementacemi Sparku. |

Úplnou dokumentaci najdete na stránce s [dokumentací rozdílových Lake](https://docs.delta.io/latest/delta-intro.html) .

Další informace najdete v tématu [rozdílový projekt Lake](https://lfprojects.org).

## <a name="next-steps"></a>Další kroky

- [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
