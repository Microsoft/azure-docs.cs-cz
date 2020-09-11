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
ms.openlocfilehash: 653ad7e9b41e1c8a4708f2ab951676c144316192
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005850"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Co je Azure synapse Analytics (pracovní prostory verze Preview)?

[!INCLUDE [preview](includes/note-preview.md)]

Současná podniková analýza vyžaduje provoz v obrovském měřítku na jakémkoli druhu dat, ať už se jedná o nezpracovaný, rafinovaný nebo vysoce spravovaný. V minulosti si sestavíme tyto druhy analytických řešení, které podniky vyžadují pro spojování velkých objemů dat a technologií datových skladů, jako je Spark a SQL. V dalším kroku je potřeba je integrovat do bohatých datových kanálů, které fungují napříč daty v relačních úložištích a datových jezerech.  

Toto řešení je obtížné sestavovat, konfigurovat, zabezpečovat a udržovat, což zpozdí extrakci inteligentního přehledu.

**Azure synapse** je integrovaná analytická služba, která urychluje přehled ze všech dat v jakémkoli měřítku napříč datovými sklady a systémy pro analýzu velkých objemů dat. Přináší dohromady nejlepší technologie **SQL** , které se používají v datových skladech v podniku, technologie **Sparku** používané při analýze velkých objemů dat a **kanály** pro integraci dat a ETL/ELT.

Azure synapse se dodává s uživatelským prostředím pro web Native **Studio** , které poskytuje jednotné prostředí a model pro správu, monitorování, kódování a zabezpečení.

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

* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití sady Synapse Studio](quickstart-synapse-studio.md)
* [Vytvoření fondu SQL](quickstart-create-sql-pool-portal.md)
* [Používání SQL na vyžádání](quickstart-sql-on-demand.md)
* [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool-portal.md)
