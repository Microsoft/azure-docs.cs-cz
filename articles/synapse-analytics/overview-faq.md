---
title: Nejčastější dotazy k Azure synapse Analytics (pracovní prostory Preview)
description: Nejčastější dotazy ke službě Azure synapse Analytics (pracovní prostory verze Preview)
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 530ebe09ee0ca44b0a5fbd4b8da2784e2c7ae7ea
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843889"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Nejčastější dotazy ke službě Azure synapse Analytics (pracovní prostory ve verzi Preview)

V této příručce najdete nejčastější dotazy k synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Obecné

### <a name="q-what-is-azure-synapse-analytics"></a>Otázka: co je Azure synapse Analytics

Odpověď: Azure synapse je integrovaná datová platforma pro BI, AI a nepřetržitou analýzu. Spojuje různé běhové moduly analýzy, jako je SQL a Apache Spark, prostřednictvím jedné platformy, která poskytuje jednotný způsob:

- Zabezpečte své analytické prostředky, včetně sítě, spravujte přístup s jednotným přihlašováním ke fondům, datům a vývojovým artefaktům.
- Snadno Sledujte a rychle Optimalizujte, reagují a ladíte události, které probíhají v aktivitách pracovního prostoru v libovolné vrstvě.
- Spravujte svá metadata napříč moduly. Vytvořte tabulku fondu Apache Spark bez serveru, která bude automaticky dostupná ve vašich databázích Azure synapse.
- Interakci s daty prostřednictvím jednotného uživatelského prostředí. Synapse Studio přináší vývojářům pro velké objemy dat, datové inženýry, specializující, analytiky dat a odborníky na data na stejné platformě.

Další informace najdete v tématu [co je Azure synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics/overview-what-is).

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>Otázka: Jaké jsou hlavní součásti Azure synapse Analytics

Odpověď: Azure synapse má následující možnosti:

- Možnosti analýzy jsou nabízeny prostřednictvím vyhrazeného fondu SQL nebo bez serveru SQL (verze Preview).
- Fond Apache Spark bez serveru (Preview) s plnou podporou pro Scala, Python, Spark, SQL a C #
- Tok dat nabízí prostředí pro transformaci velkých objemů dat bez kódu.
- Integrace dat & orchestrace pro integraci vašich dat a zprovozněníí veškerého vývoje kódu
- Studio pro přístup ke všem těmto funkcím prostřednictvím jediného webového uživatelského rozhraní

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>Otázka: jak se vztahuje Azure synapse Analytics na Azure SQL Data Warehouse

Odpověď: Azure synapse Analytics je vývojem Azure SQL Data Warehouse na analytické platformě, která zahrnuje vyhrazený fond SQL jako řešení datového skladu. Tato platforma kombinuje zkoumání dat, ingestování, transformaci, přípravu a obsluhu analytických vrstev.

## <a name="use-cases"></a>Případy použití

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>Otázka: Návody přejmenování publikovaného artefaktu (datová sada, Poznámkový blok, SQL Script atd.) v Azure synapse?

Odpověď: Chcete-li přejmenovat publikovaný soubor artefaktů, nejprve naklonujte soubor a přejmenujte nový soubor na název, kterému dáváte přednost. Budete muset ručně aktualizovat všechny odkazy na artefakt na nový název souboru a odstranit starý.

### <a name="q-what-is-a-good-use-case-for-dedicated-sql-pool"></a>Otázka: co je dobrým případem použití vyhrazeného fondu SQL

Odpověď: vyhrazený fond SQL je srdcem vašich potřeb datového skladu. Je to přední řešení datového skladu v [ceně a výkonu](https://azure.microsoft.com/services/sql-data-warehouse/compare/). Vyhrazený fond SQL je špičkovým řešením cloudového datového skladu, protože můžete:

- Díky vysoké souběžnosti a izolaci úloh slouží velká a smíšená škála úloh bez dopadu na výkon.
- Snadné zabezpečení dat prostřednictvím pokročilých funkcí v rozsahu od zabezpečení sítě až po jemně odstupňovaný přístup
- výhoda z široké škály ekosystémového systému

### <a name="q-what-is-a-good-use-case-for-serverless-apache-spark-pool-in-azure-synapse"></a>Otázka: co je vhodný případ použití pro fond Apache Spark bez serveru v Azure synapse

Odpověď: naším prvním cílem je zajistit Skvělé prostředí pro datovou přípravu pro transformaci dat prostřednictvím jezera v dávce nebo streamu. Jeho těsná a jednoduchá integrace k naší orchestraci dat usnadňuje práci vývojového prostředí.

Dalším případem použití pro Apache Spark je odborník na data:

- extrakce funkce
- Prozkoumat data
- Výuka modelu

### <a name="q-what-is-a-good-use-case-for-serverless-sql-pool-in-azure-synapse"></a>Otázka: co je dobrým případem použití pro fond SQL bez serveru v Azure synapse

Odpověď: fond SQL bez serveru je dotazovací služba nad daty ve službě Data Lake. Umožňuje Demokratizujte přístup ke všem datům tím, že poskytuje známou syntaxi T-SQL pro dotazování dat na místě, aniž by bylo nutné kopírovat nebo načítat data do specializovaného úložiště.

Příklady případů použití zahrnují následující:

- základní zjišťování a průzkum – poskytuje analytikům dat, vznikajícím datovým specialistům a datovým technikům jednoduchou cestu k prvnímu přehledu o živých datech v Data Lake pomocí schématu pro čtení dotazů T-SQL.
- logický datový sklad – datové analytiky můžou spouštět úplné expresivity jazyka T-SQL, aby se mohli přímo dotazovat a analyzovat data, která se nacházejí v Azure Storage, a pomocí známých nástrojů BI (např. analýzy Azure, Power BI Premium atd.) aktualizovat řídicí panely tím, že se znovu spustí dotazy dotazů Starlight.
- ETL "Single Query" – umožňuje datovým technikům transformovat Azure Storage data založená na jiném formátu na jiný, filtrovat, agregovat atd. při výkonném paralelním zpracování, uchovávat výsledky dotazů Azure Storage a zpřístupnit je okamžitě pro další zpracování v Starlight dotazech nebo jiných službách, které vás zajímají.

### <a name="q-what-is-a-good-use-case-for-data-flow-in-azure-synapse"></a>Otázka: co je dobrý případ použití pro tok dat v Azure synapse

Odpověď: tok dat umožňuje inženýrům dat vyvíjet grafické logiky pro transformaci dat bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v rámci integrace dat & orchestrace. Aktivity toku dat je možné provozovat prostřednictvím stávajících možností plánování, řízení, toku a monitorování.

## <a name="security-and-access"></a>Zabezpečení a přístup

O: ucelené prostředí jednotného přihlašování je důležitým ověřovacím procesem v synapse Analytics. Správa a předávání identit prostřednictvím úplné integrace služby Azure AD je nutné.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>Otázka: Návody získat přístup k souborům a složkám v ADLS Gen2

Odpověď: přístup k souborům a složkám je aktuálně spravován prostřednictvím ADLS Gen2. Další informace najdete v tématu [Data Lake řízení přístupu k úložišti](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>Otázka: je možné použít business intelligence nástroje třetích stran pro přístup ke službě Azure synapse Analytics

Odpověď: Ano, můžete použít obchodní aplikace třetích stran, jako je Tableau a Power BI, pro připojení k vyhrazenému fondu SQL a SQL fondu bez serveru. Spark podporuje IntelliJ.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)
