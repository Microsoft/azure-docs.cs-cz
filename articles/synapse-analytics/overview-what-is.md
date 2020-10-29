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
ms.openlocfilehash: 09429e26c5b7192141262e1a31c0ef90427bdd32
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899031"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Co je Azure synapse Analytics (pracovní prostory verze Preview)?

[!INCLUDE [preview](includes/note-preview.md)]

Podniková analýza musí fungovat na velkém rozsahu libovolného druhu dat bez ohledu na to, jestli jsou hrubá, rafinovaná nebo vysoce popracovaná. To obvykle vyžaduje, aby podniky spolupracovaly s velkými objemy dat a technologie datových skladů do složitých datových kanálů, které pracují s daty v relačních úložištích a v datových lakůch. Tyto druhy řešení, jako je obtížné sestavovat, udržovat a zabezpečit. Jejich složitost se doručí na to, co Enteprises potřebuje.

**Azure synapse** je integrovaná analytická služba, která zrychluje přehled napříč datovými sklady a systémy velkých objemů dat. Azure synapse spojuje nejlepší technologie **SQL** , které se používají v datových skladech v podniku, technologie **Sparku** používané pro velké objemy dat a **kanály** pro integraci dat a ETL/ELT. **Synapse Studio** poskytuje jednotné prostředí pro správu, monitorování, kódování a zabezpečení. Synapse má rozsáhlou integraci s dalšími službami Azure, jako jsou **PowerBI** , **CosmosDB** a **AzureML** .

## <a name="key-features--benefits"></a>Klíčové funkce & výhody

### <a name="industry-leading-sql"></a>Špičkový SQL v oboru

* **Synapse SQL** je distribuovaný systém dotazů, který umožňuje podnikům implementovat scénáře datových skladů a virtualizace dat pomocí standardních a známých prostředí T-SQL. Rozšiřuje také možnosti scénářů SQL pro řešení streamování a strojového učení.

* Synapse SQL nabízí jak **servery bez serveru** , tak i **vyhrazené** modely prostředků, které nabízejí možnosti spotřeby a fakturace podle vašich potřeb. Pro předvídatelný výkon a náklady Vytvořte vyhrazené fondy SQL pro rezervaci výkonu pro data uložená v tabulkách SQL. Pro neplánované nebo rozstupné úlohy použijte vždy dostupný koncový bod SQL bez serveru.
* Použití integrovaných funkcí **streamování** k obstání dat z cloudových zdrojů dat do tabulek SQL
* Integrujte AI s SQL pomocí modelů **strojového učení** pro stanovení skóre dat pomocí [funkce prediktivního jazyka T-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest) .

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

Azure synapse přináší integrovaný nástroj se stejným modulem pro integraci dat a prostředí jako Azure Data Factory, což vám umožní vytvářet bohatý kanály ETL v měřítku, aniž byste museli opustit analýzu synapse.

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

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Používání SQL na vyžádání](quickstart-sql-on-demand.md)
