---
title: Nejčastější dotazy k Azure synapse Analytics
description: Nejčastější dotazy k Azure synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9a0fb8ed8ac54fa866b6db7d8f808c011c0c6758
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695817"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Nejčastější dotazy k Azure synapse Analytics

V této příručce najdete nejčastější dotazy k Azure synapse Analytics.

## <a name="general"></a>Obecné

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>Otázka: Jak můžu použít role RBAC k zabezpečení pracovního prostoru?

Odpověď: Azure synapse zavádí řadu rolí a oborů, které jim přiřadí. tím se zjednoduší zabezpečení vašeho pracovního prostoru.

Synapse role RBAC:
* Správce synapse
* Správce synapse SQL
* Správce synapse Spark
* Přispěvatel synapse (Preview)
* Vydavatel artefaktu synapse (Preview)
* Uživatel artefaktu synapse (Preview)
* Synapse – operátor COMPUTE (Preview)
* Uživatel s přihlašovacími údaji synapse (Preview)

Pokud chcete zabezpečit svůj pracovní prostor synapse, přiřaďte role RBAC těmto oborům RBAC:
* Pracovní prostory
* Fondy Spark
* Prostředí Integration runtime
* Propojené služby
* Přihlašovací údaje

Kromě toho mají vyhrazené fondy SQL stejné funkce zabezpečení, které znáte a máte rádi.

### <a name="q-how-do-i-control-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>Otázka: Návody řízení vyhrazených fondů SQL, fondů SQL bez serveru a fondů Spark bez serveru?

Odpověď: jako výchozí bod funguje Azure synapse s integrovanou analýzou nákladů a cenovými výstrahami dostupnými na úrovni předplatného Azure.

- Vyhrazené fondy SQL – máte přímý přehled o nákladech a kontrole nad náklady, protože vytvoříte a určíte velikost vyhrazených fondů SQL. Můžete dále řídit, kteří uživatelé můžou vytvářet nebo škálovat vyhrazené fondy SQL pomocí rolí Azure RBAC.

- Fondy SQL bez serveru – můžete monitorovat a řídit náklady na správu, které vám umožní na denní, týdenní a měsíční úrovni. Další informace [najdete v tématu Správa nákladů pro fond SQL bez serveru](./sql/data-processed.md) . 

- Fondy Spark bez serveru – můžete omezit, kdo může vytvářet fondy Spark s rolemi RBAC synapse.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>Otázka: bude pracovní prostor synapse podporovat organizaci objektů a členitosti na GA?

Odpověď: pracovní prostory synapse podporují uživatelsky definované složky.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>Otázka: Mohu propojit více než jeden Power BI pracovní prostor k jednomu pracovnímu prostoru Azure synapse?
    
Odpověď: v současné době můžete propojit pouze jeden Power BI pracovní prostor k pracovnímu prostoru Azure synapse. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>Otázka: je synapse odkazem na Cosmos DB GA?

Odpověď: synapse odkaz na Apache Spark je GA. Odkaz synapse pro fond SQL bez serveru je v Public Preview.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>Otázka: poskytuje Azure synapse Workspace podporu CI/CD? 

Odpověď: Ano! Všechny artefakty kanálů, poznámkové bloky, skripty SQL a definice úloh Sparku se budou nacházet v Gitu. Všechny definice fondů budou uloženy v Gitu jako šablony ARM. Vyhrazené objekty fondu SQL (schémata, tabulky, zobrazení atd.) se budou spravovat pomocí databázových projektů s podporou CI/CD.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>Otázka: Návody vědět, jaké přihlašovací údaje se používají ke spuštění kanálu? 

Odpověď: Každá aktivita v kanálu synapse se spustí s použitím přihlašovacích údajů zadaných v propojené službě.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>Otázka: jsou SSIS finanční úřad podporovaný v synapse Integration?

Odpověď: ne v tomto okamžiku. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>Otázka: Návody migrovat existující kanály z Azure Data Factory do pracovního prostoru Azure synapse?

Odpověď: v tuto chvíli musíte ručně znovu vytvořit kanály Azure Data Factory a související artefakty tím, že vyexportujete JSON z původního kanálu a importujete ho do svého pracovního prostoru synapse.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>Otázka: Jaký je rozdíl mezi Apache Spark pro synapse a Apache Spark?

Odpověď: Apache Spark pro synapse je Apache Spark s přidanou podporou pro integraci s jinými službami (AAD, AzureML atd.) a dalšími knihovnami (mssparktuils, Hummingbird) a předem vyladěnými konfiguracemi výkonu.

Jakékoli zatížení, které aktuálně běží na Apache Spark, se spustí v Apache Spark pro Azure synapse beze změny. 

### <a name="q-what-versions-of-spark-are-available"></a>Otázka: Jaké verze Sparku jsou k dispozici?

Odpověď: Azure synapse Apache Spark plně podporuje Spark 2,4. Úplný seznam základních komponent a aktuálně podporované verze najdete v tématu [podpora Apache Spark verzí](./spark/apache-spark-version-support.md).

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>Otázka: existuje ekvivalent DButils v Azure synapse Spark?

Odpověď: Ano, Azure synapse Apache Spark poskytuje knihovnu **mssparkutils** . Úplnou dokumentaci k nástroji najdete v tématu [Úvod do nástrojů Microsoft Spark](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>Otázka: Návody nastavení parametrů relace v Apache Spark?

Odpověď: Chcete-li nastavit parametry relace, použijte%% Configure Magic available. Aby se parametry projevily, je nutné restartovat relaci. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>Otázka: Návody nastavení parametrů na úrovni clusteru ve fondu Spark bez serveru?

Odpověď: Chcete-li nastavit parametry na úrovni clusteru, můžete zadat soubor Spark. conf pro fond Spark. Tento fond pak bude akceptovat parametry v konfiguračním souboru za minulosti. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>Otázka: mohu v Azure synapse Analytics spustit cluster Spark s více uživateli?
 
Odpověď: Azure synapse poskytuje účelově sestavené moduly pro konkrétní případy použití. Apache Spark pro synapse je navržená jako služba úloh, nikoli model clusteru. Existují dva scénáře, kdy se uživatelé dotazují na model clusteru s více uživateli.

**Scénář #1: mnoho uživatelů, kteří přistupují k clusteru pro poskytování dat pro účely BI.**

Nejjednodušší způsob, jak tento úkol provést, je vyšetřit data pomocí Sparku a pak využít možnosti synapse SQL k tomu, aby se mohly k těmto datovým sadám připojit Power BI.

**Scénář #2: máte více vývojářů v jednom clusteru, abyste ušetřili peníze.**
 
Pro splnění tohoto scénáře byste měli všem vývojářům poskytnout fond Spark bez serveru, který je nastavený tak, aby používal malý počet prostředků Sparku. Vzhledem k tomu, že fondy Spark bez serveru neúčtují žádné náklady, dokud je nebudete aktivně používat, minimalizují se náklady, pokud existuje více vývojářů Fondy sdílejí metadata (tabulky Spark), aby s nimi mohli snadno pracovat.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>Otázka: Návody zahrnout, spravovat a instalovat knihovny?

Odpověď: můžete nainstalovat externí balíčky pomocí requirements.txt souboru při vytváření fondu Spark, z pracovního prostoru synapse nebo z Azure Portal. Viz [Správa knihoven pro Apache Spark ve službě Azure synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Vyhrazené fondy SQL

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>Otázka: Jaké jsou funkční rozdíly mezi vyhrazenými fondy SQL a fondy bez serveru?

Odpověď: můžete najít úplný seznam rozdílů v [rozdílech funkcí T-SQL v synapse SQL](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>Otázka: teď, když je Azure synapse v GA, jak mohu přesunout své vyhrazené fondy SQL, které byly dřív samostatné do Azure synapse? 

Odpověď: není k dispozici žádný "Přesun" nebo "migrace". Ve stávajících fondech se můžete rozhodnout, že povolíte nové funkce pracovního prostoru. Pokud to uděláte, neexistují žádné zásadní změny, ale budete moct používat nové funkce, jako jsou synapse Studio, Spark a SQL Server bez serveru.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>Otázka: Jaké je nyní výchozí nasazení vyhrazených fondů SQL? 

Odpověď: ve výchozím nastavení budou všechny nové vyhrazené fondy SQL nasazeny do pracovního prostoru. Pokud ale potřebujete, můžete i nadále vytvořit vyhrazený fond SQL (dřív SQL DW) v samostatném faktoru formuláře. 

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)
