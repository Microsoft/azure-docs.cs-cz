---
title: Použití Azure CLI 2.0 s Azure Storage | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku Azure (Azure CLI) 2.0, s Azure Storage pro vytváření a správu účtů úložiště a pracovat s Azure BLOB a soubory. Azure CLI 2.0 je nástroj napříč platformami napsané v Pythonu.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: rogarana
ms.openlocfilehash: 99e67b9c8469f08f1cbfc980568eec35694d1ae2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Použití Azure CLI 2.0 s Azure Storage

Open source, a platformy Azure CLI 2.0 obsahuje sadu příkazů pro práci s platformou Azure. Poskytuje hodně podobné funkce v nalezen [portál Azure](https://portal.azure.com), včetně přístupu bohaté data.

V této příručce, jsme ukazují, jak používat [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) provést několik úkolů práci s prostředky v účtu úložiště Azure. Doporučujeme stáhnout a nainstalovat nebo upgradovat na nejnovější verzi 2.0 rozhraní příkazového řádku před použitím tohoto průvodce.

V příkladech v Průvodci se předpokládá použití prostředí Bash na Ubuntu, ale jiné platformy proveďte podobně. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Požadavky
Tato příručka předpokládá, že chápete základní koncepty Azure Storage. Předpokládá také, že jste vyhovět požadavkům vytvoření účtu, které jsou uvedeny níže pro Azure a služby úložiště.

### <a name="accounts"></a>Účty
* **Účet Azure**: Pokud ještě nemáte předplatné Azure, [vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/).
* **Účet Storage**: Viz část [Vytvoření účtu úložiště](storage-create-storage-account.md#create-a-storage-account) v článku [Informace o účtech Azure Storage](storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Instalace Azure CLI 2.0

Stáhněte a nainstalujte podle pokynů uvedených v Azure CLI 2.0 [nainstalovat Azure CLI 2.0](/cli/azure/install-az-cli2).

> [!TIP]
> Pokud máte potíže s instalací, podívejte se [řešení potíží s instalací](/cli/azure/install-az-cli2#installation-troubleshooting) v článku a [nainstalovat řešení potíží s](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) Průvodce na Githubu.
>

## <a name="working-with-the-cli"></a>Práce s rozhraní příkazového řádku

Po instalaci rozhraní příkazového řádku, můžete `az` příkazu ve svém rozhraní příkazového řádku (Bash, terminálu, příkazového řádku) pro přístup k příkazy rozhraní příkazového řádku Azure. Typ `az` příkaz chcete zobrazit úplný seznam základní příkazy (výstupu v následujícím příkladu byla zkrácena):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

Ve svém rozhraní příkazového řádku spusťte příkaz `az storage --help` do seznamu `storage` příkaz podskupiny. Popisy podskupiny poskytují přehled funkcí, které poskytuje Azure CLI pro práci s vaše prostředky úložiště.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Připojení rozhraní příkazového řádku k předplatnému Azure

Chcete-li pracovat s prostředky ve vašem předplatném Azure, musíte nejprve se přihlásit k účtu Azure s `az login`. Existuje několik způsobů, které se můžete přihlásit:

* **Interaktivní přihlášení**: `az login`
* **Přihlaste se pomocí uživatelského jména a hesla**: `az login -u johndoe@contoso.com -p VerySecret`
  * To nebude fungovat s účty Microsoft nebo účty, které používají službu Multi-Factor authentication.
* **Přihlaste se pomocí objektu služby**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Azure CLI 2.0 ukázkový skript

V dalším kroku jsme budete pracovat se skript malé prostředí, který vystavuje pár základních příkazů 2.0 rozhraní příkazového řádku Azure k interakci s prostředky Azure Storage. Skript nejprve vytvoří nový kontejner ve vašem účtu úložiště, a poté odešle existující soubor (jako objekt blob) do tohoto kontejneru. Potom zobrazí seznam všech objektů BLOB v kontejneru a nakonec stáhne soubor do cílového umístění v místním počítači, který určíte.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Nakonfigurujte a spusťte skript**

1. Otevřete svém oblíbeném textovém editoru, pak zkopírujte a vložte uvedený skript do editoru.

2. Potom aktualizujte proměnné ke skriptu, aby odrážela nastavení konfigurace. Nahraďte uvedeného následující hodnoty:

   * **\<storage_account_name\>**  název účtu úložiště.
   * **\<storage_account_key\>**  primární nebo sekundární přístupový klíč účtu úložiště.
   * **\<container_name\>**  A název je nový kontejner, pokud chcete vytvořit, jako je například "azure-rozhraní příkazového řádku – ukázka container".
   * **\<blob_name\>**  název pro cílový objekt blob v kontejneru.
   * **\<file_to_upload\>**  cestu k malým souboru v místním počítači, jako například "~ / images/HelloWorld.png".
   * **\<destination_file\>**  cíl cesta k souboru, jako například "~ / downloadedImage.png".

3. Po aktualizaci nezbytné proměnné, uložte skript a ukončete editor. Další kroky předpokládají, že jste s názvem vašeho skriptu **my_storage_sample.sh**.

4. Označte do skriptu jako spustitelný soubor, v případě potřeby: `chmod +x my_storage_sample.sh`

5. Spusťte skript. Například v Bash: `./my_storage_sample.sh`

Měli byste vidět výstup podobný následujícímu a **\<destination_file\>** kterou jste zadali v skriptu by se měla objevit v místním počítači.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> Tento výstup je v **tabulky** formátu. Můžete určit, který výstupní formát používat tak, že zadáte `--output` argument v příkazech rozhraní příkazového řádku, nebo nastavit globálně pomocí `az configure`.
>

## <a name="manage-storage-accounts"></a>Správa účtů úložiště

### <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště
Pokud chcete vyzkoušet službu Azure Storage, potřebujete účet úložiště. Můžete vytvořit nový účet úložiště Azure, po dokončení konfigurace počítače pro [připojení k vašemu předplatnému](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Vyžaduje]: umístění. Například "západní USA".
* `--name` [Vyžaduje]: název účtu úložiště. Název musí mít délku 3 až 24 znaků a používat jenom malé alfanumerické znaky.
* `--resource-group` [Vyžaduje]: název skupiny prostředků.
* `--sku` [Vyžaduje]: účet úložiště SKU. Povolené hodnoty:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`


### <a name="set-default-azure-storage-account-environment-variables"></a>Nastavení proměnných prostředí výchozí účet úložiště Azure
Můžete mít více účtů úložiště ve vašem předplatném Azure. Vyberte jednu z nich chcete použít pro žádné další příkazy, můžete nastavit tyto proměnné prostředí:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Jiný způsob, jak nastavit výchozí účet úložiště je pomocí připojovacího řetězce. Nejdřív získat připojovací řetězec s `show-connection-string` příkaz:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Pak zkopírujte připojovací řetězec výstup a nastavte `AZURE_STORAGE_CONNECTION_STRING` proměnnou prostředí (budete muset uzavřete připojovací řetězec v uvozovkách):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Všechny příklady v následujících částech v tomto článku předpokládá, že jste nastavili `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_ACCESS_KEY` proměnné prostředí.
>

## <a name="create-and-manage-blobs"></a>Vytvářet a spravovat objekty BLOB
Azure Blob storage je služba pro ukládání velkého objemu nestrukturovaných dat, jako je například textu nebo binárních dat, která jsou přístupná odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS. V této části se předpokládá, že jste již obeznámeni s koncepty úložiště objektů Blob v Azure. Podrobné informace najdete v tématu [Začínáme s Azure Blob storage pomocí rozhraní .NET](../blobs/storage-dotnet-how-to-use-blobs.md) a [koncepty služby objektů Blob](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Vytvoření kontejneru
Každý objekt blob v úložišti Azure musí být v kontejneru. Kontejner můžete vytvořit pomocí `az storage container create` příkaz:

```azurecli
az storage container create --name <container_name>
```

Můžete nastavit jednu ze tří úrovní přístupu pro čtení pro nový kontejner zadáním volitelného `--public-access` argument:

* `off` (výchozí): Kontejner dat je soukromé pro vlastníka účtu.
* `blob`: Pro objekty BLOB veřejný přístup pro čtení.
* `container`: Veřejné pro čtení a seznamu přístup k celé kontejneru.

Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Odeslání objektu blob do kontejneru
Podporuje Azure úložiště objektů Blob bloku, připojit a objekty BLOB stránky. Odešlete objektů BLOB do kontejneru `blob upload` příkaz:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 Ve výchozím nastavení `blob upload` příkaz nahrávání souborů *.vhd objekty BLOB stránky, nebo objekty BLOB bloku, jinak hodnota. Chcete-li zadat jiný typ, pokud nahrát objekt blob, můžete použít `--type` argument – povolené hodnoty jsou `append`, `block`, a `page`.

 Další informace o typech jiný objektu blob najdete v tématu [Principy objekty BLOB bloku a doplňovacích objektů BLOB, objekty BLOB stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Stažení objektu blob z kontejneru
Tento příklad ukazuje, jak stáhnout z kontejneru objektu blob:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru s [az úložiště objektů blob seznamu](/cli/azure/storage/blob#az_storage_blob_list) příkaz.

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Kopírování objektů BLOB
Můžete asynchronně kopírovat objekty blob v rámci účtů a oblastí nebo napříč nimi.

Následující příklad ukazuje, jak kopírovat objekt blob z jednoho účtu úložiště do druhého. Nejdřív vytvoříme kontejner ve zdrojovém účtu úložiště a pro jeho objekty blob zadáme veřejné oprávnění ke čtení. Potom do tohoto kontejneru odešleme soubor a nakonec objekt blob z tohoto kontejneru zkopírujeme do kontejneru v cílovém účtu úložiště.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

V tomto příkladu cílový kontejner již musí existovat v cílový účet úložiště pro kopírování proběhla úspěšně. Zdrojový objekt blob zadaný argumentem `--source-uri` musí navíc buď zahrnovat token sdíleného přístupového podpisu (SAS), nebo musí být veřejně přístupný jako v tomto příkladu.

### <a name="delete-a-blob"></a>Odstranění objektu blob
Pokud chcete odstranit objekt blob, použijte `blob delete` příkaz:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Vytvořit a spravovat sdílené složky
Soubory Azure nabízí sdílené úložiště pro aplikace, které používají protokol Server Message Block (SMB). Virtuální počítače Microsoft Azure a cloudové služby i místní aplikace můžou sdílet souborová data přes sdílené složky. Můžete spravovat sdílené složky a data souborů prostřednictvím rozhraní příkazového řádku Azure. Další informace o Azure souborů najdete v tématu [Úvod do Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Azure File sdílená složka je sdílená složka SMB v Azure. Všechny adresáře a soubory musí být vytvořeny ve sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílená složka můžete obsahovat neomezený počet souborů až limity kapacity účtu úložiště. Následující příklad vytvoří sdílenou složku s názvem **název_sdílené_položky**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Vytvoření adresáře
Adresář poskytuje hierarchické uspořádání sdílenou složku Azure. Následující příklad vytvoří adresář s názvem **adresář** do sdílené složky.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Cestu k adresáři může obsahovat více úrovních, například **dir1/dir2**. Nicméně je nutné zajistit, že před vytvořením podadresáři existují všechny nadřazeného adresáře. Například pro cestu **dir1/dir2**, je nutné nejprve vytvořit adresář **dir1**, pak vytvořit adresář **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Uložení místního souboru do sdílené složky
V následujícím příkladu se uloží soubor z **~/temp/samplefile.txt** kořenového adresáře **název_sdílené_položky** sdílené složky. `--source` Argument určuje existující místní soubor k odeslání.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Jako s vytváření adresáře, můžete určit cestu k adresáři ve sdílené složce nahrát soubor do existujícího adresáře ve sdílené složce:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Soubor ve sdílené složce může být velikost až 1 TB.

### <a name="list-the-files-in-a-share"></a>Seznam souborů ve sdílené složce
Můžete vytvořit seznam souborů a adresářů ve sdílené složce pomocí `az storage file list` příkaz:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopírování souborů      
Soubor můžete zkopírovat do jiného souboru, soubor do objektu blob, nebo objekt blob do souboru. Chcete-li například kopírování souboru do jiného adresáře v jinou sdílenou složku:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Vytvoření sdílené složky snímku
Můžete vytvořit sdílenou složku snímku pomocí `az storage share snapshot` příkaz:

```cli
az storage share snapshot -n <share name>
```

Ukázkový výstup
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Snímky sdílené položky seznamu

Lze zadat seznam konkrétní sdílenou složku pomocí snímků sdílené složky `az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Ukázkový výstup**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Procházet snímky sdílené složky
Může také procházet do konkrétní sdílenou složku snímku na serveru obsahu pomocí `az storage file list`. Je nutné zadat název sdílené složky `--share-name <snare name>` a časové razítko `--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Ukázkový výstup**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Obnovení ze sdílené složky snímků

Soubor můžete obnovit pomocí kopírování nebo stažení souboru ze sdílené složky snímek pomocí `az storage file download` příkaz

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Ukázkový výstup**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Odstranit snímek sdílené složky
Sdílenou složku snímku lze odstranit pomocí `az storage share delete` příkaz tím, že poskytuje `--snapshot` parametr s časovým razítkem snímku sdílené složky:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Ukázkový výstup
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Další postup
Tady jsou některé další zdroje informací o další informace o práci s Azure CLI 2.0.

* [Začínáme s Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Přehled příkazů Azure CLI 2.0](/cli/azure)
* [Azure CLI 2.0 na Githubu](https://github.com/Azure/azure-cli)
