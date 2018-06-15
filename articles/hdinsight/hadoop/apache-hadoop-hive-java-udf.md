---
title: Java uživatelem definované funkce (UDF) s Hive v HDInsight - Azure | Microsoft Docs
description: Postup vytvoření založené na jazyce Java uživatelem definované funkce (UDF), funguje s Hive. Tento příklad UDF převede tabulku textové řetězce na malá písmena.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 00af8ca67af6ba3242c0fee6c50640944768ec4c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200754"
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Použít Java UDF s Hive v HDInsight

Postup vytvoření založené na jazyce Java uživatelem definované funkce (UDF), funguje s Hive. Java UDF v tomto příkladu převede tabulku textové řetězce na malá všechny znaky.

## <a name="requirements"></a>Požadavky

* Cluster služby HDInsight 

    > [!IMPORTANT]
    > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

    Většina kroky v tomto dokumentu fungovat na obou clusterech se systémem Windows a Linux. Nicméně jsou specifické pro clustery se systémem Linux s postupem nahrání kompilované UDF do clusteru a potom ho spusťte. Jsou uvedeny odkazy na informace, které lze použít s clustery se systémem Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 nebo novější (nebo ekvivalentní, jako je například OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Textového editoru nebo Java IDE

    > [!IMPORTANT]
    > Pokud vytvoříte soubory Python v klientovi Windows, musíte použít editor, který používá LF jako ukončování řádků. Pokud si nejste jisti, zda vaše editor používá LF nebo Line FEED, přečtěte si téma [Poradce při potížích s](#troubleshooting) na odebrání znak CR kroky v sekci.

## <a name="create-an-example-java-udf"></a>Vytvořte například Java UDF 

1. Z příkazového řádku použijte následující postupy k vytvoření nového projektu Maven:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Pokud používáte prostředí PowerShell, je nutné umístit parametry v uvozovkách. Například, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Tento příkaz vytvoří adresář s názvem **exampleudf**, který obsahuje projekt Maven.

2. Po vytvoření projektu odstranit **exampleudf/src/testování** adresář, který byl vytvořen jako součást projektu.

3. Otevřete **exampleudf/pom.xml**a nahradit existující `<dependencies>` položka se následující kód XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Tyto položky zadejte verzi systému Hadoop a Hive součástí HDInsight 3.6. Informace o verzích Hadoop a Hive s HDInsight z můžete najít [Správa verzí komponenty HDInsight](../hdinsight-component-versioning.md) dokumentu.

    Přidat `<build>` části před `</project>` řádek na konec souboru. V této části by měl obsahovat následující kód XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Tyto položky definovat, jak sestavte projekt. Konkrétně verze Java, která používá projektu a jak sestavit uberjar pro nasazení do clusteru.

    Po provedení změn uložte soubor.

4. Přejmenování **exampleudf/src/main/java/com/microsoft/examples/App.java** k **ExampleUDF.java**a pak otevřete soubor ve svém editoru.

5. Nahraďte obsah **ExampleUDF.java** soubor s následující a pak soubor uložte.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Tento kód implementuje UDF, který přijímá hodnotu řetězce a vrátí malá verzi řetězce.

## <a name="build-and-install-the-udf"></a>Sestavení a nainstalujte UDF

1. Pro zkompilování a balíček UDF použijte následující příkaz:

    ```bash
    mvn compile package
    ```

    Tento příkaz vytvoří a balíčky UDF do `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` souboru.

2. Použití `scp` příkaz pro kopírování souboru do clusteru HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Nahraďte `myuser` pomocí uživatelského účtu SSH pro váš cluster. Nahraďte `mycluster` se název clusteru. Pokud jste použili heslo k zabezpečení účtu SSH, zobrazí se výzva k zadání hesla. Pokud jste použili certifikát, budete možná muset použít `-i` parametr k určení souboru privátního klíče.

3. Připojte se ke clusteru pomocí protokolu SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Z relace SSH zkopírujte soubor jar do úložiště HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Použití UDF z Hive

1. Použijte následující spuštění klienta Beeline z relace SSH.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Tento příkaz předpokládá, že jste použili výchozí **správce** pro přihlašovací účet pro váš cluster.

2. Až přijedete do `jdbc:hive2://localhost:10001/>` výzva, zadejte následující příkaz a přidejte UDF do Hive a vystavit jako funkce.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Tento příklad předpokládá, že Azure Storage je výchozí úložiště pro cluster. Pokud váš cluster používá místo Data Lake Store, změňte `wasb:///` hodnotu `adl:///`.

3. Použijte UDF k převedení hodnoty získané z tabulky na řetězce na malá písmena.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Tento dotaz vybere platforem zařízení (Android, Windows, iOS, atd.) z tabulky, převést řetězec, který má nižší případ a jejich zobrazení. Výstup se zobrazí podobná následující text:

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Další postup

Další způsoby, jak pracovat s Hive, najdete v části [používání Hive s HDInsight](hdinsight-use-hive.md).

Další informace týkající se Hive User-Defined funkcí najdete v tématu [Hive operátory a funkce definované uživatelem](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) části wiki Hive na apache.org.
