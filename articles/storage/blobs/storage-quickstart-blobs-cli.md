---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí Azure CLI | Microsoft Docs
description: V tomto rychlém startu použijete v úložišti objektů (blob) Azure CLI. Pak použijete rozhraní příkazového řádku k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: rogarana
ms.openlocfilehash: 1819d1dba777d97590c0b02a89cde3b46af78fff
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749231"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Rychlý start: Nahrávání, stahování a výpis objektů BLOB pomocí Azure CLI

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete ho používat ve svém prohlížeči prostřednictvím služby Azure Cloud Shell. Můžete ho také nainstalovat v systému macOS, Linux nebo Windows a spouštět z příkazového řádku. V tomto rychlém startu zjistíte, jak pomocí Azure CLI nahrávat data do úložiště objektů blob v Azure a stahovat data z úložiště objektů blob v Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Svou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob se vždy nahrávají do kontejneru. Skupiny objektů blob můžete organizovat podobně, jako organizujete soubory do složek na svém počítači.

K vytvoření kontejneru pro ukládání objektů blob použijte příkaz [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Nahrání objektu blob

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Většina souborů uložených v úložišti objektů blob se ukládá jako objekty blob bloku. Doplňovací objekty blob se používají, když je k existujícímu objektu blob potřeba přidat data a neměnit přitom jeho stávající obsah, například pro účely protokolování. Objekty blob stránky zálohují soubory virtuálního pevného disku virtuálních počítačů IaaS.

Nejprve vytvořte soubor, který nahrajete do objektu blob.
Pokud používáte Azure Cloud Shell, vytvořte soubor následujícím způsobem: Spusťte příkaz `vi helloworld`, po otevření souboru stiskněte **Insert**, zadejte Hello world a stiskněte **Esc**, zadejte `:x` a stiskněte **Enter**.

V tomto příkladu nahrajete objekt blob do kontejneru, který jste vytvořili v předchozím kroku, a to pomocí příkazu [az storage blob upload](/cli/azure/storage/blob).

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

Pokud chcete nahrát více souborů najednou, můžete použít příkaz [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Pomocí příkazu [az storage blob list](/cli/azure/storage/blob) zobrazte seznam objektů blob v kontejneru.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Stažení objektu blob

Pomocí příkazu [az storage blob download](/cli/azure/storage/blob) stáhněte objekt blob, který jste nahráli dříve.

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

Pokud již ze skupiny prostředků nepotřebujete žádné prostředky, včetně účtu úložiště, který jste vytvořili v rámci tohoto rychlého startu, odstraňte ji pomocí příkazu [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a kontejnerem v úložišti objektů blob v Azure. Další informace o práci s objekty blob ve službě Azure Storage najdete v kurzu práce s úložištěm objektů blob v Azure.

> [!div class="nextstepaction"]
> [Postup: Operace úložiště objektů BLOB pomocí Azure CLI](storage-how-to-use-blobs-cli.md)
