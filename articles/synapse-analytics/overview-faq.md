---
title: Časté otázky - Azure Synapse Analytics
description: Nejčastější dotazy k Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 00a98fdeb4b8febbcb9d1183fabffc7298cc9e4c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770732"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Azure Synapse Analytics (náhled pracovního prostoru) nejčastější dotazy

V této příručce najdete nejčastěji kladené otázky pro Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Obecné

### <a name="q-what-is-azure-synapse-analytics"></a>Otázka: Co je Azure Synapse Analytics

Odpověď: Azure Synapse je integrovaná datová platforma pro BI, AI a nepřetržitou inteligenci. Propojuje různé analytické moduly runtimes, jako je SQL a Spark, prostřednictvím jediné platformy, která poskytuje jednotný způsob:

- Zabezpečte své analytické prostředky, včetně sítě, správy jednotného přihlašování přístup u fondu, dat a artefaktů vývoje.
- Snadno monitorujte a rychle optimalizujte, reagujte a laděte události, které se dějí ve vašich aktivitách pracovního prostoru v libovolné vrstvě.
- Spravujte metadata napříč motory. Vytvořte tabulku Spark a bude automaticky dostupná ve vašich databázích Azure Synapse.
- Pracujte s daty prostřednictvím jednotného uživatelského prostředí. Synapse Studio přináší na stejnou platformu vývojáře velkých objemů dat, datové inženýry, správce dat, datové analytiky a datové vědce.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>Otázka: Jak začít s Azure Synapse Analytics

A: Chcete-li začít používat Azure Synapse Analytics, [vytvořte synapse pracovní prostor](https://portal.azure.com) (je to zdarma!) a vytvořit prostředky, které chcete v rámci tohoto pracovního prostoru. Můžete sledovat jeden z našich kurzů rychlého startu, jako je [například vytvořit fond SQL Synapse](quickstart-create-sql-pool.md) nebo [Vytvořit pracovní prostor](quickstart-create-workspace.md), který vás provede jednoduchým případem použití. 

Ukázkové poznámkové bloky a SQL skripty najdete také v našem [úložišti](https://github.com/Azure/azure-synapse-analytics/tree/master/samples). Pokud se potřebujete připojit k veřejné datové sadě, vytvořte novou propojenou službu s následujícími atributy:

- azure_storage_account_name = "azureopendatastorage"
- azure_storage_sas_token = "" (napište **""**)

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>Otázka: Jaké jsou hlavní součásti Azure Synapse Analytics

A: Azure Synapse má následující možnosti:

- Funkce analýzy jsou nabízeny prostřednictvím fondu SQL nebo SQL na vyžádání (náhled) (Bez serveru).
- Fond Apache Spark (preview) s plnou podporou Scala, Pythonu, SparkSQL a C #
- Flow data nabízí prostředí pro transformaci velkých objemů dat bez kódu
- Integrace dat & Orchestraci pro integraci dat a zprovoznění veškerého vývoje kódu
- Studio pro přístup ke všem těmto funkcím prostřednictvím jediného webového uživatelského uživatelského

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>Otázka: Jak Azure Synapse Analytics souvisí s Azure SQL Data Warehouse

Odpověď: Azure Synapse Analytics je vývoj Azure SQL Data Warehouse do analytické platformy, která zahrnuje fond SQL jako řešení datového skladu. Tato platforma kombinuje zkoumání dat, ingestování, transformace, příprava a sloužící analytická vrstva.

## <a name="use-cases"></a>Případy použití

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>Otázka: Co je dobrý případ použití pro fond SYNAPse SQL

A: SQL fond je srdcem vašich potřeb datového skladu. Jedná se o přední řešení datového skladu v [ceně/výkonu](https://azure.microsoft.com/services/sql-data-warehouse/compare/). Fond SQL je špičkové řešení cloudového datového skladu, protože můžete:

- obsluhovat širokou a smíšenou škálu úloh bez dopadu na výkon díky vysoké souběžnosti a izolaci pracovního vytížení
- zabezpečení dat snadno prostřednictvím pokročilých funkcí od zabezpečení sítě až po jemný přístup
- využívat širokou škálu ekologických systémů

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>Otázka: Co je dobrý případ použití pro Spark v Synapse

A: Naším prvním cílem je poskytnout skvělé prostředí datového inženýrství pro transformaci dat přes jezero v dávce nebo datovém proudu. Díky těsné a jednoduché integraci do naší orchestrace dat je zprovoznění vaší vývojové práce jednoduché.

Dalším případem použití pro Spark je pro datovévědce:

- extrahování funkce
- prozkoumat data
- trénování modelu

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>Otázka: Co je dobrý případ použití pro SQL on-demand v Synapse

A: SQL on-demand je dotazová služba přes data v datovém jezeře. Umožňuje demokratizovat přístup ke všem datům tím, že poskytuje známou syntaxi T-SQL pro dotazování dat na místě, aniž byste museli kopírovat nebo načítat data do specializovaného úložiště.

Příklady případu použití zahrnují následující:

- základní zjišťování a průzkum - poskytuje datovým analytikům, nově vznikajícím datovým vědcům a datovým inženýrům snadnou cestu k prvnímu nahlédnutí do dat žijících v jejich datovém jezeře se schématem na čtených dotazech T-SQL
- logický datový sklad – analytici dat můžou spustit plnou expresivitu jazyka T-SQL a přímo se dotazovat a analyzovat data s bydlištěm ve Službě Azure a používat známé nástroje BI (např. služby Azure Analyses Services, Power BI Premium atd.) k aktualizaci řídicích panelů opětovným spuštěním dotazů Starlight Query
- "jeden dotaz" ETL – umožňuje datovým inženýrům transformovat data založená na Azure Storage z jednoho formátu do druhého, filtrovat, agregovat atd.

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>Otázka: Co je dobrý případ použití pro tok dat v Synapse

A: Tok dat umožňuje technikům dat rozvíjet logiku grafické transformace dat bez psaní kódu. Výsledné toky dat jsou prováděny jako aktivity v rámci integrace dat & Orchestraci. Aktivity toku dat lze zprovoznit prostřednictvím stávajících možností plánování, řízení, toku a monitorování.

## <a name="security-and-access"></a>Zabezpečení a přístup

Odpověď: Komplexní jednotné přihlašování je v synapse Analytics důležitým procesem ověřování. Správa a předávání identity prostřednictvím úplné integrace AAD je nutností.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adlsg2"></a>Otázka: Jak získám přístup k souborům a složkám v ADLSg2

A: Přístup k souborům a složkám je aktuálně spravován prostřednictvím ADLSg2. Další informace naleznete v [tématu Řízení přístupu k úložišti datového jezera](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>Otázka: Můžu použít nástroje business intelligence třetích stran pro přístup k Azure Synapse Analytics

A: Ano, můžete použít vaše obchodní aplikace třetích stran, jako je Tableau a Power BI, pro připojení k fondu SQL a SQL na vyžádání. Spark podporuje IntelliJ.

## <a name="next-steps"></a>Další kroky

- [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
- [Použití Synapse Studio](quickstart-synapse-studio.md)
- [Vytvoření fondu SQL](quickstart-create-sql-pool.md)
- [Používání SQL na vyžádání](quickstart-sql-on-demand.md)
- [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool.md) 
