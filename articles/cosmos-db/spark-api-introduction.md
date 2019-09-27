---
title: Úvod do integrovaných provozních analýz v Azure Cosmos DB s využitím Apache Spark
description: Přečtěte si, jak můžete používat integrovanou podporu Apache Spark v Azure Cosmos DB ke spouštění Operational Analytics a AI.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 952dd084bccd67a0a8833002d73d3aaf1d5dfb25
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338613"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Integrovaná provozní analýza v Azure Cosmos DB s využitím Apache Spark

Pomocí Azure Cosmos DB můžete v transakčních datech spustit globálně distribuované a nízké latence analýzy a AI. Díky nativní podpoře pro poznámkové bloky Apache Spark a Jupyter Azure Cosmos DB pomáhá zkrátit čas na přehledy. Vzhledem k tomu, že se data ingestují, doplní a Analytics se spouští na místní replice databáze v oblasti Azure. Můžete přímo spouštět Apache Spark dotazy na indexované data s více modely uložená v datových oddílech.

Integrovaná podpora Apache Spark v Azure Cosmos DB umožňuje spouštět analýzy z Apache Spark na vašich datech uložených v účtu Azure Cosmos. Poskytuje nativní podporu pro Apache Spark úlohy, které se spouštějí přímo v globálně distribuovaných databázích Cosmos. Díky těmto funkcím můžou vývojáři, pracovníci datových techniků a odborníci na data používat Azure Cosmos DB jako flexibilní, škálovatelnou a výkonnou datovou platformu pro spouštění úloh **OLTP a OLAP/HTAP** .

Podpora Apache Spark v Azure Cosmos DB nabízí následující výhody:

* Můžete získat nejrychlejší čas pro přehled o geograficky distribuovaných uživatelích a datech.

* Můžete zjednodušit architekturu vašeho řešení a snížit [celkové náklady na vlastnictví](total-cost-ownership.md) . Systém bude mít nejmenší počet komponent pro zpracování dat a vyhnout se tak jakémukoli nepotřebnému přesunu dat mezi nimi.

* Vytvoří hranici [zabezpečení](secure-access-to-data.md), [dodržování předpisů](compliance.md)a auditování, která zahrnuje všechna data, která jsou spravována.

* Poskytuje "Always On" nebo [vysoce dostupné](high-availability.md) analýzy koncových uživatelů, které jsou zajištěné přísným SLA.

![Podpora Apache Spark v Azure Cosmos DB vizualizaci](./media/spark-api-introduction/spark-api-visualization.png)
 
Díky podpoře Apache Spark v Azure Cosmos DB můžete vytvářet a nasazovat řešení, jako jsou AI a modely hloubkového učení, prediktivní analýzy, doporučení, IoT, zákazník 360, rozpoznávání podvodů, text mínění, navštívených analýza. Tato řešení fungují přímo na vašich Azure Cosmos DBch datech.

V Azure Cosmos DB můžete nastavit úlohu ETL Batch a streamování, aniž byste museli jít mimo databázovou službu nebo přidat další výpočetní služby. Výpočetní prostředí můžete elasticky škálovat, pokud potřebujete provést úlohu ETL a po dokončení úlohy ji škálovat zpátky.

Podpora Apache Spark v Azure Cosmos DB nabízí integrovanou Machine Learning podporu v modulech runtime Apache Spark. Modul runtime zahrnuje Spark MLLib, Microsoft Machine Learning pro Spark, Azure Machine Learning a Cognitive Services. S těmito funkcemi můžou odborníci na data, datové technici a analytiky dat vytvářet a zprovoznění modely strojového učení přímo v rámci Azure Cosmos DB, a to za zlomek času a s nízkými náklady.


## <a name="key-benefits"></a>Klíčové výhody

### <a name="low-latency-operational-analytics-and-ai"></a>Provozní analýzy s nízkou latencí a AI

Díky Apache Spark v globálně distribuované databázi Azure Cosmos teď můžete rychle získat přehled o celém světě. Azure Cosmos DB umožňuje **globálně distribuované provozní analýzy s nízkou latencí** v elastickém škálování se třemi klíčovými technikami:

* Vzhledem k tomu, že je vaše databáze Azure Cosmos globálně distribuovaná, všechna data se ingestují místně, kde se nacházejí producenti dat (například uživatelé). Dotazy jsou obsluhovány s místními replikami, které jsou nejbližší výrobcům i uživatelům dat bez ohledu na to, kde se nacházejí na světě.

* Všechny vaše analytické dotazy jsou spouštěny přímo na indexovaných datech uložených v datových oddílech bez nutnosti přesunu jakéhokoli nepotřebného přesunu dat.

* Vzhledem k tomu, že Spark se nachází společně s Azure Cosmos DB, je k dispozici méně průběžných překladů a pohybů dat. Výsledkem je lepší výkon a škálovatelnost.

* Všechny Azure Cosmos DB základní funkce, jako je například vícenásobné, automatické převzetí služeb při selhání, Zóny dostupnosti atd. jsou k dispozici pro integrované Apache Spark v Azure Cosmos DB.

### <a name="unified-serverless-experience-for-apache-spark"></a>Jednotné prostředí bez serveru pro Apache Spark

Jako databáze s více modely Azure Cosmos DB teď rozšiřuje podporu rozhraní API OSS tím, že poskytuje **sjednocené prostředí bez serveru pro Apache Spark** s datovými modely řady klíč-hodnota, dokument, graf a řada sloupců. Různé datové modely se podporují pomocí rozhraní API MongoDB, Cassandra, Gremlin, Etcd a SQL – všechny fungují na stejných podkladových datech. 

Díky podpoře Apache Spark v Azure Cosmos DB můžete nativně podporovat aplikace napsané v Scala, Pythonu, Java a používat několik úzce integrovaných knihoven pro SQL. Mezi tyto knihovny patří ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark [MLlib](https://spark.apache.org/mllib/)), zpracování streamu ([strukturované streamování Spark](https://spark.apache.org/streaming/)) a zpracování grafů (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Tyto nástroje usnadňují používání Apache Spark v různých případech použití. Nemusíte se zabývat správou clusterů Spark a Spark. Můžete použít známá Apache Spark rozhraní API a **Jupyter poznámkové bloky** pro analýzy a SQL API nebo jakákoli z rozhraní API OSS NoSQL, jako je Cassandra, pro zpracování transakčních dat na stejných podkladových datech současně.

### <a name="no-schema-or-index-management"></a>Žádná Správa schématu ani indexu

Na rozdíl od tradičních analytických databází se Azure Cosmos DB, datovými inženýry a odborníky na data už nemusíte zabývat náročnými správou schémat a indexů. Databázový stroj v Azure Cosmos DB nevyžaduje žádné explicitní schéma ani správu indexů a je schopný automaticky indexovat všechna data, která ingestuje, aby se rychle obsluhují dotazy Apache Spark.

### <a name="consistency-choices"></a>Volby konzistence

Vzhledem k tomu, že se úlohy Apache Spark spouštějí v datových oddílech databáze Azure Cosmos, dotazy získají [pět jasně definovaných voleb konzistence](consistency-levels.md). Tyto modely konzistence poskytují flexibilitu pro výběr striktní konzistence k zajištění nejpřesnější výsledků pro algoritmy strojového učení, aniž by došlo k ohrožení latence a vysoké dostupnosti.

### <a name="comprehensive-slas"></a>Komplexní smlouvy SLA

Apache Spark úlohy budou mít Azure Cosmos DB výhody, jako je například špičkové komplexní [SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) bez jakýchkoli režijních nákladů na správu samostatných Apache Spark clusterů. Tyto SLA zahrnují propustnost, latenci na 99 percentil, konzistenci a vysokou dostupnost. 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>Smíšené provozní a analytické úlohy s kompletní izolací

Integrace Apache Spark do Azure Cosmos DB přemostění transakčního a analytického oddělení, které se staly jedním z hlavních bodových zákazníků při sestavování nativních cloudových aplikací v globálním měřítku. Úlohy OLTP a OLAP se spouštějí souběžně a vzájemně se neovlivňují.

### <a name="low-latency-analytical-and-transactional-storage"></a>Analytické a transakční úložiště s nízkou latencí

Azure Cosmos DB nativně ukládá data ve dvou různých vrstvách úložiště: transakční (orientované na řádky) a analytické úložiště (ve formátu souboru Apache Parquet jsou orientované na sloupce). Replikuje data v každé úrovni globálně a umožňuje uživatelům nezávisle spravovat data v těchto úrovních na základě zásad uchovávání informací.

Tato architektura úložiště umožňuje provádět kritické transakční i analytické úlohy na stejných globálně distribuovaných datech. U Cosmos DB nepotřebujete žádné operace ETL ani nemusíte předávat žádné zbytečné přesuny dat. Můžete jednoduše spustit transakční i analytické úlohy se stejnými podkladovými daty. Transakční úlohy můžou používat známá transakční rozhraní API pro přístup, včetně SQL, Cassandra, MongoDB, Gremlin a Etcd. Vzhledem k tomu, že úlohy analýzy a AI můžou využívat integrované Apache Spark SQL, ML architektury a celé Apache Spark sada nástrojů.

### <a name="snapshots-and-historical-analytical-queries"></a>Snímky a historické analytické dotazy

Můžete vytvořit snímky na vyžádání nebo na základě plánu v rámci sloupcových komprimovaných dat uložených v úrovni analýzy a provádět tak analytické dotazy přímo proti určitému snímku. Tato funkce umožňuje Flashback nebo dotazování analytických dotazů, vrácení zpět, úplné historické stopy auditu a reprodukovat strojové učení a experimenty AI.

## <a name="next-steps"></a>Další kroky

* Další informace o výhodách Azure Cosmos DB najdete v článku [Přehled](introduction.md) .
* [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](mongodb-introduction.md)
* [Začínáme s Azure Cosmos DB rozhraní API Cassandra](cassandra-introduction.md)
* [Začínáme s rozhraním API Azure Cosmos DB Gremlin](graph-introduction.md)
* [Začínáme s Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md)