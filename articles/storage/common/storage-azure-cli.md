---
title: Použití Azure CLI s Azure Storage | Dokumentace Microsoftu
description: Zjistěte, jak používat rozhraní příkazového řádku Azure (Azure CLI) pomocí služby Azure Storage k vytváření a Správa účtů úložiště a pracovat s objekty BLOB Azure a soubory.
services: storage
author: roygara
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: d815bae2478d8853c63cd773cbfeaf5d3e90458c
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659759"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Použití Azure CLI s Azure Storage

Azure open source, multiplatformní rozhraní příkazového řádku poskytuje sadu příkazů pro práci s platformou Azure. Poskytuje většinu stejné funkce, které jsou součástí [webu Azure portal](https://portal.azure.com), včetně velké množství dat přístup.

V této příručce, ukážeme, jak používat [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) provedení několika úkolů práci s prostředky ve vašem účtu úložiště Azure. Doporučujeme stáhnout a nainstalovat nebo upgradovat na nejnovější verzi rozhraní příkazového řádku před použitím tohoto průvodce.

V příkladech v Průvodci se předpokládá použití prostředí Bash na Ubuntu, ale jiné platformy by měl provádět podobně. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Požadavky
Tento průvodce to předpokládá, že chápete základní koncepty služby Azure Storage. Dále předpokládá, že jste schopen dál uspokojit požadavky na vytvoření účtů, které jsou uvedeny níže pro Azure a služby úložiště.

### <a name="accounts"></a>Účty
* **Účet Azure**: Pokud ještě nemáte předplatné Azure, [vytvořit si bezplatný účet Azure](https://azure.microsoft.com/free/).
* **Účet úložiště**: Zobrazit [vytvořit účet úložiště](storage-quickstart-create-account.md) v [účty Azure storage](storage-create-storage-account.md).

### <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Stáhnout a nainstalovat rozhraní příkazového řádku Azure podle pokynů uvedených v [instalace rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2).

> [!TIP]
> Pokud máte potíže s instalací, podívejte se [řešení potíží s instalací](/cli/azure/install-az-cli2) části tohoto článku a [nainstalovat řešení potíží s](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) Průvodce na Githubu.
>

## <a name="working-with-the-cli"></a>Práce pomocí rozhraní příkazového řádku

Po instalaci rozhraní příkazového řádku, můžete použít `az` příkaz v rozhraní příkazového řádku (Bash, terminál, příkazový řádek) pro přístup k příkazům rozhraní příkazového řádku Azure. Typ `az` příkazu zobrazte úplný seznam základních příkazů (následující příklad výstupu byla zkrácena):

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

V rozhraní příkazového řádku spusťte příkaz `az storage --help` do seznamu `storage` příkaz podskupiny. Popisy podskupiny poskytovat přehled o funkce, které poskytuje rozhraní příkazového řádku Azure CLI pro práci s prostředky úložiště.

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

Pro práci s prostředky ve vašem předplatném Azure, musíte nejdřív přihlásit ke svému účtu Azure pomocí `az login`. Existuje několik způsobů, jimiž se můžete přihlásit:

* **Interaktivní přihlášení**: `az login`
* **Přihlaste se pomocí uživatelského jména a hesla**: `az login -u johndoe@contoso.com -p VerySecret`
  * To nebude fungovat s účty Microsoft a účty, které používají ověřování službou Multi-Factor Authentication.
* **Přihlaste se pomocí instančního objektu**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Ukázkový skript Azure CLI

V dalším kroku budete spolupracujeme se skript malé prostředí, která vydává několik základních příkazů rozhraní příkazového řádku Azure k interakci s prostředky služby Azure Storage. Skript nejprve vytvoří nový kontejner v účtu úložiště a pak odešle existujícího souboru (jako objekt blob) do tohoto kontejneru. Pak zobrazí seznam všech objektů BLOB v kontejneru a nakonec stáhne soubor do cílového umístění na místním počítači, který zadáte.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

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

1. Otevřete oblíbený textový editor, pak zkopírujte a vložte předchozí skript do editoru.

2. V dalším kroku aktualizujte tento skript proměnné tak, aby odrážely nastavení konfigurace. Nahraďte následující hodnoty, jak je uvedeno:

   * **\<název_účtu_úložiště\>**  název účtu úložiště.
   * **\<klíč_účtu_úložiště\>**  primární nebo sekundární přístupový klíč účtu úložiště.
   * **\<container_name\>**  A pojmenujte nový kontejner, pokud chcete vytvořit, jako je například "azure-cli ukázka container".
   * **\<blob_name\>**  název cílové objektů blob v kontejneru.
   * **\<file_to_upload\>**  cestu k malé souboru v místním počítači, jako například "~ / images/HelloWorld.png".
   * **\<destination_file\>**  cíl cesta k souboru, například "~ / downloadedImage.png".

3. Po aktualizaci nezbytné proměnné, uložte skript a ukončete editor. Další kroky předpokládají, že pojmenujete skriptu **my_storage_sample.sh**.

4. Označí skript jako spustitelný soubor, v případě potřeby: `chmod +x my_storage_sample.sh`

5. Spusťte skript. Například v prostředí Bash: `./my_storage_sample.sh`

Byste měli vidět výstup podobný následujícímu a **\<destination_file\>** jste zadali ve skriptu by se měla objevit v místním počítači.

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
> Tento výstup je v **tabulky** formátu. Můžete určit, které výstupní formát používat tak, že zadáte `--output` argument v příkazům rozhraní příkazového řádku, nebo nastavte globálně pomocí `az configure`.
>

## <a name="manage-storage-accounts"></a>Správa účtů úložiště

### <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště
Pokud chcete vyzkoušet službu Azure Storage, potřebujete účet úložiště. Můžete vytvořit nový účet Azure Storage po dokončení konfigurace počítače [připojení k vašemu předplatnému](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Povinné]: Umístění. Například "západní USA".
* `--name` [Povinné]: Název účtu úložiště. Název musí mít délku 3 až 24 znaků a používejte pouze malé alfanumerické znaky.
* `--resource-group` [Povinné]: Název skupiny prostředků.
* `--sku` [Povinné]: SKU účtu úložiště. Povolené hodnoty:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Nastavení proměnných prostředí výchozí účet úložiště Azure

Ve vašem předplatném Azure můžete mít více účtů úložiště. Pokud chcete vybrat jeden z nich se má použít pro všechny následné příkazy, můžete nastavit tyto proměnné prostředí:

Nejprve zobrazte klíče účtu úložiště pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Teď, když máte klíč, můžete definovat jako proměnné prostředí a název účtu:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Dalším způsobem, jak nastavit výchozí účet úložiště je pomocí připojovacího řetězce. Nejprve získejte připojovací řetězec s `show-connection-string` příkaz:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Potom zkopírujte připojovací řetězec výstup a nastavit `AZURE_STORAGE_CONNECTION_STRING` proměnné prostředí (může být nutné uvést připojovací řetězec v uvozovkách):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Všechny příklady v následujících částech tohoto článku se předpokládá, že jste nastavili `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_KEY` proměnné prostředí.

## <a name="create-and-manage-blobs"></a>Vytvoření a Správa objektů BLOB
Azure Blob storage je služba pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data, ke kterým můžete přistupovat odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS. V této části se předpokládá, že jste už obeznámení s koncepty úložiště objektů Blob v Azure. Podrobné informace najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../blobs/storage-dotnet-how-to-use-blobs.md) a [koncepty služby Blob Service](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Vytvoření kontejneru
Každý objekt blob ve službě Azure storage, musí být v kontejneru. Kontejner můžete vytvořit pomocí `az storage container create` příkaz:

```azurecli
az storage container create --name <container_name>
```

Můžete nastavit jednu ze tří úrovní oprávnění ke čtení pro nový kontejner zadáním nepovinného `--public-access` argument:

* `off` (výchozí): Kontejner data jsou privátní pro vlastníka účtu.
* `blob`: Veřejné oprávnění ke čtení pro objekty BLOB.
* `container`: Veřejné čtení a seznam přístupu k celému kontejneru.

Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Odeslání objektu blob do kontejneru
Azure Blob storage podporuje bloku, doplňovací a objekty BLOB stránky. Nahrání objektů BLOB do kontejneru s použitím `blob upload` příkaz:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Pokud chcete odeslat přímo do složky uvnitř kontejneru v účtu úložiště, nahraďte `--name <blob_name>` s `--name <folder/blob_name>`.

 Ve výchozím nastavení `blob upload` příkaz *.vhd soubory nahraje do objektů BLOB stránky, nebo objekty BLOB bloku, jinak. Chcete-li určit jiný typ při nahrání objektu blob, můžete použít `--type` argument--povolené hodnoty jsou `append`, `block`, a `page`.

 Další informace o typech jiný objektu blob, naleznete v tématu [vysvětlení objektů BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Stažení objektu blob z kontejneru
Tento příklad ukazuje, jak stáhnout objekt blob z kontejneru:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Výpis objektů BLOB v kontejneru s [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) příkazu.

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

V příkladu výše cílový kontejner už musí existovat v cílovém účtu úložiště pro operace kopírování neproběhne úspěšně. Zdrojový objekt blob zadaný argumentem `--source-uri` musí navíc buď zahrnovat token sdíleného přístupového podpisu (SAS), nebo musí být veřejně přístupný jako v tomto příkladu.

### <a name="delete-a-blob"></a>Odstranění objektu blob
Chcete-li odstranit objekt blob, použijte `blob delete` příkaz:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Vytvoření a správě sdílených složek
Služba soubory Azure nabízí sdílené úložiště pro aplikace používající protokol zprávy bloku SMB (Server). Virtuální počítače Microsoft Azure a cloudových služeb, jakož i místní aplikace můžou sdílet souborová data přes sdílené složky. Můžete spravovat sdílené složky a data souborů přes rozhraní příkazového řádku Azure. Další informace o službě soubory Azure najdete v tématu [Úvod do služby soubory Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Sdílené složky Azure je sdílené složky protokolu SMB v Azure. Všechny adresáře a soubory musí být vytvořeny ve sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílené složky můžete ukládat neomezený počet souborů až do omezení kapacity účtu úložiště. Následující příklad vytvoří sdílenou **myshare**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Vytvoření adresáře
Adresáře zajistí hierarchickou strukturu do sdílené složky Azure. Následující příklad vytvoří adresář s názvem **slozka** ve sdílené složce.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Cestu k adresáři může obsahovat několik úrovní, například **dir1/dir2**. Ale musíte zajistit, že před vytvořením podadresáře existovaly všechny nadřazené adresáře. Například pro cestu **dir1/dir2**, musíte nejprve vytvořit adresář **dir1**, vytvořte adresář **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Nahrání místního souboru do sdílené složky
Následující příklad nahraje soubor z **~/temp/samplefile.txt** kořenového adresáře **myshare** sdílenou složku. `--source` Argument určuje existující místní soubor k odeslání.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Jako s vytváření adresáře, můžete zadat cestu k adresáři ve sdílené složce pro nahrání souboru do stávajícího adresáře ve sdílené složce:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Soubor ve sdílené složce může být velikost až 1 TB.

### <a name="list-the-files-in-a-share"></a>Seznam souborů ve sdílené složce
Soubory a adresáře ve sdílené složce můžete vypsat pomocí `az storage file list` příkaz:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopírování souborů      
Soubor můžete zkopírovat do jiného souboru, soubor do objektu blob nebo objekt blob do souboru. Chcete-li například kopírování souboru do jiného adresáře v jiné sdílené složky:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Vytvořit snímek sdílené složky
Snímek sdílené složky můžete vytvořit pomocí `az storage share snapshot` příkaz:

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

### <a name="list-share-snapshots"></a>Výpis snímků sdílené složky

Lze zadat seznam snímků sdílené složky pomocí konkrétní sdílenou složku `az storage share list --include-snapshots`

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

### <a name="browse-share-snapshots"></a>Procházení snímků sdílené složky
Může také přejít do konkrétní sdílenou složku snímku k zobrazení obsahu pomocí `az storage file list`. Je nutné zadat název sdílené složky `--share-name <snare name>` a časové razítko `--snapshot '2017-10-04T19:45:18.0000000Z'`

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
### <a name="restore-from-share-snapshots"></a>Obnovit ze snímků sdílené složky

Kopírování nebo stažení souboru ze snímku pomocí sdílené složky můžete obnovit soubor `az storage file download` příkaz

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
Snímek sdílené složky můžete odstranit pomocí `az storage share delete` příkaz tím, že poskytuje `--snapshot` parametr s časovým razítkem snímku sdílené složky:

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
Tady jsou některé další prostředky pro dostávat další informace o práci s Azure CLI. 

* [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Referenční informace k příkazu Azure CLI](/cli/azure)
* [Azure CLI on GitHub](https://github.com/Azure/azure-cli)
