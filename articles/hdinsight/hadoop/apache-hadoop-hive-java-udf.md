---
title: Uživatelem definovaná funkce Java (UDF) s Apache Hive Azure HDInsight
description: Přečtěte si, jak vytvořit uživatelsky definovanou funkci (UDF) založenou na javě, která funguje s Apache Hive. Tento příklad UDF převede tabulku textových řetězců na malá písmena.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327196"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Použití Java UDF s Apache Hive v HDInsight

Přečtěte si, jak vytvořit uživatelsky definovanou funkci (UDF) založenou na javě, která funguje s Apache Hive. Java UDF v tomto příkladu převede tabulku textových řetězců na znaky s velkým a malých písmen.

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalován](https://maven.apache.org/install.html) podle Apache.  Maven je systém vytváření projektů pro java projekty.
* [Schéma URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To by bylo wasb:// pro Azure Storage, abfs:// pro Azure Data Lake Storage Gen2 nebo adl:// pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI bude `wasbs://`.  Viz také [bezpečný přenos](../../storage/common/storage-require-secure-transfer.md).

* Textový editor nebo Java IDE

    > [!IMPORTANT]  
    > Pokud vytvoříte soubory Pythonu v klientovi systému Windows, musíte použít editor, který používá LF jako konec řádku. Pokud si nejste jisti, zda editor používá LF nebo CRLF, postup odebrání znaku CR najdete v části [Poradce při potížích.](#troubleshooting)

## <a name="test-environment"></a>Testovací prostředí

Prostředí používané pro tento článek byl počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upraveny pomocí poznámkového bloku. Upravte odpovídajícím způsobem pro vaše prostředí.

Z příkazového řádku zadejte níže uvedené příkazy a vytvořte pracovní prostředí:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Vytvoření příkladu Java UDF

1. Vytvořte nový projekt Maven zadáním následujícího příkazu:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Tento příkaz vytvoří `exampleudf`adresář s názvem , který obsahuje projekt Maven.

2. Po vytvoření projektu odstraňte `exampleudf/src/test` adresář, který byl vytvořen jako součást projektu, zadáním následujícího příkazu:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Otevřít `pom.xml` zadáním níže uvedeného příkazu:

    ```cmd
    notepad pom.xml
    ```

    Poté nahraďte existující `<dependencies>` položku následujícím xml:

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

    Tyto položky určují verzi Hadoop a Hive, která je součástí HDInsight 3.6. Informace o verzích Hadoopu a Hive s rozhraním HDInsight naleznete v dokumentu [pro správu verzí komponent HDInsight.](../hdinsight-component-versioning.md)

    Přidejte `<build>` oddíl `</project>` před řádek na konec souboru. Tato část by měla obsahovat následující xml:

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
                <version>3.2.1</version>
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

    Tyto položky definují způsob sestavení projektu. Konkrétně verze Java, který projekt používá a jak vytvořit uberjar pro nasazení do clusteru.

    Po provedení změn soubor uložte.

4. Chcete-li vytvořit a otevřít nový `ExampleUDF.java`soubor, zadejte následující příkaz :

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Pak zkopírujte a vložte java kód níže do nového souboru. Potom soubor zavřete.

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

    Tento kód implementuje UDF, který přijímá řetězcovou hodnotu a vrací malou verzi řetězce.

## <a name="build-and-install-the-udf"></a>Sestavení a instalace udf

V níže uvedených `sshuser` příkazech nahraďte skutečným uživatelským jménem, pokud se liší. Nahraďte `mycluster` skutečným názvem clusteru.

1. Zkompilujte a zabalte UDF zadáním následujícího příkazu:

    ```cmd
    mvn compile package
    ```

    Tento příkaz vytvoří a zabalí `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` UDF do souboru.

2. Pomocí `scp` příkazu zkopírujte soubor do clusteru HDInsight zadáním následujícího příkazu:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Připojte se ke clusteru pomocí SSH zadáním následujícího příkazu:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Z otevřené relace SSH zkopírujte soubor jar do úložiště HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Použití UDF z Úlu

1. Spusťte klienta Beeline z relace SSH zadáním následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Tento příkaz předpokládá, že jste použili výchozí **nastavení správce** pro přihlašovací účet pro váš cluster.

2. Jakmile se dostanete `jdbc:hive2://localhost:10001/>` na výzvu, zadejte následující přidat UDF do Hive a vystavit jako funkci.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Pomocí udf převést hodnoty načtené z tabulky na řetězce s malá písmena.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Tento dotaz vybere stav z tabulky, převede řetězec na malá písmena a zobrazí je spolu s nezměněným názvem. Výstup se podobá následujícímu textu:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění úlohy podregistru se může naskytnout chyba podobná následujícímu textu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Tento problém může být způsoben zakončení řádků v souboru Pythonu. Mnoho editorů systému Windows výchozí použití CRLF jako konec řádku, ale linuxové aplikace obvykle očekávají LF.

Před nahráním souboru do HDInsightu můžete odebrat následující příkazy prostředí PowerShell:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Další kroky

Další způsoby práce s Hive najdete v [tématu Použití Apache Hive s HDInsight](hdinsight-use-hive.md).

Další informace o uživatelem definovaných funkcích Hive najdete v článku [Apache Hive Operators and User-Defined Functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) na wiki Hive na apache.org.
