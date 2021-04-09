---
title: Python UDF s Apache Hive a Apache prasete – Azure HDInsight
description: Naučte se používat uživatelsky definované funkce jazyka Python (UDF) z Apache Hive a Apache prasete v HDInsight, Apache Hadoop technologie v Azure.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-python
ms.openlocfilehash: 593b809813f949cd1d0bcc17e1d1b7255ea19130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944271"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Použití uživatelem definovaných funkcí Pythonu (UDF) s Apache Hive a Apache prasetem v HDInsight

Naučte se používat uživatelsky definované funkce Pythonu (UDF) s Apache Hive a Apache prasetem v Apache Hadoop ve službě Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python v HDInsight

Python 2.7 se ve výchozím nastavení instaluje v HDInsight 3,0 a novějších verzích. Apache Hive lze použít s touto verzí Pythonu pro zpracování datových proudů. Zpracování streamu používá STDOUT a STDIN k předávání dat mezi podregistru a systémem souborů UDF.

HDInsight také zahrnuje Jython, což je implementace Pythonu napsaná v jazyce Java. Jython běží přímo na prostředí Java Virtual Machine a nepoužívá streamování. Jython je doporučený interpret Pythonu při použití Pythonu s prasetem.

## <a name="prerequisites"></a>Požadavky

* **Cluster Hadoop ve službě HDInsight**. Viz Začínáme [se službou HDInsight v systému Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Klient SSH**. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [Schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To Azure Storage pro `wasb://` `abfs://` Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2 nebo ADL://. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI by byl wasbs://.  Viz také [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).
* **Možná změna konfigurace úložiště.**  Pokud používáte druh účtu úložiště, podívejte se na téma [Konfigurace úložiště](#storage-configuration) `BlobStorage` .
* Nepovinný parametr.  Pokud plánujete používat PowerShell, budete potřebovat nainstalovaný [modul AZ Module](/powershell/azure/new-azureps-module-az) .

> [!NOTE]  
> Účet úložiště použitý v tomto článku byl Azure Storage s povoleným [zabezpečeným přenosem](../../storage/common/storage-require-secure-transfer.md) , a proto `wasbs` se používá v celém článku.

## <a name="storage-configuration"></a>Konfigurace úložiště

Pokud je účet úložiště použitý jako typ nebo, není nutná žádná `Storage (general purpose v1)` Akce `StorageV2 (general purpose v2)` .  Proces v tomto článku bude mít výstup alespoň do `/tezstaging` .  Výchozí konfigurace Hadoop bude obsažena `/tezstaging` v `fs.azure.page.blob.dir` konfigurační proměnné v nástroji `core-site.xml` for Service `HDFS` .  Tato konfigurace způsobí, že se výstup do adresáře načte do objektů blob stránky, které se nepodporují pro druh účtu úložiště `BlobStorage` .  Chcete-li použít `BlobStorage` Tento článek, odeberte `/tezstaging` z `fs.azure.page.blob.dir` konfigurační proměnné.  Ke konfiguraci je možné přistupovat z [uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).  V opačném případě se zobrazí chybová zpráva: `Page blob is not supported for this account type.`

> [!WARNING]  
> Kroky v tomto dokumentu provedou následující předpoklady:  
>
> * Ve svém místním vývojovém prostředí vytvoříte skripty v Pythonu.
> * Skripty se nahrávají do HDInsight pomocí `scp` příkazu nebo zadaného skriptu PowerShellu.
>
> Pokud chcete použít [Azure Cloud Shell (bash)](../../cloud-shell/overview.md) pro práci s HDInsight, musíte:
>
> * Vytvořte skripty uvnitř prostředí cloud Shell.
> * Slouží `scp` k nahrání souborů z Cloud shellu do HDInsight.
> * Pomocí `ssh` služby Cloud Shell se připojte ke službě HDInsight a spusťte příklady.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python se dá použít jako UDF z podregistru prostřednictvím příkazu HiveQL `TRANSFORM` . Následující HiveQL například vyvolá `hiveudf.py` soubor uložený ve výchozím účtu Azure Storage pro daný cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Tady je příklad:

1. `add file`Příkaz na začátku souboru přidá `hiveudf.py` soubor do distribuované mezipaměti, takže bude přístupný pro všechny uzly v clusteru.
2. `SELECT TRANSFORM ... USING`Příkaz vybere data z `hivesampletable` . Také předá do skriptu hodnoty ClientID, devicemake a devicemodel `hiveudf.py` .
3. `AS`Klauzule popisuje pole vrácená z `hiveudf.py` .

<a name="streamingpy"></a>

### <a name="create-file"></a>Vytvořit soubor

Ve vývojovém prostředí vytvořte textový soubor s názvem `hiveudf.py` . Jako obsah souboru použijte následující kód:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Tento skript provede následující akce:

1. Přečte řádek dat ze standardního vstupu.
2. Koncový znak nového řádku se odebere pomocí `string.strip(line, "\n ")` .
3. Při zpracování datového proudu obsahuje jeden řádek všechny hodnoty se znakem tabulátoru mezi každou hodnotou. Takže `string.split(line, "\t")` se dá použít k rozdělení vstupu na jednotlivých kartách a vrácení pouze polí.
4. Po dokončení zpracování musí být výstup zapsán do STDOUT jako jeden řádek, přičemž karta mezi jednotlivými poli. Například, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while`Smyčka se opakuje `line` , dokud není přečtena žádná.

Výstup skriptu je zřetězení vstupních hodnot pro a a hodnotu `devicemake` `devicemodel` hash zřetězené hodnoty.

### <a name="upload-file-shell"></a>Nahrát soubor (prostředí)

V následujících příkazech nahraďte `sshuser` skutečným uživatelským jménem, pokud se liší.  Nahraďte `mycluster` skutečným názvem clusteru.  Ujistěte se, že je soubor umístěný v pracovním adresáři.

1. Použijte `scp` ke zkopírování souborů do clusteru HDInsight. Upravte a zadejte následující příkaz:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Pomocí SSH se připojte ke clusteru.  Upravte a zadejte následující příkaz:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH přidejte do úložiště pro cluster soubory Pythonu, které jste předtím nahráli.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Použití podregistru UDF (Shell)

1. Pokud se chcete připojit k podregistru, použijte následující příkaz z otevřené relace SSH:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Tento příkaz spustí klienta Beeline.

2. Do `0: jdbc:hive2://headnodehost:10001/>` příkazového řádku zadejte následující dotaz:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Po zadání posledního řádku by se měla úloha spustit. Po dokončení úlohy vrátí výstup podobný následujícímu příkladu:

    ```output
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    ```

4. Pokud chcete ukončit Beeline, zadejte následující příkaz:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Nahrát soubor (PowerShell)

PowerShell se dá použít taky ke vzdálenému spuštění dotazů na podregistr. Ujistěte se, kde je umístěn pracovní adresář `hiveudf.py` .  Pomocí následujícího skriptu prostředí PowerShell spusťte dotaz na podregistr, který používá `hiveudf.py` skript:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Další informace o nahrávání souborů najdete v tématu [nahrávání dat pro úlohy Apache Hadoop v dokumentu HDInsight](../hdinsight-upload-data.md) .

#### <a name="use-hive-udf"></a>Použití podregistru UDF

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Výstup úlohy **podregistru** by měl vypadat podobně jako v následujícím příkladu:

```output
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
```

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache prasete UDF

Skript Pythonu se dá použít jako UDF z prasete prostřednictvím `GENERATE` příkazu. Skript můžete spustit buď pomocí Jython, nebo C Pythonu.

* Jython běží na JVM a dá se nativně volat z prasete.
* C Python je externí proces, takže data z prasete v JVM se odesílají do skriptu spuštěného v rámci procesu Pythonu. Výstup skriptu Pythonu se pošle zpátky do prasete.

K určení interpretu Pythonu použijte `register` při odkazování na skript Pythonu. V následujících příkladech jsou registrovány skripty pomocí prasete jako `myfuncs` :

* **Použití Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Použití jazyka C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Při použití Jython může být cesta k souboru pig_jython buď místní cesta, nebo cesta WASBS://. Při použití jazyka C Python však musíte odkazovat na soubor v místním systému souborů uzlu, který používáte k odeslání úlohy prasete.

Po registraci v minulosti je latinku pro tento příklad stejný jako u obou:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Tady je příklad:

1. První řádek načte Ukázkový datový soubor `sample.log` do `LOGS` . Také definuje každý záznam jako `chararray` .
2. Další řádek filtruje všechny hodnoty null a ukládá výsledek operace do `LOG` .
3. Dále provede iteraci záznamů v `LOG` a používá `GENERATE` k vyvolání `create_structure` metody obsažené v skriptu Python/Jython, který byl načten jako `myfuncs` . `LINE` slouží k předání aktuálního záznamu do funkce.
4. Nakonec jsou výstupy do STDOUT pomocí `DUMP` příkazu. Tento příkaz zobrazí výsledky po dokončení operace.

### <a name="create-file"></a>Vytvořit soubor

Ve vývojovém prostředí vytvořte textový soubor s názvem `pigudf.py` . Jako obsah souboru použijte následující kód:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

V latinském příkladu pro vepřové písmo `LINE` je vstup definovaný jako CharArray, protože pro vstup neexistuje žádné konzistentní schéma. Skript Pythonu transformuje data do konzistentního schématu pro výstup.

1. `@outputSchema`Příkaz definuje formát dat vrácených do prasete. V tomto případě je to **datový kontejner**, což je datový typ prasete. Penalta obsahuje následující pole, z nichž všechny jsou CharArray (řetězce):

   * Datum – datum vytvoření položky protokolu
   * čas – čas vytvoření položky protokolu
   * ClassName – název třídy, pro kterou byla položka vytvořena
   * úroveň – úroveň protokolu
   * Podrobnosti – podrobné informace o položce protokolu

2. Dále `def create_structure(input)` definuje funkci, do které prase předá položky řádku.

3. Tato ukázková data `sample.log` většinou odpovídají schématu data, čas, ClassName, Level a detail. Obsahuje však několik řádků, které začínají na `*java.lang.Exception*` . Tyto řádky musí být upraveny tak, aby odpovídaly schématu. `if`Příkaz tyto prvky vyhledá a pak zprávy vstupní data, aby se řetězec přesunul na `*java.lang.Exception*` konec, a tak přinese data do očekávaného výstupního schématu.

4. V dalším kroku se `split` pomocí příkazu rozdělí data na první čtyři znaky mezery. Výstup je přiřazen do `date` , `time` ,, `classname` `level` a `detail` .

5. Nakonec se hodnoty vrátí do prasete.

Když se vrátí data do prasete, má konzistentní schéma, jak je definováno v `@outputSchema` příkazu.

### <a name="upload-file-shell"></a>Nahrát soubor (prostředí)

V následujících příkazech nahraďte `sshuser` skutečným uživatelským jménem, pokud se liší.  Nahraďte `mycluster` skutečným názvem clusteru.  Ujistěte se, že je soubor umístěný v pracovním adresáři.

1. Použijte `scp` ke zkopírování souborů do clusteru HDInsight. Upravte a zadejte následující příkaz:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Pomocí SSH se připojte ke clusteru.  Upravte a zadejte následující příkaz:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH přidejte do úložiště pro cluster soubory Pythonu, které jste předtím nahráli.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Použití systému souborů prasete (Shell)

1. Pokud se chcete připojit k prase, použijte následující příkaz z otevřené relace SSH:

    ```bash
    pig
    ```

2. Na příkazovém řádku zadejte následující příkazy `grunt>` :

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Po zadání následujícího řádku by se měla úloha spustit. Po dokončení úlohy vrátí výstup podobný následujícímu datům:

    ```output
    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
    ```

4. Použijte `quit` k ukončení prostředí grunt a pak použijte následující pro úpravu souboru pigudf.py v místním systému souborů:

    ```bash
    nano pigudf.py
    ```

5. V editoru odkomentujte následující řádek odebráním `#` znaku z začátku řádku:

    ```bash
    #from pig_util import outputSchema
    ```

    Tento řádek upraví skript Pythonu pro práci s C Python místo Jython. Po provedení změny můžete Editor ukončit **stisknutím kombinace kláves CTRL + X** . Vyberte **Y** a pak **zadáním** uložte změny.

6. Pomocí `pig` příkazu znovu spusťte prostředí. Po `grunt>` zobrazení výzvy použijte následující příkaz ke spuštění skriptu Pythonu s použitím interpretu jazyka C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po dokončení této úlohy byste měli vidět stejný výstup jako při předchozím spuštění skriptu pomocí Jython.

### <a name="upload-file-powershell"></a>Nahrát soubor (PowerShell)

PowerShell se dá použít taky ke vzdálenému spuštění dotazů na podregistr. Ujistěte se, kde je umístěn pracovní adresář `pigudf.py` .  Pomocí následujícího skriptu prostředí PowerShell spusťte dotaz na podregistr, který používá `pigudf.py` skript:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Použití systému souborů prasete (PowerShell)

> [!NOTE]  
> Při vzdáleném odesílání úlohy pomocí PowerShellu není možné jako interpret použít jazyk C Python.

PowerShell můžete také použít ke spouštění úloh v latince pro vepřové prostředí. Chcete-li spustit úlohu s latinkou prasete, která používá `pigudf.py` skript, použijte následující skript prostředí PowerShell:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Výstup pro úlohu **vepřového chovu** by měl vypadat podobně jako u následujících dat:

```output
((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
```

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Řešení potíží

### <a name="errors-when-running-jobs"></a>Chyby při spouštění úloh

Při spuštění úlohy podregistru se může zobrazit chyba podobná následujícímu textu:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Tento problém může být způsoben koncem řádku v souboru Python. Mnoho editorů Windows ve výchozím nastavení používá klávesu CRLF jako zakončení řádku, ale aplikace pro Linux obvykle očekávají LF.

Před nahráním souboru do HDInsight můžete pomocí následujících příkazů PowerShellu odebrat znaky CR:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Skripty prostředí PowerShell

Oba příklady skriptů PowerShellu, které se používají ke spuštění příkladů, obsahují řádek s komentářem, který zobrazuje chybový výstup úlohy. Pokud nevidíte očekávaný výstup pro úlohu, zrušte komentář následujícího řádku a zkontrolujte, zda informace o chybě signalizuje problém.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Informace o chybě (STDERR) a výsledek úlohy (STDOUT) jsou také protokolovány do úložiště HDInsight.

| Pro tuto úlohu... | Podívejte se na tyto soubory v kontejneru objektů BLOB. |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Další kroky

Pokud potřebujete načíst moduly Pythonu, které nejsou k dispozici ve výchozím nastavení, přečtěte si téma [nasazení modulu do Azure HDInsight](/archive/blogs/benjguin/how-to-deploy-a-python-module-to-windows-azure-hdinsight).

Další způsoby použití prasete, podregistru a informací o použití MapReduce najdete v následujících dokumentech:

* [Použití Apache Hive se službou HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)