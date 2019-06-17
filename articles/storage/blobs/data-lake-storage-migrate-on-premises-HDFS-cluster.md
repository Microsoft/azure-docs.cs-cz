---
title: Použití Azure Data Box pro migraci dat z místní HDFS ukládat do služby Azure Storage
description: Migrovat data z úložiště místní HDFS do služby Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 65ef586d74297e692160dc075dead2f0b1d973b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116974"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Použití Azure Data Box pro migraci dat z úložiště místní HDFS do služby Azure Storage

Můžete migrovat data z HDFS úložiště v místním clusteru Hadoop do služby Azure Storage (úložiště objektů blob nebo Data Lake Storage Gen2) s použitím zařízení Data Box. Můžete vybrat z pole 80 TB dat nebo velkého pole Data 770 TB.

Tento článek vám pomůže dokončit tyto úlohy:

> [!div class="checklist"]
> * Příprava na migraci vaše data.
> * Kopírování dat do zařízení Data Box nebo zařízení Data Box náročné.
> * Odešlete zařízení zpět do Microsoftu.
> * Přesun dat do Data Lake Storage Gen2.

## <a name="prerequisites"></a>Požadavky

Potřebujete tyto věci k dokončení migrace.

* Dva účty úložiště; ten, který má hierarchického oboru názvů na něm povolený a ten, který se nepodporuje.

* Místní cluster Hadoop, která obsahuje zdrojová data.

* [Zařízení Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Pořadí Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) nebo [Data Box náročná na výkon](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Při řazení zařízení, nezapomeňte si zvolte účet úložiště, který **nebude** mají hierarchické obory názvů na něm povolený. Je to proto, že zařízení Data Box ještě nepodporují přímý ingestování do Azure Data Lake Storage Gen2. Je potřeba zkopírovat do účtu úložiště a pak proveďte druhá kopie do účtu ADLS Gen2. Pokyny k tomu jsou uvedeny v následujících krocích.

  * Zapojení a připojte vaše [zařízení Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) nebo [Data Box náročné](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) k místní síti.

Pokud je všechno připravené, Pojďme začít.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopírování dat do zařízení Data Box

Pokud se vaše data se vejde do jednoho zařízení Data Box, budete zkopírovat data do zařízení Data Box. 

Pokud velikost vašich dat překračuje kapacitu zařízení Data Box, použijte [volitelný postup rozdělit data mezi více zařízení Data Box](#appendix-split-data-across-multiple-data-box-devices) a pak proveďte tento krok. 

Pokud chcete zkopírovat data z vašeho místního úložiště HDFS do zařízení Data Box, budete nastavit pár věcí a pak použít [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) nástroj.

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

4. Nastavte proměnnou prostředí `azjars` umístění `hadoop-azure` a `azure-storage` jar soubory. Tyto soubory v adresáři instalace Hadoop můžete najít.

    Pokud chcete zjistit, zda tyto soubory existují, použijte následující příkaz: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Nahradit `<hadoop_install_dir>` zástupný symbol cestu k adresáři, kam jste nainstalovali Hadoop. Nezapomeňte použít plně kvalifikovanou cestou.

    Příklady:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Vytvoření kontejneru úložiště, který chcete použít pro kopírování dat. Jako součást tohoto příkazu byste zadat také cílový adresář. To může být v tuto chvíli fiktivní cílový adresář.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Nahradit `<blob_service_endpoint>` zástupný symbol s názvem vašeho koncového bodu služby objektů blob.

    * Nahradit `<account_key>` zástupný symbol přístupovým klíčem vašeho účtu.

    * Nahradit `<container-name>` zástupný symbol s názvem vašeho kontejneru.

    * Nahradit `<destination_directory>` zástupný symbol s názvem adresáře, který chcete zkopírovat data.

6. Spusťte příkaz seznamu k zajištění, že byly vytvořeny kontejneru a adresář.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Nahradit `<blob_service_endpoint>` zástupný symbol s názvem vašeho koncového bodu služby objektů blob.

   * Nahradit `<account_key>` zástupný symbol přístupovým klíčem vašeho účtu.

   * Nahradit `<container-name>` zástupný symbol s názvem vašeho kontejneru.

7. Zkopírujte data z Hadoop HDFS do úložiště objektů Blob Data pole, do kontejneru, který jste vytvořili dříve. Pokud není nalezen, která kopírujete do adresáře, příkaz ho automaticky vytvoří.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Nahradit `<blob_service_endpoint>` zástupný symbol s názvem vašeho koncového bodu služby objektů blob.

    * Nahradit `<account_key>` zástupný symbol přístupovým klíčem vašeho účtu.

    * Nahradit `<container-name>` zástupný symbol s názvem vašeho kontejneru.

    * Nahradit `<exlusion_filelist_file>` zástupný prvek s názvem souboru, který obsahuje seznam vyloučení souborů.

    * Nahradit `<source_directory>` zástupný symbol s názvem adresáře, který obsahuje data, která chcete kopírovat.

    * Nahradit `<destination_directory>` zástupný symbol s názvem adresáře, který chcete zkopírovat data.

    `-libjars` Možnost se používá, aby `hadoop-azure*.jar` a rolích dependent `azure-storage*.jar` soubory k dispozici `distcp`. Už to může nastat u některých clusterů.

    Následující příklad ukazuje způsob, jakým `distcp` příkazu se použije ke zkopírování dat.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Aby se zvýšila rychlost kopírování:

    * Zkuste změnit počet mapovačů. (Výše uvedený příklad používá `m` = 4 mapovačů.)

    * Zkuste spustit více `distcp` paralelně.

    * Mějte na paměti, že velké soubory poskytují vyšší výkon než malých souborů.

## <a name="ship-the-data-box-to-microsoft"></a>Příjemce zařízení Data Box společnosti Microsoft

Postupujte podle těchto kroků Připravíme a odešleme zařízení Data Box společnosti Microsoft.

1. Nejprve je potřeba [přípravu k odeslání na zařízení Data Box nebo Data Box náročné](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Po dokončení přípravy zařízení stahujte soubory BOM. Budete používat tyto BOM nebo manifest soubory později chcete ověřit nahrání dat do Azure.

3. Vypněte zařízení a odeberte kabely.

4. Vyzvednutí s UPS naplánujte.

    * Zařízení Data Box, naleznete v tématu [odeslání vašeho zařízení Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Data Box náročné zařízení, najdete v části [dodávat vaše Data Box náročné](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Poté, co Microsoft obdrží zařízení, je připojen do sítě datového centra a nahrání dat do účtu úložiště, který jste zadali (s hierarchické obory názvů, zakázáno) Pokud je uskutečněn objednávky zařízení. Proti BOM soubory ověřte, že všechna vaše data se nahraje do Azure. Tato data teď můžete přesunout do účtu úložiště Data Lake Storage Gen2.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Přesun dat do Azure Data Lake Storage Gen2

Již máte data do svého účtu Azure Storage. Nyní se zkopírovat data do účtu úložiště Azure Data Lake a použít oprávnění k přístupu k souborům a adresářům.

> [!NOTE]
> Tento krok je nutný, pokud použijete jako úložiště dat Azure Data Lake Storage Gen2. Pokud používáte pouze účet blob storage bez hierarchického oboru názvů jako úložiště dat, můžete tuto část přeskočit.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Kopírování dat do účtu Azure Data Lake Storage Gen 2

Pomocí služby Azure Data Factory, nebo pomocí clusteru Hadoop založené na Azure, může kopírovat data.

* Použití Azure Data Factory najdete v článku [Azure Data Factory k přesunu dat do ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Ujistěte se, že chcete zadat **Azure Blob Storage** jako zdroj.

* Chcete-li použít cluster Hadoop založené na Azure, spusťte tento příkaz DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Nahradit `<source_account>` a `<dest_account>` zástupné symboly názvy zdrojových a cílových účtů úložiště.

    * Nahradit `<source_container>` a `<dest_container>` zástupné symboly názvy zdrojových a cílových kontejnerů.

    * Nahradit `<source_path>` a `<dest_path>` zástupné symboly cesty zdrojového a cílového adresáře.

    * Nahradit `<source_account_key>` zástupný symbol přístupový klíč účtu úložiště, který obsahuje data.

    Tento příkaz zkopíruje data i metadata z vašeho účtu úložiště do účtu úložiště Data Lake Storage Gen2.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Vytvoření instančního objektu pro váš účet Azure Data Lake Storage Gen2

Vytvoření instančního objektu najdete v tématu [jak: Použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Při provádění kroků v [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) části tohoto článku, ujistěte se, že k přiřazení **Přispěvatel dat objektu Blob úložiště** roli instančnímu objektu služby.

* Při provádění kroků v [získání hodnot pro přihlášení](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) část článku, ID aplikace a hodnoty tajných kódů klienta do textového souboru. Brzy ty budete potřebovat.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Vygeneruje seznam zkopírované soubory s jejich oprávněními

Z místní cluster Hadoop spusťte tento příkaz:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Tento příkaz vytvoří seznam zkopírované soubory s jejich oprávněními.

> [!NOTE]
> V závislosti na počtu souborů v HDFS tento příkaz může trvat dlouhou dobu spuštění.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Vygenerovat seznam identit a jejich namapování na identity Azure Active Directory (Přidat)

1. Stáhněte si `copy-acls.py` skriptu. Zobrazit [stáhnout pomocné skripty a nastavit hraniční uzel k jejich spuštění](#download-helper-scripts) části tohoto článku.

2. Spuštěním tohoto příkazu vygenerujte seznam jedinečných identit.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Tento skript generuje soubor s názvem `id_map.json` obsahující identity, které je potřeba namapovat na identity založené na Přidat.

3. Otevřít `id_map.json` souboru v textovém editoru.

4. Pro každý objekt JSON, který se zobrazí v souboru, aktualizujte `target` atribut AAD hlavní název uživatele (UPN) nebo ID objektu (OID) s příslušnou mapován identity. Až budete hotovi, uložte soubor. Bude nutné tento soubor v dalším kroku.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Zavedení oprávnění vůči zkopírované soubory a použít mapování identit

Spusťte tento příkaz použít oprávnění k datům, který jste zkopírovali do účtu Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Nahradit `<storage-account-name>` zástupný symbol s názvem účtu úložiště.

* Nahradit `<container-name>` zástupný symbol s názvem vašeho kontejneru.

* Nahradit `<application-id>` a `<client-secret>` zástupné symboly aplikaci ID a tajný kód klienta, které jste shromáždili při vytváření instančního objektu.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Dodatek: Rozdělení dat napříč více zařízení Data Box

Před přesunutím dat do zařízení Data Box, budete muset stáhnout některé pomocné skripty, ujistěte se, že vaše data jsou uspořádaná rozměrů na zařízení Data Box a vyloučit nepotřebné soubory.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Stáhněte si pomocné skripty a nastavte hraničního uzlu pro jejich spuštění

1. Z edge nebo hlavního uzlu clusteru v místním systému Hadoop spusťte tento příkaz:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Tento příkaz naklonuje úložiště GitHub, který obsahuje pomocné skripty.

2. Ujistěte se, které mají [jq](https://stedolan.github.io/jq/) balíček nainstalován v místním počítači.

   ```bash
   
   sudo apt-get install jq
   ```

3. Nainstalujte [požadavky](http://docs.python-requests.org/en/master/) balíček pythonu.

   ```bash
   
   pip install requests
   ```

4. Sada oprávnění ke spouštění na požadované skripty.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Ujistěte se, že vaše data jsou uspořádaná podle do zařízení Data Box

Pokud velikost vašich dat překračuje velikost jedno zařízení Data Box, můžete rozdělit souborů do skupin, které můžete ukládat do více zařízení Data Box.

Pokud vaše data nepřekračuje velikost jednom zařízení Data Box, můžete přejít k další části.

1. Se zvýšenými oprávněními, spusťte `generate-file-list` skript, který jste si stáhli podle pokynů v předchozí části.

   Tady je popis parametry příkazu:

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

2. Zkopírujte vygenerovaný soubor obsahuje seznam do rozhraní HDFS, tak, aby byly přístupné [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) úlohy.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Vyloučení nepotřebných souborů

Budete muset z úlohy DisCp vyloučit některé adresáře. Například vylučte adresáře, které obsahují informace o stavu, které udržují clusteru se systémem.

Na místní cluster Hadoop, jež spusťte úlohu DistCp vytvořte soubor, který určuje seznam adresářů, které chcete vyloučit.

Tady je příklad:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak služba Data Lake Storage Gen2 pracuje s clustery HDInsight. Zobrazit [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
