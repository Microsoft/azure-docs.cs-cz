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
ms.openlocfilehash: aaade03edbbb109656fb7371a063cdc2512c5a20
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461624"
---
# <a name="azure-cognitive-services-for-big-data"></a>Cognitive Services Azure pro velké objemy dat

![Cognitive Services Azure pro velké objemy dat](media/cognitive-services-big-data-overview.svg)

Azure Cognitive Services pro velké objemy dat umožňuje uživatelům kanálovat terabajty dat prostřednictvím Cognitive Services [pomocí &trade; Apache Spark](/dotnet/spark/what-is-spark). Díky Cognitive Services pro velké objemy dat je snadné vytvářet rozsáhlé inteligentní aplikace s libovolným úložištěm dat.

Díky Cognitive Services pro velké objemy dat můžete průběžně zlepšovat inteligentní modely přímo do &trade; výpočtů Apache Spark a SQL. Tyto nástroje uvolňují vývojáře z podrobností o sítích nízké úrovně, aby se mohli soustředit na vytváření inteligentních distribuovaných aplikací.

## <a name="features-and-benefits"></a>Funkce a výhody

Cognitive Services pro velké objemy dat může využívat služby z libovolné oblasti světa a také [Cognitive Services kontejnerů](../cognitive-services-container-support.md). Kontejnery podporují nedostatečné nebo žádné nasazení připojení s odezvou extrémně nízké latence. Kontejnerové Cognitive Services lze spustit místně, přímo na pracovních uzlech clusteru Spark nebo na externím Orchestrator, jako je Kubernetes.

## <a name="supported-services"></a>Podporované služby

[Cognitive Services](../index.yml), ke kterým se dostanete prostřednictvím rozhraní API a sad SDK, vám vývojářům umožní sestavovat inteligentní aplikace, aniž by měli zkušenosti s AI nebo Pomocí Cognitive Services můžete své aplikace zobrazit, slyšet, mluvit, pochopit a důvod. Chcete-li použít Cognitive Services, musí vaše aplikace odesílat data službě přes síť. Po přijetí služba pošle inteligentní odpověď v rámci návratu. Pro úlohy s velkými objemy dat jsou k dispozici následující služby:

### <a name="vision"></a>Obraz

|Název služby|Popis služby|
|:-----------|:------------------|
|[Počítačové zpracování obrazu](../computer-vision/index.yml "Počítačové zpracování obrazu")| Služba Počítačové zpracování obrazu poskytuje přístup k pokročilým algoritmům pro zpracování imagí a vracení informací. |
|[Rozpoznávání tváře](../face/index.yml "Rozpoznávání tváře")| Služba obličeje poskytuje přístup k pokročilým algoritmům obličeje a umožňuje detekci a rozpoznávání atributů obličeje. |

### <a name="speech"></a>Řeč

|Název služby|Popis služby|
|:-----------|:------------------|
|[Služba Speech](../speech-service/index.yml "Služba Speech")|Služba rozpoznávání řeči poskytuje přístup k funkcím, jako je rozpoznávání řeči, syntéza řeči, překlad řeči a ověřování a identifikace mluvčího.|

### <a name="decision"></a>Rozhodnutí

|Název služby|Popis služby|
|:-----------|:------------------|
|[Detektor anomálií](../anomaly-detector/index.yml "Detektor anomálií") | Služba detektoru anomálií (Preview) umožňuje monitorovat a zjišťovat anomálie v datech časových řad.|

### <a name="language"></a>Jazyk

|Název služby|Popis služby|
|:-----------|:------------------|
|[Analýza textu](../text-analytics/index.yml "Analýza textu")| Služba Analýza textu zajišťuje zpracování v přirozeném jazyce přes nezpracovaný text pro analýzu míněníí, extrakci klíčových frází a rozpoznávání jazyka.|

### <a name="search"></a>Hledat

|Název služby|Popis služby|
|:-----------|:------------------|
|[Vyhledávání obrázků Bingu](/azure/cognitive-services/bing-image-search "Vyhledávání obrázků Bingu")|Služba Vyhledávání obrázků Bingu vrací zobrazení obrázků, které byly pro dotaz uživatele důležité.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Podporované programovací jazyky pro Cognitive Services pro velké objemy dat

Cognitive Services pro velké objemy dat jsou postaveny na Apache Spark. Apache Spark je knihovna distribuovaných výpočetních prostředí, která podporuje jazyky Java, Scala, Python, R a spoustu dalších jazyků. Tyto jazyky jsou aktuálně podporovány.

### <a name="python"></a>Python

Poskytujeme rozhraní PySpark API v `mmlspark.cognitive` oboru názvů [Microsoft ML pro Apache Spark](https://aka.ms/spark). Další informace najdete v tématu [rozhraní API pro vývojáře v Pythonu](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Příklady použití najdete v [ukázkách Pythonu](samples-python.md).

### <a name="scala-and-java"></a>Scala a Java

Poskytujeme rozhraní Spark API pro Scala a Java v `com.microsoft.ml.spark.cognitive` oboru názvů [Microsoft ML pro Apache Spark](https://aka.ms/spark). Další informace najdete v tématu [rozhraní API pro vývojáře v Scala](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). Příklady použití najdete v [ukázkách Scala](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Podporované platformy a konektory

Cognitive Services pro velké objemy dat vyžaduje Apache Spark. Existuje několik Apache Spark platforem, které podporují Cognitive Services pro velké objemy dat.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) je analytická platforma založená na Apache Spark optimalizovaná pro platformu Microsoft Azure cloudové služby. Nabízí nastavení jedním kliknutím, zjednodušené pracovní toky a interaktivní pracovní prostor, který podporuje spolupráci mezi odborníky přes data, datovými inženýry a obchodními analytiky.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) je jako podnikový datový sklad, který využívá obrovský paralelní zpracování. Pomocí analýzy synapse můžete rychle spouštět složité dotazy napříč petabajty daty. Azure synapse Analytics poskytuje spravované fondy Spark pro spouštění úloh Sparku s intuitivním Jupyter Notebookovým rozhraním.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Služba Azure Kubernetes Service (AKS)](../../aks/index.yml) orchestruje kontejnery Docker a distribuované aplikace v obrovské škále. AKS je spravovaná nabídka Kubernetes, která zjednodušuje používání Kubernetes v Azure. Kubernetes může umožnit jemně odstupňované řízení škály, latence a sítě pro rozpoznávání. Pokud však Apache Spark neznáte, doporučujeme použít Azure Databricks nebo Azure synapse Analytics.

### <a name="data-connectors"></a>Datové konektory

Po vytvoření clusteru Spark se další krok připojí k vašim datům. Apache Spark má širokou kolekci databázových konektorů. Tyto konektory umožňují aplikacím pracovat s velkými datovými sadami bez ohledu na to, kde jsou uložené. Další informace o podporovaných databázích a konektorech najdete v [seznamu podporovaných zdrojů dat pro Azure Databricks](/azure/databricks/data/data-sources/).

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
- [Dokument White Paper: inteligentní mikroslužby škálované velkými objemy](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Webináře a videa

- [Azure Cognitive Services ve Sparku: clustery s integrovanými inteligentními službami](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark summit vystoupení: škálovatelný AI pro dobré](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cognitive Services pro velké objemy dat v Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Bleskový rozhovor na rozsáhlých inteligentních mikroslužbách škály](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Další kroky

- [Začínáme s Cognitive Services pro velké objemy dat](getting-started.md)
- [Jednoduché příklady Pythonu](samples-python.md)
- [Jednoduché příklady Scala](samples-scala.md)