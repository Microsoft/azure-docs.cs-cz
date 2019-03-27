---
title: Python UDF s Apache Hivu a Apache Pig – Azure HDInsight
description: Naučte se používat Python uživatele definované funkce (UDF) z Apache Hivu a Apache Pig v HDInsight, zásobníku technologie Apache Hadoop v Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: adcfb308bbbc8e3de456c4e7a71c543f988db02a
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497988"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Použití Pythonu uživatelem definované funkce (UDF) s Apache Hivu a Apache Pig v HDInsight

Naučte se používat Python uživatelem definované funkce (UDF) s Apache Hivu a Apache Pig v Apache Hadoop v Azure HDInsight.

## <a name="python"></a>Pythonu v HDInsight

Python2.7 se instaluje standardně na HDInsight 3.0 nebo novější. Apache Hive je možné s touto verzí Pythonu ke zpracování datových proudů. Zpracování Stream používá k předávání dat mezi aplikací Hive a UDF STDOUT a STDIN.

HDInsight také zahrnuje Jython, což je implementace Python napsané v jazyce Java. Jython běží přímo na virtuálním počítači Java a nepoužívá streamování. Jython je doporučené interpret Pythonu při použití Pythonu s Pig.

## <a name="prerequisites"></a>Požadavky

* **Cluster Hadoop v HDInsight**. Zobrazit [Začínáme s HDInsight v Linuxu](apache-hadoop-linux-tutorial-get-started.md).
* **Klient SSH**. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [Schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) jako primární úložiště vašich clusterů. To může být wasb: / / pro službu Azure Storage, abfs: / / pro Azure Data Lake Storage Gen2 nebo adl: / / pro Azure Data Lake Storage Gen1. Pokud pro Azure Storage nebo Azure Data Lake Storage Gen2 je povoleno zabezpečený přenos, identifikátor URI by wasbs: / / nebo abfss: / /, respektive naleznete také [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).
* **Možné změny úložiště konfigurace.**  Zobrazit [konfiguraci úložiště](#storage-configuration) při použití druhu účtu úložiště `BlobStorage`.
* Volitelné.  Pokud plánujete použít PowerShell, bude nutné [AZ modulu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) nainstalované.

> [!NOTE]  
> Účet úložiště používané v tomto článku se službou Azure Storage [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md) povolena. proto `wasbs` se používá v celém článku.

## <a name="storage-configuration"></a>Konfigurace úložiště
Není vyžadována žádná akce, pokud je účet úložiště používané druhu `Storage (general purpose v1)` nebo `StorageV2 (general purpose v2)`.  Proces v tomto článku vygeneruje výstup do alespoň `/tezstaging`.  Výchozí konfigurace hadoop bude obsahovat `/tezstaging` v `fs.azure.page.blob.dir` konfigurační proměnnou v `core-site.xml` služby `HDFS`.  Způsobí, že tato konfigurace výstup do adresáře se objekty BLOB stránky, které nejsou podporovány pro typ účtu úložiště `BlobStorage`.  Použití `BlobStorage` pro účely tohoto článku, odeberte `/tezstaging` z `fs.azure.page.blob.dir` konfigurační proměnnou.  Konfigurace je přístupný z [uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).  V opačném případě se zobrazí chybová zpráva: `Page blob is not supported for this account type.`

> [!WARNING]  
> Kroky v tomto dokumentu vytvořit následující předpoklady:  
>
> * Vytváření skriptů Pythonu ve svém místním vývojovém prostředí.
> * Nahrajete do služby HDInsight buď pomocí skriptů `scp` příkaz nebo dodaný skript Powershellu.
>
> Pokud chcete použít [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) pro práci s HDInsight, pak je nutné:
>
> * Vytváření skriptů v prostředí cloud shell.
> * Použití `scp` k nahrání souborů ze služby cloud shell pro HDInsight.
> * Použití `ssh` od služby cloud shell pro připojení k HDInsight a spuštění příkladů.

## <a name="hivepython"></a>Apache Hive UDF

Python může sloužit jako UDF z Hive prostřednictvím HiveQL `TRANSFORM` příkazu. Například následující HiveQL vyvolá `hiveudf.py` souboru uloženém na výchozí účet úložiště Azure pro cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Zde je, co dělá v tomto příkladu:

1. `add file` Příkaz na začátku souboru přidá `hiveudf.py` soubor do distribuované mezipaměti, tak, aby byl přístupný pro všechny uzly v clusteru.
2. `SELECT TRANSFORM ... USING` Příkaz vybere data z `hivesampletable`. Také předá hodnoty clientid, devicemake a devicemodel `hiveudf.py` skriptu.
3. `AS` Klauzule popisuje pole vrácená `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Vytvořit soubor

Ve svém vývojovém prostředí, vytvořte textový soubor s názvem `hiveudf.py`. Použijte následující kód jako obsah souboru:

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

1. Přečte řádek dat ze STDIN.
2. Koncový znak nového řádku se odebere pomocí `string.strip(line, "\n ")`.
3. Při zpracování datového proudu, jeden řádek obsahuje všechny hodnoty znakem tabulátoru mezi jednotlivými hodnotami. Takže `string.split(line, "\t")` slouží k rozdělení vstupu na každé kartě vrací pouze pole.
4. Po dokončení zpracování se musí být napsané výstup STDOUT jako jeden řádek, na kartě mezi jednotlivými poli. Například, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while` Smyčka se opakuje dokud ne `line` je pro čtení.

Výstup skriptu je tvořen vstupní hodnoty pro `devicemake` a `devicemodel`a hodnotu hash zřetězené hodnotě.

### <a name="upload-file-shell"></a>Nahrání souboru (prostředí)
V následujících příkazů nahraďte `sshuser` s skutečné uživatelské jméno, pokud se liší.  Nahraďte `mycluster` s názvem skutečné clusteru.  Ujistěte se, že je váš pracovní adresář, kde je umístěn soubor.

1. Použití `scp` ke zkopírování souborů do vašeho clusteru HDInsight. Upravte a zadejte následující příkaz:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Připojte se ke clusteru pomocí SSH.  Upravte a zadejte následující příkaz:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH přidejte soubory pythonu dříve nahráli do úložiště pro cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Použijte Hive UDF (prostředí)

1. Pro připojení k Hivu, použijte následující příkaz z otevřít relaci SSH:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Tento příkaz spustí Beeline klienta.

2. Zadejte následující dotaz na `0: jdbc:hive2://headnodehost:10001/>` řádku:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Po zadání na posledním řádku, se má úloha spustit. Po dokončení úlohy vrátí výstup podobný následujícímu příkladu:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Chcete-li ukončit Beeline, zadejte následující příkaz:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Nahrání souboru (PowerShell)

> [!IMPORTANT]  
> Tyto skripty Powershellu nebudou fungovat, pokud [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md) je povolená.  Použít příkazy prostředí nebo zakázání zabezpečeného přenosu.

Prostředí PowerShell také umožňuje vzdáleně spouštět dotazy Hive. Zkontrolujte pracovní adresář je tam, kde `hiveudf.py` nachází.  Pomocí následujícího skriptu prostředí PowerShell ke spuštění dotazu Hive, který používá `hiveudf.py` skriptu:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

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
> Další informace o nahrávání souborů, najdete v článku [nahrávání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md) dokumentu.


#### <a name="use-hive-udf"></a>Použití Hivu UDF


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

Výstup **Hive** úloha by měla vypadat podobně jako v následujícím příkladu:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>Apache Pig UDF

Skript v jazyce Python se může sloužit jako UDF z Pig prostřednictvím `GENERATE` příkazu. Spuštěním skriptu s použitím Jython nebo C Python.

* Jython poběží JVM a může být volána nativně z Pig.
* C Python je externí proces, takže je odeslání dat z Pig na JVM do skript spuštěný v procesu Pythonu. Výstup skriptu Pythonu se odešle zpět do Pig.

Chcete-li určit interpret Pythonu, použijte `register` při odkazování na skript v jazyce Python. Následující příklady zaregistrovat skriptů Pig jako `myfuncs`:

* **Chcete-li použít Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Použití Pythonu C**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Při použití Jython, cesta k souboru pig_jython může být místní cestu nebo WASBS: / / cestu. Ale při použití Pythonu C, musí odkazovat na soubor v místním systému souborů uzlu, který se používá k odeslání úlohy Pig.

Jednou za registraci, Pig Latin pro účely tohoto příkladu je stejný pro:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Zde je, co dělá v tomto příkladu:

1. První řádek načte ukázkový datový soubor `sample.log` do `LOGS`. Definuje také každý záznam jako `chararray`.
2. Další řádek vyfiltruje všechny hodnoty null ukládání výsledek operace do `LOG`.
3. Pak Iteruje přes záznamy v `LOG` a používá `GENERATE` vyvolat `create_structure` metoda obsažených ve skriptu Python/Jython načíst jako `myfuncs`. `LINE` slouží k předání do funkce aktuální záznam.
4. Nakonec jsou zálohované výstupy STDOUT pomocí `DUMP` příkazu. Tento příkaz zobrazí výsledky po dokončení operace.

### <a name="create-file"></a>Vytvořit soubor

Ve svém vývojovém prostředí, vytvořte textový soubor s názvem `pigudf.py`. Použijte následující kód jako obsah souboru:

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

V tomto příkladu Pig Latin `LINE` vstup je definován jako chararray, protože neexistuje žádný konzistentní schéma pro vstup. Skript v jazyce Python transformuje data na konzistentní schéma pro výstup.

1. `@outputSchema` Prohlášení definuje formát data, která je vrácena pro Pig. V tomto případě má **datový kontejner**, což je datový typ Pig. Kontejneru objektů a dat obsahuje následující pole, z nichž všechny jsou chararray (řetězec):

   * Datum – datum, kdy byla položka protokolu vytvořena
   * čas – čas vytvoření položky protokolu
   * Název třídy položku ClassName - bylo vytvořeno za
   * úroveň – úroveň protokolování
   * Podrobnosti - verbose podrobnosti pro záznam protokolu

2. Dále `def create_structure(input)` definuje funkci, která Pig předá položky řádku.

3. Ukázková data `sample.log`, většinou odpovídá datum, čas, classname, úrovni a podrobně popisují schéma. Však obsahuje několik řádků, které začínají `*java.lang.Exception*`. Tyto řádky musí změnit tak, aby odpovídala schématu. `if` Příkaz vyhledává ty pak massages vstupní data pro přesun `*java.lang.Exception*` řetězec do konce a přenesení dat v řádku se schématem očekávaný výstup.

4. Dále `split` příkaz se používá k rozdělení dat na první čtyři znaky. Výstup je přiřazen do `date`, `time`, `classname`, `level`, a `detail`.

5. Nakonec se vrátí hodnoty pro Pig.

Když data se vrátí do Pig, konzistentní schéma má definované v `@outputSchema` příkazu.



### <a name="upload-file-shell"></a>Nahrání souboru (prostředí)

V následujících příkazů nahraďte `sshuser` s skutečné uživatelské jméno, pokud se liší.  Nahraďte `mycluster` s názvem skutečné clusteru.  Ujistěte se, že je váš pracovní adresář, kde je umístěn soubor.

1. Použití `scp` ke zkopírování souborů do vašeho clusteru HDInsight. Upravte a zadejte následující příkaz:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Připojte se ke clusteru pomocí SSH.  Upravte a zadejte následující příkaz:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH přidejte soubory pythonu dříve nahráli do úložiště pro cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Použití Pigu UDF (prostředí)

1. Pro připojení k pig, použijte následující příkaz z otevřít relaci SSH:

    ```bash
    pig
    ```

2. Zadejte následující příkazy `grunt>` řádku:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Po zadání následujícího řádku, se má úloha spustit. Po dokončení úlohy vrátí výstup podobný následující data:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Použít `quit` opusťte prostředí Grunt, a potom pomocí následujícího postupu upravte soubor pigudf.py v místním systému souborů:

    ```bash
    nano pigudf.py
    ```

5. Jednou v editoru, zrušte komentář u následujícího řádku tak, že odeberete `#` znaků od začátku řádku:

    ```bash
    #from pig_util import outputSchema
    ```

    Tento řádek změní skript v jazyce Python pro práci s Pythonem C namísto Jython. Jakmile změny byly provedeny, použijte **Ctrl + X** ukončete editor. Vyberte **Y**a potom **Enter** a uložte změny.

6. Použití `pig` příkazu se znovu spustit prostředí. Jakmile budete na `grunt>` výzvu, pomocí následujícího postupu spuštění skriptu Pythonu s použitím překladač Pythonu C.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po dokončení této úlohy byste měli vidět stejný výstup jako když jste dříve spustili skriptu s použitím Jython.


### <a name="upload-file-powershell"></a>Nahrání souboru (PowerShell)

> [!IMPORTANT]  
> Tyto skripty Powershellu nebudou fungovat, pokud [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md) je povolená.  Použít příkazy prostředí nebo zakázání zabezpečeného přenosu.

Prostředí PowerShell také umožňuje vzdáleně spouštět dotazy Hive. Zkontrolujte pracovní adresář je tam, kde `pigudf.py` nachází.  Pomocí následujícího skriptu prostředí PowerShell ke spuštění dotazu Hive, který používá `pigudf.py` skriptu:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

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

### <a name="use-pig-udf-powershell"></a>Použití Pigu UDF (PowerShell)

> [!NOTE]  
> Při vzdálené odeslání úlohy pomocí Powershellu, není možné použít jako překladač Pythonu C.

Prostředí PowerShell je také možné spouštět úlohy Pig Latin. Ke spuštění úlohy Pig Latin, který používá `pigudf.py` skriptu, použijte následující příkaz powershellu:

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

Výstup **Pig** úlohy by mělo vypadat zhruba následující data:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Řešení potíží

### <a name="errors-when-running-jobs"></a>Chyby při spouštění úloh

Při spuštění úlohy hive, může dojít k chybě, které jsou podobné následujícímu textu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Tento problém může být způsobeno konce řádků v souboru Python. Mnoho výchozí Windows editory nepoužívají znaky CRLF jako ukončení řádku, ale Linuxové aplikace obvykle můžete očekávat LF.

Odebrat znaky CR před nahráním souboru do HDInsight, můžete použít následující příkazy Powershellu:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Skripty prostředí PowerShell

Oba příklady skriptů Powershellu použitý ke spuštění příkladů obsahují komentovaného řádku, který zobrazuje chybový výstup pro úlohu. Pokud se nezobrazují očekávaný výstup pro úlohu, zrušte komentář následujícího řádku a zobrazit, pokud informace o chybě indikuje problém.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Do úložiště HDInsight jsou také zaznamenány informace chyby (STDERR) a výsledek úlohy (STDOUT).

| Pro tuto úlohu... | Podívejte se na těchto souborů v kontejneru objektů blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/ HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Další kroky

Pokud je potřeba načíst moduly Pythonu, které nejsou k dispozici ve výchozím nastavení, přečtěte si téma [nasazení modulu do Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Další způsoby použití Pig, Hive a další informace o použití prostředí MapReduce, naleznete v následujících dokumentech:

* [Použití Apache Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Apache Pig s HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
