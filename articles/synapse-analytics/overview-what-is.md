---
title: Co je Azure Synapse Analytics?
description: Přehled služby Azure synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 78b31cb32e3df9b0d8e198d8c2122e492e609283
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625805"
---
# <a name="what-is-azure-synapse-analytics"></a>Co je Azure Synapse Analytics?

**Azure synapse** je podniková analytická služba, která zrychluje přehled napříč datovými sklady a systémy velkých objemů dat. Azure synapse spojuje nejlepší technologie **SQL** , které se používají v podnikových datových skladech, technologie **Sparku** používané pro velké objemy dat, **kanály** pro integraci dat a ETL/ELT a rozsáhlou integraci s dalšími službami Azure, jako jsou **Power BI**, **CosmosDB** a **AzureML**.

![Diagram architektury Azure synapse Analytics](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>Špičkový SQL v oboru

**Synapse SQL** je systém distribuovaných dotazů pro T-SQL, který umožňuje scénáře datových skladů a virtualizace dat a rozšiřuje T-SQL na řešení scénářů streamování a strojového učení.

* Synapse SQL nabízí jak **servery bez serveru** , tak i **vyhrazené** modely prostředků. Pokud potřebujete předvídatelný výkon a náklady, vytvoříte vyhrazené fondy SQL, kterými si rezervujete výkon pro data uložená v tabulkách SQL. Pro neplánované nebo rozstupné úlohy použijte vždy dostupný koncový bod SQL bez serveru.
* Použití integrovaných funkcí **streamování** k obstání dat z cloudových zdrojů dat do tabulek SQL
* Integrujte AI s SQL pomocí modelů **strojového učení** pro stanovení skóre dat pomocí [funkce prediktivního jazyka T-SQL](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true) .

## <a name="industry-standard-apache-spark"></a>Oborová standardní Apache Spark

**Apache Spark pro Azure synapse** hluboko a hladce integruje Apache Spark – nejoblíbenější Open Source velký stroj dat, který se používá pro přípravu dat, inženýry dat, ETL a strojové učení.

* Modely ML s SparkML algoritmy a integrací AzureML pro Apache Spark 2,4 s integrovanou podporou pro systém Linux Foundation Delta Lake.
* Zjednodušený model prostředků, který vám zadarmo nemusíte dělat starosti se správou clusterů.
* Rychlé spuštění Sparku a agresivní automatické škálování.
* Integrovaná podpora rozhraní .NET pro Spark umožňuje znovu použít vaše odbornosti v jazyce C# a stávající kód .NET v rámci aplikace Spark.

## <a name="working-with-your-data-lake"></a>Práce s Data Lake

Azure synapse odstraňuje tradiční technologické bariéry mezi použitím SQL a Spark společně. V závislosti na vašich potřebách a odborných znalostech můžete bezproblémově kombinovat a rozlišovat.

* Sdílený systém metadat, který je kompatibilní s podregistrem, umožňuje, aby se tabulky definované na souborech v Data Lake plynule využily pomocí Sparku nebo podregistru.
* SQL a Spark můžou přímo prozkoumat a analyzovat soubory Parquet, CSV, TSV a JSON, které jsou uložené v Data Lake.
* Rychlé škálovatelné načítání a uvolňování dat mezi databázemi SQL a Spark

## <a name="built-in-data-integration"></a>Integrovaná integrace dat

Azure synapse obsahuje stejný modul pro integraci dat a prostředí jako Azure Data Factory, což vám umožní vytvářet bohatě škálovatelné kanály ETL bez nutnosti opustit Azure synapse Analytics.

* Ingestování dat z devadesáti zdrojů dat
* Code-Free ETL s aktivitami toku dat
* Orchestrace poznámkových bloků, úloh Sparku, uložených procedur, skriptů SQL a dalších

## <a name="unified-management-monitoring-and-security"></a>Sjednocená Správa, monitorování a zabezpečení

Azure synapse nabízí pro podniky jediný způsob, jak spravovat analytické prostředky, monitorovat využití a aktivity a vysazovat zabezpečení.

* Přiřazení uživatelů k roli pro zjednodušení přístupu k prostředkům analýzy
* Jemně odstupňované řízení přístupu k datům a kódu
* Jeden řídicí panel pro monitorování prostředků, využití a uživatelů napříč SQL a Spark

## <a name="unified-experience"></a>Jednotné prostředí

**Synapse Studio** je uživatelské prostředí, které spojuje všechno dohromady s datovými inženýry. Umožňuje jim provádět všechny úlohy, které potřebují k vytvoření kompletního analytického řešení.

* Klíčové úlohy datových techniků na jednom místě: ingestování, prozkoumávání, příprava, orchestrace, vizualizace
* Špičková produktivita pro psaní kódu SQL nebo Spark: vytváření, ladění a optimalizace výkonu
* Integrace s procesem Enterprise CI/CD

## <a name="engage-with-the-synapse-engineering-team"></a>Zapojení týmu synapse Engineering

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Položte otázky pro vývoj.
- [Microsoft Q&Stránka s otázkou](/answers/topics/azure-synapse-analytics.html): Položte technické dotazy.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)
