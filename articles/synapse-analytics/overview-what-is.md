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
ms.openlocfilehash: be124bdde4427113d56f44f0c1fa19c600bd768e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496043"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Co je Azure synapse Analytics (pracovní prostory verze Preview)?

[!INCLUDE [preview](includes/note-preview.md)]

Současná podniková analýza vyžaduje provoz v obrovském měřítku na jakémkoli druhu dat, ať už se jedná o nezpracovaný, rafinovaný nebo vysoce spravovaný. V minulosti si sestavíme tyto druhy analytických řešení, které podniky vyžadují pro spojování velkých objemů dat a technologií datových skladů, jako je Spark a SQL. V dalším kroku je potřeba je integrovat do bohatých datových kanálů, které fungují napříč daty v relačních úložištích a datových jezerech.  

Toto řešení je obtížné sestavovat, konfigurovat, zabezpečovat a udržovat, což zpozdí extrakci inteligentního přehledu.

**Azure synapse** je integrovaná analytická služba, která urychluje přehled ze všech dat v jakémkoli měřítku napříč datovými sklady a systémy pro analýzu velkých objemů dat. Přináší dohromady nejlepší technologie **SQL** , které se používají v datových skladech v podniku, technologie **Sparku** používané při analýze velkých objemů dat, a **kanály** k orchestraci aktivit a přesunu dat.

Azure synapse se dodává s uživatelským prostředím pro web Native **Studio** , které poskytuje jednotné prostředí a model pro správu, monitorování, kódování a zabezpečení.

Azure synapse poskytuje nejjednodušší a nejrychlejší způsob, jak může společnost shromažďovat přehledy o všech datech v libovolné velikosti, a to pomocí analýz, které jsou pro vás nejdůležitější. Integruje se hluboko s **Power BI** , která umožňuje datovým technikům vytvářet Analytická řešení, která fungují pro účely Business Intelligence.

Kromě toho Azure synapse usnadňuje vytváření prediktivních modelů a pokročilou analýzu pomocí strojového učení prostřednictvím integrované podpory pro **AzureML**.

## <a name="key-features--benefits"></a>Klíčové funkce & výhody

### <a name="industry-leading-sql"></a>Špičkový SQL v oboru

* **Synapse SQL** je distribuovaný systém dotazů, který podnikům umožňuje implementovat scénáře datových skladů a virtualizace dat pomocí standardních prostředí T-SQL, které znají technici pro práci s daty. Rozšiřuje také možnosti scénářů SQL pro řešení streamování a strojového učení.

* Synapse SQL nabízí jak **servery bez serveru** , tak i **zřízené** modely prostředků, což nabízí možnosti spotřeby a fakturace podle vašich potřeb. V případě předvídatelného výkonu a nákladů zřídí fondy, aby vyhradí výpočetní výkon pro data uložená v tabulkách SQL. Pro neplánované nebo rozstupné úlohy můžete použít koncový bod SQL bez serveru, který je k dispozici vždy.
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

### <a name="built-in-orchestration-via-pipelines"></a>Vestavěná orchestrace přes kanály

Azure synapse nabízí integrovaný modul pro integraci dat a prostředí, jako Azure Data Factory, což vám umožní vytvářet kanály s bohatou datovou částí bez použití samostatného modulu orchestrace.

* Přesun dat mezi Azure synapse a 90 + místními zdroji dat
* Orchestrace poznámkových bloků, kanálů, úloh Spark, skriptů SQL, uložených procedur
* ETL bez kódu s aktivitami toku dat

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
* [Použití synapse studia](quickstart-synapse-studio.md)
* [Vytvoření fondu SQL](quickstart-create-sql-pool-portal.md)
* [Používání SQL na vyžádání](quickstart-sql-on-demand.md)
* [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool-portal.md)
