---
title: Pomocí Azure Data Box migrujte data z místního úložiště HDFS do Azure Storage
description: Migrace dat z místního úložiště HDFS do Azure Storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: ff23b27b73918734e10a481cbe9b1f77519b8764
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847268"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Pomocí Azure Data Box migrujte data z místního úložiště HDFS do Azure Storage

Data můžete migrovat z místního úložiště HDFS vašeho clusteru Hadoop do Azure Storage (úložiště objektů BLOB nebo Data Lake Storage Gen2) pomocí zařízení Data Box. Můžete vybrat z Data Box 80 TB nebo 770-TB Data Box Heavy.

Tento článek vám pomůže dokončit tyto úlohy:

> [!div class="checklist"]
> * Připravte se na migraci dat.
> * Zkopírujte data do Data Box nebo Data Box Heavyho zařízení.
> * Dodejte zařízení zpět společnosti Microsoft.
> * Přesuňte data na Data Lake Storage Gen2.

## <a name="prerequisites"></a>Požadavky

K dokončení migrace potřebujete tyto věci.

* Dva účty úložiště; ten, který má povolen hierarchický obor názvů, a druhý.

* Místní cluster Hadoop, který obsahuje vaše zdrojová data.

* [Zařízení Azure Data box](https://azure.microsoft.com/services/storage/databox/).

  * [Seřazení data box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) nebo [data box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Při objednávání zařízení si nezapomeňte zvolit účet úložiště, na kterém **nejsou** povolené hierarchické obory názvů. Důvodem je to, že zařízení Data Box ještě nepodporují přímé přijímání do Azure Data Lake Storage Gen2. Budete se muset zkopírovat do účtu úložiště a pak provést druhou kopii účtu ADLS Gen2. Pokyny k tomuto postupu jsou uvedené v následujících krocích.

  * Připojte [data box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) nebo [data box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) k místní síti kabelem.

Pokud jste připraveni, začněte.

## <a name="copy-your-data-to-a-data-box-device"></a>Zkopírování dat do zařízení Data Box

Pokud se data vejdou do jednoho Data Box zařízení, zkopírují se data do Data Box zařízení. 

Pokud velikost dat překročí kapacitu Data Box zařízení, pak pomocí [volitelného postupu rozdělte data mezi více zařízení data box](#appendix-split-data-across-multiple-data-box-devices) a pak tento krok proveďte. 

Pokud chcete zkopírovat data z místního úložiště HDFS do zařízení Data Box, nastavili jste několik věcí a pak použijte nástroj [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

Pomocí těchto kroků zkopírujte data prostřednictvím rozhraní REST API pro úložiště objektů BLOB a objektů do zařízení Data Box. Rozhraní REST API umožní, aby se zařízení zobrazilo jako úložiště HDFS do vašeho clusteru.

1. Před kopírováním dat pomocí REST Identifikujte prvky zabezpečení a připojení, které se připojí k rozhraní REST na Data Box nebo Data Box Heavy. Přihlaste se k místnímu webovému uživatelskému rozhraní Data Box a přejít na stránku **připojit a kopírovat** . Na účtu úložiště Azure pro vaše zařízení v části **nastavení přístupu**vyhledejte a vyberte **REST**.

    ![Stránka připojit a kopírovat](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. V dialogu pro přístup k účtu úložiště a nahrávání dat zkopírujte **BLOB Service koncový bod** a **klíč účtu úložiště**. Z koncového bodu služby objektů BLOB vynechejte `https://` a koncové lomítko.

    V tomto případě je koncový bod: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Část hostitele identifikátoru URI, kterou budete používat, je: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Příklad najdete v tématu Jak se [připojit k REST přes protokol HTTP](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialogová okna přístup k účtu úložiště a nahrání dat](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Přidejte koncový bod a data box nebo data box Heavy IP adresu uzlu do `/etc/hosts` každého uzlu.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Pokud používáte nějaký jiný mechanismus pro DNS, měli byste zajistit, aby bylo možné přeložit koncový bod Data Box.

4. Nastavte proměnnou `azjars` prostředí na umístění `hadoop-azure` `azure-storage` souborů JAR. Tyto soubory najdete v instalačním adresáři Hadoop.

    Chcete-li zjistit, zda tyto soubory existují, použijte následující `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`příkaz:. `<hadoop_install_dir>` Zástupný text nahraďte cestou k adresáři, do kterého jste nainstalovali Hadoop. Nezapomeňte použít plně kvalifikované cesty.

    Příklady:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar``azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Vytvořte kontejner úložiště, který chcete použít pro kopírování dat. Jako součást tohoto příkazu byste měli taky zadat cílový adresář. V tomto okamžiku může být fiktivní cílový adresář.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * `<blob_service_endpoint>` Zástupný symbol nahraďte názvem vašeho koncového bodu služby BLOB Service.

    * `<account_key>` Zástupný symbol nahraďte přístupovým klíčem vašeho účtu.

    * `<container-name>` Zástupný symbol nahraďte názvem vašeho kontejneru.

    * `<destination_directory>` Zástupný symbol nahraďte názvem adresáře, do kterého chcete zkopírovat data.

6. Spuštěním příkazu seznam zkontrolujte, zda byl vytvořen kontejner a adresář.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * `<blob_service_endpoint>` Zástupný symbol nahraďte názvem vašeho koncového bodu služby BLOB Service.

   * `<account_key>` Zástupný symbol nahraďte přístupovým klíčem vašeho účtu.

   * `<container-name>` Zástupný symbol nahraďte názvem vašeho kontejneru.

7. Zkopírujte data ze služby Hadoop HDFS do úložiště objektů BLOB Data Box do kontejneru, který jste vytvořili dříve. Pokud adresář, který kopírujete do, nebyl nalezen, příkaz jej automaticky vytvoří.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * `<blob_service_endpoint>` Zástupný symbol nahraďte názvem vašeho koncového bodu služby BLOB Service.

    * `<account_key>` Zástupný symbol nahraďte přístupovým klíčem vašeho účtu.

    * `<container-name>` Zástupný symbol nahraďte názvem vašeho kontejneru.

    * `<exlusion_filelist_file>` Zástupný text nahraďte názvem souboru, který obsahuje seznam vyloučení souborů.

    * `<source_directory>` Zástupný text nahraďte názvem adresáře, který obsahuje data, která chcete kopírovat.

    * `<destination_directory>` Zástupný symbol nahraďte názvem adresáře, do kterého chcete zkopírovat data.

    Možnost slouží k `hadoop-azure*.jar` zpřístupnění a závislých `azure-storage*.jar` souborů pro `distcp`. `-libjars` K tomu může již dojít u některých clusterů.

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
  
    Zlepšení rychlosti kopírování:

    * Zkuste změnit počet mapovačů. (Výše uvedený příklad používá `m` = 4 mapovače.)

    * Zkuste spustit více `distcp` paralelně.

    * Pamatujte, že velké soubory fungují lépe než malé soubory.

## <a name="ship-the-data-box-to-microsoft"></a>Odeslání Data Box Microsoftu

Pomocí těchto kroků Připravte a odešlete zařízení Data Box společnosti Microsoft.

1. Nejprve [Příprava k odeslání na data box nebo data box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Až se dokončí příprava zařízení, Stáhněte soubory BOM. Tyto soubory kusovníku nebo manifest budete později používat k ověření dat odesílaných do Azure.

3. Vypněte zařízení a odeberte kabely.

4. Naplánujte vyzvednutí pomocí zdroje UPS.

    * Data Box zařízení najdete v tématu věnovaném dodávání [data box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Data Box Heavy zařízení najdete v tématu věnovaném dodávání [data box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Jakmile Microsoft přijme vaše zařízení, připojí se k síti datového centra a data se nahrají do zadaného účtu úložiště (s nepovolenými hierarchickými obory názvů), když jste umístili pořadí zařízení. Ověřte soubory kusovníku, že všechna vaše data jsou nahraná do Azure. Tato data teď můžete přesunout do účtu úložiště Data Lake Storage Gen2.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Přesunout data do Azure Data Lake Storage Gen2

Data už máte ve svém účtu Azure Storage. Teď budete kopírovat data do svého účtu úložiště Azure Data Lake a použít přístupová oprávnění k souborům a adresářům.

> [!NOTE]
> Tento krok je nutný, pokud používáte Azure Data Lake Storage Gen2 jako úložiště dat. Pokud používáte pouze účet Blob Storage bez hierarchického oboru názvů jako úložiště dat, můžete tuto část přeskočit.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Kopírovat data na účet Azure Data Lake Storage Gen 2

Data můžete kopírovat pomocí Azure Data Factory nebo pomocí clusteru Hadoop založeného na Azure.

* Pokud chcete použít Azure Data Factory, [přejeďte data na adls Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)v tématu Azure Data Factory. Ujistěte se, že jako zdroj určíte **Azure Blob Storage** .

* Pokud chcete použít cluster Hadoop založený na Azure, spusťte tento příkaz DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Zástupné `<dest_account>` symboly anahraďtenázvyzdrojovéhoacílovéhoúčtuúložiště.`<source_account>`

    * Zástupné `<dest_container>` symboly anahraďtenázvyzdrojovéhoacílovéhokontejneru.`<source_container>`

    * Nahraďte zástupné symboly `<dest_path>` acestamikezdrojovémuadresářiacílovýmadresářům.`<source_path>`

    * `<source_account_key>` Zástupný symbol nahraďte přístupovým klíčem účtu úložiště, který obsahuje data.

    Tento příkaz zkopíruje data i metadata z vašeho účtu úložiště do svého účtu služby Data Lake Storage Gen2 Storage.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Vytvoření instančního objektu pro účet Azure Data Lake Storage Gen2

Chcete-li vytvořit instanční objekt, [Přečtěte si téma How to: Portál můžete použít k vytvoření aplikace a instančního objektu služby Azure AD, který má](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)přístup k prostředkům.

* Při provádění kroků v části [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) v článku se ujistěte, že k instančnímu objektu přiřadíte roli **Přispěvatel dat objektu BLOB služby Storage** .

* Při provádění kroků v části [získat hodnoty pro přihlášení v](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) článku, ID aplikace a hodnoty tajného klíče klienta do textového souboru. Budete je potřebovat brzy.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Vygeneruje seznam kopírovaných souborů s jejich oprávněními.

Z místního clusteru Hadoop spusťte tento příkaz:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Tento příkaz vygeneruje seznam kopírovaných souborů s jejich oprávněními.

> [!NOTE]
> V závislosti na počtu souborů v HDFS může spuštění tohoto příkazu trvat dlouhou dobu.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generování seznamu identit a jejich mapování na Azure Active Directory (přidání) identit

1. Stáhněte si `copy-acls.py` skript. Podívejte se na téma [stažení pomocníka a nastavte svůj hraniční uzel tak, aby se spouštěl](#download-helper-scripts) v tomto článku.

2. Spuštěním tohoto příkazu vygenerujte seznam jedinečných identit.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Tento skript vygeneruje soubor s `id_map.json` názvem, který obsahuje identity, které potřebujete mapovat na identity založené na doplňku.

3. `id_map.json` Otevřete soubor v textovém editoru.

4. Pro každý objekt JSON, který se zobrazí v souboru, aktualizujte `target` atribut buď na hlavní název uživatele (UPN) služby AAD, nebo na objectID (OID), s příslušnou namapovanou identitou. Až budete hotovi, soubor uložte. Tento soubor budete potřebovat v dalším kroku.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Použít oprávnění pro zkopírované soubory a použít mapování identit

Spuštěním tohoto příkazu můžete použít oprávnění pro data, která jste zkopírovali do účtu Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* `<storage-account-name>` Zástupný symbol nahraďte názvem vašeho účtu úložiště.

* `<container-name>` Zástupný symbol nahraďte názvem vašeho kontejneru.

* Nahraďte zástupné symboly `<client-secret>` apomocíIDaplikaceatajnéhoklíčeklienta,kterýjsteshromáždilipřivytvářeníinstančníhoobjektu.`<application-id>`

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Obsažen Rozdělení dat mezi několik Data Boxch zařízení

Před přesunutím dat do zařízení Data Box budete muset stáhnout některé pomocné skripty, zajistit, aby vaše data byla uspořádaná do Data Box zařízení a vyloučila všechny nepotřebné soubory.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Stáhněte si pomocné skripty a nastavte si hraniční uzel, ve kterém se budou spouštět.

1. Z hraničního nebo hlavního uzlu vašeho místního clusteru Hadoop spusťte tento příkaz:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Tento příkaz naklonuje úložiště GitHub, které obsahuje pomocné skripty.

2. Ujistěte se, že je na vašem místním počítači nainstalovaný balíček [JQ](https://stedolan.github.io/jq/) .

   ```bash
   
   sudo apt-get install jq
   ```

3. Nainstalujte balíček [žádosti](http://docs.python-requests.org/en/master/) Python.

   ```bash
   
   pip install requests
   ```

4. Nastavte oprávnění EXECUTE pro požadované skripty.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Ujistěte se, že jsou vaše data uspořádaná tak, aby se vešla na Data Box zařízení.

Pokud velikost dat překročí velikost jednoho Data Box zařízení, můžete soubory rozdělit do skupin, které můžete ukládat na více Data Boxch zařízení.

Pokud vaše data nepřekročí velikost jednotlivě Data Box zařízení, můžete přejít k další části.

1. Se zvýšenými oprávněními spusťte `generate-file-list` skript, který jste stáhli, a to podle pokynů v předchozí části.

   Tady je popis parametrů příkazu:

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

2. Zkopírujte seznam vygenerovaných souborů do HDFS, aby byly dostupné pro úlohu [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Vyloučit nepotřebné soubory

Musíte vyloučit některé adresáře z úlohy DisCp. Například vylučte adresáře, které obsahují informace o stavu, které udržují cluster spuštěný.

V rámci místního clusteru Hadoop, kde plánujete spustit úlohu DistCp, vytvořte soubor, který určuje seznam adresářů, které chcete vyloučit.

Tady je příklad:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Další postup

Přečtěte si, jak Data Lake Storage Gen2 pracuje s clustery HDInsight. Viz [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
