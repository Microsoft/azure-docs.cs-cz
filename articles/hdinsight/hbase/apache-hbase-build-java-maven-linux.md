---
title: Použití Apache Maven k vytvoření klienta Java HBA pro Azure HDInsight
description: Naučte se používat Apache Maven k vytváření aplikací Apache HBA založených na jazyce Java a pak ji nasadit do HBA v Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seodec18, devx-track-java
ms.date: 12/24/2019
ms.openlocfilehash: 13616cf99db39ec4dac1d13e3dcd2cefc5a44614
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942956"
---
# <a name="build-java-applications-for-apache-hbase"></a>Vytváření aplikací Java pro Apache HBA

Naučte se vytvořit aplikaci [Apache HBA](https://hbase.apache.org/) v jazyce Java. Pak použijte aplikaci s adaptéry HBA v Azure HDInsight.

Kroky v tomto dokumentu používají [Apache Maven](https://maven.apache.org/) k vytvoření a sestavení projektu. Maven je nástroj pro správu a porozumění projektů softwaru, který umožňuje sestavovat software, dokumentaci a sestavy pro projekty v jazyce Java.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache HBA v HDInsight. Přečtěte si téma Začínáme [s Apache HBA](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) verze 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Pokud používáte PowerShell, budete potřebovat [AZ Module](/powershell/azure/).

* Textový editor Tento článek používá program Poznámkový blok společnosti Microsoft.

## <a name="test-environment"></a>Testovací prostředí

Prostředí použité pro tento článek bylo počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upraveny pomocí poznámkového bloku. Upravte odpovídajícím způsobem pro vaše prostředí.

Z příkazového řádku zadejte níže uvedené příkazy pro vytvoření funkčního prostředí:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

1. Zadejte následující příkaz a vytvořte tak projekt Maven s názvem **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Tento příkaz vytvoří adresář s názvem `hbaseapp` v aktuálním umístění, který obsahuje základní projekt Maven. Druhý příkaz změní pracovní adresář na `hbaseapp` . Třetí příkaz vytvoří nový adresář, `conf` který bude později použit. `hbaseapp`Adresář obsahuje následující položky:

    * `pom.xml`: Model objektu projektu ([pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) obsahuje informace a podrobnosti o konfiguraci použité k sestavení projektu.
    * `src\main\java\com\microsoft\examples`: Obsahuje kód vaší aplikace.
    * `src\test\java\com\microsoft\examples`: Obsahuje testy pro vaši aplikaci.

2. Odeberte generovaný ukázkový kód. Odstraňte vygenerované soubory testů a aplikace `AppTest.java` a `App.java` zadáním následujících příkazů:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizace modelu objektu projektu

Úplný odkaz na soubor pom.xml naleznete v tématu https://maven.apache.org/pom.html .  Otevřete zadáním `pom.xml` následujícího příkazu:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Přidat závislosti

V `pom.xml` přidejte do oddílu následující text `<dependencies>` :

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

V této části je uvedeno, že projekt potřebuje komponenty **HBA-Client** a **Phoenix-Core** . V době kompilace se tyto závislosti stáhnou z výchozího úložiště Maven. K získání dalších informací o této závislosti můžete použít [vyhledávání v centrálním úložišti Maven](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) .

> [!IMPORTANT]  
> Číslo verze adaptérů HBA – klient se musí shodovat s verzí Apache HBA, která je k dispozici v clusteru HDInsight. Pomocí následující tabulky vyhledejte správné číslo verze.

| Verze clusteru HDInsight | Verze Apache HBA, které se mají použít |
| --- | --- |
| 3,6 | 1.1.2 |
| 4,0 | 2.0.0 |

Další informace o verzích a součástech služby HDInsight najdete v tématu [co jsou různé Apache Hadoop komponenty dostupné v HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in Maven umožňují přizpůsobit fáze sestavení projektu. Tato část slouží k přidání modulů plug-in, prostředků a dalších možností konfigurace sestavení.

Do souboru přidejte následující kód `pom.xml` a soubor uložte a zavřete. Tento text musí být uvnitř `<project>...</project>` značek v souboru, například mezi `</dependencies>` a `</project>` .

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

V této části nakonfigurujete prostředek ( `conf/hbase-site.xml` ), který obsahuje informace o konfiguraci pro adaptéry HBA.

> [!NOTE]  
> Můžete také nastavit konfigurační hodnoty prostřednictvím kódu. Podívejte se na komentáře v `CreateTable` příkladu.

Tato část také nakonfiguruje modul [Plug-in kompilátoru Apache Maven](https://maven.apache.org/plugins/maven-compiler-plugin/) a [modul plug-in Apache Maven pro stíny](https://maven.apache.org/plugins/maven-shade-plugin/). Modul plug-in kompilátoru se používá ke kompilaci topologie. Modul plug-in pro barevný nádech se používá k tomu, aby se zabránilo duplicitě licencí v balíčku JAR, který je sestavený pomocí Maven. Tento modul plug-in se používá k tomu, aby v clusteru HDInsight nedocházelo k chybě duplicitních licenčních souborů v době běhu. Použití Maven-destínového plug-in s `ApacheLicenseResourceTransformer` implementací zabraňuje chybu.

Maven-barevný modul plug-in také vytvoří Uber jar, který obsahuje všechny závislosti, které aplikace požaduje.

### <a name="download-the-hbase-sitexml"></a>Stáhnout hbase-site.xml

K zkopírování konfigurace adaptérů HBA z clusteru HBA do adresáře použijte následující příkaz `conf` . Nahraďte `CLUSTERNAME` názvem clusteru HDInsight a pak zadejte příkaz:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Vytvoření aplikace

### <a name="implement-a-createtable-class"></a>Implementace třídy Create

Zadáním následujícího příkazu vytvořte nový soubor a otevřete ho `CreateTable.java` . V příkazovém řádku vyberte **Ano** , pokud chcete vytvořit nový soubor.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Pak zkopírujte a vložte kód Java níže do nového souboru. Pak soubor zavřete.

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

Tento kód je `CreateTable` třída, která vytvoří tabulku s názvem `people` a naplní ji některými předdefinovanými uživateli.

### <a name="implement-a-searchbyemail-class"></a>Implementace třídy SearchByEmail

Zadáním následujícího příkazu vytvořte nový soubor a otevřete ho `SearchByEmail.java` . V příkazovém řádku vyberte **Ano** , pokud chcete vytvořit nový soubor.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Pak zkopírujte a vložte kód Java níže do nového souboru. Pak soubor zavřete.

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

`SearchByEmail`Třída se dá použít k dotazování na řádky podle e-mailové adresy. Vzhledem k tomu, že používá filtr regulárního výrazu, lze při použití třídy zadat buď řetězec, nebo regulární výraz.

### <a name="implement-a-deletetable-class"></a>Implementace třídy DELETE

Zadáním následujícího příkazu vytvořte nový soubor a otevřete ho `DeleteTable.java` . V příkazovém řádku vyberte **Ano** , pokud chcete vytvořit nový soubor.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Pak zkopírujte a vložte kód Java níže do nového souboru. Pak soubor zavřete.

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

`DeleteTable`Třída vyčistí tabulky HBA vytvořené v tomto příkladu tím, že zakáže a vyřadí tabulku vytvořenou `CreateTable` třídou.

## <a name="build-and-package-the-application"></a>Sestavení a zabalení aplikace

1. Z `hbaseapp` adresáře použijte následující příkaz k vytvoření souboru jar, který obsahuje aplikaci:

    ```cmd
    mvn clean package
    ```

    Tento příkaz sestaví a zabalí aplikaci do souboru. jar.

2. Po dokončení příkazu `hbaseapp/target` adresář obsahuje soubor s názvem `hbaseapp-1.0-SNAPSHOT.jar` .

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar`Soubor je Uber jar. Obsahuje všechny závislosti potřebné ke spuštění aplikace.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Nahrát úlohy JAR a spustit (SSH)

Následující postup slouží `scp` ke zkopírování jar do primárního hlavního uzlu HBA HBA v clusteru HDInsight. `ssh`Příkaz se pak použije pro připojení ke clusteru a spuštění příkladu přímo na hlavním uzlu.

1. Nahrajte jar do clusteru. Nahraďte `CLUSTERNAME` názvem clusteru HDInsight a potom zadejte následující příkaz:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Připojte se ke clusteru HBA. Nahraďte `CLUSTERNAME` názvem clusteru HDInsight a potom zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Chcete-li vytvořit tabulku HBA pomocí aplikace Java, použijte následující příkaz v otevřeném připojení SSH:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Tento příkaz vytvoří tabulku HBA s názvem **lidé** a naplní ji daty.

4. Pokud chcete vyhledat e-mailové adresy uložené v tabulce, použijte následující příkaz:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Zobrazí se následující výsledky:

    ```console
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

5. Chcete-li odstranit tabulku, použijte následující příkaz:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Nahrání úloh JAR a spuštění (PowerShell)

Následující postup slouží k nahrání JAR do výchozího úložiště pro cluster Apache HBA pomocí Azure PowerShell [AZ Module](/powershell/azure/new-azureps-module-az) . Rutiny HDInsight se pak používají ke vzdálenému spuštění příkladů.

1. Po instalaci a konfiguraci modulu AZ Module vytvořte soubor s názvem `hbase-runner.psm1` . Jako obsah souboru použijte následující text:

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

   * **Add-HDInsightFile** – slouží k nahrání souborů do clusteru.
   * **Start-HBaseExample** – používá se ke spuštění tříd vytvořených dříve.

2. Uložte `hbase-runner.psm1` soubor do `hbaseapp` adresáře.

3. Zaregistrujte moduly pomocí Azure PowerShell. Otevřete nové okno Azure PowerShell a upravte následující příkaz tak, že nahradíte `CLUSTERNAME` názvem vašeho clusteru. Pak zadejte následující příkazy:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Pomocí následujícího příkazu nahrajte `hbaseapp-1.0-SNAPSHOT.jar` do svého clusteru.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Po zobrazení výzvy zadejte jméno a heslo přihlášení clusteru (správce). Příkaz nahraje `hbaseapp-1.0-SNAPSHOT.jar` do `example/jars` umístění v primárním úložišti pro váš cluster.

5. Chcete-li vytvořit tabulku pomocí `hbaseapp` , použijte následující příkaz:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Po zobrazení výzvy zadejte jméno a heslo přihlášení clusteru (správce).

    Tento příkaz vytvoří v clusteru HDInsight tabulku s názvem **lidé** ve HBA. Tento příkaz nezobrazuje žádný výstup v okně konzoly.

6. Chcete-li vyhledat položky v tabulce, použijte následující příkaz:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Po zobrazení výzvy zadejte jméno a heslo přihlášení clusteru (správce).

    Tento příkaz používá `SearchByEmail` třídu k vyhledání všech řádků, ve kterých `contactinformation` rodina sloupců a `email` sloupec obsahuje řetězec `contoso.com` . Měli byste obdržet následující výsledky:

    ```output
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

    Použití **fabrikam.com** pro `-emailRegex` hodnotu vrátí uživatele, kteří mají **fabrikam.com** v poli e-mail. Jako hledaný termín můžete použít také regulární výrazy. Například **^ r** vrátí e-mailové adresy, které začínají písmenem "r".

7. Chcete-li odstranit tabulku, použijte následující příkaz:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Žádné výsledky nebo neočekávané výsledky při použití Start-HBaseExample

Pomocí `-showErr` parametru zobrazíte standardní chybu (stderr), která je vytvořena při spuštění úlohy.

## <a name="next-steps"></a>Další kroky

[Naučte se používat SQLLine s Apache HBA](apache-hbase-query-with-phoenix.md)