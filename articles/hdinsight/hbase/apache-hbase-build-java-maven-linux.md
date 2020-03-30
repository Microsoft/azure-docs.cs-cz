---
title: Vytvoření klienta Java HBase pro Azure HDInsight pomocí Apache Maven
description: Naučte se používat Apache Maven k vytvoření java-založené aplikace Apache HBase a pak ji nasadit do HBase na Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/24/2019
ms.openlocfilehash: 3e9b23ce450e45dfedcee8b20e09b1c2b52b6e68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495784"
---
# <a name="build-java-applications-for-apache-hbase"></a>Vytváření java aplikací pro Apache HBase

Přečtěte si, jak vytvořit aplikaci [Apache HBase](https://hbase.apache.org/) v Javě. Pak použijte aplikaci s HBase na Azure HDInsight.

Kroky v tomto dokumentu používají [Apache Maven](https://maven.apache.org/) k vytvoření a sestavení projektu. Maven je software pro řízení projektů a pochopení nástroj, který vám umožní vytvářet software, dokumentaci a sestavy pro java projekty.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache HBase na HDInsight. Viz [Začínáme s Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalován](https://maven.apache.org/install.html) podle Apache.  Maven je systém vytváření projektů pro java projekty.

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Pokud používáte PowerShell, budete potřebovat [modul AZ](https://docs.microsoft.com/powershell/azure/overview).

* Textový editor Tento článek používá poznámkový blok Společnosti Microsoft.

## <a name="test-environment"></a>Testovací prostředí

Prostředí používané pro tento článek byl počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upraveny pomocí poznámkového bloku. Upravte odpovídajícím způsobem pro vaše prostředí.

Z příkazového řádku zadejte níže uvedené příkazy a vytvořte pracovní prostředí:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

1. Zadejte následující příkaz pro vytvoření projektu Maven s názvem **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Tento příkaz vytvoří `hbaseapp` adresář pojmenovaný v aktuálním umístění, který obsahuje základní projekt Maven. Druhý příkaz změní pracovní `hbaseapp`adresář na . Třetí příkaz vytvoří nový `conf`adresář , který bude použit později. Adresář `hbaseapp` obsahuje následující položky:

    * `pom.xml`: Objektový model projektu ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) obsahuje informace a podrobnosti konfigurace použité k sestavení projektu.
    * `src\main\java\com\microsoft\examples`: Obsahuje kód aplikace.
    * `src\test\java\com\microsoft\examples`: Obsahuje testy pro vaši aplikaci.

2. Odeberte generovaný ukázkový kód. Odstraňte generované testovací a `AppTest.java`aplikační `App.java` soubory a zadáním následujících příkazů:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizace objektového modelu projektu

Úplný odkaz na soubor pom.xml https://maven.apache.org/pom.htmlnaleznete v tématu .  Otevřít `pom.xml` zadáním níže uvedeného příkazu:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Přidání závislostí

V `pom.xml`oddíle přidejte `<dependencies>` následující text:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Tato část označuje, že projekt potřebuje **komponenty hbase-client** a **phoenix-core.** V době kompilace jsou tyto závislosti staženy z výchozího úložiště Maven. Další informace o této závislosti najdete v [hledání centrálního úložiště Maven.](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar)

> [!IMPORTANT]  
> Číslo verze klienta hbase musí odpovídat verzi Apache HBase, která je k dispozici s vaším clusterem HDInsight. V následující tabulce vyhledejte správné číslo verze.

| Verze clusteru HDInsight | Apache HBase verze k použití |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

Další informace o verzích a součástech HDInsight najdete [v tématu Jaké jsou různé komponenty Apache Hadoop dostupné v hdinsightu](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in Maven umožňují přizpůsobit fáze sestavení projektu. Tato část slouží k přidání modulů plug-in, prostředků a dalších možností konfigurace sestavení.

Přidejte do souboru `pom.xml` následující kód a potom soubor uložte a zavřete. Tento text musí `<project>...</project>` být uvnitř tagů v `</dependencies>` souboru, například mezi a `</project>`.

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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

Tato část konfiguruje prostředek (`conf/hbase-site.xml`), který obsahuje informace o konfiguraci pro HBase.

> [!NOTE]  
> Můžete také nastavit hodnoty konfigurace pomocí kódu. Podívejte se na `CreateTable` komentáře v příkladu.

Tato část také konfiguruje [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) a [Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/). Modul plug-in kompilátoru se používá ke kompilaci topologie. Modul plug-in shade se používá k zabránění duplikaci licencí v balíčku JAR, který je vytvořen společností Maven. Tento plugin se používá k zabránění chyby "duplicitních licenčních souborů" za běhu v clusteru HDInsight. Použití maven-shade-plugin `ApacheLicenseResourceTransformer` s implementací zabraňuje chybě.

Maven-shade-plugin také vytváří uber jar, který obsahuje všechny závislosti požadované aplikací.

### <a name="download-the-hbase-sitexml"></a>Stáhnout soubor hbase-site.xml

Pomocí následujícího příkazu zkopírujte konfiguraci HBase `conf` z clusteru HBase do adresáře. Nahraďte `CLUSTERNAME` název clusteru HDInsight a zadejte příkaz:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Vytvoření aplikace

### <a name="implement-a-createtable-class"></a>Implementace třídy CreateTable

Chcete-li vytvořit a otevřít nový `CreateTable.java`soubor , zadejte následující příkaz . Vyberte **Ano** na výzvu k vytvoření nového souboru.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Pak zkopírujte a vložte java kód níže do nového souboru. Potom soubor zavřete.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Tento kód `CreateTable` je třída, která `people` vytvoří tabulku s názvem a naplnit ji s některými předdefinovanými uživateli.

### <a name="implement-a-searchbyemail-class"></a>Implementace třídy SearchByEmail

Chcete-li vytvořit a otevřít nový `SearchByEmail.java`soubor , zadejte následující příkaz . Vyberte **Ano** na výzvu k vytvoření nového souboru.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Pak zkopírujte a vložte java kód níže do nového souboru. Potom soubor zavřete.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

Třídu `SearchByEmail` lze použít k dotazování na řádky podle e-mailové adresy. Vzhledem k tomu, že používá filtr regulárních výrazů, můžete při použití třídy zadat řetězec nebo regulární výraz.

### <a name="implement-a-deletetable-class"></a>Implementace třídy DeleteTable

Chcete-li vytvořit a otevřít nový `DeleteTable.java`soubor , zadejte následující příkaz . Vyberte **Ano** na výzvu k vytvoření nového souboru.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Pak zkopírujte a vložte java kód níže do nového souboru. Potom soubor zavřete.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

Třída `DeleteTable` vyčistí tabulky HBase vytvořené v tomto příkladu zakázáním a uvolněním tabulky vytvořené třídou. `CreateTable`

## <a name="build-and-package-the-application"></a>Sestavení a balíček aplikace

1. Z `hbaseapp` adresáře vytvořte soubor JAR obsahující aplikaci pomocí následujícího příkazu:

    ```cmd
    mvn clean package
    ```

    Tento příkaz vytvoří a zabalí aplikaci do souboru .jar.

2. Po dokončení příkazu `hbaseapp/target` obsahuje adresář soubor `hbaseapp-1.0-SNAPSHOT.jar`s názvem .

   > [!NOTE]  
   > Soubor `hbaseapp-1.0-SNAPSHOT.jar` je uber jar. Obsahuje všechny závislosti potřebné ke spuštění aplikace.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Nahrát JAR a spustit úlohy (SSH)

Následující kroky `scp` slouží ke zkopírování JAR do primárního hlavního uzlu vašeho clusteru Apache HBase v clusteru HDInsight. Příkaz `ssh` se pak používá k připojení ke clusteru a spustit příklad přímo na hlavním uzlu.

1. Nahrajte nádobu do clusteru. Nahraďte `CLUSTERNAME` název clusteru HDInsight a zadejte následující příkaz:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Připojte se ke clusteru HBase. Nahraďte `CLUSTERNAME` název clusteru HDInsight a zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Chcete-li vytvořit tabulku HBase pomocí aplikace Java, použijte v otevřeném připojení ssh následující příkaz:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Tento příkaz vytvoří tabulku HBase s názvem **lidé**a naplní ji daty.

4. Chcete-li vyhledat e-mailové adresy uložené v tabulce, použijte následující příkaz:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Obdržíte následující výsledky:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Chcete-li tabulku odstranit, použijte následující příkaz:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Nahrání jar a spuštění úloh (PowerShell)

Následující kroky používají modul Azure PowerShell [AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) k nahrání JAR do výchozího úložiště pro váš cluster Apache HBase. Rutiny HDInsight se pak používají ke vzdálenému spuštění příkladů.

1. Po instalaci a konfiguraci modulu AZ `hbase-runner.psm1`vytvořte soubor s názvem . Jako obsah souboru použijte následující text:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Tento soubor obsahuje dva moduly:

   * **Add-HDInsightFile** – slouží k nahrávání souborů do clusteru
   * **Start-HBaseExample** - slouží ke spuštění tříd vytvořených dříve

2. Uložte `hbase-runner.psm1` soubor `hbaseapp` do adresáře.

3. Zaregistrujte moduly pomocí Azure PowerShellu. Otevřete nové okno Azure PowerShellu a `CLUSTERNAME` upravte níže uvedený příkaz nahrazením názvem clusteru. Poté zadejte následující příkazy:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Pomocí následujícího příkazu `hbaseapp-1.0-SNAPSHOT.jar` nahrajte do clusteru.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Po zobrazení výzvy zadejte přihlašovací jméno a heslo pro přihlášení k clusteru (správce). Příkaz odešle `hbaseapp-1.0-SNAPSHOT.jar` do `example/jars` umístění v primárním úložišti pro váš cluster.

5. Chcete-li vytvořit `hbaseapp`tabulku pomocí příkazu , použijte následující příkaz:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Po zobrazení výzvy zadejte přihlašovací jméno a heslo pro přihlášení k clusteru (správce).

    Tento příkaz vytvoří tabulku s názvem **lidé** v HBase v clusteru HDInsight. Tento příkaz nezobrazuje žádný výstup v okně konzoly.

6. Chcete-li vyhledat položky v tabulce, použijte následující příkaz:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Po zobrazení výzvy zadejte přihlašovací jméno a heslo pro přihlášení k clusteru (správce).

    Tento příkaz `SearchByEmail` používá třídu k vyhledání všech řádků, kde rodina `contactinformation` sloupců a `email` sloupec, obsahuje řetězec `contoso.com`. Měli byste obdržet následující výsledky:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Použití **fabrikam.com** `-emailRegex` pro hodnotu vrátí uživatelé, kteří **mají fabrikam.com** v poli e-mailu. Jako hledaný výraz můžete také použít regulární výrazy. Například **^r** vrátí e-mailové adresy, které začínají písmenem 'r'.

7. Chcete-li tabulku odstranit, použijte následující příkaz:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Žádné výsledky nebo neočekávané výsledky při použití Start-HBaseExample

Pomocí `-showErr` parametru můžete zobrazit standardní chybu (STDERR), která je vytvořena při spuštění úlohy.

## <a name="next-steps"></a>Další kroky

[Naučte se používat SQLLine s Apache HBase](apache-hbase-query-with-phoenix.md)
