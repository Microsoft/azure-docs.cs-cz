---
title: Úvod do integrovanou provozní analýzy ve službě Azure Cosmos DB s Apache Sparkem
description: Zjistěte, jak využít integrovanou podporu pro Apache Spark ve službě Azure Cosmos DB pro spouštění provozní analýzy a AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/06/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 504f463f55edf1a22f99e1ba02449121e8bf5efc
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472261"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Integrované operační analýza ve službě Azure Cosmos DB s Apache Sparkem (preview) 

Integrovaná podpora pro Apache Spark ve službě Azure Cosmos DB umožňuje spouštět analýzy z Apache Spark pro vaše data uložená v účtu služby Azure Cosmos. Poskytuje nativní podporu pro úlohy Apache Spark ke spouštění přímo na váš globálně distribuované databáze Cosmos. Tyto možnosti vývojáře, datovými architekty a odborníky na zpracování dat pomocí služby Azure Cosmos DB jako flexibilní, škálovatelná a výkonné datové platformy spustit obě **OLTP a OLAP/HTAP** úlohy. 

> [!NOTE]
> Integrovaná podpora pro Apache Spark ve službě Azure Cosmos DB je aktuálně ve verzi limited preview. Pokud chcete zaregistrovat verzi preview, přejděte na [zaregistrovat verzi preview](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB) stránky. 

Podpora Apache Spark ve službě Azure Cosmos DB nabízí následující výhody:

* Nejrychlejší doba na přehled můžete získat pro geograficky distribuovanou uživatele a data.

* Můžete zjednodušit architektury řešení a nižší [celkové náklady na vlastnictví](total-cost-ownership.md) (TCO). Systém bude mít nejmenší počet komponent zpracování dat a přitom vylučuje všechny nepotřebná data přesunu mezi nimi.

* Vytvoří [zabezpečení](secure-access-to-data.md), [dodržování předpisů](compliance.md)a auditování hranic, která zahrnuje všechna data v rámci správy.

* Poskytuje "always on" nebo [s vysokou dostupností](high-availability.md) koncového uživatele analytics, která využívají přísné smlouvy o úrovni služeb.

![Podpora Apache Spark ve službě Azure Cosmos DB vizualizaci](./media/spark-api-introduction/spark-api-visualization.png)
 
Pomocí podpory Apache Spark ve službě Azure Cosmos DB, můžete vytvářet a nasazovat řešení, jako je například AI a hloubkového učení modely prediktivní analýzy, doporučení, IoT, zákazník 360, zjišťování možných podvodů, analýzy dat o navštívených stránkách a mínění v textu. Tato řešení pracovat přímo s daty služby Azure Cosmos DB.

Můžete nastavit dávkové a streamování úloha ETL ve službě Azure Cosmos DB, aniž byste museli opustit databázová služba nebo přidat další výpočetní služby. Když budete chtít provádět úlohy ETL a škálovat zase snížit, až úlohu můžete Elasticky škálovat výpočetní prostředí.

Podpora Apache Spark ve službě Azure Cosmos DB nabízí vestavěnou podporu Machine Learning v modulech runtime Apache Spark. Modulů runtime zahrnují knihovna Spark MLLib Microsoft Machine Learning pro Spark, Azure Machine Learning a Cognitive Services. S těmito funkcemi můžou odborníci přes data, datovými architekty a analytiky dat. sestavení a zprovoznit modely strojového učení přímo ve službě Azure Cosmos DB, za zlomek času a s nízkými náklady.


## <a name="key-benefits"></a>Klíčové výhody

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globálně distribuovaná, nízká latence provozní analýzy a AI

S Apache Sparkem v globálně distribuované databáze Azure Cosmos teď můžete získat rychlý čas vytváření přehledů všechny po celém světě. Azure Cosmos DB umožňuje **provozní analýzy globálně distribuovaná, nízká latence** na elastické škálování s tři klíčové techniky:

* Vzhledem k tomu, že databáze Azure Cosmos je globálně distribuovaná, všechna data ingestují místně ve kterém jsou umístěny generátory dat (třeba uživatelé). Dotazy se obsluhují proti místní repliky nejblíže producenti a spotřebitelé dat bez ohledu na to, kde jsou umístěny na světě. 

* Analytické dotazy jsou provedeny přímo na indexovaná data uložená v datové oddíly bez nutnosti jakékoli přesun nepotřebná data. 

* Spark je umístěna společně s Azure Cosmos DB, méně zprostředkující překlady a přesuny dat trvat místa, což vede k lepší výkon a škálovatelnost.

### <a name="unified-serverless-experience-for-apache-spark"></a>Jednotné prostředí bez serveru pro Apache Spark

Jako vícemodelová databáze Azure Cosmos DB nyní rozšiřuje podporu pro rozhraní API OSS tím, že poskytuje **jednotné prostředí bez serveru pro Apache Spark** s klíč hodnota, dokumentů, grafů, sloupec řady datových modelů. Různé datové modely jsou podporovány při použití MongoDB, Cassandra, Gremlin, Etcd a rozhraní SQL API – všechny práce na stejná podkladová data. 

Díky podpoře Apache Spark ve službě Azure Cosmos DB můžete nativně podporují aplikace napsané v Scala, Python, Java a používat několik úzce integrovaných knihovny pro SQL. Zahrnout tyto knihovny ([Spark SQL](https://spark.apache.org/sql/)), služby machine learning (Spark [MLlib](https://spark.apache.org/mllib/)), zpracování datových proudů ([strukturovaného streamování Sparku](https://spark.apache.org/streaming/)) a zpracování grafů (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Tyto nástroje usnadňují využijte Apache Spark pro různé případy použití. Není potřeba zabývat se správou Spark nebo clustery Spark. Můžete známá rozhraní API Apache Spark a **poznámkové bloky Jupyter** pro analýzy a rozhraní SQL API nebo některou z rozhraní API NoSQL OSS, jako jsou Cassandra pro zpracování transakcí na stejná podkladová data ve stejnou dobu.

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




