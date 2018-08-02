---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí Azure CLI | Microsoft Docs
description: V tomto rychlém startu použijete v úložišti objektů (blob) Azure CLI. Pak použijete rozhraní příkazového řádku k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: 106c84a6eaff3766c5308ccb130dc7c21e7ec4d8
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397008"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí Azure CLI

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete ho používat ve svém prohlížeči prostřednictvím služby Azure Cloud Shell. Můžete ho také nainstalovat v systému macOS, Linux nebo Windows a spouštět z příkazového řádku. V tomto rychlém startu zjistíte, jak pomocí Azure CLI nahrávat data do úložiště objektů blob v Azure a stahovat data z úložiště objektů blob v Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Svou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob se vždy nahrávají do kontejneru. Skupiny objektů blob můžete organizovat podobně, jako organizujete soubory do složek na svém počítači.

K vytvoření kontejneru pro ukládání objektů blob použijte příkaz [az storage container create](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Nahrání objektu blob

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Většina souborů uložených v úložišti objektů blob se ukládá jako objekty blob bloku. Doplňovací objekty blob se používají, když je k existujícímu objektu blob potřeba přidat data a neměnit přitom jeho stávající obsah, například pro účely protokolování. Objekty blob stránky zálohují soubory virtuálního pevného disku virtuálních počítačů IaaS.

Nejprve vytvořte soubor, který nahrajete do objektu blob.
Pokud používáte Azure Cloud Shell, vytvořte soubor následujícím způsobem: Spusťte příkaz `vi helloworld`, po otevření souboru stiskněte **Insert**, zadejte Hello world a stiskněte **Esc**, zadejte `:x` a stiskněte **Enter**.

V tomto příkladu nahrajete objekt blob do kontejneru, který jste vytvořili v předchozím kroku, a to pomocí příkazu [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Pokud jste vytvořili soubor výše popsaným způsobem ve službě Azure Cloud Shell, můžete místo toho použít tento příkaz rozhraní příkazového řádku (všimněte si, že i když byste za normálních okolností museli zadat cestu, teď to nebylo potřeba, protože soubor se vytvořil v základním adresáři):

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld
    --file helloworld
```

Tato operace vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval. Než budete pokračovat, můžete nahrát libovolné množství souborů.

Pokud chcete nahrát více souborů najednou, můžete použít příkaz [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch).

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Pomocí příkazu [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) zobrazte seznam objektů blob v kontejneru.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Stažení objektu blob

Pomocí příkazu [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) stáhněte objekt blob, který jste nahráli dříve.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Přenos dat pomocí AzCopy

Nástroj [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) představuje další možnost vysoce výkonného a skriptovatelného přenosu dat pro službu Azure Storage. Pomocí AzCopy můžete přenášet data do a ze služeb Blob, File a Table Storage.

Tady je jednoduchý příklad příkazu AzCopy pro nahrání souboru *myfile.txt* do kontejneru *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již ze skupiny prostředků nepotřebujete žádné prostředky, včetně účtu úložiště, který jste vytvořili v rámci tohoto rychlého startu, odstraňte ji pomocí příkazu [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a kontejnerem v úložišti objektů blob v Azure. Další informace o práci s objekty blob ve službě Azure Storage najdete v kurzu práce s úložištěm objektů blob v Azure.

> [!div class="nextstepaction"]
> [Postup: Operace s úložištěm objektů blob pomocí Azure CLI](storage-how-to-use-blobs-cli.md)
