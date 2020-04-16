---
title: Co je Azure Synapse Analytics?
description: Přehled Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 65f6c6627093358f8cbc66055bb9b16561f7c610
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424661"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Co je Azure Synapse Analytics (náhled pracovních prostorů)?

[!INCLUDE [preview](includes/note-preview.md)]

Dnes podniková analytika vyžaduje, aby fungovala v masivním měřítku na jakémkoli druhu dat, ať už nezpracovaných, rafinovaných nebo vysoce kurátorských. V minulosti budování těchto druhů analytických řešení vyžadovalo, aby podniky spojily velké objemy dat a technologie datových skladů, jako je Spark a SQL. Dále je budou muset integrovat do bohatých datových kanálů, které fungují napříč daty v relačních úložištích a datových jezerech.  

Řešení, jako je tato, je obtížné vytvářet, konfigurovat, zabezpečit a udržovat, což oddaluje rychlé získávání inteligentního přehledu.

**Azure Synapse** je integrovaná analytická služba, která urychluje čas na přehled ze všech dat v libovolném měřítku, napříč datovými sklady a systémy analýzy velkých objemů dat. Spojuje to nejlepší z **SQL** technologií používaných v podnikových datových skladech, **technologiích Spark** používaných v analýze velkých objemů dat a **pipelines** pro orchestraci aktivit a pohybu dat. 

Azure Synapse je dodáván s web-nativní **studio** uživatelské prostředí, které poskytuje jediné prostředí a model pro správu, monitorování, kódování a zabezpečení.

Azure Synapse poskytuje pro podnik nejjednodušší a nejrychlejší způsob, jak získat přehled o všech datech v libovolné velikosti pomocí analýzy, kterou zná nejvíce. Hluboce se integruje s **Power BI,** která umožňuje datovým inženýrům vytvářet analytická řešení, která fungují od konce a poskytují business intelligence. 

Azure Synapse navíc usnadňuje prediktivní vytváření modelů a pokročilou analýzu pomocí strojového učení prostřednictvím integrované podpory **AzureML**.

## <a name="key-features--benefits"></a>Klíčové vlastnosti & výhod

### <a name="industry-leading-sql"></a>Špičková SQL

* **Synapse SQL** je distribuovaný dotazovací systém, který umožňuje podnikům implementovat scénáře ukládání dat a virtualizace dat pomocí standardních prostředí T-SQL známých datovým inženýrům. Také rozšiřuje možnosti SQL k řešení streamování a strojového učení scénáře.

* Synapse SQL nabízí modely **prostředků bez serveru** i **zřízené** prostředky, které nabízejí možnosti spotřeby a fakturace, které vyhovují vašim potřebám. Pro předvídatelný výkon a náklady zřizování fondů pro rezervaci výpočetního výkonu pro data uložená v tabulkách SQL. Pro neplánované nebo prasknutí úlohy použijte koncový bod SQL bez serveru, který je vždy k dispozici.
* Použití integrovaných funkcí **streamování** k přiřazování dat ze zdrojů cloudových dat do tabulek SQL
* Integrace ai s SQL pomocí modelů **strojového učení** pro skóre dat pomocí funkce T-SQL PREDICT

### <a name="industry-standard-apache-spark"></a>Standardní Apache Spark

**Synapse Spark** hluboce a hladce integruje Apache Spark - nejoblíbenější open source big data engine používaný pro přípravu dat, datové inženýrství, ETL a strojové učení.

* Modely ML s algoritmy SparkML a integrace AzureML pro Apache Spark 2.4 s integrovanou podporou pro Linux Foundation Delta Lake.
* Zjednodušený model prostředků, který vás osvobozuje od nutnosti starat se o správu clusterů.
* Rychlé spuštění Spark a agresivní automatické škálování.
* Integrovaná podpora rozhraní .NET pro Spark, která umožňuje znovu použít vaše odborné znalosti jazyka C# a existující kód .NET v rámci aplikace Spark.

### <a name="interop-of-sql-and-spark-on-your-data-lake"></a>Interop SQL a Spark na vašem datovém jezeře

Azure Synapse odstraňuje tradiční technologie bariéry mezi pomocí SQL a Spark společně. Můžete bez problémů kombinovat a odpovídat na základě vašich potřeb a odborných znalostí.

* Sdílený systém metadat kompatibilní s Hive umožňuje bezproblémovou spotřebovanou tabulkami na souborech v datovém jezeře buď Sparkem, nebo Hive.
* SQL a Spark mohou přímo zkoumat a analyzovat soubory parket, CSV, TSV a JSON uložené v datovém jezeře.
* Rychlé škálovatelné načítání a uvolňování dat mezi databázemi SQL a Spark

### <a name="built-in-orchestration-via-pipelines"></a>Integrovaná orchestrace pomocí kanálů

Azure Synapse je součástí integrovaného modulu integrace dat a prostředí jako Azure Data Factory, což vám umožní vytvářet bohaté datové kanály bez použití samostatného modulu orchestrace.

* Přesun dat mezi synapse a 85+ místními zdroji dat
* Orchestrace poznámkových bloků, kanálů, úloh Spark, skriptů SQL, uložených procedur
* ETL bez kódu s aktivitami toku dat

### <a name="unified-management-monitoring-and-security"></a>Jednotná správa, monitorování a zabezpečení

Azure Synapse poskytuje podnikům jediný způsob správy analytických prostředků, monitorování využití a aktivity a vynucení zabezpečení.

* Přiřazení uživatelů k roli pro zjednodušení přístupu k analytickým zdrojům
* Jemně odstupňované řízení přístupu k datům a kódu
* Jeden řídicí panel pro monitorování prostředků, využití a uživatelů napříč SQL a Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** je webové nativní prostředí, které spojuje vše dohromady pro datové inženýry, což jim umožňuje na jednom místě provádět každý úkol, který potřebují k vytvoření kompletního řešení.

* Vytvořte komplexní analytické řešení na jednom místě: ingestujte, prozkoumejte, připravte se, vizualizujte
* Špičková produktivita pro datové inženýry, kteří píší kód SQL nebo Spark: vytváření, ladění a optimalizace výkonu
* Integrace s podnikovými procesy CI/CD

## <a name="next-steps"></a>Další kroky

* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití Synapse Studio](quickstart-synapse-studio.md)
* [Vytvoření fondu SQL](quickstart-create-sql-pool.md)
* [Použití sql na vyžádání](quickstart-sql-on-demand.md)
* [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool.md)
