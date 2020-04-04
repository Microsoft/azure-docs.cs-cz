---
title: Spuštění úlohy Apache Spark se službou Azure Kubernetes Service (AKS)
description: Pomocí služby Azure Kubernetes Service (AKS) můžete vytvořit a spustit úlohu Apache Spark pro rozsáhlé zpracování dat.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 8ebd8990a2fdd43b243f5dd6feb632d782fdeb0b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632690"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Spuštění pracovních míst Apache Spark na AKS

[Apache Spark][apache-spark] je rychlý motor pro rozsáhlé zpracování dat. Od [verze Spark 2.3.0][spark-latest-release]podporuje Apache Spark nativní integraci s klastry Kubernetes. Azure Kubernetes Service (AKS) je spravované prostředí Kubernetes běžící v Azure. Tento dokument podrobně popisuje přípravu a spuštění úloh Apache Spark v clusteru služby Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto článku, budete potřebovat následující.

* Základní znalosti Kubernetes a [Apache Spark][spark-quickstart].
* [Účet Docker Hub][docker-hub] u. Azure nebo [registr kontejnerů Azure][acr-create].
* Azure CLI [nainstalované][azure-cli] ve vašem vývojovém systému.
* [JDK 8][java-install] nainstalován na vašem systému.
* SBT ([Scala Build Tool][sbt-install]) nainstalovaný ve vašem systému.
* Nástroje příkazového řádku Git nainstalované ve vašem systému.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Spark se používá pro rozsáhlé zpracování dat a vyžaduje, aby uzly Kubernetes byly dimenzovány tak, aby splňovaly požadavky na prostředky Spark. Doporučujeme minimální velikost `Standard_D3_v2` pro vaše uzly Služby Azure Kubernetes (AKS).

Pokud potřebujete cluster AKS, který splňuje toto minimální doporučení, spusťte následující příkazy.

Vytvořte skupinu prostředků pro cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Vytvořte instanční objekt pro cluster. Po vytvoření budete potřebovat appId instančního objektu a heslo pro další příkaz.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Vytvořte cluster AKS s uzly, které jsou velikosti `Standard_D3_v2`a hodnoty appId a heslo předané jako parametry instančního objektu a klientského tajného klíče.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Připojte se ke clusteru AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Pokud k ukládání ibikopií kontejnerů používáte Azure Container Registry (ACR), nakonfigurujte ověřování mezi AKS a ACR. Viz [ověřování ACR dokumentace][acr-aks] pro tyto kroky.

## <a name="build-the-spark-source"></a>Sestavení zdroje Spark

Před spuštěním úloh Spark v clusteru AKS je potřeba vytvořit zdrojový kód Spark a zabalit ho do image kontejneru. Zdroj Spark obsahuje skripty, které lze použít k dokončení tohoto procesu.

Naklonujte úložiště projektu Spark do vašeho vývojového systému.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Změňte se do adresáře klonovaného úložiště a uložte cestu zdroje Spark do proměnné.

```bash
cd spark
sparkdir=$(pwd)
```

Pokud máte nainstalováno více verzí JDK, nastavte `JAVA_HOME` pro aktuální relaci použít verzi 8.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Spusťte následující příkaz k vytvoření zdrojového kódu Spark s podporou Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Následující příkazy vytvoří image kontejneru Spark a zasunou ji do registru bitové kopie kontejneru. Nahraďte `registry.example.com` název registru kontejnerů a `v1` značkou, kterou chcete použít. Pokud používáte Docker Hub, tato hodnota je název registru. Pokud používáte Azure Container Registry (ACR), tato hodnota je název přihlašovacího serveru ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Zasuňte bitovou kopii kontejneru do registru bitové kopie kontejneru.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Příprava úlohy Spark

Dále připravte úlohu Spark. Jar soubor se používá k uložení úlohy Spark `spark-submit` a je potřeba při spuštění příkazu. Nádoba může být zpřístupněna prostřednictvím veřejné adresy URL nebo zabalena v rámci obrázku kontejneru. V tomto příkladu je vytvořena ukázková nádoba pro výpočet hodnoty Pi. Tato nádoba se pak nahraje do úložiště Azure. Pokud máte existující nádobu, neváhejte a

Vytvořte adresář, ve kterém chcete vytvořit projekt pro úlohu Spark.

```bash
mkdir myprojects
cd myprojects
```

Vytvořte nový projekt Scala ze šablony.

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

Spusťte následující příkazy pro přidání pluginu SBT, který umožňuje balení projektu jako jar souboru.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Spusťte tyto příkazy zkopírovat ukázkový kód do nově vytvořeného projektu a přidat všechny potřebné závislosti.

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

Chcete-li projekt zabalit do nádoby, spusťte následující příkaz.

```bash
sbt assembly
```

Po úspěšném balení byste měli vidět výstup podobný následujícímu.

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

Nahrajte soubor jar do účtu úložiště Azure pomocí následujících příkazů.

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

Proměnná `jarUrl` nyní obsahuje veřejně přístupnou cestu k souboru jar.

## <a name="submit-a-spark-job"></a>Odeslat úlohu Spark

Spusťte kube-proxy v samostatném příkazovém řádku s následujícím kódem.

```bash
kubectl proxy
```

Přejděte zpět do kořenového adresáře úložiště Spark.

```bash
cd $sparkdir
```

Vytvořte účet služby, který má dostatečná oprávnění pro spuštění úlohy.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Odešlete `spark-submit`úlohu pomocí aplikace .

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

Tato operace spustí úlohu Spark, která streamuje stav úlohy do relace prostředí. Zatímco úloha je spuštěna, můžete vidět Spark driver pod a executor pods pomocí příkazu kubectl get pods. Chcete-li tyto příkazy spustit, otevřete druhou relaci terminálu.

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

Když je úloha spuštěná, můžete také přistupovat k ui Spark. V druhé terminálové relaci `kubectl port-forward` použijte příkaz, který poskytuje přístup k uzlení Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Chcete-li získat přístup k `127.0.0.1:4040` uzlení, otevřete adresu v prohlížeči.

![Jiskra UI](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Získání výsledků úlohy a protokolů

Po dokončení úlohy bude pod ovladače ve stavu Dokončeno. Získejte název modulu pomocí následujícího příkazu.

```bash
kubectl get pods --show-all
```

Výstup:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Pomocí `kubectl logs` příkazu získat protokoly z pod ovladače jiskry. Nahraďte název podu názvem modulu ovladače.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

V těchto protokolech můžete zobrazit výsledek úlohy Spark, což je hodnota Pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Nádoba na balení s obrázkem kontejneru

Ve výše uvedeném příkladu byl soubor sídž jar nahrán do úložiště Azure. Další možností je zabalit soubor jar do vlastních iobrazů Dockeru.

Chcete-li tak `dockerfile` učinit, najděte `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` obrázek Spark umístěný v adresáři. Přidat `ADD` am prohlášení pro `jar` úlohu Spark někde mezi `WORKDIR` a `ENTRYPOINT` deklarace.

Aktualizujte cestu jar na `SparkPi-assembly-0.1.0-SNAPSHOT.jar` umístění souboru ve vývojovém systému. Můžete také použít vlastní jar soubor.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Vytvořte a posuňte obraz pomocí zahrnutých skriptů Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Při spuštění úlohy namísto označení adresy URL `local://` vzdálené nádoby lze schéma použít s cestou k souboru jar v bitové kopii Dockeru.

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
> Z [dokumentace][spark-docs]Spark : "Plánovač Kubernetes je v současné době experimentální. V budoucích verzích mohou být změny chování kolem konfigurace, image kontejneru a entrypoints".

## <a name="next-steps"></a>Další kroky

Další podrobnosti najdete v dokumentaci ke Sparku.

> [!div class="nextstepaction"]
> [Dokumentace k jiskrám][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
