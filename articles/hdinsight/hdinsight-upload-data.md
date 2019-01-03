---
title: Nahrání dat pro úlohy Apache Hadoop v HDInsight
description: Zjistěte, jak nahrát a přístup k datům pro úlohy Apache Hadoop v HDInsight pomocí Azure classic CLI, Průzkumníka služby Azure Storage, Azure Powershellu, příkazového řádku Hadoopu nebo Sqoopu.
keywords: ETL hadoop, přesun dat do služby hadoop, hadoop načítání dat
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: a54c47c0f67052f2ce486a97e009293a118919d4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994108"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Nahrání dat pro úlohy Apache Hadoop v HDInsight

Azure HDInsight nabízí v porovnání s Azure Storage a Azure Data Lake Storage (Gen1 a Gen2) plně funkční distribuovaného systému souborů Hadoop (HDFS). Azure Storage a Data lake Storage Gen1 a Gen2 jsou navrženy jako rozšíření HDFS poskytuje bezproblémové prostředí pro zákazníky. Umožňují celá sada komponent v ekosystému Hadoop pracovat přímo na data, která spravuje. Azure Storage, Data Lake Storage Gen1 a Gen2 jsou systémy různých souborů, které jsou optimalizované pro úložiště dat a výpočty na těchto datech. Informace o výhodách používání služby Azure Storage najdete v tématu [použití služby Azure Storage s HDInsight][hdinsight-storage], [použití Data Lake Storage Gen1 s HDInsight](hdinsight-hadoop-use-data-lake-store.md) a [ Pomocí HDInsight v Data Lake Storage Gen2](../storage/data-lake-storage/use-hdi-cluster.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, vezměte na vědomí následující požadavky:

* Cluster Azure HDInsight. Pokyny najdete v tématu [Začínáme s Azure HDInsight] [ hdinsight-get-started] nebo [clusterů HDInsight vytvořit](hdinsight-hadoop-provision-linux-clusters.md).
* Znalost těchto dvou článcích:

    - [Použití služby Azure Storage s HDInsight][hdinsight-storage]
    - [Data Lake Storage Gen1 pomocí služby HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Data Lake Storage Gen2 pomocí služby HDInsight](../storage/data-lake-storage/use-hdi-cluster.md)   

## <a name="upload-data-to-azure-storage"></a>Nahrání dat do služby Azure Storage

### <a name="command-line-utilities"></a>Nástroje příkazového řádku
Společnost Microsoft poskytuje následující nástroje pro práci s Azure Storage:

| Nástroj | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Klasické rozhraní příkazového řádku Azure][azurecli] |✔ |✔ |✔ |
| [Prostředí Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Příkaz Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> Rozhraní příkazového řádku Azure Classic, prostředí Azure PowerShell a AzCopy můžete využít z mimo Azure, příkaz Hadoop je dostupná pouze na clusteru HDInsight. A tento příkaz umožňuje pouze načítání dat z místního systému souborů do služby Azure Storage.
>
>

#### <a id="xplatcli"></a>Klasické rozhraní příkazového řádku Azure
Rozhraní příkazového řádku Azure Classic je multiplatformní nástroj, který vám umožní spravovat služby Azure. Následujícím postupem nahrání dat do služby Azure Storage:

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. [Instalace a konfigurace rozhraní příkazového řádku Classic Azure pro Mac, Linux a Windows](../cli-install-nodejs.md).
2. Otevřete příkazový řádek, prostředí bash nebo jiné prostředí a použijte následující postup k ověření ke svému předplatnému Azure.

    ```cli
    azure login
    ```

    Po zobrazení výzvy zadejte uživatelské jméno a heslo pro vaše předplatné.
3. Zadejte následující příkaz pro výpis účtů úložiště pro vaše předplatné:

    ```cli
    azure storage account list
    ```

4. Vyberte účet úložiště, který obsahuje objekt blob, kterou chcete pracovat, pak použijte následující příkaz pro načtení klíče pro tento účet:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Tento příkaz vrátí **primární** a **sekundární** klíče. Kopírovat **primární** hodnotu klíče, protože se použije v dalších krocích.
5. Chcete-li načíst seznam kontejnerů objektů blob v účtu úložiště, použijte následující příkaz:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Použijte následující příkazy pro nahrávání a stahování souborů do objektu blob:

   * Pokud chcete nahrát soubor:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Stažení souboru:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]  
> Pokud pracujete se vždy pomocí stejného účtu úložiště, můžete nastavit následující proměnné prostředí místo zadání účtu a klíč pro každý příkaz:
>
> * **AZURE\_ÚLOŽIŠTĚ\_ÚČET**: Název účtu úložiště
> * **AZURE\_ÚLOŽIŠTĚ\_PŘÍSTUP\_KLÍČ**: Klíč účtu úložiště
>
>

#### <a id="powershell"></a>Prostředí Azure PowerShell
Prostředí Azure PowerShell je skriptovací prostředí, které můžete použít k řízení a automatizaci nasazení a správu vašich úloh v Azure. Informace o konfiguraci pracovní stanice ke spuštění prostředí Azure PowerShell najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**K nahrání místního souboru do služby Azure Storage**

1. Otevřete konzolu prostředí Azure PowerShell podle pokynů v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
2. Nastavte hodnoty prvních pět proměnných v následujícím skriptu:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Vložte skript do konzoly prostředí Azure PowerShell ho zkopírovat soubor spustit.

Příklad skriptů PowerShell vytvořili za účelem práce s HDInsight, naleznete v tématu [nástroje HDInsight](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy je nástroj příkazového řádku, který je navržené pro zjednodušení úloh přenosu dat do a z účtu služby Azure Storage. Můžete použít jako samostatný nástroj nebo začlenit do existující aplikace tento nástroj. [Stáhněte si nástroj AzCopy][azure-azcopy-download].

AzCopy syntaxe je následující:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Další informace najdete v tématu [AzCopy – nahrávání a stahování souborů Azure BLOBS][azure-azcopy].

Azcopy v Linuxu ve verzi preview je k dispozici.  Zobrazit [oznamujeme AzCopy v Linuxu ve verzi Preview](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Hadoop příkazového řádku
Příkazový řádek systému Hadoop je platný pouze pro ukládání dat do úložiště Azure blob, pokud data se již nachází hlavního uzlu clusteru.

Chcete-li použít příkaz systému Hadoop, musíte nejdřív připojit k hlavnímu uzlu pomocí jedné z následujících metod:

* **HDInsight se systémem Windows**: [Připojit pomocí vzdálené plochy](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight se systémem Linux**: Připojte se pomocí [SSH nebo PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Jakmile budete připojeni, můžete k nahrání souboru do úložiště následující syntaxi.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Například `hadoop fs -copyFromLocal data.txt /example/data/data.txt`.

Protože výchozí systém souborů pro HDInsight je ve službě Azure Storage, /example/data.txt ve skutečnosti je ve službě Azure Storage. Můžete také odkazovat na soubor jako:

    wasb:///example/data/data.txt

nebo

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Seznam dalších příkazech Hadoop, které pracují se soubory naleznete v tématu [https://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Na clusterech Apache HBase zablokuje výchozí velikost použitou při zápisu dat je 256 KB. I když to funguje správně při použití rozhraní API HBase nebo rozhraní REST API, používat `hadoop` nebo `hdfs dfs` příkazy k zápisu dat větší než ~ 12 GB za následek chyby. Další informace najdete v tématu [výjimce úložiště pro zápis u objektu blob](#storageexception) části v tomto článku.

### <a name="graphical-clients"></a>Grafické klientů
Existuje také několik aplikací, které poskytuje grafické rozhraní pro práci s Azure Storage. V následující tabulce je seznam některé z těchto aplikací:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools pro HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](https://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloudové úložiště Studio 2](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Průzkumník služby Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools pro HDInsight
Další informace najdete v tématu [procházejte propojené prostředky](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources).

#### <a id="storageexplorer"></a>Průzkumník služby Azure Storage
*Průzkumník služby Azure Storage* je užitečným nástrojem pro kontrolu a změnu dat v objektech BLOB. Je bezplatný open source nástroj, který si můžete stáhnout z [ https://storageexplorer.com/ ](https://storageexplorer.com/). Zdrojový kód je k dispozici také tento odkaz.

Před použitím nástroje, musíte znát vaše služby Azure storage účtu název a klíč účtu. Pokyny o tom, jak tyto informace najdete v tématu "jak: Zobrazení, kopírování a znovu vygenerovat přístupové klíče úložiště"část [vytvořit, spravovat nebo odstranit účet úložiště][azure-create-storage-account].

1. Spusťte Průzkumníka služby Azure Storage. Pokud je první spustíte Storage Explorer, zobrazí se výzva k zadání **název účtu _úložiště** a **klíč účtu úložiště**. Pokud jste spustili před, použijte **přidat** tlačítko můžete přidat název nového účtu úložiště a klíč.

    Zadejte název a klíč pro účet úložiště používá HDInsight cluster a pak vyberte **u & LOŽIT OPEN**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. V seznamu kontejnerů nalevo od rozhraní klikněte na název kontejneru, který je spojen s vaším clusterem HDInsight. Ve výchozím nastavení to je název clusteru HDInsight, ale může lišit, pokud jste zadali při vytváření clusteru určitý název.
3. Z panelu nástrojů vyberte ikonu nahrání.

    ![Panel nástrojů se zvýrazněným nahrát ikonu](./media/hdinsight-upload-data/toolbar.png)
4. Zadejte soubor, a potom klikněte na **otevřít**. Po zobrazení výzvy vyberte **nahrát** pro nahrání souboru do kořenového adresáře kontejneru úložiště. Pokud chcete nahrát soubor s konkrétní cestou, zadejte cestu **cílové** pole a pak vyberte **nahrát**.

    ![Dialogové okno pro nahrání souboru](./media/hdinsight-upload-data/fileupload.png)

    Po dokončení nahrávání souboru můžete z úloh na clusteru HDInsight.

### <a name="mount-azure-storage-as-local-drive"></a>Připojení Azure Storage jako místní disk
Zobrazit [připojení Azure Storage jako místní jednotku](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Nahrát pomocí služby
#### <a name="azure-data-factory"></a>Azure Data Factory
Služba Azure Data Factory je plně spravovaná služba pro vytváření dat úložiště, zpracování dat a dat přesun služeb efektivní, škálovatelné a spolehlivé datové kanály, produkčního prostředí.

Azure Data Factory slouží k přesouvání dat do služby Azure Storage a vytvářet kanály zpracování dat, které přímo používat HDInsight funkce, jako je například Hive a Pig.

Další informace najdete v tématu [dokumentace ke službě Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
Sqoop je nástroj určený pro přenos dat mezi platformou Hadoop a relačními databázemi. Můžete ho použít pro import dat ze systému správy relačních databází (RDBMS), jako jsou SQL Server, MySQL nebo Oracle do distribuovaného systému souborů Hadoop (HDFS), transformujte data v Hadoop MapReduce nebo Hive a pak exportovat data zpět do relační databázový systém.

Další informace najdete v tématu [Sqoop použití s HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>Sady SDK pro vývoj
Úložiště Azure lze rovněž přistupovat pomocí sady Azure SDK z těchto programovacích jazycích:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Další informace o instalaci sady Azure SDK najdete v tématu [Azure, soubory ke stažení](https://azure.microsoft.com/downloads/)

### <a name="troubleshooting"></a>Řešení potíží
#### <a id="storageexception"></a>Výjimka úložiště pro zápis u objektu blob
**Příznaky**: Při použití `hadoop` nebo `hdfs dfs` příkazy k zápisu souborů, které jsou přibližně 12 GB nebo více v clusteru služby HBase můžete setkat s následující chybu:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Příčina**: HBase v HDInsight clustery výchozí velikost bloku 256 kB při zápisu do úložiště Azure. Zatímco funguje pro rozhraní API HBase nebo rozhraní REST API, je výsledkem k chybě při použití `hadoop` nebo `hdfs dfs` nástroje příkazového řádku.

**Rozlišení**: Použití `fs.azure.write.request.size` k určení větší velikost bloku. Můžete to provést na základě za použití pomocí `-D` parametru. Následující příkaz je uvedený příklad používající tento parametr se `hadoop` příkaz:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Můžete taky zvýšit hodnotu `fs.azure.write.request.size` globálně pomocí Apache Ambari. Následující postup slouží ke změně hodnoty v webovému uživatelskému rozhraní Ambari:

1. V prohlížeči přejděte na uživatelské rozhraní Ambari Web pro váš cluster. Toto je https://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název vašeho clusteru.

    Po zobrazení výzvy zadejte uživatelské jméno admin a heslo pro cluster.
2. Na levé straně obrazovky vyberte **HDFS**a pak vyberte **Configs** kartu.
3. V **filtr...**  zadejte `fs.azure.write.request.size`. Zobrazí se pole a aktuální hodnota uprostřed stránky.
4. Změňte hodnotu z 262144 (256KB) na novou hodnotu. Například 4194304 (4MB).

![Obrázek změna hodnoty prostřednictvím webového uživatelského rozhraní Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Další informace o použití Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Další postup
Teď, když chápete, jak načíst data do HDInsight, přečtěte si, jak provádět analýzu následujících článcích:

* [Začínáme se službou Azure HDInsight][hdinsight-get-started]
* [Odeslání úloh systému Apache Hadoop prostřednictvím kódu programu][hdinsight-submit-jobs]
* [Použití Apache Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití Apache Pig s HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: https://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-adls-gen1]: hdinsight-hadoop-use-data-lake-store.md
[hdinsight-adls-gen2]: ../storage/data-lake-storage/use-hdi-cluster.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
