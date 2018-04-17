---
title: Python UDF s Apache Hive a vepřových - Azure HDInsight | Microsoft Docs
description: Další informace o použití Python uživatele definované funkce (UDF) z Hive a Pig v HDInsight, technologie zásobníku Hadoop v Azure.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.devlang: python
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: c508ffcdd5cccaad5c7b0717d33e2ea1449ab522
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Funkce (UDF) s Hive a Pig definované uživatelem Python použití v HDInsight

Naučte se používat s Apache Hive a Pig v Hadoop v Azure HDInsight Python uživatelsky definované funkce (UDF).

## <a name="python"></a>Python v HDInsight

Python2.7 je nainstalována ve výchozím nastavení na HDInsight 3.0 a novější. Apache Hive lze použít s touto verzí jazyka Python pro zpracování datového proudu. Zpracování datového proudu používá STDOUT a stdin – k předávání dat mezi Hive a UDF.

HDInsight zahrnuje taky Jython, což je implementace Python napsanou v jazyce Java. Jython běží přímo na virtuálním počítači Java a nepoužívá streamování. Jython je doporučené překladač Pythonu při použití Python s Pig.

> [!WARNING]
> Kroky v tomto dokumentu provést následující předpoklady: 
>
> * Vytváření skriptů Python ve svém místním vývojovém prostředí.
> * Nahrát skripty do HDInsight pomocí `scp` příkaz z relace místní Bash nebo zadaný skript prostředí PowerShell.
>
> Pokud chcete použít [prostředí cloudu Azure (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) preview pro práci s HDInsight, pak musíte:
>
> * Vytváření skriptů v prostředí shell cloudovému prostředí.
> * Použití `scp` ukládání souborů z prostředí cloudu do HDInsight.
> * Použití `ssh` z cloudové prostředí pro připojení k HDInsight a spuštění příkladů.

## <a name="hivepython"></a>Hive UDF

Python lze použít jako UDF z Hive prostřednictvím HiveQL `TRANSFORM` příkaz. Například následující HiveQL vyvolá `hiveudf.py` souboru uložený v výchozí účet úložiště Azure pro cluster.

**HDInsight se systémem Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight se systémem Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Na clusterech HDInsight se systémem Windows `USING` klauzule musíte zadat úplnou cestu k python.exe.

Zde je, jaké jsou v tomto příkladu:

1. `add file` Přidá příkaz na začátku souboru `hiveudf.py` soubor do distribuované mezipaměti, tak, aby byl přístupný pro všechny uzly v clusteru.
2. `SELECT TRANSFORM ... USING` Příkaz vybere data z `hivesampletable`. Také předává clientid, devicemake a devicemodel hodnoty, které mají `hiveudf.py` skriptu.
3. `AS` Klauzule popisuje pole vrácená z `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Vytvoření souboru hiveudf.py


Na vašem vývojovém prostředí, vytvořte textový soubor s názvem `hiveudf.py`. Použijte následující kód jako obsah souboru:

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

1. Čtení řádek dat z STDIN.
2. Koncový znak nového řádku je odebrat pomocí `string.strip(line, "\n ")`.
3. Při provádění zpracování datového proudu, jeden řádek obsahuje všechny hodnoty pomocí znaku tabulátoru mezi každé hodnotě. Proto `string.split(line, "\t")` slouží k rozdělení vstupu v každé kartě vrací pouze pole.
4. Po dokončení zpracování musí být výstup zapsané do datového proudu STDOUT jako jeden řádek, na kartě mezi každé pole. Například, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while` Smyčky opakuje, dokud ne `line` je pro čtení.

Výstup skriptu je tvořen vstupní hodnoty pro `devicemake` a `devicemodel`a hodnota hash zřetězených hodnoty.

V tématu [spuštění příkladů](#running) informace o spuštění tohoto příkladu v clusteru HDInsight.

## <a name="pigpython"></a>Pig UDF

Skript v jazyce Python, můžete využít jako UDF z Pig prostřednictvím `GENERATE` příkaz. Můžete spustit skript pomocí Jython nebo C Python.

* Jython běží na systém JVM a lze volat nativně z Pig.
* C Python je externí proces, takže data z Pig na systém JVM odeslání do skriptu, který běží v procesu Python. Výstup skriptu Python je odeslána zpět do Pig.

Pokud chcete zadat překladač Pythonu, použijte `register` při odkazování na skript Pythonu. Následující příklady zaregistrovat skriptů Pig jako `myfuncs`:

* **Chcete-li použít Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Použít C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Při použití Jython, cestu k souboru pig_jython může být místní cesta nebo WASB: / / cesta. Pokud používáte C Python, ale musí odkazovat souboru v místním systému souborů uzlu, který používáte se odeslat úlohu Pig.

Jednou za registraci Pig Latin pro tento příklad je stejný pro obě:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Zde je, jaké jsou v tomto příkladu:

1. První řádek načte ukázkový datový soubor, `sample.log` do `LOGS`. Definuje také každý záznam jako `chararray`.
2. Na další řádek odfiltruje všechny hodnoty null ukládání výsledek operace do `LOG`.
3. V dalším kroku ji iteruje nad záznamy v `LOG` a používá `GENERATE` má být vyvolán `create_structure` metoda obsažených ve skriptu Python nebo Jython načíst jako `myfuncs`. `LINE` slouží k předávání na aktuální záznam funkce.
4. Nakonec jsou zálohované výstupy STDOUT pomocí `DUMP` příkaz. Tento příkaz zobrazí výsledky po dokončení operace.

### <a name="create-the-pigudfpy-file"></a>Vytvoření souboru pigudf.py

Na vašem vývojovém prostředí, vytvořte textový soubor s názvem `pigudf.py`. Použijte následující kód jako obsah souboru:

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

V příkladu Pig Latin `LINE` vstup je definován jako chararray, protože neexistuje žádný konzistentní schéma pro vstup. Skript Pythonu transformuje dat na konzistentní schéma pro výstup.

1. `@outputSchema` Příkaz definuje formát data, která se vrátí do Pig. V takovém případě má **datový kontejner**, což je datový typ Pig. Kontejneru objektů a dat obsahuje následující pole, které jsou chararray (řetězce):

   * Datum - datum vytvoření položky protokolu
   * Time – čas vytvoření položky protokolu
   * Název třídy - název třídy položka byla vytvořena pro
   * úroveň – úroveň protokolu
   * Podrobnosti o - verbose podrobnosti záznam protokolu

2. Dále `def create_structure(input)` definuje funkci, která Pig předá řádku položek.

3. Příklad dat, `sample.log`, většinou vyhovuje datum, čas, classname úrovni a podrobností schématu. Obsahuje však pár řádků, které začínají `*java.lang.Exception*`. Tyto řádky musí být upraven tak, aby odpovídala schématu. `if` Příkaz kontroluje pro ty pak massages vstupní data přesunout `*java.lang.Exception*` řetězec za účelem uvedení data v řádku se schématem očekávaný výstup.

4. Dále `split` příkaz slouží k rozdělení dat na první čtyři znaky. Výstup je přiřazen do `date`, `time`, `classname`, `level`, a `detail`.

5. Nakonec hodnoty jsou vráceny do Pig.

Pokud data se vrátí k Pig, má konzistentní schéma definované v `@outputSchema` příkaz.

## <a name="running"></a>Nahrání a spuštění příkladů

> [!IMPORTANT]
> **SSH** kroky fungovat jenom s clusterem HDInsight se systémem Linux. **Prostředí PowerShell** kroky fungovat s clusterem Linux nebo HDInsight se systémem Windows, ale vyžaduje klienta Windows.

### <a name="ssh"></a>SSH

Další informace o používání SSH najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Použití `scp` zkopírovat soubory ke svému clusteru HDInsight. Například následující příkaz zkopíruje soubory do clusteru s názvem **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Připojte se ke clusteru pomocí SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Z relace SSH přidejte soubory python dříve nahrán do úložiště WASB pro cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Mezi nahráním souborů, použijte následující postup ke spuštění úloh Hive a Pig.

#### <a name="use-the-hive-udf"></a>Použijte Hive UDF

1. Pro připojení k Hive, použijte následující příkaz:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Tento příkaz spustí Beeline klienta.

2. Zadejte následující dotaz na `0: jdbc:hive2://headnodehost:10001/>` řádku:

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Po zadání poslední řádek, by měla spustit úlohu. Po dokončení úlohy vrátí výstup podobně jako v následujícím příkladu:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Chcete-li ukončit Beeline, použijte následující příkaz:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Použijte Pig UDF

1. Pro připojení k pig, použijte následující příkaz:

    ```bash
    pig
    ```

2. Zadejte následující příkazy v `grunt>` řádku:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Po zadání následujícího řádku, by měla spustit úlohu. Po dokončení úlohy vrátí výstup podobná následující data:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Použít `quit` opusťte prostředí Grunt, a potom pomocí následující upravit soubor pigudf.py v místním systému souborů:

    ```bash
    nano pigudf.py
    ```

5. Jednou v editoru, zrušte komentář u následujícího řádku odebráním `#` znak od začátku řádku:

    ```bash
    #from pig_util import outputSchema
    ```

    Tento řádek upravuje skript Pythonu pro práci s C Python místo Jython. Jakmile změny byly provedeny, použijte **Ctrl + X** ukončete editor. Vyberte **Y**a potom **Enter** a uložte změny.

6. Použití `pig` příkaz ke spuštění prostředí znovu. Jakmile jste na `grunt>` řádku, použijte tento příkaz pro spuštění skriptu Python pomocí překladač Pythonu C.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po dokončení této úlohy, měli byste vidět stejný výstup jako když byl již spuštěn skript pomocí Jython.

### <a name="powershell-upload-the-files"></a>Prostředí PowerShell: Odesílat soubory

Prostředí PowerShell můžete použít k nahrání souborů na HDInsight server. K odeslání souborů Python použijte následující skript:

> [!IMPORTANT] 
> Postup v této části použijte prostředí Azure PowerShell. Další informace o použití prostředí Azure PowerShell najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Změna `C:\path\to` hodnotu do cesty k souborům na vašem vývojovém prostředí.

Tento skript načte informace pro váš cluster HDInsight a extrahuje účtu a klíč pro výchozí účet úložiště a odesílá soubory do kořenového adresáře kontejneru.

> [!NOTE]
> Další informace o nahrávání souborů najdete v tématu [nahrávání dat pro úlohy Hadoop do HDInsight](../hdinsight-upload-data.md) dokumentu.

#### <a name="powershell-use-the-hive-udf"></a>Prostředí PowerShell: Podregistr UDF pomocí

Prostředí PowerShell lze také vzdáleně spouštět dotazy Hive. Pomocí následujícího skriptu prostředí PowerShell pro spouštění dotazů Hive, který používá **hiveudf.py** skriptu:

> [!IMPORTANT]
> Dřív, než spustíte, skript zobrazí výzvu pro protokol HTTPs nebo správu informací o účtu pro váš cluster HDInsight.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

Výstup **Hive** úlohy by měl vypadat přibližně v následujícím příkladu:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

Prostředí PowerShell můžete použít také ke spuštění úloh Pig Latin. Spustit úlohu Pig Latin, který používá **pigudf.py** skriptu, použijte následující skript prostředí PowerShell:

> [!NOTE]
> Při odesílání vzdáleně úlohu pomocí prostředí PowerShell, není možné použít C Python jako překladač.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

Výstup **Pig** úlohy by měl vypadat přibližně následující data:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Řešení potíží

### <a name="errors-when-running-jobs"></a>Chyby při spuštění úlohy

Při spuštění úlohy hive, můžete se setkat chyba podobná následující text:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Tento problém může být způsobeno konce řádků v souboru Python. Mnoho Windows editory výchozí nastavení Line FEED ukončování řádků, ale aplikace Linux obvykle očekávat LF.

Chcete-li odebrat znaky CR před nahráním souboru do HDInsight můžete použít následující příkazy prostředí PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Skripty prostředí PowerShell

Obě skriptů prostředí PowerShell příklad, který se používá ke spouštění příklady obsahují komentáři řádek, který zobrazí chyba výstup úlohy. Pokud nevidíte očekávaný výstup úlohy, zrušte komentář u následujícího řádku a zobrazit, pokud informace o chybě indikuje problém.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Informace o chybě (STDERR) a výsledek úlohy (STDOUT) jsou taky zaznamenává do úložiště HDInsight.

| Pro tuto úlohu... | Podívejte se na tyto soubory v kontejneru objektů blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/ HivePython/stdout |
| Pig |/ PigPython/stderr<p>/ PigPython/stdout |

## <a name="next"></a>Další kroky

Pokud budete potřebovat načíst moduly jazyka Python, které nejsou k dispozici ve výchozím nastavení, najdete v části [nasazení modul Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Další způsoby použití vepřových, Hive a další informace o použití prostředí MapReduce, najdete v následujících dokumentech:

* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s HDInsight](hdinsight-use-mapreduce.md)
