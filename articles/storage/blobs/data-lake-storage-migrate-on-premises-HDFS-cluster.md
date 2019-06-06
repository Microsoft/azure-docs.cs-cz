---
title: Použití Azure Data Box pro migraci dat z místní HDFS ukládat do služby Azure Storage
description: Migrovat data z úložiště místní HDFS do služby Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 06/05/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9a42135df38cde91cc6626a3f7d0328334af0a5d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729047"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Použití Azure Data Box pro migraci dat z úložiště místní HDFS do služby Azure Storage

Můžete migrovat data z HDFS úložiště v místním clusteru Hadoop do služby Azure Storage (úložiště objektů blob nebo Data Lake Storage Gen2) s použitím zařízení Data Box. Můžete vybrat z pole 80 TB dat nebo velkého pole Data 770 TB.

Tento článek vám pomůže dokončit tyto úlohy:

:heavy_check_mark: Kopírování dat do zařízení Data Box nebo zařízení Data Box náročné.

:heavy_check_mark: Odešlete zařízení zpět do Microsoftu.

:heavy_check_mark: Přesun dat do účtu úložiště Data Lake Storage Gen2.

## <a name="prerequisites"></a>Požadavky

Potřebujete tyto věci k dokončení migrace.

* Účet služby Azure Storage, který **nebude** mají hierarchické obory názvů na něm povolený.

* Pokud chcete používat účet Azure Data Lake Storage Gen2 (účet úložiště, který **nemá** mají hierarchické obory názvů na něm povolený), pak můžete chtít vytvořit v tomto okamžiku.

* Místní cluster Hadoop, která obsahuje zdrojová data.

* [Zařízení Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

    - [Pořadí Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) nebo [Data Box náročná na výkon](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Při řazení zařízení, nezapomeňte si zvolte účet úložiště, který **nebude** mají hierarchické obory názvů na něm povolený. Je to proto, že zařízení Data Box ještě nepodporují přímý ingestování do Azure Data Lake Storage Gen2. Je potřeba zkopírovat do účtu úložiště a pak proveďte druhá kopie do účtu ADLS Gen2. Pokyny k tomu jsou uvedeny v následujících krocích.
    - Zapojení a připojte vaše [zařízení Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) nebo [Data Box náročné](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) k místní síti.

Pokud je všechno připravené, Pojďme začít.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopírování dat do zařízení Data Box

Pokud chcete zkopírovat data z vašeho místního úložiště HDFS do zařízení Data Box, budete nastavit pár věcí a pak použít [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) nástroj.

Pokud se množství dat, která kopírujete je větší než kapacita jednu zařízení Data Box nebo že jeden uzel na Data Box náročné, rozdělte vaši datovou sadu do velikosti, které patří do vašeho zařízení.

Postupujte podle těchto kroků ke kopírování dat pomocí REST API z/objekt Blob úložiště pro vaše zařízení Data Box. Zařízení se zobrazí jako úložiště HDFS do clusteru způsobí, že rozhraní REST API. 


1. Před kopírováním dat přes REST, identifikujte primitivy zabezpečení a připojení pro připojení k rozhraní REST na zařízení Data Box nebo Data Box náročné. Přihlaste se k místní webové uživatelské rozhraní zařízení Data Box a přejděte na **připojit a Kopírovat** stránky. Využívající službu Azure storage account pro vaše zařízení v části **přístup k nastavení**, vyhledejte a vyberte **REST**.

    ![Stránka "Připojit a kopírovat."](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. V účtu úložiště přístup a nahrávání dat dialogového okna, zkopírujte **koncový bod služby Blob service** a **klíč účtu úložiště**. Z koncového bodu služby objektů blob, vynechejte `https://` a do adresy koncové lomítko.

    V takovém případě je koncový bod: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Je část hostitele v identifikátoru URI, které budete používat: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Příklad najdete v tématu Jak [připojit k REST přes protokol http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialogové okno "Přístup k účtu úložiště a nahrávání dat"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Přidat koncový bod a adresu IP zařízení Data Box nebo Data Box náročné uzel k `/etc/hosts` na každém uzlu.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Pokud použijete některé mechanismus pro službu DNS, ujistěte se, že dá vyřešit koncový bod zařízení Data Box.
    
4. Nastavte proměnnou prostředí `azjars` přejděte `hadoop-azure` a `microsoft-windowsazure-storage-sdk` jar soubory. Tyto soubory jsou v instalačním adresáři Hadoop (pokud tyto soubory existují, použijte tento příkaz můžete zkontrolovat `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` kde `<hadoop_install_dir>` je adresář, kam jste nainstalovali Hadoop) používat úplné cesty. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

5. Vytvoření kontejneru úložiště, který chcete použít pro kopírování dat. Jako součást tohoto příkazu byste zadat také cílovou složku. Může to být fiktivní cílové složky v tomto okamžiku.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -mkdir -p  wasb://[container_name]@[blob_service_endpoint]/[destination_folder]
    ```

6. Spusťte příkaz seznamu k zajištění, že byly vytvořeny kontejner a složku.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -ls -R  wasb://[container_name]@[blob_service_endpoint]/
    ```

7. Zkopírujte data z Hadoop HDFS do úložiště objektů Blob Data pole, do kontejneru, který jste vytvořili dříve. Pokud není nalezen, která kopírujete do složky, příkaz ho automaticky vytvoří.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   `-libjars` Možnost se používá, aby `hadoop-azure*.jar` a rolích dependent `azure-storage*.jar` soubory k dispozici `distcp`. Už to může nastat u některých clusterů.
   
   Následující příklad ukazuje způsob, jakým `distcp` příkazu se použije ke zkopírování dat.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
Aby se zvýšila rychlost kopírování:
- Zkuste změnit počet mapovačů. (Výše uvedený příklad používá `m` = 4 mapovačů.)
- Zkuste spustit více `distcp` paralelně.
- Mějte na paměti, že velké soubory poskytují vyšší výkon než malých souborů.
    
## <a name="ship-the-data-box-to-microsoft"></a>Příjemce zařízení Data Box společnosti Microsoft

Postupujte podle těchto kroků Připravíme a odešleme zařízení Data Box společnosti Microsoft.

1. Jakmile se kopírování dat dokončí, spusťte:
    
    - [Příprava k odeslání na zařízení Data Box nebo Data Box náročné](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).
    - Po dokončení přípravy zařízení stahujte soubory BOM. Budete používat tyto BOM nebo manifest soubory později chcete ověřit nahrání dat do Azure. 
    - Vypněte zařízení a odeberte kabely.
2.  Vyzvednutí s UPS naplánujte. Postupujte podle pokynů:

    - [Odeslání vašeho zařízení Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) 
    - [Dodávejte vaše Data pole těžké](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).
3.  Poté, co Microsoft obdrží zařízení, je připojený k síti datového centra a že data jsou odeslána do účtu úložiště, který jste zadali (s hierarchické obory názvů, zakázáno) Pokud je uskutečněn pořadí zařízení. Proti BOM soubory ověřte, že všechna vaše data se nahraje do Azure. Tato data teď můžete přesunout do účtu úložiště Data Lake Storage Gen2.


## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Přesun dat do účtu úložiště Data Lake Storage Gen2

Tento krok je nutný, pokud použijete jako úložiště dat Azure Data Lake Storage Gen2. Pokud používáte pouze účet blob storage bez hierarchického oboru názvů jako úložiště dat, není potřeba tento krok.

Můžete to provést dvěma způsoby.

- Použití [Azure Data Factory k přesunu dat do ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Budete muset zadat **Azure Blob Storage** jako zdroj.

- Použijte cluster Hadoop založené na Azure. Můžete spustit tento příkaz DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Tento příkaz zkopíruje data i metadata z vašeho účtu úložiště do účtu úložiště Data Lake Storage Gen2.

## <a name="next-steps"></a>Další postup

Zjistěte, jak služba Data Lake Storage Gen2 pracuje s clustery HDInsight. Zobrazit [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
