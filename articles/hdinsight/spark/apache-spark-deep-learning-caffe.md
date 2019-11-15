---
title: Použití Caffe na Azure HDInsight Spark pro distribuované hloubkové učení
description: Pro distribuované hloubkové učení ve službě Azure HDInsight použijte Caffe na Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: e5988bf1955502d89cc31bcc30672de983a399ec
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083348"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Použití Caffe na Azure HDInsight Spark pro distribuované hloubkové učení

## <a name="introduction"></a>Úvod

Obsáhlý Learning má vliv na vše od zdravotní péče až po dopravu do výroby a další. Společnosti usnadňují podrobné učení, aby vyřešily pevné problémy, jako je [klasifikace obrázků](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [rozpoznávání řeči](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), rozpoznávání objektů a strojový překlad.

Existuje [spousta oblíbených platforem](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), včetně [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano atd. [Caffe](https://caffe.berkeleyvision.org/) je jedním z nejvíce slavnýchch nesymbolických (imperativních) neuronové síťových rozhraní a často se používá v mnoha oblastech, včetně počítačové vize. [CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark) navíc spojuje Caffe s Apache Spark. v takovém případě je možné snadno použít rozsáhlou výuku v existujícím clusteru Hadoop. Obsáhlý Learning můžete použít spolu s kanály Spark ETL, snížením složitosti systému a latencí pro kompletní výuku řešení.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) je cloudová Apache Hadoop nabídka, která poskytuje optimalizované Open Source analytické clustery pro Apache Spark, Apache Hive, Apache Hadoop, Apache hba, Apache Storm, Apache Kafka a ml služby. HDInsight je zajištěné smlouvou SLA 99,9%. Každou z těchto technologií pro velké objemy dat a ISV aplikace je snadno nasaditelné jako spravované clustery se zabezpečením a monitorováním pro podniky.

Tento článek ukazuje, jak nainstalovat [Caffe ve Sparku](https://github.com/yahoo/CaffeOnSpark) pro cluster HDInsight. V tomto článku se také používá integrovaná ukázka MNIST ručně zapsaných, která ukazuje, jak používat distribuovaný hloubkový Learning pomocí HDInsight Spark na procesorech.

Úkol se dá provést čtyřmi kroky:

1. Nainstalovat požadované závislosti na všech uzlech
2. Sestavování Caffe na Spark pro HDInsight na hlavní uzel
3. Distribuujte požadované knihovny do všech pracovních uzlů.
4. Vytvořte Caffe model a spusťte ho distribuovaným způsobem.

Vzhledem k tomu, že HDInsight je PaaS řešení, nabízí skvělé funkce platformy – díky tomu je snadné provádět některé úlohy. Jedna z funkcí, které se používají v tomto blogovém příspěvku, se nazývá [akce skriptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), pomocí které můžete provádět příkazy prostředí pro přizpůsobení uzlů clusteru (hlavní uzel, pracovní uzel nebo hraniční uzel).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Krok 1: instalace požadovaných závislostí na všech uzlech

Abyste mohli začít, musíte nainstalovat závislosti. Lokalita Caffe a [CaffeOnSpark web](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) nabízí několik užitečných wiki pro instalaci závislostí pro Spark v režimu příze. HDInsight používá také Spark v režimu PŘÍZe. Pro platformu HDInsight ale potřebujete přidat několik dalších závislostí. K tomu je třeba použít akci skriptu a spustit ji ve všech hlavních uzlech a pracovních uzlech. Tato akce skriptu trvá přibližně 20 minut, protože závislosti závisejí i na dalších balíčcích. Měli byste ho umístit do nějakého umístění, které je přístupné pro váš cluster HDInsight, jako je například umístění GitHubu nebo výchozí účet BLOB Storage.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"

Akce skriptu obsahuje dva kroky. Prvním krokem je instalace všech požadovaných knihoven. Tyto knihovny zahrnují potřebné knihovny pro kompilaci Caffe (například Gflags, glog) a spouštění Caffe (například NumPy). používáte libatlas pro optimalizaci procesoru, ale při instalaci dalších knihoven optimalizace, jako je MKL nebo CUDA (pro GPU), můžete vždy postupovat podle wikiwebu CaffeOnSpark.

Druhým krokem je stažení, kompilování a instalace protobuf 2.5.0 pro Caffe během běhu. [Vyžaduje](https://github.com/yahoo/CaffeOnSpark/issues/87)se Protobuf 2.5.0, ale tato verze není k dispozici jako balíček na Ubuntu 16, takže je potřeba ji zkompilovat ze zdrojového kódu. K dispozici je také několik prostředků na internetu, jak je zkompilovat. Další informace najdete [tady](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Chcete-li začít, stačí spustit tuto akci skriptu pro váš cluster na všechny uzly pracovních procesů a hlavní uzly (pro HDInsight 3,5). Můžete buď spustit akce skriptu pro existující cluster, nebo použít akce skriptu během vytváření clusteru. Další informace o akcích skriptu najdete v dokumentaci [zde](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Akce skriptů pro instalaci závislostí](./media/apache-spark-deep-learning-caffe/submit-script-action.png)

## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Krok 2: sestavování Caffe na Apache Spark pro HDInsight na hlavní uzel

Druhým krokem je sestavování Caffe na hlavnímu uzlu a následné distribuci kompilovaných knihoven do všech pracovních uzlů. V tomto kroku musíte [ke svému hlavnímu uzluu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Potom je nutné postupovat podle [procesu sestavení CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Níže je skript, který můžete použít k sestavování CaffeOnSpark pomocí několika dalších kroků.

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Možná budete muset udělat víc, než kolik dokumentace CaffeOnSpark říká. Změny jsou následující:
- Změňte pouze na CPU a k tomuto konkrétnímu účelu použijte libatlas.
- Datové sady vložte do úložiště objektů BLOB, což je sdílené umístění, které je přístupné pro všechny uzly pracovního procesu pro pozdější použití.
- Zkompilované knihovny Caffe umístěte do úložiště objektů BLOB a později je zkopírujte do všech uzlů pomocí akcí skriptu, aby nedocházelo k další době kompilace.

### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Řešení potíží: došlo k chybě ANT BuildException: exec vráceno: 2

Při prvním pokusu o sestavení CaffeOnSpark se někdy říká

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Vyčistěte úložiště kódu pomocí příkazu "vyčistit" a potom spuštěním příkazu "vytvořit sestavení" vyřešte tento problém, pokud máte správné závislosti.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Řešení potíží: časový limit připojení úložiště Maven

Někdy Maven poskytuje chybu časového limitu připojení, podobně jako následující fragment kódu:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Po několika minutách se musíte opakovat.

### <a name="troubleshooting-test-failure-for-caffe"></a>Řešení potíží: selhání testu pro Caffe

Při závěrečné kontrole CaffeOnSpark se pravděpodobně zobrazí chyba testu. To se pravděpodobně týká kódování UTF-8, ale nemělo by mít vliv na použití Caffe.

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Krok 3: distribuce požadovaných knihoven do všech pracovních uzlů

Dalším krokem je distribuce knihoven (v podstatě knihovny v CaffeOnSpark/Caffe-Public/Distributed/lib/a CaffeOnSpark/Caffe-distribuční/Distribute/lib/) na všechny uzly. V kroku 2 umístíte tyto knihovny do úložiště objektů BLOB a v tomto kroku použijete akce skriptů ke zkopírování do všech hlavních uzlů a pracovních uzlů.

Chcete-li to provést, spusťte akci skriptu, jak je znázorněno v následujícím fragmentu kódu:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Ujistěte se, že je potřeba nasměrovat do správného umístění, které je specifické pro váš cluster.)

Vzhledem k tomu, že v kroku 2 ho umístíte do úložiště objektů BLOB, které je přístupné pro všechny uzly, v tomto kroku ho stačí zkopírovat do všech uzlů.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Krok 4: vytvoření modelu Caffe a jeho spuštění distribuovaným způsobem

Caffe se nainstaluje po spuštění předchozích kroků. Dalším krokem je vytvoření modelu Caffe. 

Caffe používá "" užitečnou architekturu ", kde pro sestavování modelu stačí definovat pouze konfigurační soubor a bez kódování (ve většině případů). Pojďme si to udělat tady. 

Model, který je výukový, je ukázkový model pro školení MNIST ručně zapsaných. Databáze MNIST ručně zapsaných psaných číslic má školicí sadu 60 000 příkladů a sadu testů 10 000 příkladů. Je podmnožinou větší sady, která je k dispozici z NIST. Číslice mají normalizovanou velikost a jsou umístěné ve středu obrázku s pevnou velikostí. CaffeOnSpark obsahuje některé skripty pro stažení této datové sady a jejich převádění do správného formátu.

CaffeOnSpark poskytuje některé síťové topologie jako příklad pro školení MNIST ručně zapsaných. Má dobrý návrh na rozdělení síťové architektury (topologie sítě) a optimalizaci. V tomto případě jsou vyžadovány dva soubory:

k zobrazení optimalizace a generování aktualizací parametrů se používá soubor Řešitel ($ {CAFFE_ON_SPARK}/data/lenet_memory_solver. prototxt). Například definuje, zda se používá CPU nebo GPU, co je potenciál, kolik iterací atd. Také definuje, která topologie sítě neuron by měla používat program (což je druhý soubor, který potřebujete). Další informace o Řešiteli najdete v [dokumentaci k Caffe](https://caffe.berkeleyvision.org/tutorial/solver.html).

V tomto příkladu, protože používáte procesor spíše než GPU, byste měli poslední řádek změnit na:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Příklad konfigurace HDInsight Caffe](./media/apache-spark-deep-learning-caffe/caffe-configuration1.png
)

Podle potřeby můžete změnit další řádky.

Druhý soubor ($ {CAFFE_ON_SPARK}/data/lenet_memory_train_test. prototxt) definuje, jak bude síť neuron vypadat jako a příslušný vstupní a výstupní soubor. také je nutné soubor aktualizovat tak, aby odrážel umístění školicích dat. Změňte následující část v lenet_memory_train_test. prototxt (musíte odkazovat na správné umístění specifické pro váš cluster):

- Změňte "File:/Users/mridul/bigml/demodl/mnist_train_lmdb" na "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"
- Změňte "File:/Users/mridul/bigml/demodl/mnist_test_lmdb/" na "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Příklad konfigurace HDInsight Caffe](./media/apache-spark-deep-learning-caffe/caffe-configuration2.png)

Další informace o tom, jak definovat síť, najdete v [dokumentaci k Caffe v datové sadě mnist ručně zapsaných](https://caffe.berkeleyvision.org/gathered/examples/mnist.html) .

Pro účely tohoto článku použijete tento MNIST ručně zapsaných příklad. Z hlavního uzlu spusťte následující příkazy:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Předchozí příkaz distribuuje požadované soubory (lenet_memory_solver. prototxt a lenet_memory_train_test. prototxt) do každého kontejneru PŘÍZe. Příkaz také nastaví příslušnou cestu jednotlivých ovladačů a prováděcích modulů Spark na LD_LIBRARY_PATH. LD_LIBRARY_PATH je definována v předchozím fragmentu kódu a odkazuje na umístění, které obsahuje knihovny CaffeOnSpark. 

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení potíží

Vzhledem k tomu, že používáte režim clusteru PŘÍZe, v takovém případě se ovladač Spark naplánuje na libovolný kontejner (a na libovolný pracovní uzel), který byste měli vidět jenom v konzole, jako je například:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Pokud chcete zjistit, co se stalo, je obvykle potřeba získat protokol ovladače Spark, který obsahuje další informace. V takovém případě je nutné přejít na uživatelské rozhraní PŘÍZe a najít relevantní protokoly PŘÍZe. Pomocí této adresy URL můžete získat uživatelské rozhraní PŘÍZe:

    https://yourclustername.azurehdinsight.net/yarnui

![zobrazení prohlížeče pro službu Apache příze v prohlížeči](./media/apache-spark-deep-learning-caffe/apache-yarn-window-1.png)

Můžete si prohlédnout, kolik prostředků je pro tuto konkrétní aplikaci přiděleno. Můžete kliknout na odkaz Scheduler (Plánovač) a potom uvidíte, že pro tuto aplikaci jsou spuštěné devět kontejnerů. požádáte o PŘÍZi, aby poskytovala osm prováděcích modulů, a další kontejner pro proces ovladače. 

![Zobrazení plánovače HDI Apache nitě](./media/apache-spark-deep-learning-caffe/apache-yarn-scheduler.png)

Pokud dojde k selhání, můžete chtít ověřit protokoly ovladačů nebo protokoly kontejnerů. V případě protokolů ovladačů můžete kliknout na ID aplikace v uživatelském rozhraní PŘÍZe a potom kliknout na tlačítko protokoly. Protokoly ovladačů se zapisují do stderr.

![zobrazení prohlížeče oken v Apache nitě](./media/apache-spark-deep-learning-caffe/apache-yarn-window-2.png)

Například se může zobrazit některá z následujících chyb z protokolů ovladačů, což značí, že přidělíte příliš mnoho prováděcích modulů.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Někdy k problému může dojít v vykonavatelích, nikoli v ovladačích. V takovém případě je nutné ověřit protokoly kontejneru. Vždy můžete získat protokoly kontejneru a pak získat kontejner, který selhal. Tuto chybu můžete například splnit při spuštění Caffe.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

V takovém případě musíte získat ID kontejneru, který selhal (ve výše uvedeném případě je container_1485916338528_0008_05_000005). Pak je potřeba spustit 

    yarn logs -containerId container_1485916338528_0008_03_000005

z hlavnímu uzlu. Po kontrole selhání kontejneru je to způsobeno použitím režimu GPU (místo toho byste měli použít režim procesoru) v lenet_memory_solver. prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.

## <a name="getting-results"></a>Získávání výsledků

Vzhledem k tomu, že přidělujete 8 prováděcích modulů a topologie sítě je jednoduchá, měla by být výsledkem spuštění pouze zhruba 30 minut. Z příkazového řádku vidíte, že model vložíte do wasb:///mnist.model a výsledky vložíte do složky s názvem wasb:///mnist_features_result.

Výsledky můžete získat spuštěním

    hadoop fs -cat hdfs:///mnist_features_result/*

a výsledek vypadá takto:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID představuje ID v datové sadě MNIST ručně zapsaných a popisek je číslo, které model identifikuje.

## <a name="conclusion"></a>Závěr

V této dokumentaci jste se pokusili nainstalovat CaffeOnSpark se spuštěným jednoduchým příkladem. HDInsight je plně spravovaná cloudová platforma pro distribuované výpočetní prostředí a je to nejlepší místo pro provoz strojového učení a pokročilé analýzy pro velkou datovou sadu a pro distribuované hloubkové učení. k zajištění hloubkového učení můžete použít Caffe v HDInsight Spark. provádění.

## <a name="seealso"></a>Viz také

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
