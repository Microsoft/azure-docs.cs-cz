---
title: Migrace z úložiště HDFS on-prem do Azure Storage pomocí Azure Data Boxu
description: Migrace dat z místního úložiště HDFS do Úložiště Azure
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301996"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migrace z úložiště HDFS on-prem do Azure Storage pomocí Azure Data Boxu

Data z místního úložiště HDFS vašeho clusteru Hadoop můžete migrovat do Azure Storage (úložiště objektů blob nebo Storage Data Lake Storage Gen2) pomocí zařízení Data Box. Můžete si vybrat z datové schránky o velikosti 80 TB nebo datové schránky o velikosti 770 TB.

Tento článek vám pomůže dokončit tyto úkoly:

> [!div class="checklist"]
> * Připravte se na migraci dat.
> * Zkopírujte data do datové schránky nebo do zařízení Data Box Heavy.
> * Doručte zařízení zpět společnosti Microsoft.
> * Použití přístupových oprávnění pro soubory a adresáře (pouze data lake storage gen2)

## <a name="prerequisites"></a>Požadavky

K dokončení migrace potřebujete tyto věci.

* Účet služby Azure Storage.

* Místní cluster Hadoop, který obsahuje zdrojová data.

* Zařízení [Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Objednejte si datovou schránku](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) nebo [datovou schránku Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). 

  * Kabel a připojte [datovou schránku](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) nebo [datovou schránku Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) k místní síti.

Pokud jste připraveni, začněme.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopírování dat do zařízení Datové schránky

Pokud se vaše data vejdou do jednoho zařízení Data Box, zkopírujete je do zařízení Data Box. 

Pokud velikost dat překročí kapacitu zařízení Data Box, použijte [volitelný postup k rozdělení dat mezi více zařízení datové schránky](#appendix-split-data-across-multiple-data-box-devices) a proveďte tento krok. 

Pokud chcete zkopírovat data z místního úložiště HDFS do zařízení Data Box, nastavíte několik věcí a pak použijete nástroj [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Podle těchto kroků zkopírujte data prostřednictvím rozhraní REST API úložiště objektů Blob/Object do zařízení datové schránky. Rozhraní rozhraní REST API způsobí, že se zařízení zobrazí jako úložiště HDFS do clusteru.

1. Před kopírováním dat přes REST identifikujte základní prvky zabezpečení a připojení pro připojení k rozhraní REST v datové schránce nebo datové schránce Heavy. Přihlaste se k místnímu webovému uživatelskému uživatelskému uživatelskému nastavení datové schránky a přejděte na stránku **Připojit a zkopírovat.** V části Nastavení **přístupu**v části Azure storage account pro vaše zařízení vyhledejte a vyberte **REST**.

    ![Stránka "Připojit a zkopírovat"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. V dialogovém okně úložiště accessu a nahrávání dat zkopírujte **koncový bod služby Blob** a **klíč účtu úložiště**. Z koncového bodu služby objektu `https://` blob vynechete koncové lomítko a koncové.

    V tomto případě je koncový `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`bod: . Hostitelská část identifikátoru URI, kterou `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`budete používat, je: . Například viz jak se [připojit k rest přes http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialogové okno Přístup k účtu úložiště a nahrávání dat](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Přidejte koncový bod a ip adresu datové schránky `/etc/hosts` nebo datové schránky těžký uzel na každém uzlu.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Pokud používáte jiný mechanismus pro DNS, měli byste zajistit, že koncový bod datové schránky lze vyřešit.

4. Nastavte proměnnou `azjars` prostředí na `hadoop-azure` umístění `azure-storage` souborů a jar. Tyto soubory najdete v instalačním adresáři Hadoop.

    Chcete-li zjistit, zda tyto soubory `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`existují, použijte následující příkaz: . Nahraďte `<hadoop_install_dir>` zástupný symbol cestou k adresáři, do kterého jste hadoop nainstalovali. Ujistěte se, že používáte plně kvalifikované cesty.

    Příklady:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Vytvořte kontejner úložiště, který chcete použít pro kopírování dat. Jako součást tohoto příkazu byste měli také zadat cílový adresář. To by mohlo být fiktivní cílový adresář v tomto bodě.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Nahraďte `<blob_service_endpoint>` zástupný symbol názvem koncového bodu služby objektů blob.

    * Nahraďte `<account_key>` zástupný symbol přístupovým klíčem svého účtu.

    * Nahraďte `<container-name>` zástupný symbol názvem kontejneru.

    * Nahraďte `<destination_directory>` zástupný symbol názvem adresáře, do kterého chcete data zkopírovat.

6. Spusťte příkaz seznamu a ujistěte se, že byl vytvořen kontejner a adresář.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Nahraďte `<blob_service_endpoint>` zástupný symbol názvem koncového bodu služby objektů blob.

   * Nahraďte `<account_key>` zástupný symbol přístupovým klíčem svého účtu.

   * Nahraďte `<container-name>` zástupný symbol názvem kontejneru.

7. Zkopírujte data z úložiště objektů blob hadoophdfs do úložiště objektů blob datové schránky do kontejneru, který jste vytvořili dříve. Pokud není nalezen adresář, do kterého kopírujete, příkaz jej automaticky vytvoří.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Nahraďte `<blob_service_endpoint>` zástupný symbol názvem koncového bodu služby objektů blob.

    * Nahraďte `<account_key>` zástupný symbol přístupovým klíčem svého účtu.

    * Nahraďte `<container-name>` zástupný symbol názvem kontejneru.

    * Nahraďte `<exlusion_filelist_file>` zástupný symbol názvem souboru, který obsahuje seznam vyloučení souborů.

    * Nahraďte `<source_directory>` zástupný symbol názvem adresáře, který obsahuje data, která chcete zkopírovat.

    * Nahraďte `<destination_directory>` zástupný symbol názvem adresáře, do kterého chcete data zkopírovat.

    Tato `-libjars` možnost se používá `hadoop-azure*.jar` k `azure-storage*.jar` zpřístupnění `distcp`a závislých souborů společnosti . K tomu může již dojít u některých clusterů.

    Následující příklad ukazuje, `distcp` jak se příkaz používá ke kopírování dat.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Chcete-li zvýšit rychlost kopírování:

    * Zkuste změnit počet mapovačů. (Výše uvedený `m` příklad používá = 4 mappers.)

    * Zkuste spustit `distcp` více souběžně.

    * Nezapomeňte, že velké soubory fungují lépe než malé soubory.

## <a name="ship-the-data-box-to-microsoft"></a>Odeslání datové schránky společnosti Microsoft

Podle těchto kroků připravte a dodejte zařízení datové schránky společnosti Microsoft.

1. Nejprve [se připravte k odeslání na datovou schránku nebo datovou schránku Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Po dokončení přípravy zařízení stáhněte soubory kusovníku. Tato kusovník nebo soubory manifestu budete později používat k ověření dat nahraných do Azure.

3. Vypněte zařízení a odstraňte kabely.

4. Naplánujte vyzvednutí službou UPS.

    * Informace o zařízeních s datovou schránkou najdete [v tématu Odeslání datové schránky](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Informace o zařízeních Data Box Heavy najdete [v tématu Odeslání datové schránky Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Poté, co společnost Microsoft přijme vaše zařízení, je připojeno k síti datového centra a data se nahrají do účtu úložiště, který jste zadali při objednávce zařízení. U souborů kusovníků ověřte, že se všechna vaše data nahrají do Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Použití přístupových oprávnění pro soubory a adresáře (pouze data lake storage gen2)

Data už máte do svého účtu Azure Storage. Nyní použijete přístupová oprávnění k souborům a adresářům.

> [!NOTE]
> Tento krok je potřeba jenom v případě, že používáte Azure Data Lake Storage Gen2 jako úložiště dat. Pokud používáte pouze účet úložiště objektů blob bez hierarchického oboru názvů jako úložiště dat, můžete tuto část přeskočit.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Vytvoření instančního objektu pro váš účet Azure Data Lake Storage Gen2

Pokud chcete vytvořit instanční objekt, [přečtěte si postup: Pomocí portálu vytvořte instanční objekt Azure AD, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Při provádění kroků v [části Přiřazení aplikace k](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) části role článku nezapomeňte přiřadit roli **přispěvatele dat objektů blob úložiště** k instančnímu objektu.

* Při provádění kroků v [get hodnoty pro přihlášení v](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) části článku, uložit ID aplikace a tajné hodnoty klienta do textového souboru. Brzy je budeš potřebovat.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generování seznamu zkopírovaných souborů s jejich oprávněními

Z místního clusteru Hadoop spusťte tento příkaz:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Tento příkaz generuje seznam zkopírovaných souborů s jejich oprávněními.

> [!NOTE]
> V závislosti na počtu souborů v HDFS může spuštění tohoto příkazu trvat dlouho.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Vygenerujte seznam identit a namapujte je na identity služby Azure Active Directory (ADD)

1. Stáhněte `copy-acls.py` si skript. Podívejte [se na stažení pomocných skriptů a nastavte hraniční uzel tak, aby je spouštěl](#download-helper-scripts) v tomto článku.

2. Spusťte tento příkaz a vygenerujte seznam jedinečných identit.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Tento skript generuje soubor `id_map.json` s názvem, který obsahuje identity, které je třeba mapovat na identity založené na ADD.

3. Otevřete `id_map.json` soubor v textovém editoru.

4. Pro každý objekt JSON, který se `target` zobrazí v souboru, aktualizujte atribut hlavního jména uživatele AAD (UPN) nebo ObjectId (OID) příslušnou mapovanou identitou. Po dokončení soubor uložte. Tento soubor budete potřebovat v dalším kroku.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Použití oprávnění ke zkopírovaným souborům a použití mapování identit

Spuštěním tohoto příkazu můžete použít oprávnění k datům, která jste zkopírovali do účtu Gen2 úložiště datového jezera:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Nahraďte `<storage-account-name>` zástupný symbol názvem svého účtu úložiště.

* Nahraďte `<container-name>` zástupný symbol názvem kontejneru.

* `<application-id>` Nahraďte `<client-secret>` zástupné symboly a ID aplikace a tajný klíč klienta, které jste shromáždili při vytváření instančního objektu.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Dodatek: Rozdělení dat mezi více zařízení datové schránky

Před přesunutím dat do zařízení Data Box si budete muset stáhnout některé pomocné skripty, zajistit, aby se vaše data vešla do zařízení Data Box, a vyloučit nepotřebné soubory.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Stažení pomocných skriptů a nastavení hraničního uzlu pro jejich spuštění

1. Z hraničního nebo hlavního uzlu místního clusteru Hadoop spusťte tento příkaz:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Tento příkaz klonuje úložiště GitHub, které obsahuje pomocné skripty.

2. Ujistěte se, že mají balíček [jq](https://stedolan.github.io/jq/) nainstalovaný v místním počítači.

   ```bash
   
   sudo apt-get install jq
   ```

3. Nainstalujte [balíček Požadavky](https://2.python-requests.org/en/master/) pythonu.

   ```bash
   
   pip install requests
   ```

4. Nastavte oprávnění ke spuštění požadovaných skriptů.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Ujistěte se, že jsou vaše data uspořádána tak, aby se vešla do zařízení Data Box

Pokud velikost dat přesáhne velikost jednoho zařízení Datové schránky, můžete soubory rozdělit do skupin, které můžete uložit na více zařízení Data Box.

Pokud vaše data nepřekročí velikost zařízení singe Data Box, můžete přejít k další části.

1. Se zvýšenými oprávněními `generate-file-list` spusťte skript, který jste stáhli podle pokynů v předchozí části.

   Zde je popis parametrů příkazu:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Zkopírujte seznamy generovaných souborů do systému HDFS tak, aby byly přístupné úloze [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Vyloučení nepotřebných souborů

Budete muset vyloučit některé adresáře z úlohy DisCp. Vylučte například adresáře, které obsahují informace o stavu, které udržují cluster spuštěný.

V místním clusteru Hadoop, kde plánujete zahájit úlohu DistCp, vytvořte soubor, který určuje seznam adresářů, které chcete vyloučit.

Tady je příklad:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak funkce Data Lake Storage Gen2 funguje s clustery HDInsight. Viz [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
