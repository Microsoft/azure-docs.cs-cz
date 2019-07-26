---
title: Úvod do integrovaných provozních analýz v Azure Cosmos DB s využitím Apache Spark
description: Přečtěte si, jak můžete používat integrovanou podporu Apache Spark v Azure Cosmos DB ke spouštění Operational Analytics a AI.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/23/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 245df0632765c4000bdf5da3e428187d2b068866
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402082"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Integrovaná provozní analýza v Azure Cosmos DB s využitím Apache Spark (Preview) 

Integrovaná podpora Apache Spark v Azure Cosmos DB umožňuje spouštět analýzy z Apache Spark na vašich datech uložených v účtu Azure Cosmos. Poskytuje nativní podporu pro Apache Spark úlohy, které se spouštějí přímo v globálně distribuovaných databázích Cosmos. Díky těmto funkcím můžou vývojáři, pracovníci datových techniků a odborníci na data používat Azure Cosmos DB jako flexibilní, škálovatelnou a výkonnou datovou platformu pro spouštění úloh **OLTP a OLAP/HTAP** . 

Výpočetní prostředí Spark je automaticky dostupné ve všech oblastech Azure přidružených k vašemu účtu Azure Cosmos. Úlohy Sparku používají možnost více hlavních možností Azure Cosmos DB a můžou zapisovat nebo dotazovat se na místní repliky v jednotlivých oblastech. 

> [!NOTE]
> Integrovaná podpora pro Apache Spark v Azure Cosmos DB je aktuálně ve verzi omezené verze Preview. Pokud se chcete přihlásit k verzi Preview, přejděte na [stránku Preview](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB)a zaregistrujte se na ni. 

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

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globálně distribuované, provozní analýzy s nízkou latencí a AI

Díky Apache Spark v globálně distribuované databázi Azure Cosmos teď můžete rychle získat přehled o celém světě. Azure Cosmos DB umožňuje **globálně distribuované provozní analýzy s nízkou latencí** v elastickém škálování se třemi klíčovými technikami:

* Vzhledem k tomu, že je vaše databáze Azure Cosmos globálně distribuovaná, všechna data se ingestují místně, kde se nacházejí producenti dat (například uživatelé). Dotazy jsou obsluhovány s místními replikami, které jsou nejbližší výrobcům i uživatelům dat bez ohledu na to, kde se nacházejí na světě. 

* Všechny vaše analytické dotazy jsou spouštěny přímo na indexovaných datech uložených v datových oddílech bez nutnosti přesunu jakéhokoli nepotřebného přesunu dat. 

* Vzhledem k tomu, že Spark se nachází společně s Azure Cosmos DB, je k dispozici méně průběžných překladů a pohybů dat. Výsledkem je lepší výkon a škálovatelnost.

### <a name="unified-serverless-experience-for-apache-spark"></a>Jednotné prostředí bez serveru pro Apache Spark

Jako databáze s více modely Azure Cosmos DB teď rozšiřuje podporu rozhraní API OSS tím, že poskytuje **sjednocené prostředí bez serveru pro Apache Spark** s datovými modely řady klíč-hodnota, dokument, graf a řada sloupců. Různé datové modely se podporují pomocí rozhraní API MongoDB, Cassandra, Gremlin, Etcd a SQL – všechny fungují na stejných podkladových datech. 

Díky podpoře Apache Spark v Azure Cosmos DB můžete nativně podporovat aplikace napsané v Scala, Pythonu, Java a používat několik úzce integrovaných knihoven pro SQL. Mezi tyto knihovny patří ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark [MLlib](https://spark.apache.org/mllib/)), zpracování streamu ([strukturované streamování Spark](https://spark.apache.org/streaming/)) a zpracování grafů (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Tyto nástroje usnadňují používání Apache Spark v různých případech použití. Nemusíte se zabývat správou clusterů Spark a Spark. Můžete použít známá Apache Spark rozhraní API a **Jupyter poznámkové bloky** pro analýzy a SQL API nebo jakákoli z rozhraní API OSS NoSQL, jako je Cassandra, pro zpracování transakčních dat na stejných podkladových datech současně.

### <a name="no-schema-or-index-management"></a>Žádná Správa schématu ani indexu

Na rozdíl od tradičních analytických databází se Azure Cosmos DB, datovými inženýry a odborníky na data už nemusíte zabývat náročnými správou schémat a indexů. Databázový stroj v Azure Cosmos DB nevyžaduje žádné explicitní schéma ani správu indexů a je schopný automaticky indexovat všechna data, která ingestuje, aby se rychle obsluhují dotazy Apache Spark. 

### <a name="consistency-choices"></a>Volby konzistence

Vzhledem k tomu, že se úlohy Apache Spark spouštějí v datových oddílech databáze Azure Cosmos, dotazy získají [pět jasně definovaných voleb konzistence](consistency-levels.md). Tyto modely konzistence poskytují flexibilitu pro výběr striktní konzistence k zajištění nejpřesnější výsledků pro algoritmy strojového učení, aniž by došlo k ohrožení latence a vysoké dostupnosti. 

### <a name="comprehensive-slas"></a>Komplexní smlouvy SLA

Apache Spark úlohy budou mít Azure Cosmos DB výhody, jako je například špičkové komplexní [SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) bez jakýchkoli režijních nákladů na správu samostatných Apache Spark clusterů. Tyto SLA zahrnují propustnost, latenci na 99 percentil, konzistenci a vysokou dostupnost. 

### <a name="mixed-workloads"></a>Smíšené úlohy

Integrace Apache Spark do Azure Cosmos DB přemostění transakčního a analytického oddělení, které se staly jedním z hlavních bodových zákazníků při sestavování nativních cloudových aplikací v globálním měřítku. 

## <a name="scenarios-for-azure-cosmos-db-spark-support"></a>Scénáře pro podporu Azure Cosmos DB Spark

### <a name="retail-and-consumer-goods"></a>Maloobchodní a spotřební zboží

Podporu Sparku v Azure Cosmos DB můžete použít k poskytování doporučení a nabídek v reálném čase. Zákazníkům můžete podávat informace o položkách, které budou potřebovat při individuálním přizpůsobení a doporučeních produktu v reálném čase.

* Pomocí integrované podpory Machine Learning poskytované modulem runtime Apache Spark můžete vygenerovat doporučení v reálném čase napříč katalogy produktů.

* Můžete kliknout na streamovat data, koupit data a zákaznická data a zajistit tak cílené doporučení, která zajišťují, aby byla hodnota životnosti.

* Pomocí funkce globální distribuce Azure Cosmos DB lze analyzovat velké objemy dat produktu, které jsou rozdělené do různých oblastí, v milisekundách.

* Můžete rychle získat přehled o geograficky distribuovaných uživatelích a datech. Můžete zvýšit míru převodu povýšení tím, že zachováte správnou reklamu správnému uživateli ve správný čas.

* Pomocí integrované funkce streamování Sparku můžete rozšířit živá data tak, že je zkombinujete se statickými Zákaznickými daty. Tímto způsobem můžete doručovat lépe přizpůsobené a cílené reklamy v reálném čase a v kontextu s tím, co zákazníci dělají.

Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá k optimalizaci cen a propagačních akcí:

![Podpora Sparku v Azure Cosmos DB pro optimalizaci cen a propagačních akcí](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá v modulu doporučení v reálném čase:

![Podpora Azure Cosmos DB Spark v modulu doporučení v reálném čase](./media/spark-api-introduction/real-time-recommendation-engine.png)

### <a name="manufacturing-and-iot"></a>Výroba a IoT

Integrovaná analytická platforma Azure Cosmos DB umožňuje povolit analýzu dat IoT v reálném čase z milionů zařízení v globálním měřítku. Můžete vytvářet moderní inovace, jako je předpověď vzorců počasí, prediktivní analýzy a optimalizace energií.

* Pomocí Azure Cosmos DB můžete svoje data, jako jsou metriky prostředků v reálném čase a povětrnostní faktory, použít k optimalizaci výkonu připojených zařízení v poli pomocí analýzy inteligentních mřížek. Analýza inteligentních mřížek je klíč, který řídí provozní náklady, vylepšuje spolehlivost v Gridech a dodává přizpůsobené energetické služby zákazníkům.

Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá ke čtení metrik ze zařízení IoT a k použití analýzy inteligentních mřížek:

![Podpora Sparku Azure Cosmos DB pro čtení metrik ze zařízení IoT](./media/spark-api-introduction/read-metrics-from-iot-devices.png)


### <a name="predictive-maintenance"></a>Prediktivní údržba

* Udržování prostředků, jako jsou například kompresory používané v malých vrtných plošinách, do hlubinných platforem je komplexní Endeavor. Tyto prostředky se nacházejí po celém světě a generují petabajty dat. Pomocí Azure Cosmos DB můžete vytvořit kompletní prediktivní datový kanál, který využívá Spark streamování ke zpracování velkých objemů telemetrie senzorů, částí prostředků a dat mapování senzorů.

* Můžete vytvářet a nasazovat modely strojového učení pro předpověď selhání prostředků předtím, než dojde k selhání, a vystavit pracovní objednávky údržby.

Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá k vytvoření prediktivního systému údržby:

![Podpora Sparku Azure Cosmos DB k vytvoření prediktivního systému údržby](./media/spark-api-introduction/predictive-maintenance-system.png)

Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá k sestavení diagnostického systému pro vozidlo v reálném čase:

![Podpora Sparku Azure Cosmos DB pro sestavení diagnostického systému pro vozidlo v reálném čase](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

### <a name="gaming"></a>Hraní her

* Díky integrované podpoře Spark vám Azure Cosmos DB umožňuje snadno sestavovat, škálovat a nasazovat pokročilé analýzy a modely strojového učení během několika minut, aby se vytvořily nejlepší možné herní prostředí.

* Můžete analyzovat data z přehrávače, nákupu a chování a vytvořit relevantní individuální nabídky pro dosažení vysoké míry převodu.

* Pomocí Spark Machine Learning můžete analyzovat a získávat přehledy o datech herních telemetrie. Můžete diagnostikovat a zabránit pomalým časům načítání a problémům s hrou.

Následující obrázek ukazuje, jak se v herních analýzách používá Azure Cosmos DB podpora Sparku:

![Podpora Sparku v Azure Cosmos DB v herních analýzách](./media/spark-api-introduction/gaming-analytics.png)

## <a name="built-in-jupyter-notebooks-support"></a>Integrovaná podpora poznámkových bloků Jupyter

Azure Cosmos DB podporuje integrované poznámkové bloky Jupyter pro všechna rozhraní API, jako je Cassandra, MongoDB, SQL, Gremlin a Table. Poznámkové bloky Jupyter se spouštějí v rámci účtů Azure Cosmos a zvyšují prostředí pro vývojáře. Integrovaná podpora poznámkových bloků pro všechna Azure Cosmos DB rozhraní API a datové modely umožňují interaktivní spouštění dotazů. Můžete také spouštět modely strojového učení a analyzovat data uložená ve vašich databázích Azure Cosmos. Pomocí prostředí poznámkového bloku Jupyter můžete analyzovat uložená data, sestavovat a naučit modely strojového učení a provádět Inferencing na datech v Azure Portal, jak je znázorněno na následujícím obrázku:

![Podpora notebooků Jupyter v Azure Cosmos DB](./media/spark-api-introduction/jupyter-notebooks-portal.png)

## <a name="next-steps"></a>Další postup

* Další informace o výhodách Azure Cosmos DB najdete v článku [Přehled](introduction.md) .
* [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](mongodb-introduction.md)
* [Začínáme s Azure Cosmos DB rozhraní API Cassandra](cassandra-introduction.md)
* [Začínáme s rozhraním API Azure Cosmos DB Gremlin](graph-introduction.md)
* [Začínáme s Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md)




