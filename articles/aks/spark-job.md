---
title: Spuštění úlohy Apache Spark se službou Azure Kubernetes Service (AKS)
description: Pomocí služby Azure Kubernetes Service (AKS) můžete vytvořit a spustit úlohu Apache Spark pro zpracování velkých objemů dat.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 74a3fe79291f3a5c7f5bbd664bf6d55a5fb77eae
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181189"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Spouštění úloh Apache Spark v AKS

[Apache Spark][apache-spark] je rychlý modul pro zpracování velkých objemů dat. Od [verze Spark 2.3.0][spark-kubernetes-earliest-version]Apache Spark podporuje nativní integraci s clustery Kubernetes. Služba Azure Kubernetes Service (AKS) je spravované prostředí Kubernetes běžící v Azure. Tento dokument popisuje přípravu a spouštění úloh Apache Spark v clusteru služby Azure Kubernetes (AKS).

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto článku budete potřebovat následující.

* Základní porozumění Kubernetes a [Apache Spark][spark-quickstart].
* Účet [Docker Hub][docker-hub] nebo [Azure Container Registry][acr-create].
* Rozhraní příkazového řádku Azure je [nainstalované][azure-cli] ve vývojovém systému.
* V systému je nainstalovaný [JDK 8][java-install] .
* [Apache Maven][maven-install] je nainstalovaná ve vašem systému.
* SBT ([Nástroj pro vytváření Scala][sbt-install]) je nainstalovaný na vašem systému.
* Nástroje příkazového řádku Git nainstalované ve vašem systému.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Spark se používá pro zpracování velkých objemů dat a vyžaduje, aby uzly Kubernetes splňovaly požadavky na prostředky Sparku. `Standard_D3_v2`Pro uzly služby Azure Kubernetes (AKS) doporučujeme minimální velikost.

Pokud potřebujete cluster AKS, který splňuje toto minimální doporučení, spusťte následující příkazy.

Vytvořte skupinu prostředků pro cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Vytvořte instanční objekt pro cluster. Po vytvoření budete pro další příkaz potřebovat instanční objekt appId a heslo.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Vytvořte cluster AKS s uzly, které mají velikost `Standard_D3_v2` , a hodnoty appId a Password předané jako parametry služby-Principal a Client-Secret.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Připojte se ke clusteru AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Pokud používáte Azure Container Registry (ACR) k ukládání imagí kontejneru, nakonfigurujte ověřování mezi AKS a ACR. Postup najdete v [dokumentaci k ověřování ACR][acr-aks] .

## <a name="build-the-spark-source"></a>Sestavení zdroje Spark

Před spuštěním úloh Sparku v clusteru AKS je potřeba vytvořit zdrojový kód Spark a zabalit ho do image kontejneru. Zdroj Spark obsahuje skripty, které lze použít k dokončení tohoto procesu.

Naklonujte úložiště Sparku do vašeho vývojového systému.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Přejděte do adresáře klonovaného úložiště a uložte cestu ke zdroji Spark do proměnné.

```bash
cd spark
sparkdir=$(pwd)
```

Pokud máte nainstalované více verzí JDK, nastavte pro `JAVA_HOME` aktuální relaci použití verze 8.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Spuštěním následujícího příkazu Sestavte zdrojový kód Spark s podporou Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Následující příkazy vytvoří image kontejneru Spark a nahrajte je do registru imagí kontejneru. Nahraďte `registry.example.com` názvem vašeho registru kontejneru a `v1` značkou, kterou preferujete použít. Pokud používáte Docker Hub, tato hodnota je název registru. Pokud používáte Azure Container Registry (ACR), je tato hodnota název přihlašovacího serveru ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Nahrajte image kontejneru do registru imagí kontejneru.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Příprava úlohy Spark

Dále Připravte úlohu Spark. Soubor JAR se používá k uchování úlohy Spark a je potřeba při spuštění `spark-submit` příkazu. JAR lze zpřístupnit prostřednictvím veřejné adresy URL nebo předběžného balení v rámci image kontejneru. V tomto příkladu je vytvořena ukázka JAR pro vypočítání hodnoty pí. Tento JAR se pak nahraje do služby Azure Storage. Pokud máte stávající jar, nebojte se nahradit

Vytvořte adresář, kde byste chtěli vytvořit projekt pro úlohu Spark.

```bash
mkdir myprojects
cd myprojects
```

Vytvoří nový projekt Scala ze šablony.

```bash
sbt new sbt/scala-seed.g8
```

Po zobrazení výzvy zadejte `SparkPi` název projektu.

```bash
name [Scala Seed Project]: SparkPi
```

Přejděte do nově vytvořeného adresáře projektu.

```bash
cd sparkpi
```

Spuštěním následujících příkazů přidejte modul plug-in SBT, který umožňuje sbalení projektu jako souboru jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Spusťte tyto příkazy pro zkopírování ukázkového kódu do nově vytvořeného projektu a přidejte všechny nezbytné závislosti.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Chcete-li projekt zabalit do JAR, spusťte následující příkaz.

```bash
sbt assembly
```

Po úspěšném sbalení by se měl zobrazit výstup podobný následujícímu.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Kopírovat úlohu do úložiště

Vytvořte účet úložiště Azure a kontejner pro uložení souboru jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Nahrajte soubor JAR do účtu služby Azure Storage pomocí následujících příkazů.

```azurecli
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

Proměnná `jarUrl` teď obsahuje veřejně dostupnou cestu k souboru jar.

## <a name="submit-a-spark-job"></a>Odeslat Sparkovou úlohu

Spusťte Kube-proxy na samostatném příkazovém řádku s následujícím kódem.

```bash
kubectl proxy
```

Přejděte zpátky do kořene úložiště Spark.

```bash
cd $sparkdir
```

Vytvořte účet služby, který má dostatečná oprávnění ke spuštění úlohy.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Odešlete úlohu pomocí `spark-submit` .

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Tato operace spustí úlohu Spark, která streamuje stav úlohy do vaší relace prostředí. Když je úloha spuštěná, můžete si prohlédnout ovladač Spark pod a prováděcí modul v části pomocí příkazu kubectl získat lusky. Otevřete druhou relaci terminálu pro spuštění těchto příkazů.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

I když je úloha spuštěná, můžete taky získat přístup k uživatelskému rozhraní Spark. V druhé relaci terminálu použijte příkaz, který `kubectl port-forward` poskytuje přístup k uživatelskému rozhraní Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Pokud chcete získat přístup k uživatelskému rozhraní Spark, otevřete adresu `127.0.0.1:4040` v prohlížeči.

![ROZHRANÍ Spark](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Získání výsledků a protokolů úlohy

Po dokončení úlohy bude ovladač pod stavem Dokončeno. Pomocí následujícího příkazu Získejte název pod názvem.

```bash
kubectl get pods --show-all
```

Výstup:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Pomocí `kubectl logs` příkazu získáte protokoly z ovladače Spark pod. Nahraďte název pod názvem ovladače pod názvem.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

V těchto protokolech vidíte výsledek úlohy Spark, což je hodnota pí.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Balíček jar s imagí kontejneru

V předchozím příkladu se soubor JAR Spark nahrál do služby Azure Storage. Další možností je zabalit soubor JAR do vlastních imagí Docker.

Provedete to tak, že vyhledáte `dockerfile` Image Sparku umístěnou v `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` adresáři. Přidejte `ADD` příkaz pro úlohu Spark `jar` mezi `WORKDIR` `ENTRYPOINT` deklaracemi a.

Aktualizujte cestu jar na umístění `SparkPi-assembly-0.1.0-SNAPSHOT.jar` souboru ve vývojovém systému. Můžete také použít vlastní soubor JAR.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Sestavte a nahrajte Image pomocí zahrnutých skriptů Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Při spuštění úlohy místo označení vzdálené adresy URL jar `local://` lze schéma použít s cestou k souboru jar v imagi Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Z [dokumentace ke][spark-docs]Sparku: "Plánovač Kubernetes je momentálně experimentální. V budoucích verzích se můžou nacházet změny v konfiguraci, image kontejnerů a vstupní soubory.

## <a name="next-steps"></a>Další kroky

Další podrobnosti najdete v dokumentaci k Sparku.

> [!div class="nextstepaction"]
> [Dokumentace Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: /azure/developer/java/fundamentals/java-jdk-long-term-support
[maven-install]: https://maven.apache.org/install.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-kubernetes-earliest-version]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./index.yml
[azure-cli]: /cli/azure/
[storage-account]: ../storage/blobs/storage-quickstart-blobs-cli.md
