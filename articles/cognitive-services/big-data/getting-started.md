---
title: Začínáme s Cognitive Services pro velké objemy dat
description: Nastavte si MMLSpark kanál pomocí Cognitive Services v Azure Databricks a spusťte ukázku.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 095f2c3ed17042bb616fb091d1af52a64c913709
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460508"
---
# <a name="getting-started"></a>Začínáme

Nastavení prostředí je prvním krokem k vytvoření kanálu pro vaše data. Až bude vaše prostředí připravené, je spuštění ukázky rychlé a snadné.

V tomto článku provedeme tyto kroky, abychom vám pomohli začít:

1. [Vytvoření prostředku služeb Cognitive Services](#create-a-cognitive-services-resource)
1. [Vytvoření clusteru Apache Spark](#create-an-apache-spark-cluster)
1. [Try a sample](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

Aby bylo možné použít Cognitive Services pro velké objemy dat, je nutné nejprve vytvořit službu rozpoznávání pro náš pracovní postup. Existují dva hlavní typy Cognitive Services: cloudové služby hostované v Azure a kontejnerové služby spravované uživateli. Doporučujeme začít s jednodušším cloudovým Cognitive Services.

### <a name="cloud-services"></a>Cloud Services

Cloudové Cognitive Services jsou inteligentní algoritmy hostované v Azure. Tyto služby jsou připravené k použití bez školení, ale potřebujete jenom připojení k Internetu. Službu pro [rozpoznávání se dá vytvořit v Azure Portal](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) nebo pomocí [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows).

### <a name="containerized-services-optional"></a>Kontejnerové služby (volitelné)

Pokud vaše aplikace nebo úloha používá velké datové sady, vyžaduje privátní sítě nebo nemůže kontaktovat Cloud, nemusí být komunikace s Cloud Services možná. V takové situaci mají kontejnery Cognitive Services tyto výhody:

* **Nízká konektivita**: Cognitive Services s využitím kontejnerů můžete nasadit v jakémkoli výpočetním prostředí, a to jak v cloudu, tak i na off. Pokud vaše aplikace nemůže kontaktovat Cloud, zvažte nasazení Cognitive Services kontejnerů ve vaší aplikaci.

* **Nízká latence**: vzhledem k tomu, že kontejnery služeb nevyžadují komunikaci s přenosovou cestou do cloudu nebo z něj, jsou odpovědi vraceny s mnohem nižší latencí.

* **Ochrana osobních údajů a zabezpečení dat**: do privátních sítí můžete nasazovat kontejnery služby, aby citlivá data nezůstala síť.

* **Vysoká škálovatelnost**: kontejnerové služby nemají omezení přenosové rychlosti a spouštějí se na počítačích spravovaných uživatelem. Takže můžete škálovat Cognitive Services bez ukončení pro zpracování mnohem větších úloh.

Podle [této příručky](../cognitive-services-container-support.md?tabs=luis) vytvořte službu pro vyvnímání z kontejneru.

## <a name="create-an-apache-spark-cluster"></a>Vytvoření clusteru Apache Spark

[Apache Spark &trade; ](http://spark.apache.org/) je distribuovaná výpočetní architektura navržená pro zpracování dat s velkými objemy dat. Uživatelé můžou pracovat s Apache Spark v Azure pomocí služeb, jako je Azure Databricks, Azure synapse Analytics, HDInsight a Azure Kubernetes Services. Pokud chcete použít Cognitive Services velkých objemů dat, musíte nejdřív vytvořit cluster. Pokud už máte cluster Spark, nebojte se vyzkoušet příklad.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks je analytická platforma založená na Apache Spark s nastavením jedním kliknutím, zjednodušenými pracovními postupy a interaktivním pracovním prostorem. Často se používá ke spolupráci mezi odborníky přes data, inženýri a obchodními analytiky. Chcete-li použít Cognitive Services velkých objemů dat v Azure Databricks, postupujte takto:

1. [Vytvoření pracovního prostoru Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Vytvoření clusteru Spark ve službě Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Instalace velkých objemů dat Cognitive Services
    * Vytvoření nové knihovny v pracovním prostoru datacihly  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Zadejte následující souřadnice Maven souřadnic:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3` úložiště: `https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Instalace knihovny do clusteru  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure synapse Analytics (volitelné)

Volitelně můžete pomocí synapse Analytics vytvořit cluster Spark. Azure synapse Analytics přináší dohromady podnikové datové sklady a analýzy velkých objemů dat. Poskytuje vám volnost v dotazování na data podle vašich podmínek, a to s využitím škálovatelných nebo zřízených prostředků na vyžádání bez serveru. Pokud chcete začít používat Azure synapse Analytics, postupujte takto:

1. [Vytvořte pracovní prostor synapse (Preview)](../../synapse-analytics/quickstart-create-workspace.md).
1. [Pomocí Azure Portal vytvořte nový fond Apache Spark bez serveru (Preview)](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

Ve výchozím nastavení je ve službě Azure synapse Analytics nainstalovaná velká data pro Cognitive Services.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pokud používáte Cognitive Services s využitím kontejnerů, je jednou z oblíbených možností nasazení Sparku vedle kontejnerů služba Azure Kubernetes.

Pokud chcete začít pracovat se službou Azure Kubernetes, postupujte takto:

1. [Nasazení clusteru služby Azure Kubernetes (AKS) pomocí Azure Portal](../../aks/kubernetes-walkthrough-portal.md)
1. [Instalace grafu Apache Spark 2.4.0 Helm](https://hub.helm.sh/charts/microsoft/spark)
1. [Instalace kontejneru vnímání Service pomocí Helm](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Try a sample

Po nastavení clusteru a prostředí Spark můžeme spustit krátký vzorek. Tato část ukazuje, jak používat velké objemy dat pro Cognitive Services v Azure Databricks.

Nejprve můžeme vytvořit Poznámkový blok v Azure Databricks. Pro jiné poskytovatele clusteru Spark použijte své poznámkové bloky nebo Spark odeslat.

1. Vytvoření nového poznámkového bloku datacihly kliknutím na **Nový Poznámkový** blok z nabídky **Azure Databricks** .

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. V dialogovém okně **vytvořit Poznámkový blok** zadejte název, vyberte **Python** jako jazyk a vyberte cluster Spark, který jste vytvořili dříve.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Vyberte **Vytvořit**.

1. Vložte tento fragment kódu do nového poznámkového bloku.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Získejte svůj klíč předplatného z nabídky **klíče a koncový bod** z řídicího panelu Analýza textu v Azure Portal.
1. Nahraďte zástupný symbol klíče předplatného v kódu vašeho poznámkového bloku datacihly vaším klíčem předplatného.
1. Ukázku spustíte tak, že v pravém horním rohu buňky poznámkového bloku vyberete symbol přehrání nebo trojúhelníku. Pokud chcete spustit všechny buňky, vyberte v horní části poznámkového bloku možnost **Spustit vše** . Odpovědi se zobrazí pod buňkou v tabulce.

### <a name="expected-results"></a>Očekávané výsledky

| text                                      |   mínění |
|:------------------------------------------|------------:|
| Jsem si dnes spokojeni, Slunečné!           |   0,978959  |
| Frustrovaní tento přenos nespěcháte hodin |   0,0237956 |
| Služba rozpoznávání na Spark aint je chybná.  |   0,888896  |

## <a name="next-steps"></a>Další kroky

- [Krátké příklady Pythonu](samples-python.md)
- [Krátké příklady Scala](samples-scala.md)
- [Recept: prediktivní údržba](recipes/anomaly-detection.md)
- [Recept: průzkum inteligentního umění](recipes/art-explorer.md)