---
title: Java uživatelem definované funkce (UDF) pomocí Hivu ve službě HDInsight – Azure
description: Zjistěte, jak vytvořit založené na jazyce Java uživatelem definované funkce (UDF), která funguje pomocí Hive. Tento příklad převede UDF tabulku textové řetězce na malá písmena.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: b2a4c7ce3ac91ade497ca59a8c2ca4fe642811a9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047499"
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Použijte Java UDF pomocí Hivu ve službě HDInsight

Zjistěte, jak vytvořit založené na jazyce Java uživatelem definované funkce (UDF), která funguje pomocí Hive. Java UDF v tomto příkladu převede tabulku textovými řetězci na všechna malá znaků.

## <a name="requirements"></a>Požadavky

* HDInsight cluster 

    > [!IMPORTANT]
    > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

    Většinu kroků v tomto dokumentu fungovat na obou clusterech založených na Windows a Linuxu. Nicméně jsou specifické pro linuxových clusterech postupem nahrání kompilované UDF do clusteru a spustíme ji. Jsou uvedeny odkazy na informace, které lze použít s clustery se systémem Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 nebo novější (nebo ekvivalentní, například OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Textový editor a integrované vývojové prostředí Java

    > [!IMPORTANT]
    > Pokud vytvoříte soubory Pythonu na klientovi Windows, je nutné použít editor, který používá LF jako ukončení řádku. Pokud si nejste jisti, zda editor používá LF nebo CRLF, přečtěte si článek [Poradce při potížích s](#troubleshooting) najdete kroky k odebrání znak CR.

## <a name="create-an-example-java-udf"></a>Vytvořte příklad Java UDF 

1. Z příkazového řádku použijte následující postupy k vytvoření nového projektu Maven:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Pokud používáte PowerShell, je nutné umístit uvozovky kolem parametry. Například, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Tento příkaz vytvoří adresář s názvem **exampleudf**, která obsahuje projekt Maven.

2. Po vytvoření projektu, odstraňte **exampleudf/src/testování** adresář, který byl vytvořen jako součást projektu.

3. Otevřít **exampleudf/pom.xml**a nahraďte existující `<dependencies>` položku s následující kód XML:

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

    Tyto položky určit verzi Hadoop a Hive součástí HDInsight 3.6. Můžete najít informace o verzích Hadoop a Hive s HDInsight z [Správa verzí komponenty HDInsight](../hdinsight-component-versioning.md) dokumentu.

    Přidat `<build>` části před `</project>` řádek na konec souboru. Tento oddíl by měl obsahovat následující kód XML:

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

    Tyto položky definovat, jak sestavit projekt. Konkrétně verzi jazyka Java, která používá projekt a jak sestavit uberjar pro nasazení do clusteru.

    Po provedení změn uložte soubor.

4. Přejmenovat **exampleudf/src/main/java/com/microsoft/examples/App.java** k **ExampleUDF.java**a pak otevřete soubor v editoru.

5. Nahraďte obsah **ExampleUDF.java** souboru následujícím kódem a pak soubor uložte.

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

    Tento kód implementuje UDF, která přijímá řetězcovou hodnotu a vrátí malá verze řetězce.

## <a name="build-and-install-the-udf"></a>Sestavit a nainstalovat UDF

1. Ke kompilaci a balíček UDF, použijte následující příkaz:

    ```bash
    mvn compile package
    ```

    Tento příkaz sestaví a zabalí UDF do `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` souboru.

2. Použití `scp` příkaz zkopírujte soubor do clusteru HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Nahraďte `myuser` pomocí uživatelského účtu SSH pro váš cluster. Nahraďte `mycluster` s názvem clusteru. Pokud jste použili heslo k zabezpečení účtu SSH, zobrazí se výzva k zadání hesla. Pokud jste použili certifikát, budete možná muset použít `-i` parametr k určení souboru privátního klíče.

3. Připojte se ke clusteru pomocí SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Z relace SSH zkopírujte soubor jar do úložiště HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Použití systému souborů UDF v Hivu

1. Pomocí následujících spusťte Beeline klienta z relace SSH.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Tento příkaz předpokládá, že jste použili výchozí **správce** pro přihlašovací účet pro váš cluster.

2. Až přijedete `jdbc:hive2://localhost:10001/>` výzva, zadejte následující příkaz pro přidání UDF Hive a zpřístupnit ji jako funkce.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Tento příklad předpokládá, že Azure Storage je výchozí úložiště pro cluster. Pokud váš cluster používá místo toho Data Lake Store, změňte `wasb:///` hodnota, která se `adl:///`.

3. UDF použijte k převodu hodnoty získané z tabulky na řetězce na malá písmena.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Tento dotaz vybere platformy zařízení (Android, Windows, iOS, atd.) z tabulky, převod řetězce na malá a velká nižší a jejich zobrazení. Zobrazí výstup podobný následujícímu textu:

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

Další způsoby, jak pracovat s Hive, najdete v části [použití Hivu se službou HDInsight](hdinsight-use-hive.md).

Další informace o funkcích Hive User-Defined, naleznete v tématu [Hive operátory a uživatelsky definovaných funkcí](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) část wiki Hive na webu apache.org.
