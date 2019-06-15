---
title: Pomocí nástroje Apache Maven k sestavení klienta HBase v Javě pro Azure HDInsight
description: Zjistěte, jak sestavit aplikaci Apache HBase založené na jazyce Java a pak ji nasadit do HBase v Azure HDInsight pomocí nástroje Apache Maven.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 04/16/2019
ms.openlocfilehash: a4c601e81390efa3bb53a6f07225bb6e939bc9bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726453"
---
# <a name="build-java-applications-for-apache-hbase"></a>Vytváření aplikací Apache HBase v Javě

Zjistěte, jak vytvořit [Apache HBase](https://hbase.apache.org/) aplikace v Javě. Potom s použitím aplikace s HBase v Azure HDInsight.

Kroky v tomto dokumentu pomocí [Apache Maven](https://maven.apache.org/) vytvořte a sestavte projekt. Maven je software, řízení projektů a porozumění nástroj, který vám umožní vytvářet software, dokumentaci a sestav pro projekty Java.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache HBase v HDInsight. Zobrazit [Začínáme s Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks).

* [Nástroje Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) podle Apache.  Maven je projekt sestavovacího systému pro projekty Java.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Pokud používáte PowerShell, budete potřebovat [AZ modulu](https://docs.microsoft.com/powershell/azure/overview).

* Textový editor Tento článek používá Microsoft Notepad.

> [!IMPORTANT]  
> Rutiny Azure Powershellu [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) a [Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) aktuálně není funkční v případě [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md) je povolená v účtu úložiště .

## <a name="test-environment"></a>Testovací prostředí
Prostředí, používá pro účely tohoto článku byl počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upravit článek přeložený překladatelem Poznámkový blok. Změňte je odpovídajícím způsobem pro vaše prostředí.

Z příkazového řádku zadejte následující příkazy k vytvoření pracovní prostředí:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Vytvořte projekt Maven

1. Zadáním následujícího příkazu vytvořte projekt Maven s názvem **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Tento příkaz vytvoří adresář s názvem `hbaseapp` na aktuální pozici, která obsahuje základní projekt Maven. Druhý příkaz změní pracovní adresář, který `hbaseapp`. Třetí příkaz vytvoří nový adresář `conf`, který se použije později. `hbaseapp` Adresář obsahuje následující položky:

    * `pom.xml`:  Model objektu projektu ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) obsahuje podrobnosti o informace a konfigurace použít k sestavení projektu.
    * `src\main\java\com\microsoft\examples`: Obsahuje kód vaší aplikace.
    * `src\test\java\com\microsoft\examples`: Obsahuje testy pro vaši aplikaci.

2. Odeberte generovaný ukázkový kód. Odstranit vygenerované testy a soubory aplikace `AppTest.java`, a `App.java` zadáním následujících příkazů:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizace objektu modelu projektu

Úplný přehled soubor pom.xml najdete v tématu https://maven.apache.org/pom.html.  Otevřít `pom.xml` tak, že zadáte následující příkaz:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Přidat závislosti

V `pom.xml`, přidejte následující text do `<dependencies>` části:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Tato část znamená, že projekt měl **hbase-client** a **phoenix core** komponenty. V době kompilace jsou tyto závislosti stáhnout z úložiště Maven výchozí. Můžete použít [vyhledávání centrálního úložiště Maven](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) získat další informace o této závislosti.

> [!IMPORTANT]  
> Číslo verze klienta hbase musí odpovídat verzi Apache HBase, který je součástí vašeho clusteru HDInsight. V následující tabulce můžete najít na správné číslo verze.

| Verze clusteru HDInsight | Apache HBase verze se má použít |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

Další informace o verzích HDInsight a komponenty, naleznete v tématu [jaké jsou různé komponenty Apache Hadoop, která je k dispozici s HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in maven umožňují přizpůsobit fáze sestavení umísťují projektu. V této části se používá k přidání modulů plug-in, prostředky a další možnosti konfigurace sestavení.

Přidejte následující kód, který `pom.xml` souboru a pak uložte a zavřete soubor. Tento text musí být uvnitř `<project>...</project>` značky v souboru, například mezi `</dependencies>` a `</project>`.

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
                <version>3.8.0</version>
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

V této části nakonfiguruje prostředku (`conf/hbase-site.xml`), který obsahuje informace o konfiguraci pro HBase.

> [!NOTE]  
> Můžete také nastavit hodnoty konfigurace prostřednictvím kódu. Viz poznámky v `CreateTable` příklad.

Tato část také nakonfiguruje [plug-in Apache Maven kompilátoru](https://maven.apache.org/plugins/maven-compiler-plugin/) a [plug-in Apache Maven odstín](https://maven.apache.org/plugins/maven-shade-plugin/). Modul plug-in kompilátoru je používá ke kompilaci topologie. Modul plug-in odstín se používá při prevenci licence duplikace v balíček JAR, který je sestavený Maven. Tento modul plug-in se používá při prevenci "duplicitní licenčních souborů" Chyba za běhu v clusteru HDInsight. Využitím odstín plug-in maven s `ApacheLicenseResourceTransformer` implementace brání chyba.

Plug-in odstín maven vytvoří také uber jar, obsahující všechny závislosti vyžadované aplikací.

### <a name="download-the-hbase-sitexml"></a>Stáhněte si hbase-site.xml

Použijte následující příkaz pro kopírování HBase konfigurace z clusteru HBase `conf` adresáře. Nahraďte `CLUSTERNAME` s vaší HDInsight název clusteru a potom zadejte příkaz:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Vytvoření aplikace

### <a name="implement-a-createtable-class"></a>Implementace třídy CreateTable

Zadejte příkaz a vytvořte a otevřete nový soubor `CreateTable.java`. Vyberte **Ano** příkazového řádku k vytvoření nového souboru.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Zkopírujte a vložte níže uvedený kód java do nového souboru. Zavřete soubor.

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

Tento kód je `CreateTable` třídu, která vytvoří tabulku s názvem `people` a jeho naplnění některé předdefinované uživatele.

### <a name="implement-a-searchbyemail-class"></a>Implementace třídy SearchByEmail

Zadejte příkaz a vytvořte a otevřete nový soubor `SearchByEmail.java`. Vyberte **Ano** příkazového řádku k vytvoření nového souboru.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Zkopírujte a vložte níže uvedený kód java do nového souboru. Zavřete soubor.

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

`SearchByEmail` Třídu lze použít k dotazování pro řádky podle e-mailovou adresu. Protože ho používá filtr regulárních výrazů, můžete zadat řetězce nebo regulárního výrazu při používání třídy.

### <a name="implement-a-deletetable-class"></a>Implementace třídy DeleteTable

Zadejte příkaz a vytvořte a otevřete nový soubor `DeleteTable.java`. Vyberte **Ano** příkazového řádku k vytvoření nového souboru.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Zkopírujte a vložte níže uvedený kód java do nového souboru. Zavřete soubor.

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

`DeleteTable` Třídy vyčištění tabulky HBase vytvořené v tomto příkladu tak, že zakázání a odstranění tabulky vytvořené `CreateTable` třídy.

## <a name="build-and-package-the-application"></a>Sestavení a zabalení aplikace

1. Z `hbaseapp` adresáře, použijte následující příkaz k sestavení souboru JAR, který obsahuje aplikace:

    ```cmd
    mvn clean package
    ```

    Tento příkaz sestaví a zabalí aplikaci do souboru .jar.

2. Po dokončení příkazu `hbaseapp/target` adresář obsahuje soubor s názvem `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar` Je soubor soubor jar uber. Obsahuje všechny závislosti potřebné ke spuštění aplikace.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Nahrát soubor JAR a spouštět úlohy (SSH)

Následující kroky použijte `scp` zkopírovat soubor JAR k primárnímu hlavnímu uzlu vaše řešení Apache HBase v clusteru HDInsight. `ssh` Příkazu se pak použije k připojení ke clusteru a spuštění příkladu přímo na hlavní uzel.

1. Nahrajte soubor jar do clusteru. Nahraďte `CLUSTERNAME` s vaší HDInsight název clusteru a potom zadejte následující příkaz:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Připojte se ke clusteru HBase. Nahraďte `CLUSTERNAME` s vaší HDInsight název clusteru a potom zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

 3. K vytvoření tabulky HBase pomocí aplikace v Javě, použijte následující příkaz v vašeho programu open ssh připojení:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Tento příkaz vytvoří tabulku HBase s názvem **lidé**a naplní daty.

4. K vyhledání e-mailové adresy, které jsou uložené v tabulce, použijte následující příkaz:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Zobrazí se následující výsledky:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Pokud chcete odstranit tabulku, použijte následující příkaz:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Nahrát soubor JAR a spouštět úlohy (PowerShell)

Následující kroky pomocí Azure Powershellu [AZ modulu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) k nahrání do výchozího úložiště pro váš cluster Apache HBase soubor JAR. HDInsight rutiny se použije pro vzdálené spuštění příkladů.

1. Po instalaci a konfiguraci modulu AZ vytvořte soubor s názvem `hbase-runner.psm1`. Jako obsah souboru použijte následující text:

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

   * **Přidat HDInsightFile** – slouží k nahrání souborů do clusteru
   * **Start-HBaseExample** – slouží ke spuštění třídy vytvořili dříve

2. Uložit `hbase-runner.psm1` soubor `hbaseapp` adresáře.

3. Zaregistrujte moduly Azure Powershellu. Otevřete nové okno Azure PowerShell a upravte následující příkaz tak, že nahradíte `CLUSTERNAME` s názvem vašeho clusteru. Potom zadejte následující příkazy:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Použijte následující příkaz k nahrání `hbaseapp-1.0-SNAPSHOT.jar` do clusteru.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Po zobrazení výzvy zadejte název clusteru (správce) a heslo. Příkaz nahraje `hbaseapp-1.0-SNAPSHOT.jar` k `example/jars` umístění primárního úložiště pro váš cluster.

5. Chcete-li vytvořit tabulku s využitím `hbaseapp`, použijte následující příkaz:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Po zobrazení výzvy zadejte název clusteru (správce) a heslo.

    Tento příkaz vytvoří tabulku s názvem **lidé** v HBase v clusteru HDInsight. Tento příkaz nezobrazí žádný výstup v okně konzoly.

6. K vyhledání položky v tabulce, použijte následující příkaz:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Po zobrazení výzvy zadejte název clusteru (správce) a heslo.

    Tento příkaz používá `SearchByEmail` třída vyhledat všechny řádky, kde `contactinformation` rodiny sloupců a `email` sloupců, obsahuje řetězec `contoso.com`. Mělo by se zobrazit následující výsledky:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Pomocí **fabrikam.com** pro `-emailRegex` hodnotu vrátí uživatele, kteří mají **fabrikam.com** v poli e-mailu. Regulární výrazy můžete použít také jako hledaný termín. Například **^ r** vrátí e-mailové adresy, které začínají písmenem "r".

7. Pokud chcete odstranit tabulku, použijte následující příkaz:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Žádné výsledky nebo neočekávané výsledky při použití Start HBaseExample

Použití `-showErr` parametr, chcete-li zobrazit standardní chyby (STDERR), který je vytvořen při spuštění úlohy.

## <a name="next-steps"></a>Další postup

[Další informace o použití s Apache HBase SQuirreL SQL](apache-hbase-phoenix-squirrel-linux.md)
