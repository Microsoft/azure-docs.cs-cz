---
title: "Provádění operací v Azure Blob Storage (úložiště objektů) pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu"
description: "Naučíte se, jak odesílat a stahovat objekty blob ve službě Azure Blob a také konstruovat sdílený přístupový podpis (SAS) pro správu přístupu k objektům blob ve vašem účtu úložiště."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: tamram
ms.openlocfilehash: 4f781508652ee784fff61c8ecc76cb87ea47f0c2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Provádění operací s úložištěm objektů blob pomocí rozhraní příkazového řádku Azure

Azure Blob Storage je služba, která slouží k ukládání velkých objemů nestrukturovaných dat objektů, například textu nebo binárních dat, která jsou přístupná odkudkoli na světě prostřednictvím protokolů HTTP nebo HTTPS. Tento kurz se zaměřuje na základní operace ve službě Azure Blob Storage, jako je odesílání, stahování a odstraňování objektů blob. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření kontejneru
> * Odeslání souboru (objektu blob) do kontejneru
> * Zobrazí seznam objektů blob v kontejneru
> * Stažení objektu blob z kontejneru
> * Kopírování objektu blob mezi účty úložiště
> * Odstranění objektu blob
> * Zobrazení a úprava metadat a vlastností objektu blob
> * Správa zabezpečení se sdíleným přístupovým podpisem (SAS)

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejnery jsou podobná adresářům ve vašem počítači. Skupiny objektů blob je v kontejneru možné uspořádat stejným způsobem jako soubory v adresáři. Účet úložiště může mít libovolný počet kontejnerů. V kontejneru můžete uložit až 500 TB dat objektů blob. To je maximální objem dat v účtu úložiště.

K vytvoření kontejneru pro ukládání objektů blob použijte příkaz [az storage container create](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

Názvy kontejnerů musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a pomlčky (-). Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="enable-public-read-access-for-a-container"></a>Povolení veřejného oprávnění ke čtení pro kontejner

Nově vytvořený kontejner je ve výchozím nastavení privátní. To znamená, že k němu nemá nikdo přístup bez [sdíleného přístupového podpisu](#create-a-shared-access-signature-sas) nebo přístupových klíčů pro účet úložiště. Pomocí Azure CLI můžete toto chování upravit nastavením oprávnění kontejneru na jednu ze tří úrovní:

| | |
|---|---|
| `--public-access off` | (Výchozí) Bez veřejného oprávnění ke čtení |
| `--public-access blob` | Veřejné oprávnění ke čtení jenom pro objekty blob |
| `--public-access container` | Veřejné oprávnění ke čtení pro objekty blob, s možností zobrazení seznamu objektů blob v kontejneru |

Pokud pro `blob` nebo `container` nastavíte veřejný přístup, povolíte tak komukoli v internetu přístup jen pro čtení. Pokud například chcete zobrazit obrázky uložené jako objekty blob na vašem webu, musíte povolit veřejné oprávnění ke čtení. Pokud chcete povolit přístup pro čtení a zápis, musíte místo toho použít [sdílený přístupový podpis (SAS)](#create-a-shared-access-signature-sas).

K povolení veřejného oprávnění ke čtení pro váš kontejner použijte příkaz [az storage container set-permission](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Odeslání objektu blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Objekty blob bloku jsou nejběžnějším typem objektů blob uložených ve službě Azure Storage. Doplňovací objekty blob se používají, když je k existujícímu objektu blob potřeba přidat data a neměnit přitom jeho stávající obsah, například pro účely protokolování. Objekty blob stránky zálohují soubory virtuálního pevného disku virtuálních počítačů IaaS.

V tomto příkladu jsme odeslali objekt blob do kontejneru, který jsme vytvořili v předchozím kroku, a to pomocí příkazu [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Tato operace vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval. Odešlete několik souborů, abyste se vám v příštím kroku, kdy zobrazíte seznam objektů blob, zobrazilo několik položek.

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Pomocí příkazu [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) zobrazte seznam objektů blob v kontejneru.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Ukázkový výstup:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Stažení objektu blob

Ke stažení objektu blob, který jste odeslali v předchozím kroku, použijte příkaz [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download).

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>Kopírování objektu blob mezi účty úložiště

Můžete asynchronně kopírovat objekty blob v rámci účtů a oblastí nebo napříč nimi.

Následující příklad ukazuje, jak kopírovat objekt blob z jednoho účtu úložiště do druhého. Nejdřív vytvoříme kontejner ve zdrojovém účtu úložiště a pro jeho objekty blob zadáme veřejné oprávnění ke čtení. Potom do tohoto kontejneru odešleme soubor a nakonec objekt blob z tohoto kontejneru zkopírujeme do kontejneru v cílovém účtu úložiště.

V tomto příkladu v cílovém účtu úložiště už musí existovat cílový kontejner, jinak operace kopírování neproběhne úspěšně. Zdrojový objekt blob zadaný argumentem `--source-uri` musí navíc buď zahrnovat token sdíleného přístupového podpisu (SAS), nebo musí být veřejně přístupný jako v tomto příkladu.

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
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Odstranění objektu blob

K odstranění objektu blob z kontejneru použijte příkaz [az storage blob delete](/cli/azure/storage/blob#az_storage_blob_delete).

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="set-the-content-type"></a>Nastavení typu obsahu

Typ obsahu, který se označuje také jako typ MIME, identifikuje formát dat v objektu blob. Prohlížeče a další software používají typ obsahu k určení způsobu zpracování dat. Následující příklad nastaví typ obsahu na `image/png`.

```azurecli-interactive
# Set the content type
az storage blob update
    --container-name mystoragecontainer 
    --name blobName 
    --content-type image/png
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>Zobrazení a úprava metadat a vlastností objektu blob

Každý objekt blob má několik vlastností definovaných službami, které můžete zobrazit pomocí příkazu [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show), včetně názvu, typu, délky a dalších nastavení. Objekt blob můžete také nakonfigurovat vlastními vlastnostmi a jejich hodnotami pomocí příkazu [az storage blob metadata update](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_update).

V tomto příkladu nejdřív zobrazíme vlastnosti objektu blob definované službami a potom budeme tento objekt blob aktualizovat pomocí dvou vlastních vlastností metadat. Nakonec zobrazíme vlastnosti metadat objektu blob a jejich hodnoty pomocí příkazu [az storage blob metadata show](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_show).

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Vytvoření sdíleného přístupového podpisu (SAS)

Sdílené přístupové podpisy (SAS) poskytují způsob, jak k objektům v účtu úložiště udělit omezený přístup, aniž byste vystavili přístupové klíče k účtu úložiště. Pokud chcete vytvořit identifikátor URI, který povoluje přístup k privátnímu prostředku, vytvoříte token SAS s požadovanými oprávněními a dobou platnosti (dobou trvání) a připojíte ho jako řetězec dotazu k adrese URL prostředku. Vytvoříte tak pro SAS kompletní identifikátor URI. Každý s tímto identifikátorem URI SAS (adresa URL prostředku plus token SAS) má k SAS přístup po dobu platnosti tokenu SAS. Můžete například povolit oprávnění ke čtení pro privátní objekt blob na dobu dvou minut, aby si ho někdo mohl prohlédnout.

V následujících krocích zakážete veřejný přístup k vašemu kontejneru, otestujete výhradně privátní přístup a potom vygenerujete identifikátor URI SAS a otestujete ho.

### <a name="disable-container-public-access"></a>Zákaz veřejného přístupu ke kontejneru

Nejdřív nastavte úroveň přístupu kontejneru na `off`. Ta určuje, že ke kontejneru a jeho objektům blob není možný přístup bez sdíleného přístupového podpisu nebo přístupového klíče účtu úložiště.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Ověření privátního přístupu

Pokud chcete ověřit, že pro objekty blob v tomto kontejneru neexistuje žádné veřejné oprávnění ke čtení, získejte adresu URL jednoho z objektů blob v tomto kontejneru pomocí příkazu [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url).

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

V privátním okně prohlížeče přejděte na adresu URL objektu blob. Zobrazí se vám chyba `ResourceNotFound`, protože objekt blob je privátní a nezahrnuli jste sdílený přístupový podpis.

### <a name="create-a-sas-uri"></a>Vytvoření URI SAS

Nyní vytvoříme identifikátor URI SAS, který k tomuto objektu blob umožňuje přístup. V následujícím příkladu nejdřív naplníme proměnnou s adresou URL pro tento objekt blob pomocí příkazu [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url) a potom další proměnnou naplníme tokenem SAS vygenerovaným pomocí příkazu [az storage blob generate-sas](/cli/azure/storage/blob#az_storage_blob_generate_sas). Nakonec odešleme výstup v podobě úplného identifikátoru URI SAS pro objekt blob zřetězením těchto dvou proměnných, které oddělí oddělovač řetězce dotazu `?`.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Otestování identifikátoru URI SAS

V privátním okně prohlížeče přejděte na úplný identifikátor URI SAS, který jste v předchozím fragmentu kódu zobrazili pomocí příkazu `echo`. Tentokrát se chyba nezobrazí a můžete objekt blob zobrazit nebo stáhnout.

Počkejte dostatečně dlouho, aby platnost adresy URL vypršela (v tomto příkladu dvě minuty) a potom na identifikátor URI přejděte v jiném privátním okně prohlížeče. Teď se vám chyba `AuthenticationFailed` zobrazí, protože platnost tokenu SAS vypršela.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již ze skupiny prostředků nepotřebujete žádné prostředky, včetně účtu úložiště, který jste vytvořili, a všech objektů blob, které jste v tomto kurzu odeslali, odstraňte ji pomocí příkazu [az group delete](/cli/azure/group#az_group_delete) příkaz.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základy práce s objekty blob ve službě Azure Storage:

> [!div class="checklist"]
> * Vytvoření kontejneru
> * Odeslání souboru (objektu blob) do kontejneru
> * Zobrazí seznam objektů blob v kontejneru
> * Stažení objektu blob z kontejneru
> * Kopírování objektu blob mezi účty úložiště
> * Odstranění objektu blob
> * Zobrazení a úprava metadat a vlastností objektu blob
> * Správa zabezpečení se sdíleným přístupovým podpisem (SAS)

Následující materiály poskytují další informace o práci s Azure CLI a také o práci s prostředky ve vašem účtu úložiště.

* Azure CLI
  * [Přihlášení pomocí Azure CLI 2.0](/cli/azure/authenticate-azure-cli) – Seznamte se s různými metodami ověřování s využitím CLI, včetně neinteraktivního přihlášení přes [instanční objekt](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) pro spouštění bezobslužných skriptů Azure CLI.
  * [Přehled příkazů Azure CLI 2.0](/cli/azure/)
* Microsoft Azure Storage Explorer
  * [Průzkumník služby Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
