---
title: Úvod do Sparku rozhraní API služby Azure Cosmos DB
description: Zjistěte, jak můžete pomocí rozhraní API Azure Cosmos DB Spark spouštět provozní analýzy a AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/06/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: de920f40f2968942b7ac66414170b43bd9317cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65081091"
---
# <a name="introduction-to-the-azure-cosmos-db-spark-api-preview"></a>Úvod do služby Azure Cosmos DB Spark API (preview) 

Rozhraní API pro Spark ve službě Azure Cosmos DB umožňuje spouštět analýzy z Apache Spark pro vaše data uložená v účtu služby Azure Cosmos.

Rozhraní API pro Spark ve službě Azure Cosmos DB poskytuje nativní podporu pro úlohy Apache Spark se promítnou u vašeho globálně distribuované databáze Cosmos přímo. Tyto možnosti vývojáře, datovými architekty a odborníky na zpracování dat pomocí služby Azure Cosmos DB jako flexibilní, škálovatelná a výkonné datové platformy spustit obě **OLTP a OLAP/HTAP** úlohy. 

> [!NOTE]
> Spark API služby Azure Cosmos DB je aktuálně ve verzi limited preview. Pokud chcete zaregistrovat verzi preview, přejděte na [zaregistrovat verzi preview](https://aka.ms/cosmos-spark-preview) stránky. 

Rozhraní API pro Spark ve službě Azure Cosmos DB nabízí následující výhody:

* Nejrychlejší doba na přehled můžete získat pro geograficky distribuovanou uživatele a data.

* Můžete zjednodušit architektury řešení a nižší [celkové náklady na vlastnictví](total-cost-ownership.md) (TCO). Systém bude mít nejmenší počet komponent zpracování dat a přitom vylučuje všechny nepotřebná data přesunu mezi nimi.

* Vytvoří [zabezpečení](secure-access-to-data.md), [dodržování předpisů](compliance.md)a auditování hranic, která zahrnuje všechna data v rámci správy.

* Poskytuje "always on" nebo [s vysokou dostupností](high-availability.md) koncového uživatele analytics, která využívají přísné smlouvy o úrovni služeb.

![Azure Cosmos DB Spark API vizualizace](./media/spark-api-introduction/spark-api-visualization.png)
 
Použití Sparku API služby Azure Cosmos DB, můžete vytvářet a nasazovat řešení například AI a hloubkového učení modely prediktivní analýzy, doporučení, IoT, zákazník 360, zjišťování možných podvodů, analýzy dat o navštívených stránkách a mínění v textu. Tyto pracovat přímo s daty služby Azure Cosmos DB.

Můžete nastavit dávkové a streamování úloha ETL ve službě Azure Cosmos DB, aniž byste museli opustit databázová služba nebo přidat další výpočetní služby. Když budete chtít provádět úlohy ETL a škálovat zase snížit, až úlohu můžete Elasticky škálovat výpočetní prostředí.

Rozhraní API pro Spark ve službě Azure Cosmos DB podporuje integrovanou podporu Machine Learning v modulech runtime Apache Spark. Modulů runtime zahrnují knihovna Spark MLLib Microsoft Machine Learning pro Spark, Azure Machine Learning a Cognitive Services. S těmito funkcemi můžou odborníci přes data, datovými architekty a analytiky dat. sestavení a zprovoznit modely strojového učení přímo ve službě Azure Cosmos DB, za zlomek času a s nízkými náklady.


## <a name="key-benefits"></a>Klíčové výhody

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globálně distribuovaná, nízká latence provozní analýzy a AI

S Apache Sparkem v globálně distribuované databáze Azure Cosmos teď můžete získat rychlý čas vytváření přehledů všechny po celém světě. Azure Cosmos DB umožňuje **provozní analýzy globálně distribuovaná, nízká latence** na elastické škálování s tři klíčové techniky:

* Vzhledem k tomu, že databáze Azure Cosmos je globálně distribuovaná, všechna data ingestují místně ve kterém jsou umístěny generátory dat (třeba uživatelé). Dotazy se obsluhují proti místní repliky nejblíže producenti a spotřebitelé dat bez ohledu na to, kde jsou umístěny na světě. 

* Analytické dotazy jsou provedeny přímo na indexovaná data uložená v datové oddíly bez nutnosti jakékoli přesun nepotřebná data. 

* Spark je umístěna společně s Azure Cosmos DB, méně zprostředkující překlady a přesuny dat trvat místa, což vede k lepší výkon a škálovatelnost.

### <a name="unified-serverless-experience-for-apache-spark"></a>Jednotné prostředí bez serveru pro Apache Spark

Jako vícemodelová databáze Azure Cosmos DB nyní rozšiřuje podporu pro rozhraní API OSS tím, že poskytuje **jednotné prostředí bez serveru pro Apache Spark** s klíč hodnota, dokumentů, grafů, sloupec řady datových modelů. Různé datové modely jsou podporovány při použití MongoDB, Cassandra, Gremlin, Etcd a rozhraní SQL API – všechny práce na stejná podkladová data. 

Pomocí rozhraní API pro Spark můžete nativně podporují aplikace napsané v Scala, Python, Java a používat několik úzce integrovaných knihovny pro SQL. Zahrnout tyto knihovny ([Spark SQL](https://spark.apache.org/sql/)), služby machine learning (Spark [MLlib](https://spark.apache.org/mllib/)), zpracování datových proudů ([strukturovaného streamování Sparku](https://spark.apache.org/streaming/)) a zpracování grafů (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Tyto nástroje snadněji využívat rozhraní API pro různé případy použití pro Spark. Není potřeba zabývat se správou Spark nebo clustery Spark. Můžete známá rozhraní API Apache Spark a **poznámkové bloky Jupyter** pro analýzy a rozhraní SQL API nebo některou z rozhraní API NoSQL OSS, jako jsou Cassandra pro zpracování transakcí na stejná podkladová data ve stejnou dobu.

### <a name="no-schema-or-index-management"></a>Žádné schéma nebo správu indexů

Na rozdíl od tradičních analytické databáze pomocí služby Azure Cosmos DB datovými architekty a datovými vědci už nemusíte zabývat náročné schématu a Správa indexů. Databázový stroj ve službě Azure Cosmos DB nevyžaduje žádné explicitní schéma nebo správu indexů a dokáže automaticky indexovat všechna data, která se ingestují rychle poskytovat dotazy Apache Spark. 

### <a name="consistency-choices"></a>Volby konzistence

Vzhledem k tomu, že úlohy Apache Spark jsou provedeny v datové oddíly databáze Azure Cosmos, se zobrazí dotazy [pět jasně definovaných voleb konzistence](consistency-levels.md). Tyto modely konzistence poskytují flexibilitu pro zvolte striktní konzistenci poskytnou nejpřesnější výsledky pro algoritmů strojového učení bez negativního vlivu latence a vysoká dostupnost. 

### <a name="slas"></a>Smlouvy SLA

Apache Spark úlohy budou mít výhody služby Azure Cosmos DB, jako je například špičkové komplexní [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) bez jakékoli režie na správu samostatné clustery Apache Spark... Tyto smlouvy o úrovni služeb zahrnují propustnosti, latenci na úrovni 99. percentilu, konzistence a vysokou dostupnost. 

### <a name="mixed-workloads"></a>Smíšené úlohy

Integrace Apache Sparku do služby Azure Cosmos DB mosty transakční a analytické oddělení, který byl jeden z hlavních zákazníků problémové body při sestavování nativních cloudových aplikací v globálním měřítku. 

## <a name="next-steps"></a>Další postup

* Další informace o výhodách služby Azure Cosmos DB najdete v tématu [přehled](introduction.md) článku.
* [Začínáme s Azure Cosmos DB přes rozhraní API pro MongoDB](mongodb-introduction.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](cassandra-introduction.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](graph-introduction.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](table-introduction.md)




