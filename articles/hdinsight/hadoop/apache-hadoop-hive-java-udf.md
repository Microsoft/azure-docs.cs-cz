---
title: Java uživatelem definované funkce (UDF) s Apache Hive v HDInsight – Azure
description: Zjistěte, jak vytvořit založené na jazyce Java uživatelem definované funkce (UDF), která funguje s Apache Hive. Tento příklad převede UDF tabulku textové řetězce na malá písmena.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: b8417fe4c15259a7fd485254cf9edd2c8c082e92
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629700"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Použijte Java UDF Apache Hive v HDInsight

Zjistěte, jak vytvořit založené na jazyce Java uživatelem definované funkce (UDF), která funguje s Apache Hive. Java UDF v tomto příkladu převede tabulku textovými řetězci na všechna malá znaků.

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks)
* [Nástroje Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) podle Apache.  Maven je projekt sestavovacího systému pro projekty Java.
* [Schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) jako primární úložiště vašich clusterů. To může být wasb: / / pro službu Azure Storage, abfs: / / pro Azure Data Lake Storage Gen2 nebo adl: / / pro Azure Data Lake Storage Gen1. Pokud pro Azure Storage nebo Azure Data Lake Storage Gen2 je povoleno zabezpečený přenos, identifikátor URI by wasbs: / / nebo abfss: / /, respektive naleznete také [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

* Textový editor a integrované vývojové prostředí Java

    > [!IMPORTANT]  
    > Pokud vytvoříte soubory Pythonu na klientovi Windows, je nutné použít editor, který používá LF jako ukončení řádku. Pokud si nejste jisti, zda editor používá LF nebo CRLF, přečtěte si článek [Poradce při potížích s](#troubleshooting) najdete kroky k odebrání znak CR.

## <a name="test-environment"></a>Testovací prostředí
Prostředí, používá pro účely tohoto článku byl počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upravit článek přeložený překladatelem Poznámkový blok. Změňte je odpovídajícím způsobem pro vaše prostředí.

Z příkazového řádku zadejte následující příkazy k vytvoření pracovní prostředí:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Vytvořte příklad Java UDF

1. Vytvořte nový projekt Maven s tak, že zadáte následující příkaz:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Tento příkaz vytvoří adresář s názvem `exampleudf`, která obsahuje projekt Maven.

2. Po vytvoření projektu, odstranit `exampleudf/src/test` adresář, který byl vytvořen jako součást projektu tak, že zadáte následující příkaz:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Otevřít `pom.xml` tak, že zadáte následující příkaz:

    ```cmd
    notepad pom.xml
    ```

    Potom nahraďte existující `<dependencies>` položku s následující kód XML:

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

    Tyto položky definovat, jak sestavit projekt. Konkrétně verzi jazyka Java, která používá projekt a jak sestavit uberjar pro nasazení do clusteru.

    Po provedení změn uložte soubor.

4. Zadejte příkaz a vytvořte a otevřete nový soubor `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Zkopírujte a vložte níže uvedený kód java do nového souboru. Zavřete soubor.

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

V následujících příkazů nahraďte `sshuser` s skutečné uživatelské jméno, pokud se liší. Nahraďte `mycluster` s názvem skutečné clusteru.

1. Kompilace a balíček UDF tak, že zadáte následující příkaz:

    ```cmd
    mvn compile package
    ```

    Tento příkaz sestaví a zabalí UDF do `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` souboru.

2. Použití `scp` příkaz zkopírujte soubor do clusteru HDInsight tak, že zadáte následující příkaz:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Připojte se ke clusteru pomocí SSH tak, že zadáte následující příkaz:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Z otevřené relace SSH zkopírujte soubor jar do úložiště HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Použití systému souborů UDF v Hivu

1. Zadáním následujícího příkazu spusťte Beeline klienta z relace SSH:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Tento příkaz předpokládá, že jste použili výchozí **správce** pro přihlašovací účet pro váš cluster.

2. Až přijedete `jdbc:hive2://localhost:10001/>` výzva, zadejte následující příkaz pro přidání UDF Hive a zpřístupnit ji jako funkce.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. UDF použijte k převodu hodnoty získané z tabulky na řetězce na malá písmena.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Tento dotaz vybere stavu z tabulky, převeďte řetězec, který má nižší malá a velká a zobrazí je spolu s názvem bez úprav. Zobrazí výstup podobný následujícímu textu:

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

Při spuštění úlohy hive, může dojít k chybě, které jsou podobné následujícímu textu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Tento problém může být způsobeno konce řádků v souboru Python. Mnoho výchozí Windows editory nepoužívají znaky CRLF jako ukončení řádku, ale Linuxové aplikace obvykle můžete očekávat LF.

Odebrat znaky CR před nahráním souboru do HDInsight, můžete použít následující příkazy Powershellu:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Další postup

Další způsoby, jak pracovat s Hive, najdete v části [použití Apache Hivu se službou HDInsight](hdinsight-use-hive.md).

Další informace o funkcích Hive User-Defined, naleznete v tématu [operátory Apache Hive a uživatelsky definovaných funkcí](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) část wiki Hive na webu apache.org.
