---
title: Spustit úlohu Apache Spark s Azure Container Service (AKS)
description: Spustit úlohu Apache Spark pomocí Azure Container Service (AKS)
services: container-service
author: lenadroid
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 3991312d7f7609bb0a206ccc0ecc67123ebec469
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="running-apache-spark-jobs-on-aks"></a>Spuštěné úlohy Apache Spark na AKS

[Apache Spark] [ apache-spark] je rychlé stroj pro zpracování velkých dat. Od [Spark 2.3.0 verze][spark-latest-release], Apache Spark podporuje nativní integrace s Kubernetes clustery. Azure Container Service (AKS) je prostředí spravované Kubernetes běžící v Azure. Tento dokument podrobně popisuje přípravu a spuštění úlohy Apache Spark v clusteru Azure Container Service (AKS).

## <a name="prerequisites"></a>Požadavky

Aby bylo možné provést kroky v tomto článku, budete potřebovat následující.

* Základní znalosti o Kubernetes a [Apache Spark][spark-quickstart].
* [Úložiště docker Hub] [ docker-hub] účtu, nebo [registru kontejner Azure][acr-create].
* Rozhraní příkazového řádku Azure [nainstalován] [ azure-cli] ve vývojovém systému.
* [JDK 8] [ java-install] v systému nainstalována.
* SBT ([nástroj pro sestavení Scala][sbt-install]) v systému nainstalována.
* Nástroje příkazového řádku Gitu v systému nainstalována.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Spark je použitý pro zpracování velkých dat a vyžaduje, aby Kubernetes uzly mají velikost pro splnění požadavků prostředky Spark. Doporučujeme minimální velikost `Standard_D3_v2` pro uzly Azure Container Service (AKS).

Pokud budete potřebovat AKS clusteru, který splňuje toto minimální doporučení, spusťte následující příkazy.

Vytvořte skupinu prostředků clusteru.

```azurecli
az group create --name mySparkCluster --location eastus
```

Vytvoření clusteru AKS s uzly, které jsou velikosti `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Připojte se ke clusteru AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Pokud používáte Azure kontejneru registru (ACR) k ukládání imagí kontejneru, nakonfigurujte ověřování mezi AKS a ACR. Najdete v článku [ACR ověřování dokumentaci] [ acr-aks] pro tyto kroky.

## <a name="build-the-spark-source"></a>Sestavení zdroje Spark

Před spuštěním úlohy Spark v clusteru AKS, potřebujete vytvořit Spark zdrojového kódu a balíček do bitové kopie kontejneru. Zdroj Spark obsahuje skripty, které slouží k dokončení tohoto procesu.

Naklonujte úložiště projektu Spark pro váš vývojový systém.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Přejděte do adresáře klonovaný úložiště a uložit cestu zdroje Spark proměnné.

```bash
cd spark
sparkdir=$(pwd)
```

Pokud máte víc nainstalovaných verzí JDK, nastavte `JAVA_HOME` používat verzi 8 pro aktuální relaci.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Spusťte následující příkaz k vytvoření zdrojového kódu s podporou Kubernetes Spark.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Následující příkazy vytvořit bitovou kopii kontejneru Spark a poslat ho přímo registru kontejneru bitové kopie. Nahraďte `registry.example.com` s názvem vašeho kontejneru registru a `v1` se značkou, který chcete použít. Pokud používáte úložiště Docker Hub, je tato hodnota registru. Pokud používáte Azure kontejneru registru (ACR), tato hodnota je název ACR přihlášení serveru.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Nabízená bitovou kopii kontejneru registru bitové kopie vašeho kontejneru.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Příprava úlohy Spark

V dalším kroku Příprava úlohy Spark. Soubor jar se používá k ukládání úlohy Spark a je potřeba při spuštění `spark-submit` příkaz. Jar můžete provedeny přístupné přes veřejnou adresu URL nebo předem zabalené v rámci bitové kopie kontejneru. V tomto příkladu se vytvoří ukázka jar vypočítat hodnotu čísla pí. Tato jar je pak nahrán do úložiště Azure. Pokud máte existující jar, klidně nahradit

Vytvořte adresář, kde chcete vytvořit projekt pro úlohu Spark.

```bash
mkdir myprojects
cd myprojects
```

Vytvoření nového projektu Scala ze šablony.

```bash
sbt new sbt/scala-seed.g8
```

Po zobrazení výzvy zadejte `SparkPi` jako název projektu.

```bash
name [Scala Seed Project]: SparkPi
```

Přejděte do adresáře nově vytvořený projekt.

```bash
cd sparkpi
```

Spusťte následující příkaz pro přidání modulu plug-in SBT, což umožňuje balení projektu jako soubor jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Spusťte tyto příkazy a zkopírujte ukázkový kód do nově vytvořený projekt a přidejte všechny potřebné závislosti.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Do projektu balíček do jar, spusťte následující příkaz.

```bash
sbt assembly
```

Po úspěšné balení, měli byste vidět výstup podobný následujícímu.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Úloha kopírování do úložiště

Vytvoření účtu úložiště Azure a kontejner pro uložení na soubor jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Nahrajte soubor jar do účtu úložiště Azure pomocí následujících příkazů.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Proměnné `jarUrl` teď obsahuje veřejně přístupná cestu na soubor jar.

## <a name="submit-a-spark-job"></a>Odeslání úlohy Spark

Spuštění kube proxy v samostatné příkazového řádku s následujícím kódem.

```bash
kubectl proxy
```

Přejděte zpět do kořenového úložiště Spark.

```bash
cd $sparkdir
```

Odeslání úlohy pomocí `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Tato operace spustí úlohy Spark, která datové proudy stav úlohy do relace prostředí. Když úloha běží, se zobrazí pod ovladač Spark a vykonavatele pracovními stanicemi soustředěnými kolem pomocí kubectl získat pracovními stanicemi soustředěnými kolem příkaz. Otevřete relaci druhý terminálu ke spuštění těchto příkazů.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Když úloha běží, je dostupný také Spark uživatelského rozhraní. V relaci druhý terminálu, použijte `kubectl port-forward` příkaz poskytnout přístup k Spark uživatelského rozhraní.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Chcete-li získat přístup k Spark uživatelského rozhraní, otevřete adresu `127.0.0.1:4040` v prohlížeči.

![Spark uživatelského rozhraní](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Získat výsledky úlohy a protokoly

Po dokončení úlohy pod ovladač bude ve stavu "Dokončeno". Získání názvu pod pomocí následujícího příkazu.

```bash
kubectl get pods --show-all
```

Výstup:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Použití `kubectl logs` příkaz k získání protokolů z pod ovladač spark. Nahradí název pod názvem vaší pod ovladačů.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

V rámci tyto protokoly se zobrazí výsledky úlohy Spark, což je hodnotu čísla pí.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Balíček jar s bitovou kopií kontejneru

V tomto příkladu byl soubor jar Spark nahrán do úložiště Azure. Další možností je balíček na soubor jar do uživatelské imagí Dockeru.

Chcete-li to provést, vyhledejte `dockerfile` bitovou kopii Spark v umístění `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` adresáře. Přidat mě `ADD` příkaz pro úlohu Spark `jar` někde mezi `WORKDIR` a `ENTRYPOINT` deklarace.

Aktualizujte jar cestu k umístění `SparkPi-assembly-0.1.0-SNAPSHOT.jar` soubor ve vývojovém systému. Můžete také použít vlastní soubor jar vlastní.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Sestavení a push bitovou kopii pomocí skriptů zahrnuté Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Při spuštění úlohy, místo označující adresu URL vzdálené jar `local://` schéma lze použít s cestu k souboru jar Docker obrázku.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Z Spark [dokumentace][spark-docs]: "Plánovač Kubernetes je aktuálně experimentální. V budoucích verzích může existovat chování změny kolem konfigurace, kontejner bitové kopie a entrypoints".

## <a name="next-steps"></a>Další postup

Projděte si dokumentaci Spark další podrobnosti.

> [!div class="nextstepaction"]
> [Spark dokumentace][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/en-us/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/en-us/azure/aks/
[azure-cli]: https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/en-us/azure/storage/common/storage-azure-cli
