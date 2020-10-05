---
title: Cognitive Services pro velký objem dat
description: Naučte se využívat Azure Cognitive Services u velkých datových sad pomocí Pythonu, Java a Scala. Díky Cognitive Services pro velké objemy dat můžete průběžně zlepšovat inteligentní modely přímo do &trade; výpočtů Apache Spark a SQL.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: 0001002636362a74915f9dae21a6beff52baea63
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "86201777"
---
# <a name="azure-cognitive-services-for-big-data"></a>Cognitive Services Azure pro velké objemy dat

![Cognitive Services Azure pro velké objemy dat](media/cognitive-services-big-data-overview.svg)

Azure Cognitive Services pro velké objemy dat umožňuje uživatelům kanálovat terabajty dat prostřednictvím Cognitive Services [pomocí &trade; Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-spark). Díky Cognitive Services pro velké objemy dat je snadné vytvářet rozsáhlé inteligentní aplikace s libovolným úložištěm dat.

Díky Cognitive Services pro velké objemy dat můžete průběžně zlepšovat inteligentní modely přímo do &trade; výpočtů Apache Spark a SQL. Tyto nástroje uvolňují vývojáře z podrobností o sítích nízké úrovně, aby se mohli soustředit na vytváření inteligentních distribuovaných aplikací.

## <a name="features-and-benefits"></a>Funkce a výhody

Cognitive Services pro velké objemy dat může využívat služby z libovolné oblasti světa a také [Cognitive Services kontejnerů](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support). Kontejnery podporují nedostatečné nebo žádné nasazení připojení s odezvou extrémně nízké latence. Kontejnerové Cognitive Services lze spustit místně, přímo na pracovních uzlech clusteru Spark nebo na externím Orchestrator, jako je Kubernetes.

## <a name="supported-services"></a>Podporované služby

[Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), ke kterým se dostanete prostřednictvím rozhraní API a sad SDK, vám vývojářům umožní sestavovat inteligentní aplikace, aniž by měli zkušenosti s AI nebo Pomocí Cognitive Services můžete své aplikace zobrazit, slyšet, mluvit, pochopit a důvod. Chcete-li použít Cognitive Services, musí vaše aplikace odesílat data službě přes síť. Po přijetí služba pošle inteligentní odpověď v rámci návratu. Pro úlohy s velkými objemy dat jsou k dispozici následující služby:

### <a name="vision"></a>Obraz

|Název služby|Popis služby|
|:-----------|:------------------|
|[Počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Počítačové zpracování obrazu")| Služba Počítačové zpracování obrazu poskytuje přístup k pokročilým algoritmům pro zpracování imagí a vracení informací. |
|[Rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/ "Rozpoznávání tváře")| Služba obličeje poskytuje přístup k pokročilým algoritmům obličeje a umožňuje detekci a rozpoznávání atributů obličeje. |

### <a name="speech"></a>Řeč

|Název služby|Popis služby|
|:-----------|:------------------|
|[Služba řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Služba Speech")|Služba rozpoznávání řeči poskytuje přístup k funkcím, jako je rozpoznávání řeči, syntéza řeči, překlad řeči a ověřování a identifikace mluvčího.|

### <a name="decision"></a>Rozhodnutí

|Název služby|Popis služby|
|:-----------|:------------------|
|[Detektor anomálií](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Detektor anomálií") | Služba detektoru anomálií (Preview) umožňuje monitorovat a zjišťovat anomálie v datech časových řad.|

### <a name="language"></a>Jazyk

|Název služby|Popis služby|
|:-----------|:------------------|
|[Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Analýza textu")| Služba Analýza textu zajišťuje zpracování v přirozeném jazyce přes nezpracovaný text pro analýzu míněníí, extrakci klíčových frází a rozpoznávání jazyka.|

### <a name="search"></a>Search

|Název služby|Popis služby|
|:-----------|:------------------|
|[Vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Vyhledávání obrázků Bingu")|Služba Vyhledávání obrázků Bingu vrací zobrazení obrázků, které byly pro dotaz uživatele důležité.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Podporované programovací jazyky pro Cognitive Services pro velké objemy dat

Cognitive Services pro velké objemy dat jsou postaveny na Apache Spark. Apache Spark je knihovna distribuovaných výpočetních prostředí, která podporuje jazyky Java, Scala, Python, R a spoustu dalších jazyků. Tyto jazyky jsou aktuálně podporovány.

### <a name="python"></a>Python

Poskytujeme rozhraní PySpark API v `mmlspark.cognitive` oboru názvů [Microsoft ML pro Apache Spark](https://aka.ms/spark). Další informace najdete v tématu [rozhraní API pro vývojáře v Pythonu](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Příklady použití najdete v [ukázkách Pythonu](samples-python.md).

### <a name="scala-and-java"></a>Scala a Java

Poskytujeme rozhraní Spark API pro Scala a Java v `com.microsoft.ml.spark.cognitive` oboru názvů [Microsoft ML pro Apache Spark](https://aka.ms/spark). Další informace najdete v tématu [rozhraní API pro vývojáře v Scala](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). Příklady použití najdete v [ukázkách Scala](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Podporované platformy a konektory

Cognitive Services pro velké objemy dat vyžaduje Apache Spark. Existuje několik Apache Spark platforem, které podporují Cognitive Services pro velké objemy dat.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) je analytická platforma založená na Apache Spark optimalizovaná pro platformu Microsoft Azure cloudové služby. Nabízí nastavení jedním kliknutím, zjednodušené pracovní toky a interaktivní pracovní prostor, který podporuje spolupráci mezi odborníky přes data, datovými inženýry a obchodními analytiky.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure synapse Analytics](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/synapse-analytics) (dříve SQL Data Warehouse) je jako podnikový datový sklad, který využívá obrovský paralelní zpracování. Pomocí analýzy synapse můžete rychle spouštět složité dotazy napříč petabajty daty. Azure synapse Analytics poskytuje spravované fondy Spark pro spouštění úloh Sparku s intuitivním Jupyter Notebookovým rozhraním.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Služba Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/) orchestruje kontejnery Docker a distribuované aplikace v obrovské škále. AKS je spravovaná nabídka Kubernetes, která zjednodušuje používání Kubernetes v Azure. Kubernetes může umožnit jemně odstupňované řízení škály, latence a sítě pro rozpoznávání. Pokud však Apache Spark neznáte, doporučujeme použít Azure Databricks nebo synapse Analytics.

### <a name="data-connectors"></a>Datové konektory

Po vytvoření clusteru Spark se další krok připojí k vašim datům. Apache Spark má širokou kolekci databázových konektorů. Tyto konektory umožňují aplikacím pracovat s velkými datovými sadami bez ohledu na to, kde jsou uložené. Další informace o podporovaných databázích a konektorech najdete v [seznamu podporovaných zdrojů dat pro Azure Databricks](https://docs.microsoft.com/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Koncepty

### <a name="spark"></a>Spark

[Apache Spark &trade; ](http://spark.apache.org/) je jednotný analytický modul pro zpracování velkých objemů dat. Rozhraní pro paralelní zpracování zvyšuje výkon velkých objemů dat a analytických aplikací. Spark může fungovat jako systém zpracování dávky i streamování, aniž by se musel měnit základní kód aplikace.

Základem Spark je datový rámec: tabulková kolekce dat distribuovaných v rámci Apache Spark pracovních uzlů. Spark dataframe je jako tabulka v relační databázi nebo v datovém snímku v R/Pythonu, ale s neomezeným škálováním. Datové rámce lze vytvořit z mnoha zdrojů, například: strukturovaných datových souborů, tabulek v podregistru nebo externích databází. Jakmile jsou data v datovém rámci Sparku, můžete:

   - Proveďte výpočty ve stylu SQL, jako je například spojování a filtr tabulek.
   - Použití funkcí u velkých datových sad pomocí MapReduce stylu paralelismus.
   - Použijte distribuované Machine Learning pomocí Microsoft Machine Learning pro Apache Spark.
   - Použijte Cognitive Services pro velké objemy dat, abyste mohli rozšířit vaše data pomocí inteligentních služeb připravených k použití.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning pro Apache Spark (MMLSpark)

[Microsoft Machine Learning for Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) je open source knihovna pro distribuované strojové učení (ml) postavená na Apache Spark. Tento balíček obsahuje Cognitive Services pro velké objemy dat. Kromě toho MMLSpark obsahuje několik dalších nástrojů ML pro Apache Spark, jako je LightGBM, pro dostupné, OpenCV, VÁPNo a další. Pomocí MMLSpark můžete vytvářet výkonné prediktivní a analytické modely z libovolného zdroje dat Spark.

### <a name="http-on-spark"></a>HTTP ve Sparku

Cognitive Services pro velké objemy dat je příkladem toho, jak můžeme integrovat inteligentní webové služby s velkým objemem dat. Webové služby jsou výkonné mnoha aplikacemi po celém světě a většina služeb komunikuje prostřednictvím protokolu HTTP (Hypertext Transfer Protocol). Abychom mohli pracovat s *libovolnými* webovými službami ve velkých měřítkech, poskytujeme protokol HTTP v Sparku. Pomocí protokolu HTTP v Sparku můžete předat terabajty dat prostřednictvím libovolné webové služby. V rámci digestoře používáme tuto technologii k tomu, aby se Cognitive Services pro velké objemy dat.

## <a name="developer-samples"></a>Ukázky pro vývojáře

- [Recept: prediktivní údržba](recipes/anomaly-detection.md)
- [Recept: průzkum inteligentního umění](recipes/art-explorer.md)

## <a name="blog-posts"></a>Příspěvky na blozích

- [Další informace o tom, jak Cognitive Services pracovat na Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Úspora sněhových Pohádků s hloubkovým učením a Počítačové zpracování obrazu ve Sparku](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research podcast: MMLSpark, který umožňuje AI dobrým způsobem s označením Hovorka](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)

## <a name="webinars-and-videos"></a>Webináře a videa

- [Azure Cognitive Services ve Sparku: clustery s integrovanými inteligentními službami](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark summit vystoupení: škálovatelný AI pro dobré](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cognitive Services pro velké objemy dat v Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)

## <a name="next-steps"></a>Další kroky

- [Začínáme s Cognitive Services pro velké objemy dat](getting-started.md)
- [Jednoduché příklady Pythonu](samples-python.md)
- [Jednoduché příklady Scala](samples-scala.md)
