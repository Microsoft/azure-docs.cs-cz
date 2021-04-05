---
title: Uživatelsky definovaná funkce Java (UDF) s Apache Hivem Azure HDInsight
description: Naučte se vytvořit uživatelsky definovanou funkci (UDF) založenou na jazyce Java, která funguje s Apache Hive. V tomto příkladu UDF převede tabulku textových řetězců na malá.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946568"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Použití Java UDF s Apache Hive v HDInsight

Naučte se vytvořit uživatelsky definovanou funkci (UDF) založenou na jazyce Java, která funguje s Apache Hive. Jazyk Java UDF v tomto příkladu převede tabulku textových řetězců na všechny znaky s malými písmeny.

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) verze 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.
* [Schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To je wasb://pro Azure Storage, abfs://pro Azure Data Lake Storage Gen2 nebo adl://pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` .  Viz také [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

* Textový editor nebo Java IDE

    > [!IMPORTANT]  
    > Pokud vytvoříte soubory Pythonu na klientovi se systémem Windows, je nutné použít editor, který jako konec řádku používá LF. Pokud si nejste jistí, jestli editor používá LF nebo CRLF, přečtěte si část [řešení potíží](#troubleshooting) , kde najdete postup odebrání znaku CR.

## <a name="test-environment"></a>Testovací prostředí

Prostředí použité pro tento článek bylo počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upraveny pomocí poznámkového bloku. Upravte odpovídajícím způsobem pro vaše prostředí.

Z příkazového řádku zadejte níže uvedené příkazy pro vytvoření funkčního prostředí:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Vytvoření ukázkového Java UDF

1. Zadáním následujícího příkazu vytvořte nový projekt Maven:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Tento příkaz vytvoří adresář s názvem `exampleudf` , který obsahuje projekt Maven.

2. Po vytvoření projektu odstraňte `exampleudf/src/test` adresář, který byl vytvořen jako součást projektu, zadáním následujícího příkazu:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Otevřete zadáním `pom.xml` následujícího příkazu:

    ```cmd
    notepad pom.xml
    ```

    Pak existující položku nahraďte `<dependencies>` následujícím kódem XML:

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

    Tyto položky určují verzi Hadoop a podregistr, který je součástí HDInsight 3,6. Můžete najít informace o verzích Hadoop a podregistru dodaných v HDInsight z dokumentu [správy verzí komponent HDInsight](../hdinsight-component-versioning.md) .

    Přidejte `<build>` oddíl před `</project>` řádek na konci souboru. Tato část by měla obsahovat následující kód XML:

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

    Tyto položky definují, jak sestavit projekt. Konkrétně verze jazyka Java, kterou projekt používá, a postup sestavení uberjar pro nasazení do clusteru.

    Až změny provedete, soubor uložte.

4. Zadáním následujícího příkazu vytvořte a otevřete nový soubor `ExampleUDF.java` :

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Pak zkopírujte a vložte kód Java níže do nového souboru. Pak soubor zavřete.

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

    Tento kód implementuje systém souborů UDF, který přijímá řetězcovou hodnotu, a vrátí verzi řetězce v malých písmenech.

## <a name="build-and-install-the-udf"></a>Sestavení a instalace systému souborů UDF

V následujících příkazech nahraďte `sshuser` skutečným uživatelským jménem, pokud se liší. Nahraďte `mycluster` skutečným názvem clusteru.

1. Zkompilujte a zabalite systém souborů UDF zadáním následujícího příkazu:

    ```cmd
    mvn compile package
    ```

    Tento příkaz sestaví a zabalí systém souborů UDF do `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` souboru.

2. Pomocí `scp` příkazu zkopírujte soubor do clusteru HDInsight zadáním následujícího příkazu:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Připojte se ke clusteru pomocí SSH zadáním následujícího příkazu:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Z otevřené relace SSH zkopírujte soubor JAR do úložiště HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Použití UDF z podregistru

1. Spusťte klienta Beeline z relace SSH zadáním následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Tento příkaz předpokládá, že jste pro svůj cluster použili výchozí účet **správce** .

2. Až se zobrazí `jdbc:hive2://localhost:10001/>` výzva, zadejte následující příkaz pro přidání systému souborů UDF do podregistru a jeho vystavení jako funkce.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Pomocí systému souborů UDF převeďte hodnoty načtené z tabulky do malých řetězců Case.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Tento dotaz vybere stav z tabulky, převede řetězec na malá písmena a pak je zobrazí spolu s nezměněným názvem. Výstup se zobrazí podobně jako následující text:

    ```output
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
    ```

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění úlohy podregistru se může vycházet z chyby podobné následujícímu textu:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Tento problém může být způsoben koncem řádku v souboru Python. Mnoho editorů Windows ve výchozím nastavení používá klávesu CRLF jako zakončení řádku, ale aplikace pro Linux obvykle očekávají LF.

Před nahráním souboru do HDInsight můžete pomocí následujících příkazů PowerShellu odebrat znaky CR:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Další kroky

Další způsoby práce s podregistrem najdete v tématu [použití Apache Hive se službou HDInsight](hdinsight-use-hive.md).

Další informace o funkcích User-Defined podregistru najdete v části [operátory Apache Hive a User-Defined funkce](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) na wikiwebu na wikiwebu na adrese Apache.org.