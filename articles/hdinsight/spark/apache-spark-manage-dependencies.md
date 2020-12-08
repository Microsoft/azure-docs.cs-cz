---
title: Správa závislostí aplikací Spark v Azure HDInsight
description: Tento článek poskytuje Úvod do správy závislostí Spark v clusteru HDInsight Spark pro aplikace PySpark a Scala.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b2cd50b1b35b87b1a11301ddc36ac355bef20dc4
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780611"
---
# <a name="manage-spark-application-dependencies"></a>Správa závislostí sparkových aplikací

V tomto článku se dozvíte, jak spravovat závislosti pro aplikace Spark běžící v HDInsight. Scala i PySpark se týkají aplikace Spark a oboru clusteru.

Pomocí rychlých odkazů můžete přejít k části na základě vašeho uživatelského případu:
* [Nastavení závislostí pro úlohy Spark pomocí Jupyter Notebook](#use-jupyter-notebook)
* [Nastavení závislosti jar úlohy Spark pomocí Azure Toolkit for IntelliJ použít](#use-azure-toolkit-for-intellij)
* [Konfigurace závislostí JAR pro cluster Spark](#jar-libs-for-cluster)
* [Zabezpečená správa závislostí Jar](#safely-manage-jar-dependencies)
* [Nastavení balíčků Python úlohy Spark pomocí Jupyter Notebook](#use-jupyter-notebook-1)
* [Bezpečně spravovat balíčky Pythonu pro cluster Spark](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>JAR knihovny pro jednu úlohu Spark
### <a name="use-jupyter-notebook"></a>Použít Jupyter Notebook
Když se relace Spark spustí v Jupyter Notebook v jádru Spark pro Scala, můžete nakonfigurovat balíčky z:

* [Maven úložiště](https://search.maven.org/)nebo balíčky komunitní komunitou v [balíčcích Spark](https://spark-packages.org/).
* Soubory jar uložené v primárním úložišti clusteru

`%%configure`K nakonfigurování poznámkového bloku pro použití externího balíčku použijete Magic. V poznámkových blocích, které používají externí balíčky, se ujistěte, že jste volali `%%configure` Magic do první buňky kódu. Tím se zajistí, že jádro je nakonfigurované na použití balíčku před spuštěním relace.

>
>[!IMPORTANT]  
>Pokud zapomenete nakonfigurovat jádro v první buňce, můžete použít `%%configure` `-f` parametr s parametrem, který ale bude restartovat relaci a veškerý průběh bude ztracen.

**Ukázka pro balíčky z úložiště Maven nebo balíčků Spark**

Po nalezení balíčku z úložiště Maven Shromážděte hodnoty pro ID **skupiny**, **ArtifactId** a **verzi**. Zřetězí tři hodnoty oddělené dvojtečkou (**:**).

   ![Zřetězení schématu balíčku](./media/apache-spark-manage-dependencies/spark-package-schema.png "Zřetězení schématu balíčku")

Ujistěte se, že hodnoty, které shromáždíte, odpovídají vašemu clusteru. V tomto případě používáme balíček spark Cosmos DB Connector pro Scala 2,11 a Spark 2,3 pro HDInsight 3,6 v clusteru Spark. Pokud si nejste jistí, spusťte `scala.util.Properties.versionString` v jádře kódu v jádru Spark, abyste získali verzi Scala clusteru. Spusťte `sc.version` pro získání verze clusteru Spark.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Ukázka pro jar uložené v primárním úložišti**

Použijte [schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro soubory jar na primárním úložišti clusterů. To Azure Storage pro `wasb://` `abfs://` Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage nebo Data Lake Storage Gen2 povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` nebo `abfss://` . Viz [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

Použijte čárkami oddělený seznam cest JAR pro více souborů JAR, globy jsou povoleny. JAR jsou součástí ovladače a prováděcích tříd pro třídy.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

Po konfiguraci externích balíčků můžete spustit import v buňce kódu a ověřit, zda byly balíčky správně umístěny.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Použít Azure Toolkit for IntelliJ
[Azure Toolkit for IntelliJ modul plug-in](./apache-spark-intellij-tool-plugin.md) poskytuje prostředí uživatelského rozhraní pro odeslání aplikace Spark Scala do clusteru HDInsight. Poskytuje `Referenced Jars` a `Referenced Files` vlastnosti ke konfiguraci knihovnych cest jar při odesílání aplikace Spark. Podívejte se na další podrobnosti o [použití modulu plug-in Azure Toolkit for IntelliJ pro HDInsight](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

![Dialogové okno pro odeslání Sparku](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>JAR knihovny pro cluster
V některých případech můžete chtít nakonfigurovat závislosti jar na úrovni clusteru, aby bylo možné každou aplikaci nastavit se stejnými závislostmi ve výchozím nastavení. Přístup je přidání vašich studijních cest do ovladače Spark a do cesty třídy prováděcího modulu.

1. Spustit pod ukázkovou akcí skriptů ke zkopírování souborů JAR z primárního úložiště `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` do místního systému souborů clusteru `/usr/libs/sparklibs` . Tento krok je potřeba, protože systém Linux používá `:` k oddělení seznamu cest třídy, ale HDInsight podporuje jenom cesty k úložišti, jako je `wasb://` . Cesta vzdáleného úložiště nebude fungovat správně, pokud ji přímo přidáte do cesty třídy.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Změňte konfiguraci služby Spark z Ambari a aktualizujte cestu třídy. Přejít na **Ambari > > konfigurace sparku > Custom Spark2-Defaults**. **Přidejte vlastnost** následujícím způsobem. Použijte `:` k oddělení cest, pokud máte více než jednu cestu, kterou chcete přidat. Globy jsou povoleny.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Změna výchozí konfigurace Sparku](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Změna výchozí konfigurace Sparku")

3. Uložte změněné konfigurace a restartujte ovlivněné služby.

   ![Restartovat ovlivněné služby](./media/apache-spark-manage-dependencies/restart-impacted-services.png "Restartovat ovlivněné služby")

Kroky můžete automatizovat pomocí [akcí skriptů](../hdinsight-hadoop-customize-cluster-linux.md). Dobrým odkazem je akce skriptu pro [Přidání vlastních knihoven podregistru](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) . Při změně konfigurace služby Spark se ujistěte, že používáte rozhraní API Ambari místo úprav konfiguračních souborů přímo. 

## <a name="safely-manage-jar-dependencies"></a>Zabezpečená správa závislostí Jar
Cluster HDInsight má integrované závislosti jar a aktualizace pro tyto verze JAR se od času stávají časem. Chcete-li se vyhnout konfliktu verzí mezi vestavěnými jar a JAR, které jste přizpůsobili pro referenci, zvažte [vystínování závislostí aplikace](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Balíčky Pythonu pro jednu úlohu Spark
### <a name="use-jupyter-notebook"></a>Použít Jupyter Notebook
Jádro HDInsight Jupyter Notebook PySpark nepodporuje instalaci balíčků Pythonu přímo z úložiště balíčků PyPi nebo Anaconda. Pokud máte `.zip` závislosti, `.egg` nebo `.py` a chcete je odkazovat na jednu relaci Sparku, postupujte podle následujících kroků:

1. Spustit pod ukázkovou akcí skriptu kopírování `.zip` `.egg` nebo `.py` souborů z primárního úložiště `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` do místního systému souborů clusteru `/usr/libs/pylibs` . Tento krok je potřeba, protože Linux používá `:` k oddělení seznamu cest hledání, ale podporuje jenom cesty úložiště se schématem, jako je `wasb://` . Cesta vzdáleného úložiště nebude při použití fungovat správně `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. V poznámkovém bloku spusťte pod kódem v buňce s kódem PySpark jádro:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. Spusťte `import` a ověřte, jestli byly balíčky úspěšně zahrnuté.  

## <a name="python-packages-for-cluster"></a>Balíčky Pythonu pro cluster
Balíčky Pythonu můžete nainstalovat z Anaconda do clusteru pomocí příkazu conda prostřednictvím akcí skriptu. Nainstalované balíčky jsou na úrovni clusteru a platí pro všechny aplikace. 

Cluster HDInsight Spark má dvě vestavěné Instalace Pythonu, Anaconda Python 2,7 a Anaconda Python 3,5. Pokud chcete získat další informace o výchozích nastaveních Pythonu pro služby a o tom, jak bezpečně nainstalovat externí balíčky Pythonu bez přerušení clusteru, přečtěte si další podrobnosti v tématu [bezpečné Správa závislostí Pythonu pro váš cluster](./apache-spark-python-package-installation.md).
