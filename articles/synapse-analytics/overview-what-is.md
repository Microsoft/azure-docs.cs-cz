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
ms.openlocfilehash: 652f98659f96b36e3185432e50d9d36dc569bd43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537948"
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

* Tabulky definované na souborech v Data Lake jsou bez problémů spotřebovány pomocí Sparku nebo podregistru.
* SQL a Spark můžou přímo prozkoumat a analyzovat soubory Parquet, CSV, TSV a JSON, které jsou uložené v Data Lake.
* Rychlé a škálovatelné načítání dat mezi databázemi SQL a Spark

## <a name="built-in-data-integration"></a>Integrovaná integrace dat

Azure synapse obsahuje stejný modul pro integraci dat a prostředí jako Azure Data Factory, což vám umožní vytvářet bohatě škálovatelné kanály ETL bez nutnosti opustit Azure synapse Analytics.

* Ingestování dat z devadesáti zdrojů dat
* Code-Free ETL s aktivitami toku dat
* Orchestrace poznámkových bloků, úloh Sparku, uložených procedur, skriptů SQL a dalších

## <a name="unified-experience"></a>Jednotné prostředí 

**Synapse Studio** nabízí jeden způsob, jak podnikům vytvářet řešení, udržovat je a zabezpečit vše v jednom uživatelském prostředí.

* Provádění klíčových úloh: ingestování, prozkoumávání, příprava, orchestrace, vizualizace
* Monitorování prostředků, používání a uživatelů v SQL a Sparku
* Zjednodušení přístupu k analytickým prostředkům pomocí řízení přístupu na základě role
* Zápis kódu SQL nebo Sparku a integrace s procesy podnikového CI/CD

## <a name="engage-with-the-synapse-community"></a>Zapojení do komunity synapse

- [Microsoft Q&A](/answers/topics/azure-synapse-analytics.html): Položte technické dotazy.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Položte otázky pro vývoj.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)
