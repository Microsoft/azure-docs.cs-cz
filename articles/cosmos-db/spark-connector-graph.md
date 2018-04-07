---
title: 'Azure Cosmos DB: Provádět analýzy grafu pomocí Spark a Apache TinkerPop Gremlin | Microsoft Docs'
description: Tento článek představuje pokyny pro nastavení a spuštění graf analýzy a paralelní výpočty v Azure DB Cosmos s Spark a TinkerPop SparkGraphComputer.
services: cosmosdb
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: sngun
ms.openlocfilehash: f44c7d432ac9c07daf9b99dd922f1dcd5de5d58e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: Proveďte analýzy grafu pomocí Spark a Apache TinkerPop Gremlin

[Azure Cosmos DB](introduction.md) je služba databáze globálně distribuované a více modelech od společnosti Microsoft. Můžete vytvořit a dotazovat dokumentu, klíč/hodnota a graf databáze, z nichž všechny využívat funkce globální distribuce a škálování vodorovných základem Azure Cosmos DB. Azure Cosmos DB podporuje online zpracování úlohy graf (OLTP), které používají transakcí [Apache TinkerPop Gremlin](graph-introduction.md).

[Spark](http://spark.apache.org/) je projekt Apache Software Foundation, který se zaměřuje na zpracování dat pro obecné účely online analytického zpracování (OLAP). Spark poskytuje hybridní v paměti nebo disk – na základě distribuovaná výpočetní model, který je podobný modelu Hadoop MapReduce. Apache Spark v cloudu můžete nasadit pomocí [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

Kombinací Azure Cosmos DB a Spark můžete provádět úlohy OLTP a technologií OLAP při použití Gremlin. Tento úvodní článek ukazuje, jak ke spouštění dotazů Gremlin Azure Cosmos DB v clusteru Azure HDInsight Spark.

## <a name="prerequisites"></a>Požadavky

Než budete moct tuto ukázku spustit, je potřeba splnit následující požadavky:
* Cluster Azure HDInsight Spark 2.0
* JDK 1.8 + (Pokud nemáte JDK, spusťte `apt-get install default-jdk`.)
* Maven (Pokud nemáte Maven, spusťte `apt-get install maven`.)
* Předplatné Azure ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Informace o tom, jak nastavit clusteru Azure HDInsight Spark najdete v tématu [clusterů HDInsight zřizování](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Vytvoření účtu Azure Cosmos DB databáze

Nejprve vytvořte účet databáze s rozhraní Graph API následujícím způsobem:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Přidání kolekce

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Získat Apache TinkerPop

Získáte Apache TinkerPop následujícím způsobem:

1. Vzdálené k hlavního uzlu clusteru HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Klonování TinkerPop3 zdrojový kód, ji vytvořit místně a nainstalujte ji do mezipaměti Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Instalace modulu plug-in Gremlin Spark 

    a. Instalace modulu plug-in se zpracovává souborem hroznového. Naplnění úložiště informace o hroznového, můžete si stáhnout modul plug-in a jeho závislé součásti. 

      Vytvoření hroznového konfiguračního souboru, pokud není přítomen v `~/.groovy/grapeConfig.xml`. Použijte následující nastavení:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Spusťte konzolu Gremlin `bin/gremlin.sh`.
        
    c. Instalace modulu plug-in Gremlin Spark s verzí 3.3.0-SNAPSHOT, který jste vytvořili v předchozích krocích:

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Zkontrolujte, zda `Hadoop-Gremlin` se aktivuje s `:plugin list`. Zakázat tento modul plug-in, protože by mohl narušovat modulu plug-in Gremlin Spark `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Příprava TinkerPop3 závislosti

Po TinkerPop3 jste vytvořili v předchozím kroku, tento proces také vyžádány všechny závislosti jar pro Spark a Hadoop v cílovém adresáři. Použijte JAR, které jsou předem nainstalované s HDI a vyžádá další závislosti pouze podle potřeby.

1. Přejděte do adresáře Gremlin konzole cílové v `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Přesunout všechny JAR pod `ext/` k `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Odeberte všechny jar knihovny pod `lib/` které nejsou v následujícím seznamu:

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Získat konektor Azure Cosmos DB Spark

1. Získat konektor Azure Cosmos DB Spark `azure-cosmosdb-spark-0.0.3-SNAPSHOT.jar` a Cosmos DB Java SDK `azure-documentdb-1.12.0.jar` z [konektoru služby Azure DB Spark Cosmos na Githubu](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. Alternativně můžete vytvořit ho místně. Protože nejnovější verzi Spark Gremlin byl sestaven s Spark 1.6.1 a není kompatibilní s Spark bodu 2.0.2, který je aktuálně používán konektor Azure Cosmos DB Spark, můžete sestavit kód nejnovější TinkerPop3 a nainstalujte JAR ručně. Udělejte toto:

    a. Klonování konektor Azure Cosmos DB Spark.

    b. Sestavení TinkerPop3 (neudělali v předchozích krocích). Nainstalujte všechny TinkerPop 3.3.0-SNAPSHOT JAR místně.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Aktualizace `tinkerpop.version` `azure-documentdb-spark/pom.xml` k `3.3.0-SNAPSHOT`.
    
    d. Sestavení s Maven. Potřebné JAR jsou umístěny v `target` a `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Zkopírujte dříve uvedených JAR do místního adresáře v ~ / azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuovat závislosti k pracovním uzlům Spark 

1. Protože transformaci dat grafu. závisí na TinkerPop3, je nutné distribuovat související závislosti pro všechny uzly pracovního procesu Spark.

2. Zkopírujte dříve uvedených Gremlin závislosti, CosmosDB Spark konektor jar a CosmosDB Java SDK k pracovním uzlům následujícím způsobem:

    a. Zkopírujte všechny JAR do `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. Získání seznamu všechny uzly pracovního procesu Spark, které můžete najít na řídicím panelu Ambari v `Spark2 Clients` v seznamu `Spark2` oddílu.

    c. Zkopírujte tento adresář na všech uzlech.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Nastavení proměnných prostředí

1. Najděte verzi softwaru HDP clusteru Spark. Je název adresáře v rámci `/usr/hdp/` (například 2.5.4.2-7).

2. Nastavte hdp.version pro všechny uzly. Na řídicím panelu Ambari, přejděte na **YARN části** > **konfigurací** > **Upřesnit**, a poté proveďte následující: 
 
    a. V `Custom yarn-site`, přidejte novou vlastnost `hdp.version` s hodnotou verze softwaru HDP na hlavní uzel. 
     
    b. Uložte konfiguraci. Existují upozornění, které můžete ignorovat. 
     
    c. Restartujte služby YARN a Oozie jako ikony oznámení označují.

3. Nastavte následující proměnné prostředí v hlavním uzlu (Nahraďte hodnoty podle potřeby):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Příprava konfiguraci grafu

1. Vytvoření konfiguračního souboru s Azure Cosmos DB parametry připojení a nastavení Spark a umístí jej na `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Aktualizace `spark.driver.extraClassPath` a `spark.executor.extraClassPath` zahrnout do adresáře JAR distribuované v předchozím kroku, se v tomto případě `/home/sshuser/azure-documentdb-spark/*`.

3. Zadejte následující podrobnosti pro Azure Cosmos DB:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Načíst TinkerPop grafu a uložit ho do Azure Cosmos DB
Abychom ukázali, jak se zachovat graf do Azure Cosmos databáze, tento příklad používá TinkerPop předdefinované TinkerPop moderní grafu. Graf je uložený ve formátu Kryo a je k dispozici v úložišti TinkerPop.

1. Protože Gremlin je spuštěn v režimu YARN, je třeba zpřístupnit data grafu v systému souborů Hadoop. Použijte následující příkazy a aby byl adresář zkopírujte soubor místní grafu do ní. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Dočasně aktualizovat `gremlin-spark.properties` soubor `GryoInputFormat` číst grafu. Taky jednat `inputLocation` jako adresář vytvoříte, jako v následujícím příkladu:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Spusťte konzolu Gremlin a pak vytvořte následující kroky výpočtu pro uložení dat do nakonfigurované kolekce Cosmos databázi Azure:  

    a. Vytvoření grafu `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Použít SparkGraphComputer pro zápis `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. V Průzkumníku dat můžete ověřit, že data se k databázi Azure Cosmos jako trvalý.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Načtení grafu ze Azure Cosmos DB a spouštět dotazy Gremlin

1. Chcete-li načíst graf, upravte `gremlin-spark.properties` nastavit `graphReader` k `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Načíst graf, procházet data a spouštět dotazy Gremlin ho následujícím způsobem:

    a. Spusťte konzolu Gremlin `bin/gremlin.sh`.

    b. Vytvoření grafu s konfigurací `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Vytvoření grafu traversal s SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Spusťte následující dotazy Gremlin grafu:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Pokud chcete zobrazit podrobnější protokolování, nastavte úroveň v protokolu `conf/log4j-console.properties` na podrobnější úrovni.
>

## <a name="next-steps"></a>Další postup

V tomto článku úvodní jsme zjistili, jak pracovat s grafy kombinací Azure Cosmos DB a Spark.

> [!div class="nextstepaction"]
