---
title: Vytvoření aplikace Java HBase pro HDInsight založené na Windows Azure
description: Zjistěte, jak pomocí nástroje Apache Maven k sestavení aplikace založené na jazyce Java Apache HBase a pak ji nasadit do clusteru Azure HDInsight založené na Windows.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ea2fe0f7e326db00a63529c0279c9c15d30c744c
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744815"
---
# <a name="use-apache-maven-to-build-java-applications-that-use-apache-hbase-with-windows-based-hdinsight-apache-hadoop"></a>Vytváření aplikací v Javě, které používají Apache HBase s HDInsight se systémem Windows (Apache Hadoop) pomocí nástroje Apache Maven
Zjistěte, jak vytvořit a sestavit [Apache HBase](https://hbase.apache.org/) aplikace v Javě pomocí nástroje Apache Maven. Potom s použitím aplikace s Azure HDInsight (Apache Hadoop).

[Nástroje Apache Maven](https://maven.apache.org/) je softwarový projekt správu procházením a porozuměním nástroj, který vám umožní vytvářet software, dokumentaci a sestav pro projekty Java. V tomto článku se dozvíte, jak ji používat k vytvoření základní aplikace v Javě, které vytváří, dotazy a odstraní tabulky HBase v clusteru Azure HDInsight.

> [!IMPORTANT]  
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, používající Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Požadavky
* [Platforma Java JDK](https://aka.ms/azure-jdks) 7 nebo novější
* [Apache Maven](https://maven.apache.org/)
* Cluster HDInsight se systémem Windows s HBase

    > [!NOTE]  
    > Kroky v tomto dokumentu byly testovány pomocí verze clusteru HDInsight 3.2 nebo 3.3. Výchozí hodnoty v příkladech se pro cluster HDInsight 3.3.

## <a name="create-the-project"></a>Vytvoření projektu
1. Z příkazového řádku ve vašem vývojovém prostředí, změňte adresáře na umístění, ve kterém chcete vytvořit projekt, například `cd code\hdinsight`.
2. Použití **mvn** příkazu, který je nainstalován pomocí Mavenu, vygenerujte generování uživatelského rozhraní pro projekt.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Tento příkaz vytvoří adresář v aktuálním umístění s názvem zadaným **artifactID** parametr (**hbaseapp** v tomto příkladu.) Tento adresář obsahuje následující položky:

   * **pom.xml**:  Model objektu projektu ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) obsahuje podrobnosti o informace a konfigurace použít k sestavení projektu.
   * **src**: Adresář, který obsahuje **main\java\com\microsoft\examples** adresáře, kde se upravit aplikaci.
3. Odstranit **src\test\java\com\microsoft\examples\apptest.java** souboru, protože není použit v tomto příkladu.

## <a name="update-the-project-object-model"></a>Aktualizace objektu modelu projektu
1. Upravit **pom.xml** a přidejte následující kód `<dependencies>` části:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Tato část říká Maven, že projekt vyžaduje **hbase-client** verze **1.1.2**. V době kompilace je tato závislost stáhnout z úložiště Maven výchozí. Můžete použít [Apache Maven centrální úložiště Search](https://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) získat další informace o této závislosti.

   > [!IMPORTANT]  
   > Číslo verze musí odpovídat verzi HBase, který je součástí vašeho clusteru HDInsight. V následující tabulce můžete najít na správné číslo verze.
   >
   >

   | Verze clusteru HDInsight | HBase verze se má použít |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    Další informace o verzích HDInsight a komponenty, naleznete v tématu [jaké jsou různé komponenty Apache Hadoop, která je k dispozici s HDInsight](hdinsight-component-versioning.md).
2. Pokud používáte cluster HDInsight 3.3, musíte taky přidat následující `<dependencies>` části:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    Tato závislost načte phoenix základních komponent, které používají Hbase verze 1.1.x.
3. Přidejte následující kód, který **pom.xml** souboru. Tato část musí být uvnitř `<project>...</project>` značky v souboru, například mezi `</dependencies>` a `</project>`.

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
                    <source>1.7</source>
                    <target>1.7</target>
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

    `<resources>` Části nakonfiguruje prostředku (**conf\hbase-site.xml**), který obsahuje informace o konfiguraci pro HBase.

   > [!NOTE]  
   > Můžete také nastavit hodnoty konfigurace prostřednictvím kódu. Viz poznámky v **CreateTable** příkladu, který následuje, jak to provést.
   >
   >

    To `<plugins>` části nakonfiguruje [plug-in Apache Maven kompilátoru](https://maven.apache.org/plugins/maven-compiler-plugin/) a [plug-in Apache Maven odstín](https://maven.apache.org/plugins/maven-shade-plugin/). Modul plug-in kompilátoru je používá ke kompilaci topologie. Modul plug-in odstín se používá při prevenci licence duplikace v balíček JAR, který je sestavený Maven. Z důvodů, proč to se používá se, že duplicitní licenčních souborů způsobit chybu v době běhu na clusteru HDInsight. Využitím odstín plug-in maven s `ApacheLicenseResourceTransformer` implementace brání k této chybě.

    Plug-in odstín maven také vytvoří soubor jar uber (nebo fat jar), která obsahuje všechny závislosti vyžadované aplikací.
4. Uložte soubor **pom.xml**.
5. Vytvořte nový adresář s názvem **conf** v **hbaseapp** adresáře. V **conf** adresáři vytvořte soubor s názvem **hbase-site.xml**. Následující tabulka obsahuje obsah souboru:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     https://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Tento soubor se použije k načtení konfigurace HBase pro HDInsight cluster.

   > [!NOTE]  
   > Toto je soubor minimálního hbase-site.xml, a obsahuje úplné minimální nastavení pro HDInsight cluster.

6. Uložit **hbase-site.xml** souboru.

## <a name="create-the-application"></a>Vytvoření aplikace
1. Přejděte **hbaseapp\src\main\java\com\microsoft\examples** adresáře a přejmenovat app.java soubor **CreateTable.java**.
2. Otevřít **CreateTable.java** soubor a nahradit existující obsah následujícím kódem:

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
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

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

    Toto je **CreateTable** třídy, který vytvoří tabulku s názvem **lidé** a jeho naplnění některé předdefinované uživatele.
3. Uložit **CreateTable.java** souboru.
4. V **hbaseapp\src\main\java\com\microsoft\examples** adresáři vytvořte nový soubor s názvem **SearchByEmail.java**. Použijte následující kód jako obsah tohoto souboru:

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

            // Create a new regex filter
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

    **SearchByEmail** třídu lze použít k dotazování pro řádky podle e-mailovou adresu. Protože ho používá filtr regulárních výrazů, můžete zadat řetězce nebo regulárního výrazu při používání třídy.
5. Uložit **SearchByEmail.java** souboru.
6. V **hbaseapp\src\main\hava\com\microsoft\examples** adresáři vytvořte nový soubor s názvem **DeleteTable.java**. Použijte následující kód jako obsah tohoto souboru:

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

    Tato třída je za vyčištění v tomto příkladu tak, že zakázání a odstranění tabulky vytvořené **CreateTable** třídy.
7. Uložit **DeleteTable.java** souboru.

## <a name="build-and-package-the-application"></a>Sestavení a zabalení aplikace
1. Otevřete příkazový řádek a přejděte do adresáře **hbaseapp** adresáře.
2. Použijte následující příkaz k sestavení souboru JAR, který obsahuje aplikace:

        mvn clean package

    To vyčistí všechny předchozí artefakty sestavení, soubory ke stažení všechny závislosti, které dosud nebyly nainstalovány, pak sestavení a balíčky aplikace.
3. Po dokončení příkazu **hbaseapp\target** adresář obsahuje soubor s názvem **hbaseapp – 1.0-SNAPSHOT.jar**.

   > [!NOTE]  
   > **Hbaseapp – 1.0-SNAPSHOT.jar** uber je soubor jar (říká se jim fat jar), který obsahuje všechny závislosti potřebné ke spuštění aplikace.

## <a name="upload-the-jar-file-and-start-a-job"></a>Nahrání souboru JAR a spuštění úlohy
Existuje mnoho způsobů, jak nahrát soubor do vašeho clusteru HDInsight, jak je popsáno v [nahrávání dat pro úlohy Apache Hadoop v HDInsight](hdinsight-upload-data.md). Následující kroky pomocí Azure Powershellu.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Po instalaci a konfiguraci prostředí Azure PowerShell, vytvořte nový soubor s názvem **hbase runner.psm1**. Následující tabulka obsahuje obsah tohoto souboru:

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

    Tento soubor obsahuje dva moduly:

   * **Přidat HDInsightFile** – slouží k nahrání souborů do HDInsight
   * **Start-HBaseExample** – slouží ke spuštění třídy vytvořili dříve
2. Uložit **hbase runner.psm1** souboru.
3. Otevřete nové okno Azure PowerShell, přejděte do adresáře **hbaseapp** adresáře a spusťte následující příkaz.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Změňte cestu na umístění **hbase runner.psm1** soubor vytvořený dříve. To zaregistruje modul pro tuto relaci Azure Powershellu.
4. Použijte následující příkaz k nahrání **hbaseapp – 1.0-SNAPSHOT.jar** ke clusteru HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Nahraďte **název_clusteru_hdinsight** s názvem vašeho clusteru HDInsight. Příkaz nahraje **hbaseapp – 1.0-SNAPSHOT.jar** k **příklad/JAR** umístění primárního úložiště pro váš cluster HDInsight.
5. Až se nahrají soubory, použijte následující kód k vytvoření tabulky pomocí **hbaseapp**:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Nahraďte **název_clusteru_hdinsight** s názvem vašeho clusteru HDInsight.

    Tento příkaz vytvoří novou tabulku s názvem **lidé** ve vašem clusteru HDInsight. Tento příkaz nezobrazí žádný výstup v okně konzoly.
6. K vyhledání položky v tabulce, použijte následující příkaz:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Nahraďte **název_clusteru_hdinsight** s názvem vašeho clusteru HDInsight.

    Tento příkaz používá **SearchByEmail** třída vyhledat všechny řádky, kde **contactinformation** rodiny sloupců a **e-mailu** sloupců, obsahuje řetězec **contoso.com**. Mělo by se zobrazit následující výsledky:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Pomocí **fabrikam.com** pro `-emailRegex` hodnotu vrátí uživatele, kteří mají **fabrikam.com** v poli e-mailu. Protože toto vyhledávání je implementovaný s využitím regulární výraz na základě filtru, můžete taky zadat regulární výrazy, jako například **^ r**, které vrátí položky e-mailu, kde začíná písmenem "r".

## <a name="delete-the-table"></a>Odstranit tabulku
Jakmile budete hotovi s příkladem, použijte následující příkaz z relace prostředí PowerShell Azure odstranit **lidé** tabulka použitá v tomto příkladu:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Nahraďte **název_clusteru_hdinsight** s názvem vašeho clusteru HDInsight.

## <a name="troubleshooting"></a>Řešení potíží
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Žádné výsledky nebo neočekávané výsledky při použití Start HBaseExample
Použití `-showErr` parametr, chcete-li zobrazit standardní chyby (STDERR), který je vytvořen při spuštění úlohy.
