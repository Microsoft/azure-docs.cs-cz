---
title: Python UDF s Apache Hive a Apache Pig - Azure HDInsight
description: Naučte se používat uživatelem definované funkce Pythonu (UDF) od Apache Hive a Apache Pig v HDInsightu, zásobníku technologie Apache Hadoop v Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 201bb40e5024442587f5508886da7e844f35be40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74148403"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Použití uživatelem definovaných funkcí Pythonu (UDF) s Apache Hive a Apache Pig v HDInsightu

Naučte se používat uživatelem definované funkce Pythonu (UDF) s Apache Hive a Apache Pig v Apache Hadoop na Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python na HDInsight

Python2.7 je ve výchozím nastavení nainstalován na HDInsight 3.0 a novější. Apache Hive lze použít s touto verzí Pythonu pro zpracování datového proudu. Zpracování datového proudu používá STDOUT a STDIN k přenosu dat mezi Hive a UDF.

HDInsight také zahrnuje Jython, což je implementace Pythonu napsaná v Javě. Jython běží přímo na Java Virtual Machine a nepoužívá streamování. Jython je doporučený překladač Pythonu při použití Pythonu s prasetem.

## <a name="prerequisites"></a>Požadavky

* **Cluster Hadoop na HDInsight**. Viz [Začínáme s HDInsight na Linuxu](apache-hadoop-linux-tutorial-get-started.md).
* **Klient SSH**. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [Schéma URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To by `wasb://` bylo pro `abfs://` Azure Storage, pro Azure Data Lake Storage Gen2 nebo adl:// pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI bude wasbs://.  Viz také [bezpečný přenos](../../storage/common/storage-require-secure-transfer.md).
* **Možná změna konfigurace úložiště.**  Viz [Konfigurace úložiště,](#storage-configuration) pokud `BlobStorage`používáte druh účtu úložiště .
* Nepovinný parametr.  Pokud plánujete používat PowerShell, budete potřebovat nainstalovaný [modul AZ.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

> [!NOTE]  
> Účet úložiště použitý v tomto článku byl Azure `wasbs` Storage s [povoleným zabezpečeným přenosem](../../storage/common/storage-require-secure-transfer.md) a proto se používá v celém článku.

## <a name="storage-configuration"></a>Konfigurace úložiště

Žádná akce je vyžadována, pokud `Storage (general purpose v1)` je `StorageV2 (general purpose v2)`použitý účet úložiště druhu nebo .  Proces v tomto článku bude produkovat `/tezstaging`výstup alespoň .  Výchozí konfigurace hadoopu `/tezstaging` bude `fs.azure.page.blob.dir` obsahovat `core-site.xml` v `HDFS`konfigurační proměnné v pro službu .  Tato konfigurace způsobí, že výstup do adresáře bude objekty BLOB `BlobStorage`stránky, které nejsou podporovány pro druh účtu úložiště .  Chcete-li použít `BlobStorage` pro `/tezstaging` tento `fs.azure.page.blob.dir` článek, odeberte z proměnné konfigurace.  Konfigurace je přístupná z [ui Ambari](../hdinsight-hadoop-manage-ambari.md).  V opačném případě se zobrazí chybová zpráva:`Page blob is not supported for this account type.`

> [!WARNING]  
> Kroky v tomto dokumentu provést následující předpoklady:  
>
> * Skripty Pythonu můžete vytvořit v místním vývojovém prostředí.
> * Skripty nahrajete do služby `scp` HDInsight pomocí příkazu nebo poskytnutého skriptu prostředí PowerShell.
>
> Pokud chcete používat [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) pro práci s HDInsight, pak musíte:
>
> * Vytvořte skripty uvnitř prostředí cloudshellu.
> * Slouží `scp` k nahrání souborů z prostředí cloudu do HDInsightu.
> * Použijte `ssh` z prostředí cloudu pro připojení k HDInsight a spusťte příklady.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python lze použít jako UDF z Hive prostřednictvím příkazu HiveQL. `TRANSFORM` Například následující HiveQL vyvolá `hiveudf.py` soubor uložený ve výchozím účtu úložiště Azure pro cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Zde je to, co tento příklad dělá:

1. Příkaz `add file` na začátku souboru `hiveudf.py` přidá soubor do distribuované mezipaměti, takže je přístupný všem uzlům v clusteru.
2. Příkaz `SELECT TRANSFORM ... USING` vybere data z `hivesampletable`. Také předá clientid, devicemake a devicemodel `hiveudf.py` hodnoty skriptu.
3. Klauzule `AS` popisuje pole vrácená z `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Vytvořit soubor

Ve vývojovém prostředí vytvořte `hiveudf.py`textový soubor s názvem . Jako obsah souboru použijte následující kód:

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

Tento skript provádí následující akce:

1. Přečte řádek dat z STDIN.
2. Koncový znak nového řádku `string.strip(line, "\n ")`je odebrán pomocí .
3. Při zpracování datového proudu obsahuje jeden řádek všechny hodnoty se znakem tabulátoru mezi jednotlivými hodnotami. Takže `string.split(line, "\t")` lze použít k rozdělení vstupu na každé kartě, vrací pouze pole.
4. Po dokončení zpracování musí být výstup zapsán do STDOUT jako jeden řádek s tabulátorem mezi jednotlivými poli. Například, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Smyčka `while` se opakuje, `line` dokud se nečte.

Výstup skriptu je zřetězení vstupních hodnot pro `devicemake` a `devicemodel`a hash zřetězené hodnoty.

### <a name="upload-file-shell"></a>Nahrát soubor (shell)

V níže uvedených `sshuser` příkazech nahraďte skutečným uživatelským jménem, pokud se liší.  Nahraďte `mycluster` skutečným názvem clusteru.  Ujistěte se, že váš pracovní adresář je tam, kde je soubor umístěn.

1. Slouží `scp` ke kopírování souborů do clusteru HDInsight. Upravit a zadat příkaz níže:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Připojení ke clusteru pomocí SSH.  Upravit a zadat příkaz níže:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH přidejte soubory pythonu nahrané dříve do úložiště pro cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Použití hive UDF (skořepina)

1. Chcete-li se připojit k Hive, použijte následující příkaz z otevřené relace SSH:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Tento příkaz spustí klienta Beeline.

2. Na `0: jdbc:hive2://headnodehost:10001/>` výzvu zadejte následující dotaz:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Po zadání posledního řádku by měla být úloha zahájena. Po dokončení úlohy vrátí výstup podobný následujícímu příkladu:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Chcete-li ukončit linku Beeline, zadejte následující příkaz:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Nahrát soubor (PowerShell)

Prostředí PowerShell lze také použít ke vzdálenému spuštění dotazů Hive. Ujistěte se, `hiveudf.py` že váš pracovní adresář je tam, kde se nachází.  Ke spuštění dotazu Hive, který `hiveudf.py` skript používá, použijte následující skript prostředí PowerShell:

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
> Další informace o nahrávání souborů najdete v [tématu Nahrávání dat pro úlohy Apache Hadoop v dokumentu HDInsight.](../hdinsight-upload-data.md)

#### <a name="use-hive-udf"></a>Použití hive UDF

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

Výstup úlohy **Hive** by měl vypadat podobně jako v následujícím příkladu:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache Prase UDF

Skript Pythonu lze použít jako UDF `GENERATE` z Pig prostřednictvím příkazu. Skript můžete spustit pomocí Jython nebo C Python.

* Jython běží na JVM, a může být nativně volána z Prase.
* C Python je externí proces, takže data z Pig na JVM jsou odesílána do skriptu spuštěného v procesu Pythonu. Výstup skriptu Pythonu je odeslán zpět do Pig.

Chcete-li určit interpret `register` Pythonu, použijte při odkazování na skript Pythonu. Následující příklady registrují skripty `myfuncs`s Pig jako :

* **Chcete-li použít Jython**:`register '/path/to/pigudf.py' using jython as myfuncs;`
* **Chcete-li použít C Python**:`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Při použití Jython, cesta k souboru pig_jython může být buď místní cestu nebo WASBS:// cestu. Však při použití C Python, musíte odkazovat na soubor v místním systému souborů uzlu, který používáte k odeslání pig úlohy.

Po předchozí registraci je prasečí latinka pro tento příklad stejná pro oba:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Zde je to, co tento příklad dělá:

1. První řádek načte ukázkový `sample.log` `LOGS`datový soubor do . Definuje také každý záznam `chararray`jako .
2. Další řádek filtruje všechny hodnoty null a ukládá výsledek `LOG`operace do .
3. Dále itetuje přes `LOG` záznamy `GENERATE` v a `create_structure` používá k vyvolání metody obsažené ve `myfuncs`skriptu Python/Jython načtenjako . `LINE`slouží k předání aktuálního záznamu funkci.
4. Nakonec výstupy jsou vypovězeny `DUMP` do STDOUT pomocí příkazu. Tento příkaz zobrazí výsledky po dokončení operace.

### <a name="create-file"></a>Vytvořit soubor

Ve vývojovém prostředí vytvořte `pigudf.py`textový soubor s názvem . Jako obsah souboru použijte následující kód:

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

V příkladu Pig `LINE` Latin je vstup definován jako chararray, protože neexistuje žádné konzistentní schéma pro vstup. Skript Pythonu transformuje data do konzistentní schéma pro výstup.

1. Příkaz `@outputSchema` definuje formát dat, která jsou vrácena pig. V tomto případě se jedná o **datový vak**, což je datový typ Pig. Taška obsahuje následující pole, z nichž všechny jsou chararray (řetězce):

   * datum - datum vytvoření položky protokolu
   * time - čas vytvoření položky protokolu
   * název třídy - název třídy, pro který byla položka vytvořena
   * úroveň - úroveň protokolu
   * detail - podrobné údaje pro záznam protokolu

2. Dále `def create_structure(input)` definuje funkci, které Pig předává řádkové položky.

3. Příkladová data `sample.log`, , většinou odpovídá schématu data, času, názvu třídy, úrovně a podrobností. Obsahuje však několik řádků, `*java.lang.Exception*`které začínají . Tyto řádky musí být upraveny tak, aby odpovídaly schématu. Příkaz `if` zkontroluje pro ty, pak masíruje vstupní data přesunout `*java.lang.Exception*` řetězec na konec, čímž data v souladu s očekávané výstupní schéma.

4. V dalším `split` případě se příkaz používá k rozdělení dat na první čtyři znaky mezery. Výstup je přiřazen `date` `time`do `classname` `level`, `detail`, , a .

5. Nakonec jsou hodnoty vráceny pig.

Když jsou data vrácena pig, má konzistentní schéma, jak `@outputSchema` je definováno v příkazu.

### <a name="upload-file-shell"></a>Nahrát soubor (shell)

V níže uvedených `sshuser` příkazech nahraďte skutečným uživatelským jménem, pokud se liší.  Nahraďte `mycluster` skutečným názvem clusteru.  Ujistěte se, že váš pracovní adresář je tam, kde je soubor umístěn.

1. Slouží `scp` ke kopírování souborů do clusteru HDInsight. Upravit a zadat příkaz níže:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Připojení ke clusteru pomocí SSH.  Upravit a zadat příkaz níže:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH přidejte soubory pythonu nahrané dříve do úložiště pro cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Použití Pig UDF (shell)

1. Chcete-li se připojit k praseti, použijte následující příkaz z otevřené relace SSH:

    ```bash
    pig
    ```

2. Na výzvu zadejte následující příkazy: `grunt>`

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Po zadání následujícího řádku by měla být úloha zahájena. Po dokončení úlohy vrátí výstup podobný následujícím datům:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Slouží `quit` k ukončení prostředí Grunt a následné úpravě souboru pigudf.py v místním systému souborů:

    ```bash
    nano pigudf.py
    ```

5. Poté, co v editoru, odkomentujte následující řádek odebráním `#` znaku ze začátku řádku:

    ```bash
    #from pig_util import outputSchema
    ```

    Tento řádek upravuje skript Pythonu pro práci s C Python místo Jython. Po provedení změny ukončete editor pomocí **kombinace kláves Ctrl+X.** Vyberte **Y**a pak **Enter** uložte změny.

6. Pomocí `pig` příkazu znovu spusťte prostředí. Jakmile jste na `grunt>` výzvu, použijte následující spuštění skriptu Pythonu pomocí interpretu C Pythonu.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po dokončení této úlohy byste měli vidět stejný výstup, jako když jste dříve spustili skript pomocí Jythonu.

### <a name="upload-file-powershell"></a>Nahrát soubor (PowerShell)

Prostředí PowerShell lze také použít ke vzdálenému spuštění dotazů Hive. Ujistěte se, `pigudf.py` že váš pracovní adresář je tam, kde se nachází.  Ke spuštění dotazu Hive, který `pigudf.py` skript používá, použijte následující skript prostředí PowerShell:

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

### <a name="use-pig-udf-powershell"></a>Použití Pig UDF (PowerShell)

> [!NOTE]  
> Při vzdáleném odesílání úlohy pomocí prostředí PowerShell není možné použít C Python jako interpret.

Prostředí PowerShell lze také použít ke spuštění úlohy Pig Latin. Chcete-li spustit úlohu `pigudf.py` Pig Latin, která používá skript, použijte následující skript prostředí PowerShell:

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

Výstup pro úlohu **Pig** by měl vypadat podobně jako následující data:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Řešení potíží

### <a name="errors-when-running-jobs"></a>Chyby při spouštění úloh

Při spuštění úlohy podregistru může dojít k chybě podobné následujícímu textu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Tento problém může být způsoben zakončení řádků v souboru Pythonu. Mnoho editorů systému Windows výchozí použití CRLF jako konec řádku, ale linuxové aplikace obvykle očekávají LF.

Před nahráním souboru do HDInsightu můžete odebrat následující příkazy prostředí PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Skripty prostředí PowerShell

Oba ukázkové skripty prostředí PowerShell použité ke spuštění příkladů obsahují řádek s poznámkami, který zobrazuje výstup chyby pro úlohu. Pokud nevidíte očekávaný výstup pro úlohu, odkomentujte následující řádek a zjistěte, zda informace o chybě označují problém.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Informace o chybě (STDERR) a výsledek úlohy (STDOUT) jsou také zaznamenány do úložiště HDInsight.

| Pro tuhle práci... | Podívejte se na tyto soubory v kontejneru objektů blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Další kroky

Pokud potřebujete načíst moduly Pythonu, které nejsou k dispozici ve výchozím nastavení, přečtěte [si, jak nasadit modul do Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Další způsoby použití pigu, úlu a informace o používání MapReduce najdete v následujících dokumentech:

* [Použití Apache Hive s HDInsight](hdinsight-use-hive.md)
* [Použití mapreduce s HDInsight](hdinsight-use-mapreduce.md)
