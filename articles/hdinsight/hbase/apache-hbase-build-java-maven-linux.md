---
title: Klient HBase Javy – Azure HDInsight
description: Zjistěte, jak sestavit aplikaci Apache HBase založené na jazyce Java a pak ji nasadit do HBase v Azure HDInsight pomocí nástroje Apache Maven.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: jasonh
ms.openlocfilehash: c62e52d9f2ee456dbaa12be24ff46285023e5eed
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417545"
---
# <a name="build-java-applications-for-apache-hbase"></a>Vytváření aplikací Apache HBase v Javě

Zjistěte, jak vytvořit [Apache HBase](http://hbase.apache.org/) aplikace v Javě. Potom s použitím aplikace s HBase v Azure HDInsight.

Kroky v tomto dokumentu pomocí [Maven](http://maven.apache.org/) vytvořte a sestavte projekt. Maven je software, řízení projektů a porozumění nástroj, který vám umožní vytvářet software, dokumentaci a sestav pro projekty Java.

> [!NOTE]
> Kroky v tomto dokumentu se jako poslední byly testovány s HDInsight 3.6.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Požadavky

* [Platforma Java JDK](https://aka.ms/azure-jdks) 8 nebo novější.

    > [!NOTE]
    > HDInsight verze 3.5 nebo novější vyžaduje Java 8. Starší verze systému HDInsight vyžadují Java 7.

* [Maven](http://maven.apache.org/)

* [Cluster Azure HDInsight založených na Linuxu s HBase](apache-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

## <a name="create-the-project"></a>Vytvoření projektu

1. Z příkazového řádku ve vašem vývojovém prostředí, změňte adresáře na umístění, ve kterém chcete vytvořit projekt, například `cd code\hbase`.

2. Použití **mvn** příkazu, který je nainstalován pomocí Mavenu, vygenerujte generování uživatelského rozhraní pro projekt.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]
    > Pokud používáte PowerShell, je nutné uzavřít `-D` parametry do dvojitých uvozovek.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Tento příkaz vytvoří adresář se stejným názvem jako **artifactID** parametr (**hbaseapp** v tomto příkladu.) Tento adresář obsahuje následující položky:

   * **pom.xml**: The Model objektu projektu ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) obsahuje podrobnosti o informace a konfigurace použít k sestavení projektu.
   * **src**: adresář obsahující **main/java/com/microsoft/příklady** adresáře, kde můžete vytvářet aplikace.

3. Odstranit `src/test/java/com/microsoft/examples/apptest.java` souboru. Není možné použít v tomto příkladu.

## <a name="update-the-project-object-model"></a>Aktualizace objektu modelu projektu

1. Upravit `pom.xml` a přidejte následující kód `<dependencies>` části:

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    Tato část znamená, že projekt měl **hbase-client** a **phoenix core** komponenty. V době kompilace jsou tyto závislosti stáhnout z úložiště Maven výchozí. Můžete použít [vyhledávání centrálního úložiště Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) získat další informace o této závislosti.

   > [!IMPORTANT]
   > Číslo verze klienta hbase musí odpovídat verzi HBase, který je součástí vašeho clusteru HDInsight. V následující tabulce můžete najít na správné číslo verze.

   | Verze clusteru HDInsight | HBase verze se má použít |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3, 3.4, 3.5 a 3.6 |1.1.2 |

    Další informace o verzích HDInsight a komponenty, naleznete v tématu [jaké jsou různé součásti platformy Hadoop HDInsight přináší](../hdinsight-component-versioning.md).

3. Přidejte následující kód, který **pom.xml** souboru. Tento text musí být uvnitř `<project>...</project>` značky v souboru, například mezi `</dependencies>` a `</project>`.

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
                    <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
            </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
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

    Tato část také nakonfiguruje [modulu plug-in Maven kompilátoru](http://maven.apache.org/plugins/maven-compiler-plugin/) a [modulu plug-in Maven odstín](http://maven.apache.org/plugins/maven-shade-plugin/). Modul plug-in kompilátoru je používá ke kompilaci topologie. Modul plug-in odstín se používá při prevenci licence duplikace v balíček JAR, který je sestavený Maven. Tento modul plug-in se používá při prevenci "duplicitní licenčních souborů" Chyba za běhu v clusteru HDInsight. Využitím odstín plug-in maven s `ApacheLicenseResourceTransformer` implementace brání chyba.

    Plug-in odstín maven vytvoří také uber jar, obsahující všechny závislosti vyžadované aplikací.

4. Uložte soubor `pom.xml`.

5. Vytvořte adresář `conf` v `hbaseapp` adresáře. Tento adresář se používá pro uložení konfigurační informace pro připojení k HBase.

6. Použijte následující příkaz pro kopírování HBase konfigurace z clusteru HBase `conf` adresáře. Nahraďte `USERNAME` s názvem vaší přihlašování přes SSH. Nahraďte `CLUSTERNAME` názvem vašeho clusteru HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   Další informace o používání `ssh` a `scp`, naleznete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Vytvoření aplikace

1. Přejděte `hbaseapp/src/main/java/com/microsoft/examples` adresáře a přejmenovat app.java soubor `CreateTable.java`.

2. Otevřít `CreateTable.java` soubor a nahradit existující obsah s následujícím textem:

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

    Tento kód je **CreateTable** třídu, která vytvoří tabulku s názvem **lidé** a jeho naplnění některé předdefinované uživatele.

3. Uložte soubor `CreateTable.java`.

4. V `hbaseapp/src/main/java/com/microsoft/examples` adresáři vytvořte soubor s názvem `SearchByEmail.java`. Jako obsah souboru použijte následující text:

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

    **SearchByEmail** třídu lze použít k dotazování pro řádky podle e-mailovou adresu. Protože ho používá filtr regulárních výrazů, můžete zadat řetězce nebo regulárního výrazu při používání třídy.

5. Uložte soubor `SearchByEmail.java`.

6. V `hbaseapp/src/main/hava/com/microsoft/examples` adresáři vytvořte soubor s názvem `DeleteTable.java`. Jako obsah souboru použijte následující text:

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

    Tato třída Vyčištění tabulky HBase vytvořené v tomto příkladu tak, že zakázání a odstranění tabulky vytvořené `CreateTable` třídy.

7. Uložte soubor `DeleteTable.java`.

## <a name="build-and-package-the-application"></a>Sestavení a zabalení aplikace

1. Z `hbaseapp` adresáře, použijte následující příkaz k sestavení souboru JAR, který obsahuje aplikace:

    ```bash
    mvn clean package
    ```

    Tento příkaz sestaví a zabalí aplikaci do souboru .jar.

2. Po dokončení příkazu `hbaseapp/target` adresář obsahuje soubor s názvem `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]
   > `hbaseapp-1.0-SNAPSHOT.jar` Je soubor soubor jar uber. Obsahuje všechny závislosti potřebné ke spuštění aplikace.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Nahrát soubor JAR a spouštět úlohy (SSH)

Následující kroky použijte `scp` zkopírovat soubor JAR k primárnímu hlavnímu uzlu vaše HBase v clusteru HDInsight. `ssh` Příkazu se pak použije k připojení ke clusteru a spuštění příkladu přímo na hlavní uzel.

1. Pokud chcete nahrát soubor jar do clusteru, použijte následující příkaz:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Nahraďte `USERNAME` s názvem vaší přihlašování přes SSH. Nahraďte `CLUSTERNAME` názvem vašeho clusteru HDInsight.

2. Pro připojení ke clusteru HBase, použijte následující příkaz:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Nahraďte `USERNAME` název vaší přihlašování přes SSH. Nahraďte `CLUSTERNAME` názvem vašeho clusteru HDInsight.

3. K vytvoření tabulky HBase pomocí aplikace v Javě, použijte následující příkaz:

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

Následující kroky soubor JAR odešlete do výchozího úložiště pro HBase cluster pomocí Azure Powershellu. HDInsight rutiny se použije pro vzdálené spuštění příkladů.

1. Po instalaci a konfiguraci prostředí Azure PowerShell vytvořte soubor s názvem `hbase-runner.psm1`. Jako obsah souboru použijte následující text:

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
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
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
        Set-AzureStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            throw "No active Azure subscription found! If you have a subscription, use the Connect-AzureRmAccount cmdlet to login to your subscription."
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
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
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
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

2. Uložte soubor `hbase-runner.psm1`.

3. Otevřete nové okno Azure PowerShell, přejděte do adresáře `hbaseapp` adresáře a spusťte následující příkaz:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Změňte cestu na umístění `hbase-runner.psm1` soubor vytvořený dříve. Tento příkaz registruje modul pomocí Azure Powershellu.

4. Použijte následující příkaz k nahrání `hbaseapp-1.0-SNAPSHOT.jar` do clusteru.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Nahraďte `hdinsightclustername` názvem svého clusteru. Po zobrazení výzvy zadejte název clusteru (správce) a heslo. Příkaz nahraje `hbaseapp-1.0-SNAPSHOT.jar` k `example/jars` umístění primárního úložiště pro váš cluster.

5. Chcete-li vytvořit tabulku s využitím `hbaseapp`, použijte následující příkaz:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Nahraďte `hdinsightclustername` názvem svého clusteru. Po zobrazení výzvy zadejte název clusteru (správce) a heslo.

    Tento příkaz vytvoří tabulku s názvem **lidé** v HBase v clusteru HDInsight. Tento příkaz nezobrazí žádný výstup v okně konzoly.

6. K vyhledání položky v tabulce, použijte následující příkaz:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Nahraďte `hdinsightclustername` názvem svého clusteru. Po zobrazení výzvy zadejte název clusteru (správce) a heslo.

    Tento příkaz používá `SearchByEmail` třída vyhledat všechny řádky, kde `contactinformation` rodiny sloupců a `email` sloupců, obsahuje řetězec `contoso.com`. Mělo by se zobrazit následující výsledky:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Pomocí **fabrikam.com** pro `-emailRegex` hodnotu vrátí uživatele, kteří mají **fabrikam.com** v poli e-mailu. Regulární výrazy můžete použít také jako hledaný termín. Například **^ r** vrátí e-mailové adresy, které začínají písmenem "r".

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Žádné výsledky nebo neočekávané výsledky při použití Start HBaseExample

Použití `-showErr` parametr, chcete-li zobrazit standardní chyby (STDERR), který je vytvořen při spuštění úlohy.

## <a name="delete-the-table"></a>Odstranit tabulku

Jakmile budete hotovi s příkladem, použijte následující postup k odstranění **lidé** tabulka použitá v tomto příkladu:

__Ze `ssh` relace__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Z prostředí Azure PowerShell__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>Další postup

[Další informace o použití SQuirreL SQL s HBase](apache-hbase-phoenix-squirrel-linux.md)
