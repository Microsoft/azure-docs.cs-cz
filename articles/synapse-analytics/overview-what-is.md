---
title: Co je Azure Synapse Analytics?
description: Přehled služby Azure synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: b93814c69a08dd757a390232804a00b2777bc20e
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90051983"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Co je Azure synapse Analytics (pracovní prostory verze Preview)?

[!INCLUDE [preview](includes/note-preview.md)]

Podniková analýza musí pracovat s rozsáhlým škálováním na jakýkoli druh dat bez ohledu na to, jestli je hrubá, rafinovaná nebo vysoce popracovaná. Tyto podniky vyžadují spolupráci s velkými objemy dat a technologie datových skladů, jako jsou Spark a SQL, do kanálů s bohatými daty, které fungují napříč daty v relačních úložištích a v datových lakůch. Toto řešení je obtížné sestavit, zabezpečit a udržovat. Zpoždění složitosti, které dodává přehled o Enteprises potřeb.

**Azure synapse** je integrovaná analytická služba, která zrychluje přehled napříč datovými sklady a systémy pro analýzy velkých objemů dat. V jádru Azure synapse přináší dohromady nejlepší technologie **SQL** , které se používají v podnikových datových skladech, technologie **Sparku** používané pro velké objemy dat a **kanály** pro integraci dat a ETL/ELT.

Azure synapse poskytuje webové prostředí **studia** , které poskytuje jediné místo pro správu, monitorování, kódování a zabezpečení.

Azure synapse poskytuje nejjednodušší a nejrychlejší způsob, jak může společnost shromažďovat přehledy o všech datech v libovolné velikosti, a to pomocí analýz, které jsou pro vás nejdůležitější. Integruje se hluboko s **Power BI** , která umožňuje datovým technikům vytvářet Analytická řešení, která fungují pro účely Business Intelligence.

Kromě toho Azure synapse usnadňuje vytváření prediktivních modelů a pokročilou analýzu pomocí strojového učení prostřednictvím integrované podpory pro **AzureML**.

## <a name="key-features--benefits"></a>Klíčové funkce & výhody

### <a name="industry-leading-sql"></a>Špičkový SQL v oboru

* **Synapse SQL** je distribuovaný systém dotazů, který podnikům umožňuje implementovat scénáře datových skladů a virtualizace dat pomocí standardních prostředí T-SQL, které znají technici pro práci s daty. Rozšiřuje také možnosti scénářů SQL pro řešení streamování a strojového učení.

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
* ETL bez kódu s aktivitami toku dat
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
