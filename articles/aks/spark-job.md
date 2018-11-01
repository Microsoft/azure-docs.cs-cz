---
title: Spuštění úlohy Apache Sparku s Azure Kubernetes Service (AKS)
description: Použití Azure Kubernetes Service (AKS) se spouští úloha Apache Sparku
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: ddaff590fd493b430a72c30dd35cb1b891b80d84
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414025"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Spuštěné úlohy Apache Spark v AKS

[Apache Spark] [ apache-spark] je rychlé modul pro zpracování rozsáhlých data. Počínaje verzí [Spark 2.3.0 vydání][spark-latest-release], Apache Sparku jednodušší, podporuje nativní integraci s clustery Kubernetes. Azure Kubernetes Service (AKS) je spravované prostředí Kubernetes v Azure. Tento dokument podrobně popisuje přípravy a spuštění úlohy Apache Spark v clusteru Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Požadavky

Abyste mohli dokončit kroky v tomto článku, budete potřebovat.

* Základní znalosti o Kubernetes a [Apache Spark][spark-quickstart].
* [Docker Hubu] [ docker-hub] účtu, nebo [Azure Container Registry][acr-create].
* Azure CLI [nainstalované] [ azure-cli] ve vývojovém systému.
* [JDK 8] [ java-install] nainstalovaný ve vašem systému.
* SBT ([nástroj pro sestavení Scala][sbt-install]) nainstalovaný ve vašem systému.
* Nástroje příkazového řádku Git nainstalovaný ve vašem systému.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Spark se používá pro rozsáhlé zpracování dat a vyžaduje, že jsou uzly Kubernetes velikosti ke splnění těchto požadavků prostředky Spark. Doporučujeme minimální velikost `Standard_D3_v2` pro uzlů Azure Kubernetes Service (AKS).

Pokud potřebujete cluster AKS, která splňuje toto minimální doporučení, spusťte následující příkazy.

Vytvořte skupinu prostředků clusteru.

```azurecli
az group create --name mySparkCluster --location eastus
```

Vytvoření clusteru AKS s uzly, které jsou o velikosti `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Připojte se ke clusteru AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Pokud používáte Azure Container Registry (ACR) k ukládání imagí kontejnerů, nakonfigurujte ověřování mezi AKS a služby ACR. Zobrazit [dokumentace ověřování služby ACR] [ acr-aks] k těmto krokům.

## <a name="build-the-spark-source"></a>Sestavení zdroje Spark

Před spuštěním úlohy Spark na AKS cluster, budete muset sestavit Spark zdrojovému kódu a zabalíte ji do image kontejneru. Spark zdroj obsahuje skripty, které můžete použít k dokončení tohoto procesu.

Naklonujte úložiště project Spark pro váš vývojový systém.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Přejděte do adresáře naklonovaného úložiště a uložit do proměnné cesty zdroje Spark.

```bash
cd spark
sparkdir=$(pwd)
```

Pokud máte nainstalovaných několik verzí sady JDK, nastavte `JAVA_HOME` používat verzi 8 pro aktuální relaci.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Spusťte následující příkaz k vytvoření Sparku zdrojový kód se podpora pro Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Následující příkazy vytvoří image kontejneru Spark a vložit do registru image kontejneru. Nahraďte `registry.example.com` s názvem vašeho registru kontejneru a `v1` se značkou, který chcete použít. Pokud používáte Docker Hubu, tato hodnota je název registru. Pokud používáte Azure Container Registry (ACR), tato hodnota je název přihlašovacího serveru ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Nasdílení image kontejneru do registru image kontejneru.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Příprava úlohy Spark job

Teď připravíte úlohu Spark. Soubor jar slouží k ukládání úlohy Spark a je potřeba při spuštění `spark-submit` příkazu. Soubor jar může být přístupné přes veřejnou adresu URL nebo předem zabalené do image kontejneru. V tomto příkladu se vytvoří soubor jar ukázka vypočítat hodnotu čísla pí. Tento soubor jar je pak nahrají do služby Azure storage. Pokud máte existující soubor jar, můžete k nahrazení

Vytvoření adresáře, ve kterém chcete vytvořit projekt pro úlohu Spark.

```bash
mkdir myprojects
cd myprojects
```

Vytvoření nového projektu Scala ze šablony.

```bash
sbt new sbt/scala-seed.g8
```

Po zobrazení výzvy zadejte `SparkPi` pro název projektu.

```bash
name [Scala Seed Project]: SparkPi
```

Přejděte do adresáře s nově vytvořeného projektu.

```bash
cd sparkpi
```

Spusťte následující příkaz pro přidání modulu plug-in SBT, což umožňuje vytváření balíčků projektu jako soubor jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Spuštěním těchto příkazů zkopírujte vzorový kód do nově vytvořený projekt a přidat všechny potřebné závislosti.

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

Pokud chcete projekt zabalit do jar, spusťte následující příkaz.

```bash
sbt assembly
```

Po úspěšném balení, byste měli vidět výstup podobný následujícímu.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Úloha kopírování do úložiště

Vytvoření účtu služby Azure storage a kontejner pro uložení na soubor jar.

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

Proměnné `jarUrl` nyní obsahuje veřejně přístupná cesta k souboru jar.

## <a name="submit-a-spark-job"></a>Odeslat úlohu Spark

Začněte kube proxy v samostatném příkazový řádek s následujícím kódem.

```bash
kubectl proxy
```

Přejděte zpět do kořenového adresáře úložiště Spark.

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

Tato operace spustí úlohu Spark, které streamuje stav úlohy do relace prostředí. Když úloha běží, zobrazí se pod ovladač Spark a podů prováděcí modul pomocí kubectl get podů příkaz. Otevřete relaci druhé terminálu ke spuštění těchto příkazů.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Když úloha běží, můžete také přistupovat Spark uživatelského rozhraní. V druhé Terminálové relaci, použijte `kubectl port-forward` příkaz poskytují přístup k uživatelskému rozhraní Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Chcete-li získat přístup k Spark UI, otevřete adresu `127.0.0.1:4040` v prohlížeči.

![Spark uživatelského rozhraní](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Získat výsledky úlohy a protokoly

Po dokončení úlohy pod ovladač bude ve stavu "Dokončeno". Získání názvu podu pomocí následujícího příkazu.

```bash
kubectl get pods --show-all
```

Výstup:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Použití `kubectl logs` příkaz vám pomůže získat protokoly z pod ovladač spark. Nahraďte název pod názvem podu ovladač.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

V rámci těchto protokolů můžete zobrazit výsledek tohoto úloha Sparku, což je hodnota čísla pí.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Soubor jar balíčku s imagí kontejneru

V předchozím příkladu byl odeslán na soubor jar Sparku do služby Azure storage. Další možností je balíček na soubor jar do vlastními silami sestavených imagí Dockeru.

Uděláte to tak, vyhledejte `dockerfile` pro Spark image uložená v `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` adresáře. Přidat mě `ADD` příkaz pro úlohu Spark `jar` někde mezi `WORKDIR` a `ENTRYPOINT` deklarace.

Aktualizovat soubor jar cestu k umístění `SparkPi-assembly-0.1.0-SNAPSHOT.jar` soubor ve vývojovém systému. Můžete také použít vlastní soubor vlastní soubor jar.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Vytvoření a nahrání image zahrnuty skripty, Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Při spuštění úlohy, místo na vzdálený soubor jar adresu URL, která `local://` schéma je možné s cestou k souboru jar do image Dockeru.

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
> Z aplikace Spark [dokumentaci][spark-docs]: "Plánovač Kubernetes je aktuálně experimentální. V budoucích verzích může být chování změny konfigurace, Image kontejnerů a entrypoints".

## <a name="next-steps"></a>Další postup

Přečtěte si další podrobnosti naleznete v dokumentaci Spark.

> [!div class="nextstepaction"]
> [Dokumentace ke službě Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
