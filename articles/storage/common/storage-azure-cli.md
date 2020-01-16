---
title: Použití Azure CLI s Azure Storage
description: Naučte se používat rozhraní příkazového řádku Azure (Azure CLI) s Azure Storage k vytváření a správě účtů úložiště a práci s objekty BLOB a soubory Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: b80c29788bd2f8d5172795aa2c92a80e460ff81f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978531"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Použití Azure CLI s Azure Storage

Rozhraní Azure CLI Open Source nabízí sadu příkazů pro práci s platformou Azure. Poskytuje mnoho stejných funkcí, které se nacházejí v [Azure Portal](https://portal.azure.com), včetně přístupu k datům.

V této příručce se dozvíte, jak pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) spustit několik úloh pracujících s prostředky ve vašem účtu Azure Storage. Před použitím této příručky doporučujeme stáhnout a nainstalovat nebo upgradovat na nejnovější verzi rozhraní příkazového řádku.

V příkladech v průvodci se předpokládá použití prostředí bash v Ubuntu, ale další platformy by se měly provádět podobně.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Požadavky
V tomto průvodci se předpokládá, že rozumíte základním konceptům Azure Storage. Také předpokládá, že budete schopní splnit požadavky na vytvoření účtu, které jsou uvedené níže pro Azure a službu úložiště.

### <a name="accounts"></a>Účty
* **Účet Azure**: Pokud ještě nemáte předplatné Azure, [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free/).
* **Účet Storage**: Viz část [Vytvoření účtu úložiště](storage-account-create.md) v článku [Informace o účtech Azure Storage](storage-account-overview.md).

### <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Stáhněte a nainstalujte rozhraní příkazového řádku Azure CLI podle pokynů uvedených v tématu [instalace rozhraní](/cli/azure/install-az-cli2)příkazového řádku Azure CLI.

> [!TIP]
> Pokud máte potíže s instalací, podívejte se do části [řešení potíží s instalací](/cli/azure/install-az-cli2) v článku a průvodce [řešením potíží](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) s instalací na GitHubu.
>

## <a name="working-with-the-cli"></a>Práce s rozhraním příkazového řádku

Po instalaci rozhraní příkazového řádku můžete pomocí příkazu `az` v rozhraní příkazového řádku (bash, terminál, příkazový řádek) získat přístup k příkazům Azure CLI. Zadáním příkazu `az` zobrazíte úplný seznam základních příkazů (Následující příklad výstupu byl zkrácen):

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

V rozhraní příkazového řádku spusťte příkaz `az storage --help` pro výpis podskupin příkazů `storage`. Popisy podskupin poskytují přehled o funkcích, které Azure CLI poskytuje pro práci s prostředky úložiště.

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

Pokud chcete pracovat s prostředky ve vašem předplatném Azure, musíte se nejdřív přihlásit k účtu Azure pomocí `az login`. K dispozici je několik způsobů, jak se můžete přihlásit:

* **Interaktivní přihlášení**: `az login`
* **Přihlaste se pomocí uživatelského jména a hesla**: `az login -u johndoe@contoso.com -p VerySecret`
  * Nefunguje s účty Microsoft nebo účty, které používají službu Multi-Factor Authentication.
* **Přihlaste se pomocí instančního objektu**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Ukázkový skript Azure CLI

Dále budeme pracovat se skriptem malého prostředí, který vydává několik základních příkazů rozhraní příkazového řádku Azure pro interakci s Azure Storagemi prostředky. Skript nejprve vytvoří nový kontejner v účtu úložiště a pak nahraje existující soubor (jako objekt BLOB) do tohoto kontejneru. Pak zobrazí seznam všech objektů BLOB v kontejneru a nakonec soubor stáhne do cílového umístění v místním počítači, který zadáte.

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

**Konfigurace a spuštění skriptu**

1. Otevřete oblíbený textový editor a potom zkopírujte a vložte předchozí skript do editoru.

2. Dále aktualizujte proměnné skriptu tak, aby odrážely nastavení konfigurace. Nahraďte následující hodnoty uvedeným způsobem:

   * **\<storage_account_name\>** Název vašeho účtu úložiště.
   * **\<storage_account_key\>** Primární nebo sekundární přístupový klíč pro váš účet úložiště.
   * **\<container_name\>** Název nového kontejneru, který se má vytvořit, například "Azure-CLI-Sample-Container".
   * **\<blob_name\>** Název cílového objektu BLOB v kontejneru.
   * **\<file_to_upload\>** Cesta k malému souboru v místním počítači, například ~/images/HelloWorld.png.
   * **\<destination_file\>** Cesta k cílovému souboru, například "~/downloadedImage.png".

3. Po aktualizaci potřebných proměnných uložte skript a ukončete Editor. V dalších krocích se předpokládá, že máte název skriptu **my_storage_sample. sh**.

4. Označte skript jako spustitelný soubor, pokud je to nutné: `chmod +x my_storage_sample.sh`

5. Spusťte skript. Například v bash: `./my_storage_sample.sh`

Měl by se zobrazit výstup podobný následujícímu a **\<destination_file\>** , které jste zadali ve skriptu, by se měly zobrazit na místním počítači.

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
> Předchozí výstup je ve formátu **tabulky** . Můžete určit, který výstupní formát se má použít, zadáním argumentu `--output` v příkazech rozhraní příkazového řádku nebo ho globálně nastavit pomocí `az configure`.
>

## <a name="manage-storage-accounts"></a>Správa účtů úložiště

### <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště
Pokud chcete vyzkoušet službu Azure Storage, potřebujete účet úložiště. Po nakonfigurování počítače pro připojení k vašemu předplatnému můžete vytvořit nový účet Azure Storage.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [povinné]: umístění. Například "Západní USA".
* `--name` [povinné]: název účtu úložiště. Název musí mít délku 3 až 24 znaků a musí obsahovat jenom malé alfanumerické znaky.
* `--resource-group` [povinné]: název skupiny prostředků.
* `--sku` [povinné]: SKU účtu úložiště. Povolené hodnoty:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`
  * `Standard_GZRS` (Preview)
  * `Standard_RAGZRS` (Preview)

### <a name="set-default-azure-storage-account-environment-variables"></a>Nastavení výchozích proměnných prostředí účtu úložiště Azure

Ve vašem předplatném Azure můžete mít víc účtů úložiště. Pokud chcete vybrat jednu z nich, která se má použít pro všechny následné příkazy úložiště, můžete nastavit tyto proměnné prostředí:

Nejprve zobrazte klíče účtu úložiště pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Teď, když máte klíč, můžete ho definovat a název účtu jako proměnné prostředí:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Dalším způsobem, jak nastavit výchozí účet úložiště, je použití připojovacího řetězce. Nejprve získejte připojovací řetězec pomocí příkazu `show-connection-string`:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Pak zkopírujte výstupní připojovací řetězec a nastavte proměnnou prostředí `AZURE_STORAGE_CONNECTION_STRING` (možná budete muset připojovací řetězec uzavřít do uvozovek):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Všechny příklady v následujících částech tohoto článku předpokládají, že jste nastavili proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_KEY`.

## <a name="create-and-manage-blobs"></a>Vytváření a správa objektů blob
Azure Blob Storage je služba pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data, ke kterým se dá dostat odkudkoli na světě přes HTTP nebo HTTPS. V této části se předpokládá, že už jste obeznámeni s koncepty služby Azure Blob Storage. Podrobné informace najdete v tématu Začínáme [s úložištěm objektů BLOB v Azure pomocí](../blobs/storage-dotnet-how-to-use-blobs.md) [konceptů .NET a služby BLOB Service](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Vytvoření kontejneru
Každý objekt BLOB ve službě Azure Storage musí být v kontejneru. Kontejner můžete vytvořit pomocí příkazu `az storage container create`:

```azurecli
az storage container create --name <container_name>
```

Můžete nastavit jednu ze tří úrovní přístupu pro čtení pro nový kontejner zadáním volitelného argumentu `--public-access`:

* `off` (výchozí): data kontejneru jsou soukromá pro vlastníka účtu.
* `blob`: veřejný přístup pro čtení objektů BLOB.
* `container`: veřejný přístup pro čtení a seznam k celému kontejneru.

Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Odeslání objektu blob do kontejneru
Azure Blob Storage podporuje objekty blob bloku, Append a Page. Nahrajte objekty blob do kontejneru pomocí příkazu `blob upload`:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Pokud chcete nahrávat přímo do složky uvnitř kontejneru v účtu úložiště, nahraďte `--name <blob_name>` `--name <folder/blob_name>`.

 Ve výchozím nastavení příkaz `blob upload` nahrává soubory *. VHD do objektů blob stránky nebo objekty blob bloku v opačném případě. Pokud chcete při nahrávání objektu BLOB zadat jiný typ, můžete použít `--type` argument--povolené hodnoty jsou `append`, `block`a `page`.

 Další informace o různých typech objektů BLOB najdete v tématu [Principy objektů blob bloku, doplňovacích objektů BLOB a objektů blob stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Stažení objektu blob z kontejneru
Tento příklad ukazuje, jak stáhnout objekt BLOB z kontejneru:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Vytvořte seznam objektů BLOB v kontejneru pomocí příkazu [AZ Storage BLOB list](/cli/azure/storage/blob) .

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

V předchozím příkladu musí cílový kontejner již existovat v cílovém účtu úložiště, aby operace kopírování proběhla úspěšně. Zdrojový objekt blob zadaný argumentem `--source-uri` musí navíc buď zahrnovat token sdíleného přístupového podpisu (SAS), nebo musí být veřejně přístupný jako v tomto příkladu.

### <a name="delete-a-blob"></a>Odstranění objektu blob
Pokud chcete odstranit objekt blob, použijte příkaz `blob delete`:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

### <a name="set-the-content-type"></a>Nastavení typu obsahu

Typ obsahu, který se označuje také jako typ MIME, identifikuje formát dat v objektu blob. Prohlížeče a další software používají typ obsahu k určení způsobu zpracování dat. Například typ obsahu pro obrázky PNG je `image/png`. Chcete-li nastavit typ obsahu, použijte příkaz `blob update`:

```azurecli
az storage blob update
    --container-name <container_name>
    --name <blob_name>
    --content-type <content_type>
```

## <a name="create-and-manage-file-shares"></a>Vytváření a Správa sdílených složek
Služba soubory Azure nabízí sdílené úložiště pro aplikace, které používají protokol SMB (Server Message Block). Microsoft Azure virtuálních počítačů a cloudových služeb i v místních aplikacích může sdílet souborová data prostřednictvím připojených sdílených složek. Pomocí Azure CLI můžete spravovat sdílené složky a data souborů. Další informace o službě soubory Azure najdete v tématu [Seznámení se soubory Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Sdílená složka Azure je sdílená složka SMB v Azure. Všechny adresáře a soubory musí být vytvořeny ve sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílená složka může ukládat neomezený počet souborů až do omezení kapacity účtu úložiště. Následující příklad vytvoří sdílenou složku s názvem **myshare**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Vytvoření adresáře
Adresář poskytuje hierarchickou strukturu sdílené složky Azure. Následující příklad vytvoří adresář s názvem **myDir** ve sdílené složce.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Cesta k adresáři může zahrnovat několik úrovní, například **Dir1/dir2**. Před vytvořením podadresáře je však nutné zajistit, aby existovaly všechny nadřazené adresáře. Například pro cestu **Dir1/dir2**je nutné nejprve vytvořit adresář **Dir1**a pak vytvořit adresář **Dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Nahrání místního souboru do sdílené složky
Následující příklad nahraje soubor z **~/TEMP/samplefile.txt** do kořenové složky sdílené složky **myshare** . Argument `--source` určuje existující místní soubor, který se má nahrát.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Stejně jako u vytvoření adresáře můžete zadat cestu k adresáři v rámci sdílené složky a nahrát ho do existujícího adresáře v rámci sdílené složky:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Velikost souboru ve sdílené složce může být až 1 TB.

### <a name="list-the-files-in-a-share"></a>Výpis souborů ve sdílené složce
Soubory a adresáře ve sdílené složce můžete zobrazit pomocí příkazu `az storage file list`:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopírování souborů      
Můžete zkopírovat soubor do jiného souboru, soubor do objektu BLOB nebo objekt blob do souboru. Například pro zkopírování souboru do adresáře v jiné sdílené složce:        

```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Vytvořit snímek sdílené složky
Snímek sdílené složky můžete vytvořit pomocí příkazu `az storage share snapshot`:

```cli
az storage share snapshot -n <share name>
```

Vzorový výstup
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

Můžete vypsat snímky sdílené složky určité sdílené složky pomocí `az storage share list --include-snapshots`

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
Můžete také přejít do konkrétního snímku sdílené složky a zobrazit jeho obsah pomocí `az storage file list`. Jedna má zadat název sdílené složky `--share-name <snare name>` a časové razítko `--snapshot '2017-10-04T19:45:18.0000000Z'`

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
### <a name="restore-from-share-snapshots"></a>Obnovení ze snímků sdílené složky

Soubor můžete obnovit zkopírováním nebo stažením souboru ze snímku sdílené složky pomocí příkazu `az storage file download`

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
Snímek sdílené složky můžete odstranit pomocí příkazu `az storage share delete` zadáním `--snapshot` parametru s časovým razítkem snímku sdílené složky:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z'
```

Vzorový výstup
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Další kroky
Tady jsou některé další zdroje informací, které vám pomohou se dozvědět víc o práci s Azure CLI.

* [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Přehled příkazů Azure CLI](/cli/azure)
* [Azure CLI na GitHubu](https://github.com/Azure/azure-cli)
