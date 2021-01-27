---
title: Co je Azure Synapse Analytics?
description: Přehled služby Azure synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 7882fc6a6823a93d09c04a8ae2c19005df4843d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917089"
---
# <a name="what-is-azure-synapse-analytics"></a>Co je Azure Synapse Analytics?

Podniková analýza musí fungovat na velkém rozsahu libovolného druhu dat bez ohledu na to, jestli jsou hrubá, rafinovaná nebo vysoce popracovaná. To obvykle vyžaduje, aby podniky spolupracovaly s velkými objemy dat a technologie datových skladů do složitých datových kanálů, které pracují s daty v relačních úložištích a v datových lakůch. Tyto druhy řešení se obtížně sestavují, udržují a zabezpečují. Jejich složitost se doručí na to, co potřebují.

**Azure synapse** je integrovaná analytická služba, která zrychluje přehled napříč datovými sklady a systémy velkých objemů dat. Azure synapse spojuje nejlepší technologie **SQL** , které se používají v podnikových datových skladech, technologie **Sparku** používané pro velké objemy dat, **kanály** pro integraci dat a ETL/ELT a rozsáhlou integraci s dalšími službami Azure, jako jsou **Power BI**, **CosmosDB** a **AzureML**.

## <a name="key-features--benefits"></a>Klíčové funkce & výhody

### <a name="industry-leading-sql"></a>Špičkový SQL v oboru

* **Synapse SQL** je systém distribuovaných dotazů pro T-SQL, který umožňuje scénáře datových skladů a virtualizace dat a rozšiřuje T-SQL na řešení scénářů streamování a strojového učení.
* Synapse SQL nabízí jak **servery bez serveru** , tak i **vyhrazené** modely prostředků, které nabízejí možnosti spotřeby a fakturace podle vašich potřeb. Pokud potřebujete předvídatelný výkon a náklady, vytvoříte vyhrazené fondy SQL, kterými si rezervujete výkon pro data uložená v tabulkách SQL. Pro neplánované nebo rozstupné úlohy použijte vždy dostupný koncový bod SQL bez serveru.
* Použití integrovaných funkcí **streamování** k obstání dat z cloudových zdrojů dat do tabulek SQL
* Integrujte AI s SQL pomocí modelů **strojového učení** pro stanovení skóre dat pomocí [funkce prediktivního jazyka T-SQL](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true) .

### <a name="industry-standard-apache-spark"></a>Oborová standardní Apache Spark

**Apache Spark pro Azure synapse** hluboko a hladce integruje Apache Spark – nejoblíbenější Open Source velký stroj dat, který se používá pro přípravu dat, inženýry dat, ETL a strojové učení.

* Modely ML s SparkML algoritmy a integrací AzureML pro Apache Spark 2,4 s integrovanou podporou pro systém Linux Foundation Delta Lake.
* Zjednodušený model prostředků, který vám zadarmo nemusíte dělat starosti se správou clusterů.
* Rychlé spuštění Sparku a agresivní automatické škálování.
* Integrovaná podpora rozhraní .NET pro Spark umožňuje znovu použít vaše odbornosti v jazyce C# a stávající kód .NET v rámci aplikace Spark.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Spolupráce SQL a Apache Spark v Data Lake

Azure synapse odstraňuje tradiční technologické bariéry mezi použitím SQL a Spark společně. V závislosti na vašich potřebách a odborných znalostech můžete bezproblémově kombinovat a rozlišovat.

* Sdílený systém metadat, který je kompatibilní s podregistrem, umožňuje, aby se tabulky definované na souborech v Data Lake plynule využily pomocí Sparku nebo podregistru.
* SQL a Spark můžou přímo prozkoumat a analyzovat soubory Parquet, CSV, TSV a JSON, které jsou uložené v Data Lake.
* Rychlé škálovatelné načítání a uvolňování dat mezi databázemi SQL a Spark

### <a name="built-in-data-integration-via-pipelines"></a>Integrovaná integrace dat prostřednictvím kanálů

Azure synapse přináší integrovaný nástroj se stejným modulem pro integraci dat a prostředí jako Azure Data Factory, což vám umožní vytvářet bohatě škálovatelné kanály ETL bez nutnosti opustit Azure synapse Analytics.

* Ingestování dat z devadesáti zdrojů dat
* Code-Free ETL s aktivitami toku dat
* Orchestrace poznámkových bloků, úloh Sparku, uložených procedur, skriptů SQL a dalších

### <a name="unified-management-monitoring-and-security"></a>Sjednocená Správa, monitorování a zabezpečení

Azure synapse nabízí pro podniky jediný způsob, jak spravovat analytické prostředky, monitorovat využití a aktivity a vysazovat zabezpečení.

* Přiřazení uživatelů k roli pro zjednodušení přístupu k prostředkům analýzy
* Jemně odstupňované řízení přístupu k datům a kódu
* Jeden řídicí panel pro monitorování prostředků, využití a uživatelů napříč SQL a Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** je nativní prostředí pro web, které spojuje všechno společně s datovými inženýry a umožňuje jim v jednom umístění provádět všechny úlohy, které potřebují k sestavení kompletního řešení.

* Vytvářejte komplexní analytické řešení na jednom místě: ingestování, prozkoumávání, příprava, orchestrace, vizualizace
* Špičková produktivita pro datové techniky napisující kód SQL nebo Spark: vytváření, ladění a optimalizace výkonu
* Integrace s procesy podnikového CI/CD

## <a name="engage-with-the-synapse-engineering-team"></a>Zapojení týmu synapse Engineering

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Položte otázky pro vývoj.
- [Microsoft Q&Stránka s otázkou](/answers/topics/azure-synapse-analytics.html): Položte technické dotazy.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)
